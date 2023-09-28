# Exploring PDF - Basic Object

## TL;DR

- PDF is composed of basic objects.
- There are 8 basic types of objects: Boolean, Numeric, String, Name, Array, Dictionary, Stream, and Null object.
  An indirect object can be referenced with the unique identifier.

## Basic Type Object

### Boolean Object

- An object that represents true and false.
- It is expressed using the `true` and `false` keywords.

### Numeric Object

- An object that represents integers and real numbers.
- It can represent signs and decimal points.
- When following the ISO standard, it is represented in base 10.

### String Object

- A string can be represented in two ways.

Literal string:

- Enclose the string literal between `(` and `)`.
  ```text
  (This is a string)
  ```
- It can be represented across multiple lines using the backslash character.
  ```text
  (These \
  two strings \
  are the same.)
  (These two strings are the same.)
  ```

Hexadecimal string:

- Place the hexadecimal numbers between `<` and `>`.
  ```text
  <4E6F762073686D6F7A206B6120706F702E>
  ```
- Process hexadecimal numbers in pairs, treating them as 1 byte each.
- If the last pair is incomplete, automatically append `00` to complete the byte.

### Name Object

- Define the string representation by appending it after a backslash character.
  `/Type` is an example of a name object "Type".
- Define atomic symbols and does not contain other objects.
- Uniquely defined; the same string representation results in the same object.
- Operates similarly to JS's `Symbol.for`.

### Array Object

- A one-dimensional array that contains objects in order.
- It can contain objects of different types in one array.
- Declare the objects between `[` and `]`.
  ```text
  [549 3.14 false (Ralph) /SomeName]
  ```
- High-dimensional arrays can be declared by including other array objects as elements.

### Dictionary Object

- A table consisting of key-value pairs.
- Use name objects for keys.
- Values can be any type of object, including dictionary objects.
- If the value is a null object, consider the entry as non-existent.
- Represent key-value entries between `<<` and `>>`.
  ```text
  << /Type /Example
     /Subtype /DictionaryExample
     /Version 0.01
     /IntegerItem 12
     /StringItem (a string)
     /Subdictionary << /Item1 0.4
                       /Item2 true
                       /LastItem (not!)
                       /VeryLastItem (OK)
                    >>
  >>

### Stream Object

- Represent a sequence of byte data.
- Comprise a dictionary object representing metadata and byte data wrapped with `stream` and `endstream`.
  ```text
  <<
     ...entries...
  >>
  stream
  ...Zero or more bytes...
  endstream
  ```
- Stream objects must always be declared in the form of indirect objects.
- Used for representing large data like images.

### Null Object

- Represented using the `null` keyword.
- Not equivalent to any other type of value.

## Indirect Object

- Objects labeled with unique identifiers.
- The unique identifier is a combination of object number and generation number.
- The object number is a natural number, usually assigned sequentially but not mandatory.
- The generation number is a non-negative integer, starting from 0 and incrementing with each modification to the
  object.

### Definition

Defined in the following format:

```text
<object number> <generation number> obj
...Object...
endobj
```

Example: String Brillig object with object number 12 and generation number 0

```text
12 0 obj
  ( Brillig )
endobj
```

### Reference

- Indirect objects can be referenced elsewhere in the form `<object number> <generation number> R`.
- For example, the indirect object defined in the example above can be referenced as `12 0 R`.

## Next

- Let's explore the structure of a PDF file.

## Reference

- [PDF Specification Document](https://opensource.adobe.com/dc-acrobat-sdk-docs/pdfstandards/PDF32000_2008.pdf)
