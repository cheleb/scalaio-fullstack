# ZIO 101

ZIO is a library for asynchronous and concurrent programming in Scala. 


Simplified, ZIO is a data type that represents a computation:
````md magic-move

```java
Future<T> // A computation that may produce a T or fail with an exception of type Throwable
```

```scala
trait ZIO[-R, +E, +A]
```
```scala {*|1,4|1,3|1,2}
trait ZIO[
  -R, // that may require an `R`
  +E, // that may fail with an error of type `E`
  +A  // that may succeed with a value of type `A`
]
```
````
<div v-click>
ZIO is a functional effect system:
</div>
<v-clicks depth="2">

 * describe the effects of a program in a purely functional way
   - no side effects
   - Hence `Runable[A]` is a better analogy than `Future[A]`
 * compose effects in a purely functional way.
 * run effects with an interpreter.

</v-clicks>


---

# ZIO 101


A simple mental model is to think of ZIO as a function:
```scala
type ZIO[R,E,A] = 
                    R => Either[E, A]
```
<div v-click="+1">
Many aliases are provided for common use cases:

```scala {*|1|2|3|4|5}
type IO[+E, +A]   = ZIO[Any, E, A]         // Succeed with an `A`, may fail with `E`        , no requirements.
type Task[+A]     = ZIO[Any, Throwable, A] // Succeed with an `A`, may fail with `Throwable`, no requirements.
type RIO[-R, +A]  = ZIO[R, Throwable, A]   // Succeed with an `A`, may fail with `Throwable`, requires an `R`.
type UIO[+A]      = ZIO[Any, Nothing, A]   // Succeed with an `A`, cannot fail              , no requirements.
type URIO[-R, +A] = ZIO[R, Nothing, A]     // Succeed with an `A`, cannot fail              , requires an `R`.
```
</div>

---

# ZIO 101

````md magic-move {lines: true, at:1}
```scala
val simple: ZIO[Any, Nothing, Int] = ZIO.succeed(42)
```
```scala
val simple: UIO[Int] = ZIO.succeed(42) // Succeed with an `A`, cannot fail, no requirements.
```
```scala
val simple: UIO[Int] = ZIO.succeed(42) // ~ Runable[A]
```
```java
// Java
var simple = new Runnable<Integer> {
  int run() = return 42;
}
```
```scala
val simple: UIO[Int] = ZIO.succeed(42) // ~ Runable[A]
```
```scala
val simple:                  UIO[Int]    = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int): UIO[String] = ZIO.succeed(s"The answer is $i")
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)

```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)

val program: UIO[Int] = simple
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)

val program: UIO[String] = simple.flatMap(i => whatIsTheAnswer(i))
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)

val program: Task[Unit] = simple.flatMap(i => whatIsTheAnswer(i))
                                .flatMap(str => sayIntLoud(str))
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)

val program: Task[Unit] = simple.flatMap(whatIsTheAnswer)
                                .flatMap(sayIntLoud)
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)

val program: Task[Unit] = simple flatMap whatIsTheAnswer flatMap sayIntLoud
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)

val program: Task[Unit] = simple flatMap whatIsTheAnswer
                              💥 flatMap sayIntLoud
```
```scala {*|7-8|7-11}

val simple:                     UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)

extension [A, E, B](zio: ZIO[A, E, B])
    def |>[C, E2 <: E](f: B => ZIO[A, E2, C]): ZIO[A, E, C] = zio.flatMap(f)

val program: Task[Unit] = simple |> whatIsTheAnswer
                                 |> flatMap sayIntLoud
```
```scala {*|1|9-10}
import zorglub.flatmapthatshit.*

val simple:                     UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)

val program: Task[Unit] = simple |> whatIsTheAnswer
                                |> flatMap sayIntLoud
```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message)

```

```scala
val simple:                             UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message).repeatN(i%2)

```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message).repeatN(i%2)

val program: Task[Unit] = simple.flatMap(i => whatIsTheAnswer(i))
                                .flatMap(str => sayIntLoud(str, 💥i)) // Won't compile
```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message).repeatN(i%2)

val program: Task[Unit]  = simple.flatMap { i => 
                                              whatIsTheAnswer(i)
                                                 .flatMap {
                                                    str => sayIntLoud(str, i)
                                                  }
                                  }

```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42) // ~ Runable[A]

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message).repeatN(i%2)

val program: Task[Unit] = for { // For comprehension
    i   <- simple
    str <- whatIsTheAnswer(i)
    _   <- sayIntLoud(str)
  } yield ()
```

```scala
private val program   =
    for {
      _ <- runMigrations
      _ <- startServer
    } yield ()
```
```scala
private val program  =
    for {
      _ <- runMigrations //  ZIO[FlywayService, Throwable, Unit]
      _ <- startServer
    } yield ()
```
```scala
private val program =
    for {
      _ <- runMigrations //  ZIO[FlywayService, Throwable, Unit]
      _ <- startServer   //  ZIO[UserService & OrganisationService & JWTService & Server, IOException, Unit]
    } yield ()
```

```scala
private val program : ZIO[FlywayService & UserService & OrganisationService & JWTService & Server, Throwable, Unit]  =
    for {
      _ <- runMigrations //  ZIO[FlywayService, Throwable, Unit]
      _ <- startServer   //  ZIO[UserService & OrganisationService & JWTService & Server, IOException, Unit]
    } yield ()
```
````


