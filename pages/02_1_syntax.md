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

## Scala Syntax - Flatmap that shit

````md magic-move
```text
Don't fear the monad!
```
```text
 Who don't know what is an Option ? ✋
```

```scala
sealed trait Option[+A]

case class Some[+A](value: A) extends Option[A]

case object None              extends Option[Nothing]
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne = roman2arabic("I") // ???
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne = roman2arabic("I") // 1
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne = roman2arabic("") // ???
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne = roman2arabic("") // throw new NoSuchElementException
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne: Option[Int] = arabic2roman.get("I")
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne: Option[Int] = arabic2roman.get("I") // Some(1)
val maybeZero: Option[Int] = arabic2roman.get("") // None
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeZero:  Option[Int] = arabic2roman.get("II")    // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("III")   // Some(3)
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeZero:  Option[Int] = arabic2roman.get("II")    // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("III")   // Some(3)

val sum = // maybeOne + maybeZero + mayBeThree
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeZero:  Option[Int] = arabic2roman.get("II")    // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("III")   // Some(3)

val sum = // maybeOne + maybeZero + mayBeThree

val sum = maybeOne.get + maybeZero.get + mayBeThree.get
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeTwo:   Option[Int] = arabic2roman.get("II")    // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("💥")    // Some(3)

val sum = // maybeOne + maybeTwo + mayBethree

val sum = maybeOne.get + maybeTwo.get + mayBeThree.get  // 💥 java.util.NoSuchElementException: None.get

```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeTwo:   Option[Int] = arabic2roman.get("II")    // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("💥")    // Some(3)

val sum = // maybeOne? + maybeTwo? + mayBethree?
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeTwo:   Option[Int] = arabic2roman.get("II")    // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("💥")    // Some(3)

val sum = maybeOne.flatMap( one => ??? )
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeTwo:  Option[Int] = arabic2roman.get("II")     // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("💥")    // Some(3)

val sum = maybeOne.flatMap( one => 
             maybeTwo.flatMap( two => 
                mayBeThree.map( three => one + two + three )
             )
         )
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeTwo:  Option[Int] = arabic2roman.get("II")     // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("💥")    // Some(3)

val sum: Option[Int] = maybeOne.flatMap( one => 
             maybeTwo.flatMap( two => 
                mayBeThree.map( three => one + two + three )
             )
         ) // None
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeTwo:   Option[Int] = arabic2roman.get("II")    // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("III")   // Some(3)

val sum: Option[Int] = maybeOne.flatMap( one => 
             maybeTwo.flatMap( two => 
                mayBeThree.map( three => one + two + three )
             )
         ) // Some(6)
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = arabic2roman.get("I")     // Some(1)
val maybeTwo:   Option[Int] = arabic2roman.get("II")    // Some(2)
val mayBeThree: Option[Int] = arabic2roman.get("III")   // Some(3)

val sum: Option[Int] = 
    for {
        one <- maybeOne
        two <- maybeTwo
        three <- mayBeThree
    } yield one + two + three // Some(6)
```
```scala
val res: Option[Int] = 
    for {
        one   <- arabic2roman.get("I")
        two   <- maybarabic2roman.get("II")
        three <- arabic2roman.get("III")
    } yield one + two + three // Some(6)
```
```scala
val res: Option[Int] = 
    for {
        one   <- arabic2roman.get("I")        // Option
        two   <- maybarabic2roman.get("II")   // Option
        three <- arabic2roman.get("III")      // Option
    } yield one + two + three // Some(6)
```
```scala

val res: Future[Int] = 
    for {
        one   <- httpClient.get("http://api.com/one") // Future
        two   <- database.read(one)                   // Future
        three <- logger.write(two)                    // Future
    } yield two
```
```scala
val res: IO[Int] = 
    for {
        one   <- httpClient.get("http://api.com/one") // IO
        two   <- database.read(one)                   // IO
        three <- logger.write(two)                    // IO
    } yield two
```
```scala
val res: UIO[Int] = 
    for {
        one   <- httpClient.get("http://api.com/one") // ZIO
        two   <- database.read(one)                   // ZIO
        three <- logger.write(two)                    // ZIO
    } yield two
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
val john = newUser("john doe", "john.doe@foo.bar", "notsecured", 42)
```
```scala
 case class User(fullname: String,
                 email: String,
                 password: String,
                 age: Int)

// Usage:
val john = User("notsecured", "john.doe@foo.bar", "john doe", -42)
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