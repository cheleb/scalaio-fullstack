---
transition: fade
layout: two-cols

---

## Java Syntax

````md magic-move {lines: true, at:1}
```java
// Variable declaration
int i = 0;
```
```java
//  Interface
interface Animal 
```
```java
// Generics
interface Veterinary<A>
```
```java
// Methods
interface Veterinary<A> {
    void treat(A animal);
}
```
```java
// Generics with methods
interface Veterinary<A> {
    void treat(A animal);

    <B> A metamorph( B b);
}
```
```java
// Generics with bounds
interface Veterinary<A> {
    void treat(A animal);

    <B extends A> A metamorph( B b);
}
```
```java
// HOF
interface Veterinary<A> {
    void treat(A animal);

    <B extends A> A metamorph( B b);

    String toJson(A a, JsonEncoder<A> encoder );
}
```
````

::right::

## Scala Syntax

````md magic-move {lines: true, at:1}
```scala
// Variable declaration
val i: Int = 0
```
```scala
// Trait
trait Animal
```
```scala
// Generics
trait Veterinary[A]
```
```scala
// Methods
trait Veterinary[A] {
    def treat(animal: A): Unit
}
```
```scala
// Generics with methods
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B](b: B): A
}
```
```scala
// Generics with bounds
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A
}
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A, encoder: JsonEncoder[A]): String
}
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A)(encoder: JsonEncoder[A]): String
}
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A): JsonEncoder[A] => String
}
```
````

---

## Scala 

````md magic-move
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A, encoder: JsonEncoder[A]): String
}
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A)(encoder: JsonEncoder[A]): String
}
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A)(using encoder: JsonEncoder[A]): String
}
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A)(using encoder: JsonEncoder[A]): String
}

// --- 

case class Bird(name: String)

// ---


val vet: Veterinary[Bird] = ???
val flami: Bird = ???

vet.toJson(flamingo)(JsonEncoder.birdEncoder)
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A)(using encoder: JsonEncoder[A]): String
}

// ---
case class Bird(name: String)

// ---

val vet: Veterinary[Bird] = ???
val flami: Bird = ???

vet.toJson(flamingo) 💥
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A)(using encoder: JsonEncoder[A]): String
}

// ---

case class Bird(name: String)

// ---
val vet: Veterinary[Bird] = ???
val flami: Bird = ???

given encoder: JsonEncoder[Bird] = JsonEncoder.birdEncoder

vet.toJson(flamingo)
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A)(using encoder: JsonEncoder[A]): String
}

// --- 

case class Bird(name: String)

object Bird:
  given JsonEncoder[Bird] = ???

// ---

val vet: Veterinary[Bird] = ???
val flami: Bird = ???

vet.toJson(flamingo)
```
```scala
// HOF
trait Veterinary[A] {
    def treat(animal: A): Unit

    def metamorh[B <: A](b: B): A

    def toJson(a: A)(using encoder: JsonEncoder[A]): String
}

object Veterinary:
  given JsonEncoder[Bird] = ???
  given JsonEncoder[Mamal] = ???

// --- 

case class Bird(name: String)

val vet: Veterinary[Bird] = ???
val flami: Bird = ???


vet.toJson(flamingo)
```

````
