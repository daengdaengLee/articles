# Prisma Query Optimization for Nested Reads

If you find yourself reading the same model multiple times within nested reads, it's recommended to **separate them into
a single regular read**.

- Prisma retrieves models inside nested reads as individual queries, allowing multiple reads of the same table.
- **DB queries can be costly.** The fewer you make, the faster it gets.

## Show me the code:

Let's take a look at code that uses nested reads.

```prisma
// schema.prisma

model A {
  id Int @id @default(autoincrement())

  bs B[]
  cs C[]
}

model B {
  id Int @id @default(autoincrement())

  a   A   @relation(fields: [aId], references: [id])
  aId Int 
}

model C {
  id Int @id @default(autoincrement())

  a   A   @relation(fields: [aId], references: [id])
  aId Int 
}
```

```typescript
const bs = await prisma.b.findMany({
  select: {
    id: true,
    a: {
      select: {
        id: true
      }
    }
  }
});
const cs = await prisma.c.findMany({
  select: {
    id: true,
    a: {
      select: {
        id: true
      }
    }
  }
});
const result = { bs: bs, cs: cs };

// Prisma query log: Queries the table A twice.
// prisma:query SELECT "public"."B"."id", "public"."B"."aId" FROM "public"."B" WHERE 1=1 OFFSET $1
// prisma:query SELECT "public"."A"."id" FROM "public"."A" WHERE "public"."A"."id" IN ($1,$2) OFFSET $3
// prisma:query SELECT "public"."C"."id", "public"."C"."aId" FROM "public"."C" WHERE 1=1 OFFSET $1
// prisma:query SELECT "public"."A"."id" FROM "public"."A" WHERE "public"."A"."id" IN ($1,$2) OFFSET $3
```

It's noticeable that model `A` is queried twice. Let's separate it into a single regular read.

```typescript
const bs = await prisma.b.findMany({
  select: {
    id: true,
    aId: true
  }
});
const cs = await prisma.c.findMany({
  select: {
    id: true,
    aId: true
  }
});
const aIds = [
  ...bs.map((b) => {
    return b.aId;
  }),
  ...cs.map((c) => {
    return c.aId;
  })
];
const as = await prisma.a.findMany({
  select: {
    id: true
  },
  where: {
    id: {
      in: aIds
    }
  }
});
const asById = new Map(
  as.map((a) => {
    return [a.id, a];
  })
);
const result = {
  bs: bs.flatMap((b) => {
    const a = asById.get(b.aId);
    return a == null ? [] : [{ id: b.id, a: a }];
  }),
  cs: cs.flatMap((c) => {
    const a = asById.get(c.aId);
    return a == null ? [] : [{ id: c.id, a: a }];
  })
};

// Prisma query log: Queries table A only once.
// prisma:query SELECT "public"."B"."id", "public"."B"."aId" FROM "public"."B" WHERE 1=1 OFFSET $1
// prisma:query SELECT "public"."C"."id", "public"."C"."aId" FROM "public"."C" WHERE 1=1 OFFSET $1
// prisma:query SELECT "public"."A"."id" FROM "public"."A" WHERE "public"."A"."id" IN ($1,$2,$3,$4,$5,$6,$7,$8) OFFSET $9
```

By separating model `A` into a single regular read, one less query is performed.

## No Silver Bullet:

In a Node.js runtime, reconstructing query results into nested forms might sometimes take longer. Verify through testing
if there's actual performance improvement.

Faster execution isn't always the most crucial factor. Expressing domain logic effectively might be even more
significant.
