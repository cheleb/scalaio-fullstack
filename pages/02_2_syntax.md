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
val roman2arabic: Map[String,Int] = Map(
  "I" -> 1,
  "II" -> 2,
  "III" -> 3,
  "IV" -> 4,
  "V" -> 5
)
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

val maybeOne: Option[Int] = roman2arabic.get("I")
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne: Option[Int] = roman2arabic.get("I") // Some(1)
val maybeZero: Option[Int] = roman2arabic.get("") // None
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeZero:  Option[Int] = roman2arabic.get("II")    // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("III")   // Some(3)
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeZero:  Option[Int] = roman2arabic.get("II")    // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("III")   // Some(3)

val sum = // maybeOne + maybeZero + mayBeThree
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeZero:  Option[Int] = roman2arabic.get("II")    // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("III")   // Some(3)

val sum = // maybeOne + maybeZero + mayBeThree

val sum = maybeOne.get + maybeZero.get + mayBeThree.get
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeTwo:   Option[Int] = roman2arabic.get("II")    // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("💥")    // None

val sum = // maybeOne + maybeTwo + mayBethree

val sum = maybeOne.get + maybeTwo.get + mayBeThree.get  // 💥 java.util.NoSuchElementException: None.get

```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeTwo:   Option[Int] = roman2arabic.get("II")    // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("💥")    // None

val sum = // maybeOne? + maybeTwo? + mayBethree?
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeTwo:   Option[Int] = roman2arabic.get("II")    // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("💥")    // None

val sum = maybeOne.flatMap( one => ??? )
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeTwo:  Option[Int] = roman2arabic.get("II")     // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("💥")    // None

val sum = maybeOne.flatMap( one => 
             maybeTwo.flatMap( two => 
                mayBeThree.map( three => one + two + three )
             )
         )
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeTwo:   Option[Int] = roman2arabic.get("II")    // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("💥")    // None

val sum: Option[Int] = maybeOne.flatMap( one => 
             maybeTwo.flatMap( two => 
                mayBeThree.map( three => one + two + three )
             )
         ) // None
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeTwo:   Option[Int] = roman2arabic.get("II")    // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("III")   // Some(3)

val sum: Option[Int] = maybeOne.flatMap( one => 
             maybeTwo.flatMap( two => 
                mayBeThree.map( three => one + two + three )
             )
         ) // Some(6)
```
```scala
val roman2arabic: Map[String,Int] = Map("I" -> 1, "II" -> 2, "III" -> 3, "IV" -> 4, "V" -> 5)

val maybeOne:   Option[Int] = roman2arabic.get("I")     // Some(1)
val maybeTwo:   Option[Int] = roman2arabic.get("II")    // Some(2)
val mayBeThree: Option[Int] = roman2arabic.get("III")   // Some(3)

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
        one   <- roman2arabic.get("I")
        two   <- roman2arabic.get("II")
        three <- roman2arabic.get("III")
    } yield one + two + three // Some(6)
```
````