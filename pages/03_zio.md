# ZIO 101

ZIO is a library for asynchronous and concurrent programming in Scala. 

<div v-click="+1">
Simplified, ZIO is a data type that represents a computation:
```scala
trait ZIO[-R, +E, +A]
```
<div v-click="+2">

<ul>
  <li v-click="+2">that may require an environment of type `R`</li>
  <li v-click="+3">that may fail with an error of type `E`</li>
  <li v-click="+4">that may succeed with a value of type `A`
</li>
</ul>
</div>
</div>

---

# ZIO 101


A simple mental model is to think of ZIO as a function:
```scala
type ZIO[R,E,A] = R => Either[E, A]
```
<div v-click="+1">
Many aliases are provided for common use cases:

```scala {*|*|2-3}
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
                                .flatMap(str => sayIntLoud(str, i)) // Won't compile
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
private val program =
    for {
      _ <- runMigrations
      _ <- startServer
    } yield ()
```

```scala
val runMigrations = for {
    flyway <- ZIO.service[FlywayService]
    _ <- flyway.runMigrations().catchSome { case e =>
           ZIO.logError(s"Error running migrations: ${e.getMessage()}")
             *> flyway.runRepair() *> flyway.runMigrations()
         }
  } yield ()
```

```scala
private val startServer =
    for {
      _                               <- Console.printLine("Starting server...")
      (apiEndpoints, streamEndpoints) <- HttpApi.endpointsZIO
      docEndpoints = SwaggerInterpreter()
                       .fromServerEndpoints(apiEndpoints ::: streamEndpoints, "zio-laminar-demo", "1.0.0")
      _ <- serveEndpoints(apiEndpoints, streamEndpoints, docEndpoints)
    } yield ()
```





````


