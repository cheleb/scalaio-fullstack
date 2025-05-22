---
transition: fade
layout: two-cols
---

## Metaprog / ADT

In scala, we can use ADT to represent data types.

<v-clicks>

- ADT = Algebraic Data Type
  - Product type = case class
  - Sum type = sealed trait
</v-clicks>

````md magic-move
```scala
.
```
```scala
// Product type
case class Box(size: Size,
               color: Color)
```
```scala
// Product type ??
case class Box(size: Size,
               color: Color)
// Sum type ??
enum Size:
  case Small
  case Medium
  case Large

enum Color:
  case Red
  case Green
  case Blue(dark: Boolean)
```
```scala
// Product type
case class Box(size: Size,
               color: Color)
// Sum type
enum Size:
  case Small
  case Medium
  case Large

enum Color:
  case Red
  case Green
  case Blue(dark: Boolean)
```

````


::right::

<img src="../images/datatypes.jpg" alt="Architecture Diagram" width="100%"/>

