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
case class Cat(name: String) extends Animal:
    def callMyName: String = s"$name says Woof!"
```
```scala
// Method 
case class Cat(name: String, weight: Int, color: Color) extends Animal:
    
    def callMyName: String = s"$name says Woof!"
    
    def highOrderFunction(predicate: String => Boolean): Boolean = predicate(name)
```
```scala
// Enum
enum Color:
    case Black
    case White
    case Isabella


case class Cat(name: String, weight: Int, color: Color) extends Animal:
    
    def callMyName: String = s"$name says Woof!"
    
    def highOrderFunction(predicate: String => Boolean): Boolean = predicate(name)

```
````

---

# Scala Syntax - the hard part

<v-clicks>

  - opaque types
  - type classes
  - implicits
  - extension methods

</v-clicks>


---

# Scala Syntax - the hard part


```scala
 case class User(fullname: String,
                 email: String,
                 password: String,
                 age: Int)
```

<v-clicks>

  - type safety concern
  - privacy concern
  - serialization topics

</v-clicks>

---

# Scala Syntax - Type safety concern

```scala
 case class User(fullname: String,
                 email: String,
                 password: String,
                 age: Int)
```

Usage:

````md magic-move {lines: true, at:1}
```scala
//
def newUser(fullname: String,
         email: String,
         password: String,
         age: Int): User = 
    // Check the values
    User(fullname, email, password, age)

//
val john = newUser("john doe", "john.doe@foo.bar", "notsecured", 42)
```
```scala
// V1
def newUser(fullname: String,
         email: String,
         password: String,
         age: Int): User = 
    // Check the values
    User(fullname, password, email, age)

//                   💥                 💥              💥        💥
val john = newUser("notsecured", "john.doe@foo.bar", "john doe", -42)
```
````
---

# Scala Syntax - Type safety concern

```scala
case class User(fullname: Fullname,
                 email: Email,
                 password: Password,
                 age: Age)
```
Usage:
````md magic-move {lines: true, at:1}
```scala
// V1
case class Fullname (name: String)
case class Email (email: String)
case class Password (password: String)

val john = newUser(Fullname("john doe"), Email("john.doe@foo.bar"), Password("notsecured"), Age(42))
```
````

<div v-click="2">
  Nice to have, but boring and boxing / unboxing ;'(
</div>

---

# Scala Syntax - Opaque types

```scala
case class User(
    fullname: User.Fullname,
    email: User.Email,
    password: User.Password,
    age: User.Age
)
```
````md magic-move {lines: true, at:1}
```scala
object User:
  opaque type Fullname = String
```
```scala
object User:
  opaque type Fullname = String

  object Fullname:
    def apply(value: String): Fullname =
      if value.isEmpty then
        throw new IllegalArgumentException("Fullname cannot be empty")
      else value
```
```scala
object User:
  opaque type Fullname = String

  object Fullname:
    def apply(value: String): Either[String, Fullname] =
      if value.isEmpty then Left("Fullname cannot be empty")
      else Right(value)
```
```scala
object User:
  opaque type Fullname = String

 // ...
 def build(
      fullname: String,
      email: String,
      password: String,
      age: Int
  ): Either[String, User] =
    for {
      fn <- Fullname(fullname)
      em <- Email(email)
      pw <- Password(password)
      ag <- Age(age)
    } yield User(fn, em, pw, ag)

```
````
---

# Scala Syntax

### Invalid state not representable.


````md magic-move {lines: true, at:1}
```scala
val john = User.build("john doe", "john.doe@foo.bar", "notsecured", 42)
```
```scala
val john: User = ... // Marshalling result
```
````




---

# Scala Syntax - Implicits

````md magic-move {lines: true, at:1}

```scala

def showUser(user: User)(using show: Show[User]): String = 
    show.show(user)
```

````

---

# Scala Syntax - the hard part

````md magic-move {lines: true, at:1}
```scala
// extension method
extension (cat: Cat)
    def asJson: String = json"{
       "name": "$name"
    }"
```
````