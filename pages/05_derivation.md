# Typesystem best friend vs boilerplate !

What is a type ?

A type is a <span v-mark="{type:'underline', color:'orange', at:1}">set of values</span> and a set of operations that can be performed on those values.

---

# Scala type system

### Rich type system

<div grid="~ cols-[30%_70%] gap-2">

<div>
<v-clicks depth="3">
  
  - Generic variance
      - invariant
      - covariant
      - contravariant
    - union types
    - intersection types
    - higher-kinded types
    - type classes
  - Compile time operations
    - context functions
    - extension
    - macros
    - metaprogramming
</v-clicks>

</div>

<div style="margin-top: 4em">

````md magic-move {lines: true, at:1}
```scala
trait Animal
trait Dog extends Animal
trait Cat extends Animal 
```
```scala
trait Animal
trait Dog extends Animal
trait Cat extends Animal 

val animals: Array[Animal] = Array[Dog]()
```
```scala
trait Animal
trait Dog extends Animal
trait Cat extends Animal 

val animals: Array[Animal] = Array[Dog]() 💥
```
```scala
trait Animal
trait Dog extends Animal
trait Cat extends Animal 

trait List[+A] // is co-variant "+"

val animals: List[Animal] = List.empty[Dog]

```

```scala
trait Animal
trait Dog extends Animal
trait Cat extends Animal 

trait Vet[-A] // is contra-variant "-"

val veterinary: Vet[Dog] = Vet[Animal]("Dr. Who") // Contra-variant
```

```scala
type pet = Cat | Dog 
```
```scala
type Environement = Database & RabbitMQ 
```
```scala
trait Functor[F[_]] extends Invariant[F]: 
  def map[A, B](fa: F[A])(f: A => B): F[B]
```

````
</div>
</div>





 
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



---
transition: fade
layout: two-cols
---

# Type class

````md magic-move
```scala
case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)
```
```scala
case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.toString)
```
```scala
case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.toString)
// Leaks sensitive data:  User("john", "doe", "john.doe@example.com", "notsecured")
```
```scala
case class User(firsname: String,
                lastname: String,
                email: String,
                password: String):
    def show: String = s"$firstname $lastname <$email>"

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.toString) ❌ // Linter error
```
```scala
case class User(firsname: String,
                lastname: String,
                email: String,
                password: String):
    def show: String = s"$firstname $lastname <$email>"

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.show)

// User: john doe <john.doe@example.com">
```
```scala
//



val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.toString) ❌ // Linter error

```
```scala
extension (user: User) 
  def show: String =
     s"${user.firstname} ${user.lastname} <$email>"

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.toString) ❌ // Linter error

```
```scala {*|2-4|*}

extension (user: User) 
  def show: String =
     s"${user.firstname} ${user.lastname} <$email>"

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.show)

// User: john doe <john.doe@example.com">
```
```scala

extension (user: User)(using sa: Show[User]) 
  def show: String =
     s"${user.firstname} ${user.lastname} <$email>"

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.show)

// User: john doe <john.doe@example.com">
```
```scala

extension (user: User)(using sa: Show[User]) 
  def show: String =
     sa.show(user)

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.show)

// User: john doe <john.doe@example.com">
```
```scala

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.show)

// User: john doe <john.doe@example.com">
```
```scala

val user: User = fetchFromDB("john.doe@example.com")
Logger.info(user.show)

// User: john doe <john.doe@example.com">
```
````

::right::

<br />
Somewhere else ...
<br />

<div v-click="+5">

````md magic-move {at:8}
```scala
//



case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)
```
```scala
trait Show[A]:
  def show(a: A): String


case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)
```
```scala
trait Show[A]:
  def show(a: A): String

case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)

object User:
  given Show[User] with
    def show(user: User): String =
      s"${user.firstname} ${user.lastname} <${user.email}>"                
```
```scala {*|*|*} 
trait Show[A]:
  def show(a: A): String

case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)

object User:
  given Show[User] with
    def show(user: User): String =
      s"${user.firstname} ${user.lastname} <${user.email}>"                
```
```scala
trait Show[A]:
  def show(a: A): String

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)

case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)

object User:
  given Show[User] with
    def show(user: User): String =
      s"${user.firstname} ${user.lastname} <${user.email}>"                
```
```scala
trait Show[A]:
  def show(a: A): String

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)

case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)

```
````

</div>


---

# Generic Derivation

````md magic-move
```scala
trait Show[A]:
  def show(a: A): String
```
```scala
trait Show[A]:
  def show(a: A): String

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)
```
```scala
trait Show[A]:
  def show(a: A): String

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)

// ... ...

case class User(firsname: String,
                lastname: String,
                email: String,
                password: String)
object User:
    given Show[User] with
        def show(user: User): String =
        s"${user.firstname} ${user.lastname} <${user.email}>"                
                    
```
```scala
trait Show[A]:
  def show(a: A): String

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)

object Show:
    given Show[String] with
        def show(a: String): String = a
    
    given Show[Int] with
        def show(a: Int): String = a.toString
                        
```
```scala
trait Show[A]:
  def show(a: A): String

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)

object Show:
    given Show[String] with
        def show(a: String): String = a
    
    given Show[Int] with
        def show(a: Int): String = a.toString

    given Show[Password] with
        def show(a: Password): String = "********"
                        
```
```scala
trait Show[A]:
  def show(a: A): String

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)

object Show extends AutoDerivation[Show]:

    given Show[String] with
        def show(a: String): String = a
    
    given Show[Int] with
        def show(a: Int): String = a.toString

    given Show[Password] with
        def show(a: Password): String = "********"
                        
```
```scala
trait Show[A]:
  def show(a: A): String

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)

object Show extends AutoDerivation[Show]:

    // Product type
    override def join[T](ctx: CaseClass[Show, T]): Show[T] = ???
    // Sum type
    override def split[T](ctx: SealedTrait[Show, T]): Show[T] = ???

    given Show[String] with
        def show(a: String): String = a
    
    given Show[Int] with
        def show(a: Int): String = a.toString

    given Show[Password] with
        def show(a: Password): String = "********"
                        
```
````

---
transition: fade
layout: two-cols
---

# Show\[A\]

````md magic-move
```scala
case class User(firsname: String,
                lastname: String,
                email: Email,
                password: Password)
```
```scala
import alib.show.*

case class User(firsname: String,
                lastname: String,
                email: Email,
                password: Password)

val user: User = fetchFromDB("john.doe@example")
Logger.info(user.show ❌)                
```
```scala
import alib.show.*

case class User(firsname: String,
                lastname: String,
                email: Email,
                password: Password)

given Show[User] with
  def show(user: User): String =
    s"${user.firstname} ${user.lastname} <${user.email}>"

val user: User = fetchFromDB("john.doe@example")
Logger.info(user.show)                

```
```scala
import alib.show.*

case class User(firsname: String,
                lastname: String,
                email: Email,
                password: Password)

given Show[User] = Show.derived[User]

val user: User = fetchFromDB("john.doe@example")
Logger.info(user.show)                

```
```scala
import alib.show.*

case class User(firsname: String,
                lastname: String,
                email: Email,
                password: Password) derives Show

val user: User = fetchFromDB("john.doe@example")
Logger.info(user.show)                

```
````


::right::

<br />
<br />

````md magic-move
```scala
package alib.show

trait Show[A]:
  def show(a: A): String

extension [A] (a: A)(using sa: Show[A]) 
  def show: String =
     sa.show(a)

object Show extends AutoDerivation[Show]:
     
     // Type class instance for String, Int, Password
     given Show[Password] with
          def show(a: Password): String = "********"
     given Show[String] with // ...
                             // ...

```
````
