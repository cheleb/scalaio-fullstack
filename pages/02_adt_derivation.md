---

# Scala Full Stack


<h2>Complex, but Safer</h2>
<hr style="margin: 1em"/> 

<v-clicks depth="2">

  * Tame the complexity with types.
    - ✅ Type safety 101.
    - Algebraic Data Type.
    - Generic Derivation.
</v-clicks>

<!-- 

- Type safety means that you can catch errors at compile time.
- "Show" the types, and the compiler will do the rest. 

  given Show[Password] with
    def show(p: Password): String = "******"


 -->

---


# ADT: Algebraic Data Type

## A kind of composite type

### (a type formed by combining other types)

<v-clicks depth="4" style="margin: 2em">
  
  How can we combine types?

  - As sum type ( A or B or C)
  - As product type ( A and B and C)
</v-clicks>

<div grid="~ cols-2">
  <div>
<v-clicks depth="4" style="margin: 2em">
  
   Sum type: 
   - Sealed trait
   - Enum
</v-clicks>    
  </div>
  <div>
<v-clicks depth="4" style="margin: 2em">
  
   Product type: 
   - Case class
</v-clicks>    
  </div>
</div>


---

# ADT by Example

## Sum Type
````md magic-move
```scala
sealed trait Shape
```
```scala
sealed trait Shape
case class Circle(radius: Double) extends Shape
```
```scala
sealed trait Shape
case class Circle(radius: Double) extends Shape
case class Rectangle(width: Double, height: Double) extends Shape
case class Square(side: Double) extends Shape
```
````

<v-click>

````md magic-move
```scala
enum Color:
  case Red, Green, Blue
  case Yellow(dark: Boolean)
```
```scala
enum Color:
  case Red, Green, Blue        //   3 colors
  case Yellow(dark: Boolean)   //   2 colors
```
```scala
enum Color:                    //   5 colors
  case Red, Green, Blue        //   3 
  case Yellow(dark: Boolean)   // + 2 
```
````
</v-click>
<v-click>

<h2>Product Type</h2>

```scala
case class Owner(name: String, age: Int)
case class Drawing(owner: Owner, shapes: Shape, color: Color)
```

</v-click>

---

# ADT: Generic Derivation

<v-clicks>

* ADT can describe any datastructure, with products and sum types.
* Scala 3, can natively derive typeclass instances from that.

If for any computation, we can handle:
</v-clicks>

<v-clicks depth="4">

   * an Int
   * a String
     * Then we can handle a Shape or a Owner
     * ... etc shapes, colors and owners.
</v-clicks>  


---

# ADT: Generic Derivation

## JSON Serialization

````md magic-move
```scala
case class Owner(name: String, age: Int)
```
```scala
case class Owner(name: String, age: Int) derives JsonCodec
```
```scala
case class Owner(name: String, age: Int) derives JsonCodec
case class Color derives JsonCodec:
    case Red, Green, Blue
    case Yellow(dark: Boolean)
```
````
<v-click>

## OpenAPI

````md magic-move
```scala
case class Owner(name: String, age: Int)
```
```scala
case class Owner(name: String, age: Int) derives Schema
```
````
</v-click>

<v-click>

#### SQL ...
#### HTML ...
#### ...

</v-click>