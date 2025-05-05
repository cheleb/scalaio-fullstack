## Scala Syntax - Flatmap that shit

````md magic-move

```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val res: Option[Int] = 
    for {
        one   <- roman2arabic.get("I")        // Option
        two   <- roman2arabic.get("II")       // Option
        three <- roman2arabic.get("III")      // Option
    } yield one + two + three // Some(6)
```
```scala
// Future

val res: Future[User] = 
    for {
        user       <- httpClient.get("http://anapi.com/user") // Future
        userDetail <- userRepo.findUserById(user.id)          // Future
        _          <- logger.write(s"accessed ${user.id} ")   // Future
    } yield user
```
```scala
// IO, Cats effects

val res: IO[User] = 
    for {
        user       <- httpClient.get("http://anapi.com/user?id=1") // IO
        userDetail <- userRepo.findUserById(user.id)               // IO
        _          <- logger.write(s"accessed ${user.id} ")        // IO
    } yield user
```
```scala
// ZIO

val res: UIO[User] = 
    for {
        user       <- httpClient.get("http://anapi.com/user?id=1") // ZIO
        userDetail <- userRepo.findUserById(user.id)               // ZIO
        _          <- logger.write(s"accessed ${user.id} ")        // ZIO
    } yield user
```
````

<v-clicks depth="2">

- We have effects.
  - Future
  - IO, Cats effects
  - ZIO
  - Kyo
</v-clicks>


---

## Scala Syntax - the hard part

<v-clicks>

- opaque types
- type classes
- implicits
- extension methods

</v-clicks>


---

## Scala Syntax - the hard part / Opaque types


````md magic-move
```scala
case class User(fullname: String,
                 email: String,
                 password: String,
                 age: Int)
```
```scala
case class User(fullname: String,
                 email: String,
                 password: String,
                 age: Int)

// Usage:
val john = User("john doe", "john.doe@foo.bar", "notsecured", 42)
```
```scala
case class User(fullname: String,
                 email: String,
                 password: String,
                 age: Int)

// Usage:             💥               💥              💥       💥
val john = User("notsecured", "john.doe&foo.bar", "john doe", -42)
```
```scala
case class User(fullname: Fullname,
                 email: Email,
                 password: Password,
                 age: Age)
//Usage:
// V1
case class Fullname (name: String)
case class Email (email: String)
case class Password (password: String)

val john = User(Fullname("john doe"), Email("john.doe@foo.bar"), Password("notsecured"), Age(42))
```
````

<v-clicks depth="2">

- Ok, but what is the problem with this code?
  - Boilerplate
  - Boxing / unboxing

</v-clicks>




---

# Scala Syntax - Opaque types

````md magic-move {lines: true, at:1}
```scala
case class User( fullname: Fullname,
                 email: Email,
                 password: Password,
                 age: Age)

case class Fullname (name:     String)
case class Email    (email:    String)
case class Password (password: String)
```
```scala
case class User( fullname: Fullname,
                 email: Email,
                 password: Password,
                 age: Age)

opaque type Fullname = String
opaque type Email = String
opaque type Password = String
opaque type Age = Int
```
```scala
case class User( fullname: User.Fullname,
                 email: User.Email,
                 password: User.Password,
                 age: User.Age)
object User:
  opaque type Fullname = String
  opaque type Email = String
  opaque type Password = String
  opaque type Age = Int
```
```scala
case class User( fullname: User.Fullname,
                 email: User.Email,
                 password: User.Password,
                 age: User.Age)
object User:
  opaque type Fullname = String

  object Fullname:
    def apply(value: String): Fullname =
      if value.isEmpty then
        throw new IllegalArgumentException("Fullname cannot be empty")
      else value
  // ...
```
```scala
case class User( fullname: User.Fullname,
                 email: User.Email,
                 password: User.Password,
                 age: User.Age)

object User:
  opaque type Fullname = String

  object Fullname:
    def apply(value: String): Either[String, Fullname] =
      if value.isEmpty then Left("Fullname cannot be empty")
      else Right(value)
    // ...
```
```scala
object User:
  opaque type Fullname = String
  opaque type Email = String
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
val johnOrError: Either[String, User] = User.build("john doe", "john.doe@foo.bar", "notsecured", 42)
```
```scala
User.build("john doe", "john.doe@foo.bar", "notsecured", 42) match
  case Right(user) => 
     // do something with user
  case Left(error) =>
     // Handle error, 402 
```
````

<v-clicks at="2" depth="2">

- Boilerplate +/-
  - this code does not exist:
    - HTTP marshaling (Tapir)
    - Database mapping
- No more Boxing / unboxing
</v-clicks>



---

# Scala Syntax - Implicits

Implicit is what make scala really different.

<v-clicks depth="2">


- Implicit is a way to pass values implicitly to functions or methods.
- Meh, why ?
- the good, the bad and the ugly implicits
- Implicit scope <span v-click="+5"> 💡 </span>

</v-clicks>


----

# Scala Syntax - Implicits

````md magic-move {lines: true, at:1}
```scala
// Implicit
def showUser(implicit user: User): String = user.toString
```
````


---

# Scala Implicits

````md magic-move {lines: true, at:2}

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