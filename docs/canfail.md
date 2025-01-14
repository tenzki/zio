---
id: can_fail
title:  "Compile Time Errors for Handling Combinators"
---

ZIO provides a variety of combinators to handle errors such as `orElse`, `catchAll`, `catchSome`, `option`, `either`, and `retry`. However, these combinators only make sense for effects that can fail (i.e. where the error type is not `Nothing`). To help you identify code that doesn't make sense, error handling combinators require implicit evidence `CanFail[E]`, which is automatically available for all types except `Nothing`. The table below includes a list of combinators that only make sense for effects that can fail along with value preserving rewrites.

**ZIO**

Code | Rewrite 
--- | ---
`uio <> zio` | `uio`
`uio.asError(e)` | `uio`
`uio.bimap(f, g)` |  `uio.map(g)`
`uio.catchAll(f)` | `uio`
`uio.catchSome(pf)` | `uio`
`uio.either` | `uio`*
`uio.eventually` | `uio`
`uio.flatMapError(f)` | `uio`
`uio.fold(f, g)` | `uio.map(g)`
`uio.foldM(f, g)` | `uio.flatMap(g)`
`uio.mapError(f)` | `uio`
`uio.option` | `uio`*
`uio.orDie` | `uio`
`uio.orDieWith(f)` | `uio`
`uio.orElse(zio)` | `uio`
`uio.orElseEither(zio)` | `uio`*
`uio.refineOrDie(pf)` | `uio`
`uio.refineOrDieWith(pf)(f)` | `uio`
`uio.refineToOrDie` | `uio`
`uio.retry(s)` | `uio`
`uio.retryOrElse(s, f)` | `uio`
`uio.retryOrElseEither(s, f)` | `uio`*
`uio.tapBoth(f, g)` | `uio.tap(g)`
`uio.tapError(f)` | `uio`

**ZManaged**

Code | Rewrite 
--- | ---
`umanaged <> zmanaged` | `umanaged`
`umanaged.bimap(f, g)` | `umanaged.map(g)`
`umanaged.catchAll(f)` | `umanaged`
`umanaged.catchSome(pf)` | `umanaged`
`umanaged.either` | `umanaged`*
`umanaged.flatMapError(f)` | `umanaged`
`umanaged.fold(f, g)` | `umanaged.map(f)`
`umanaged.foldM(f, g)` | `umanaged.flatMap(g)`
`umanaged.mapError(f)` | `umanaged`
`umanaged.option` | `umanaged`*
`umanaged.orDie` | `umanaged`
`umanaged.orDieWith(f)` | `umanaged`
`umanaged.orElse(zmanaged)` | `umanaged`
`umanaged.orElseEither(zmanaged)` | `umanaged`
`umanaged.refineOrDie(pf)` | `umanaged`
`umanaged.refineToOrDie` | `umanaged`
`umanaged.refineToOrDieWith(pf)(f)` | `umanaged`
`umanaged.retry(s)` | `umanaged`

**ZStream**

Code | Rewrite 
--- | ---
`ustream.bimap(f, g)` | `ustream.map(g)`
`ustream.catchAll(f)` | `ustream`
`ustream.either` | `ustream`*
`ustream.mapError(f)` | `ustream`
`ustream.orElse(zstream)` | `ustream`

**ZStreamChunk**

Code | Rewrite 
--- | ---
`ustream.either` | `ustream`
`ustream.orElse(zstream)` | `ustream`

* `either`, `option`, `orElseEither`, and `retryOrElseEither` wrap their results in `Some` or `Right` so after rewriting, code calling these methods can be simplified to accept an `A` rather than an `Option[A]` or `Either[E, A]`.