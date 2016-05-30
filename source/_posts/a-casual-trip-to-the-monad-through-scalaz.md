---
title: 'A casual trip to the Monad, through Scalaz'
date: 2016-05-30 04:31:20
tags:
---

We had a [meetup](http://reactive-programming-with-scala-and-akka.connpass.com/event/31710/) reading 2nd chapter of <a  href="http://www.amazon.com/gp/product/1783984341/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1783984341&linkCode=as2&tag=tkqubo-20&linkId=3DD3AV3FBDD6745N">Reactive Programming with Scala and Akka</a><img src="https://ir-na.amazon-adsystem.com/e/ir?t=tkqubo-20&l=as2&o=1&a=1783984341" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" /> last Friday.

<a  href="http://www.amazon.com/gp/product/1783984341/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1783984341&linkCode=as2&tag=tkqubo-20&linkId=JASVNACQ5LY5DXPL"><img border="0" src="https://ws-na.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=1783984341&Format=_SL110_&ID=AsinImage&MarketPlace=US&ServiceVersion=20070822&WS=1&tag=tkqubo-20" ></a><img src="https://ir-na.amazon-adsystem.com/e/ir?t=tkqubo-20&l=as2&o=1&a=1783984341" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

The first half of this chapter explains what is functional programming in Scala.  And the latter, through one of the most popular Scala library called [Scalaz](https://github.com/scalaz/scalaz), the important concepts in functional programming; Functors, Applicative Functors, and Monads.

In this blog post, I will explain about my shallow understanding of those three handsome guys.

*Disclaimer: the below is not thorough nor correct definitions of those concepts at all.  It simply is a starting point for understanding them.*

## Functors, Applicative Functors, and Monads, for the first step

If you don't know very much about functors, applicative functional, and monads (like me as a Scala newbie), maybe a table below could help understand them.

| Who are you? | does what, mainly | in which method |
|:--|:--|:--|
| **`Functor`** | *map*s a value `A` in some *container `F`* | *`map[B](f: A => B): F[B]`*|
| **`Applicative`** | *applies* a method `A => B` in some *container `F`* <br />to a value `A` in another *container F* | *`ap[A,B](f: => F[A => B]): F[B]`* |
| **`Monad`** | *flatMap*s a value `A` in some *container `F`* | *`bind[A, B](f: A => F[B]): F[B]`* |

Here, the word *container* means kind of an abstraction of the way of holding value.  `Option`, `List` and `Future` are good examples of them in Scala language.

| type | how it holds value |
|:--|:--|
| `Option[T]` | holds a value of type `T` (`Some(value)`), or nothing (`None`) |
| `List[T]` | holds multiple values of type `T`.<br /> (that is, *homogeneous collection* of type `T`) |
| `Future[T]` | holds some concurrent block that will eventually return<br />the value of type `T`, or `Throwable` as a failure result. |

*NOTE: Strictly speaking, container should actually be called a `type class`.  See [Wikipaedia](https://en.wikipedia.org/wiki/Type_class) for further explanations.*

And *Monad*s `flatMap` (or `bind`), this *flatten*s the nested container as a result of mapping.

Anyway for better understandings let's check out their implemtation in Scalaz's source code with some examples.

### Implementations & Examples

- Functor

```scala
// From Scalaz, details omitted

trait Functor[F[_]] {
  def map[A, B](fa: F[A])(f: A => B): F[B]
  ...
}

object Functor {
  def apply[F[_]](implicit F: Functor[F]): Functor[F] = F
}

class FunctorOps[F[_],A](val self: F[A])(implicit val F: Functor[F]) {
  def map[B](f: A => B): F[B] = F.map(self)(f)
  ...
}

trait FunctorSyntax[F[_]] extends InvariantFunctorSyntax[F] {
  implicit def ToFunctorOps[A](v: F[A]): FunctorOps[F, A] =
    new FunctorOps[F,A](v)(FunctorSyntax.this.F)
  def F: Functor[F]
}

// Examples

import scalaz._, Scalaz._

Functor[List].map(1 to 10) {_ + 1}
// List(2, ..., 11)

Functor[Option].map(Some(10)) {_ + 1}
// Some(11)
```

- Applicative (Applicative extends Apply)

```scala
// From Scalaz, details omitted

trait Apply[F[_]] extends Functor[F] {
  def ap[A,B](fa: => F[A])(f: => F[A => B]): F[B]
  ...
}

trait Applicative[F[_]] extends Apply[F] {
  def point[A](a: => A): F[A]
  def pure[A](a: => A): F[A] = point(a)
  def map[A, B](fa: F[A])(f: A => B): F[B] = ap(fa)(point(f))
  ...
}

object Applicative {
  def apply[F[_]](implicit F: Applicative[F]): Applicative[F] = F
}

class ApplicativeOps[F[_],A](val self: F[A])(implicit val F: Applicative[F]) {
  ...
}

trait ApplicativeSyntax[F[_]] extends ApplySyntax[F] {
  implicit def ToApplicativeOps[A](v: F[A]): ApplicativeOps[F, A] =
    new ApplicativeOps[F,A](v)(ApplicativeSyntax.this.F)
}

// Examples

import scalaz._, Scalaz._

List(1, 2, 4) <*> List({(_:Int) * 3})
// List(3, 6, 12)

List(1, 2, 4) <*> {(_:Int) * 3}.point[List]
// List(3, 6, 12)

// when you want to apply functions at a time
val values = List(1, 2, 4)
val funcs1 = List({(_:Int) * 3})
val funcs2 = List({(_:Int) + 3})

// verbose!
val resultUsingFlatMap: List[Int] =
    funcs1.flatMap { func1 =>
      funcs2.flatMap { func2 =>
        values.map { value =>
          (func1 andThen func2)(value)
        }
      }
    }

// concise!
val resultUsingFor: List[Int] =
  for {
    value <- values
    func1 <- funcs1
    func2 <- funcs2
  } yield (func1 andThen func2)(value)

// more concise!?
// <*> is an alias for ap
val resultUsingApplicative: List[Int] =
  values <*> funcs1 <*> funcs2
```

- Monad

```scala
// From Scalaz, details omitted

trait Bind[F[_]] extends Apply[F] {
  def bind[A, B](fa: F[A])(f: A => F[B]): F[B]
  def ap[A, B](fa: => F[A])(f: => F[A => B]): F[B]
}

trait Monad[F[_]] extends Applicative[F] with Bind[F] {
  def ap[A, B](fa: => F[A])(f: => F[A => B]): F[B]
  ...
}

object Monad {
  def apply[F[_]](implicit F: Monad[F]): Monad[F] = F
}

class MonadOps[F[_],A](val self: F[A])(implicit val F: Monad[F]) {
  ...
}

trait MonadSyntax[F[_]] extends ApplicativeSyntax[F] with BindSyntax[F] {
  implicit def ToMonadOps[A](v: F[A]): MonadOps[F, A] =
    new MonadOps[F,A](v)(MonadSyntax.this.F)
  def F: Monad[F]
}

// Examples

import scalaz._, Scalaz._

val value = "126"
val map = Map(10 -> "ten", 42 -> "the answer")
val toIntOption: String => Option[Int] = value => try { value.toInt.some } catch { case e: NumberFormatException => None }
val toDivBy3Option: Int => Option[Int] = value => (value % 3 == 0) ? (value / 3).some | None

// verbose version
toIntOption(value).flatMap { intValue =>
  toDivBy3Option(intValue).flatMap { divBy3Value =>
    map.get(divBy3Value)
  }
}

// for comprehension
for {
  intValue <- toIntOption(value)
  divBy3Value <- toDivBy3Option(intValue)
  mappedValue <- map.get(divBy3Value)
} yield mappedValue

// with sexy operators
// >>= is an alias for bind
Monad[Option].point(value) >>= toIntOption >>= toDivBy3Option >>= map.get
```

But what are these `XxxOps` and `XxxSyntax`???  This is kind of what we call **pimp my library** pattern in Scala, to add methods on target class or trait without modifying it, using the power of *implicit conversion*.

Its basic structure in Scalaz can be simplified like this:

```scala
class Original

trait OriginalOps(val self: Original) {
  def additionalMethod = ...
}

trait Syntax {
  implicit def ToOriginalOps(original: Original): OriginalOps =
    new OriginalOps(original)
}

object Syntax extends Syntax

// usage
import Syntax._

val original = new Original
original.additionalMethod
```

## By the way,

Is there any easy good way to write some fancy operators like `"η"`, `"⊛"`, or `"∘"`, without simply copy-and-pasting them?   Once in a while I feel like to indulge in using them with a bit of fear about Gestaltzerfall...

## Other helpful resources

- http://eed3si9n.com/learning-scalaz/scalaz-cheatsheet.html
- https://github.com/arosien/scalaz-cheatsheets

## For those who want to dive more into functional programming...

[Oneteam Inc.](https://one-team.com/) is hiring a Scala engineer for server-side development.  Please visit [HERE](https://one-team.com/recruit/)


