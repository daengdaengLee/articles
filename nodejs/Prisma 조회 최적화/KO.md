# Prisma 조회 최적화

같은 모델을 중첩된 조회(nested reads) 안에서 여러 번 읽고 있다면 **하나의 일반 조회**로 분리하세요.

- Prisma는 중첩된 조회 안쪽 모델을 개별 쿼리로 조회합니다. 같은 테이블을 여러 번 조회할 수 있어요.
- **DB 쿼리는 비싸요.** 적게 요청할수록 빠릅니다.

## Show me the code:

중첩된 조회를 사용하는 코드를 봅시다.

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

// Prisma 쿼리 로그: 테이블 A를 두 번 조회합니다.
// prisma:query SELECT "public"."B"."id", "public"."B"."aId" FROM "public"."B" WHERE 1=1 OFFSET $1
// prisma:query SELECT "public"."A"."id" FROM "public"."A" WHERE "public"."A"."id" IN ($1,$2) OFFSET $3
// prisma:query SELECT "public"."C"."id", "public"."C"."aId" FROM "public"."C" WHERE 1=1 OFFSET $1
// prisma:query SELECT "public"."A"."id" FROM "public"."A" WHERE "public"."A"."id" IN ($1,$2) OFFSET $3
```

모델 `A`를 두 번 조회하는 걸 알 수 있습니다. 하나의 일반 조회로 분리해 봅시다.

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

// Prisma 쿼리 로그: 테이블 A를 한 번 조회합니다.
// prisma:query SELECT "public"."B"."id", "public"."B"."aId" FROM "public"."B" WHERE 1=1 OFFSET $1
// prisma:query SELECT "public"."C"."id", "public"."C"."aId" FROM "public"."C" WHERE 1=1 OFFSET $1
// prisma:query SELECT "public"."A"."id" FROM "public"."A" WHERE "public"."A"."id" IN ($1,$2,$3,$4,$5,$6,$7,$8) OFFSET $9
```

모델 `A`를 하나의 일반 조회로 분리하면서 쿼리를 한 번 덜 수행합니다.

## No Silver Bullet:

Node.js 런타임에서 조회 결과를 다시 중첩된 형태로 만드는 일이 오히려 오래 걸릴 수 있습니다. 테스트를 통해 실제로 속도가 개선되었는지 확인하세요.

실행 속도가 빠른 게 항상 가장 중요한 건 아닙니다. 도메인 로직을 잘 표현하는 게 더 중요할 수 있습니다.
