# Scala Syntax

````md magic-move {lines: true, at:1}
```scala
// trait ~ java interface
trait Animal
```
```scala
// trait ~ java interface
trait Animal

// ~ java class
class Dog extends Animal
```
```scala
// ~ java interface
trait Animal

// ~ java Record aka immutable class
case class Cat(name: String) extends Animal
```
```scala
// Method 
case class Cat(name: String) extends Animal {

    def hello(): String = {
       s"Hello, $name!"
    }

}
```
```scala
// Method 
case class Cat(name: String) extends Animal:

    def hello(): String = s"Hello, $name!"

```
```scala
// Method 
case class Cat(name: String) extends Animal:

    def hello: String = s"Hello, $name!"

```
```scala
// Method 
case class Cat(name: String) extends Animal:

    def hello = s"Hello, $name!"

```
````

---

## Scala Syntax - Function first citizen

````md magic-move {lines: true, at:1}
```scala
// Method
def hello(name: String): String = s"Hello, $name!"
```
```scala
// Function
val hello: String => String = name => s"Hello, $name!"
```
```scala
// Method
def hello(name: String) : String = s"Hello, $name!"
// Function
val hello:      String => String = name => s"Hello, $name!"
```
````

