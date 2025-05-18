---
transition: fade
layout: two-cols
---

# OOP vs FP

<v-clicks depth="1" style="margin: 2em">

- OOP
  - mutable state
  - inheritance
  - polymorphism
  - statements based
- FP
  - immutable state
  - function composition
  - expression based
</v-clicks>

::right::

<div v-click>

<h2> Scala: OOP && FP</h2>

Why not both ?

</div>

<v-clicks>

- better OOP:
  - variances of type parameters
  - type classes: Ad hoc polymorphism
 
</v-clicks>

---

## OOP vs FP

<img alt="OOP vs FP" src="../images/oop-vs-fp.webp" width="70%"/>


---
transition: fade
layout: two-cols
---

## Scala Type system

Rich type system...

<v-clicks depth="2">

- Generics
- Variance ?
  - Invariant
  - Covariant
  - Contravariant
- Intersection Types
- Union Types
- Algebraic Data Types
- Opaque Types

</v-clicks>

::right::

````md magic-move {at:1}

```
. 
```

```scala
class Animal
class Dog extends Animal

// Eq java Array<A> 
class Array[A]
```
```scala
class Animal
class Dog extends Animal

// Quiz 💚 or 💥
class Array[A]

val a: Array[Animal] = new Array[Dog]()

```
```scala
class Animal
class Dog extends Animal

// Invariant
class Array[A]
                       💥
val a: Array[Animal] = new Array[Dog]()

```
```scala
class Animal
class Dog extends Animal

//Covariant
class List[+A]
                      💚 
val a: List[Animal] = new List[Dog]()

```
```scala
trait Animal
class Dog extends Animal

//Contravariant
class Vet[-A]

val a: Vet[Dog] = new Vet[Animal]()

```
```scala
type A = Database & RabbitMQ
```
```scala
type RegisterReponse = User | Unit
```
```scala
enum Option[+A]:
  case Some[A](value: A)
  case None
```
```scala
opaque type Email = String
```
```
...
```
````

<v-clicks>

- Structural Types
- Dependent Function Types
- Type lambdas
- Match types
- Existential types
- Higher-kinded types
- Singleton types
- Refinement types
- Kind polymorphis
</v-clicks>


<!--

This slide may be interesting to show the complexity of the type system. Intention is not to be arrogant :D

But the more contraint your type system have, the more you can rely on compiler. 

 -->

---
transition: fade
layout: two-cols
---
## Scala types and compiler.

Rich type system brings:

<v-clicks depth="2">

- refined types: 
  - check error early
  - overtyping
- make illegal states unrepresentable
- refactor with confidence

</v-clicks>

<div v-click>

And in the meantime:

</div>

<v-clicks>

- reduce boilerplate
- no boxing / unboxing
- enter the metaprogramming world 👈

</v-clicks>



::right::

````md magic-move {at:1}

```scala
case class User(firstname: String,
                lastname: String,
                email: String,
                password: String,
                age: Int)
```
```scala
case class User(firstname: String,
                lastname: String,
                email: String,
                password: String,
                age: Int :| Positive)
```
```scala
case class User(firstname: String,
                lastname: String,
                email: String,
                password: String,
                age: Int :| Positive)

val user = User("John",
                "Doe",
                "john.doe@gmail.com",
                "notsecured",
                -42) // 💥 compilation error              
```
```scala
case class User(firstname: Firstname,
                lastname: Lastname,
                email: Email,
                password: Password,
                age: Int :| Positive)

opaque type Firstname <: String = String
opaque type Password <: String = String


```
```scala
case class User(firstname: Firstname,
                lastname: Lastname,
                email: Email,
                password: Password,
                age: Int :| Positive)

opaque type Password <: String = String

object Password:
  def apply(value: String): Password = 
    if value.length < 8 then
      throw new IllegalArgumentException("Password must be at least 8 characters long")
    else
      value.asInstanceOf[Password]

```
```
This contraints checked will:
 - occur at compile time.
 - be enforced at runtime.

```
```
This contraints can be checked:
 - occur at compile time.
 - be enforced at runtime.

Across the whole code base:
  - from the API to the database.
  - from the database to the API.
```
````


