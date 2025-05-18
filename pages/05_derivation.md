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

extension (a: A)(using sa: Show[A]) 
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
