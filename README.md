
README.md is generated from README.Rmd. Please edit that file

# Functional Programming Jargon

Functional programming (FP) provides many advantages, and its popularity
has been increasing as a result. However, each programming paradigm
comes with its own unique jargon and FP is no exception. By providing a
glossary, we hope to make learning FP easier.

Examples are presented in R (&gt;= 4.1.0).

Where applicable, this document uses terms defined in the [Fantasy Land
spec](https://github.com/fantasyland/fantasy-land)

**Translations** \*
[Portuguese](https://github.com/alexmoreno/jargoes-programacao-funcional)
\*
[Spanish](https://github.com/idcmardelplata/functional-programming-jargon/tree/master)
\* [Chinese](https://github.com/shfshanyue/fp-jargon-zh) \* [Bahasa
Indonesia](https://github.com/wisn/jargon-pemrograman-fungsional) \*
[Python
World](https://github.com/jmesyou/functional-programming-jargon.py) \*
[Scala
World](https://github.com/ikhoon/functional-programming-jargon.scala) \*
[Rust
World](https://github.com/JasonShin/functional-programming-jargon.rs) \*
[Korean](https://github.com/sphilee/functional-programming-jargon) \*
[Haskell
Turkish](https://github.com/mrtkp9993/functional-programming-jargon)

**Table of Contents** RM(noparent,notop)

-   [Arity](#arity)
-   [Higher-Order Functions (HOF)](#higher-order-functions-hof)
-   [Closure](#closure)
-   [Partial Application](#partial-application)
-   [Currying](#currying)
-   [Auto Currying](#auto-currying)
-   [Function Composition](#function-composition)
-   [Continuation](#continuation)
-   [Purity](#purity)
-   [Side effects](#side-effects)
-   [Idempotent](#idempotent)
-   [Point-Free Style](#point-free-style)
-   [Predicate](#predicate)
-   [Contracts](#contracts)
-   [Category](#category)
-   [Value](#value)
-   [Constant](#constant)
-   [Functor](#functor)
-   [Pointed Functor](#pointed-functor)
-   [Lift](#lift)
-   [Referential Transparency](#referential-transparency)
-   [Equational Reasoning](#equational-reasoning)
-   [Lambda](#lambda)
-   [Lambda Calculus](#lambda-calculus)
-   [Lazy evaluation](#lazy-evaluation)
-   [Monoid](#monoid)
-   [Monad](#monad)
-   [Comonad](#comonad)
-   [Applicative Functor](#applicative-functor)
-   [Morphism](#morphism)
    -   [Endomorphism](#endomorphism)
    -   [Isomorphism](#isomorphism)
    -   [Homomorphism](#homomorphism)
    -   [Catamorphism](#catamorphism)
    -   [Anamorphism](#anamorphism)
    -   [Hylomorphism](#hylomorphism)
    -   [Paramorphism](#paramorphism)
    -   [Apomorphism](#apomorphism)
-   [Setoid](#setoid)
-   [Semigroup](#semigroup)
-   [Foldable](#foldable)
-   [Lens](#lens)
-   [Type Signatures](#type-signatures)
-   [Algebraic data type](#algebraic-data-type)
    -   [Sum type](#sum-type)
    -   [Product type](#product-type)
-   [Option](#option)
-   [Function](#function)
-   [Partial function](#partial-function)
-   [Functional Programming Libraries in
    JavaScript](#functional-programming-libraries-in-javascript)

/RM

## Arity

The number of arguments a function takes. From words like unary, binary,
ternary, etc. This word has the distinction of being composed of two
suffixes, “-ary” and “-ity.” Addition, for example, takes two arguments,
and so it is defined as a binary function or a function with an arity of
two. Such a function may sometimes be called “dyadic” by people who
prefer Greek roots to Latin. Likewise, a function that takes a variable
number of arguments is called “variadic,” whereas a binary function must
be given two and only two arguments, currying and partial application
notwithstanding (see below).

``` r
sum <- \(a, b) a + b

arity <- length(formals(sum))
arity
#> [1] 2

# The arity of sum is 2
```

## Higher-Order Functions (HOF)

A function which takes a function as an argument and/or returns a
function.

``` r
filter <- \(predicate, xs) Filter(predicate, xs)
```

``` r
is <- \(type) \(x) inherits(x, type)
```

``` r
filter(is("numeric"), list(0, "1", 2, NULL)) 
#> [[1]]
#> [1] 0
#> 
#> [[2]]
#> [1] 2
```

## Closure

A closure is a way of accessing a variable outside its scope. Formally,
a closure is a technique for implementing lexically scoped named
binding. It is a way of storing a function with an environment.

A closure is a scope which captures local variables of a function for
access even after the execution has moved out of the block in which it
is defined. ie. they allow referencing a scope after the block in which
the variables were declared has finished executing.

``` r
add_to <- \(x) \(y) x + y
add_to_five <- add_to(5)
add_to_five(3)
#> [1] 8
```

The function `add_to()` returns a function(internally called `add()`),
lets store it in a variable called `add_to_five` with a curried call
having parameter 5.

Ideally, when the function `add_to` finishes execution, its scope, with
local variables add, x, y should not be accessible. But, it returns 8 on
calling `add_to_five()`. This means that the state of the function
`add_to` is saved even after the block of code has finished executing,
otherwise there is no way of knowing that `add_to` was called as
`add_to(5)` and the value of x was set to 5.

Lexical scoping is the reason why it is able to find the values of x and
add - the private variables of the parent which has finished executing.
This value is called a Closure.

The stack along with the lexical scope of the function is stored in form
of reference to the parent. This prevents the closure and the underlying
variables from being garbage collected(since there is at least one live
reference to it).

Lambda Vs Closure: A lambda is essentially a function that is defined
inline rather than the standard method of declaring functions. Lambdas
can frequently be passed around as objects.

A closure is a function that encloses its surrounding state by
referencing fields external to its body. The enclosed state remains
across invocations of the closure.

**Further reading/Sources** \* [Lambda Vs
Closure](http://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)
\* [JavaScript Closures highly voted
discussion](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)

## Partial Application

Partially applying a function means creating a new function by
pre-filling some of the arguments to the original function.

``` r
# Helper to create partially applied functions
# Takes a function and some arguments
partial <- \(f, ...) {
  args <- list(...)
  # returns a function that takes the rest of the arguments
  \(...) {
    more_args <- list(...)
    # and calls the original function with all of them
    do.call(f, c(args, more_args))
  }
}

# Something to apply
add_3 <- \(a, b, c) a + b + c

# Partially applying `2` and `3` to `add_3` gives you a one-argument function
five_plus <- partial(add_3, 2, 3) 

five_plus(4)
#> [1] 9
```

Partial application helps create simpler functions from more complex
ones by baking in data when you have it. [Curried](#currying) functions
are automatically partially applied.

## Currying

The process of converting a function that takes multiple arguments into
a function that takes them one at a time.

Each time the function is called it only accepts one argument and
returns a function that takes one argument until all arguments are
passed.

``` r
sum <- \(a, b) a + b

curried_sum <- \(a) \(b) a + b

curried_sum(40)(2)
#> [1] 42

add_2 <- curried_sum(2) 

add_2(10)
#> [1] 12
```

**Further reading** \* [Favoring
Curry](http://fr.umio.us/favoring-curry/)

## Function Composition

The act of putting two functions together to form a third function where
the output of one function is the input of the other.

``` r
compose <- \(f, g) \(a) f(g(a)) # Definition
floor_and_to_string <- compose(\(val) as.character(val), floor) # Usage
floor_and_to_string(121.212121)
#> [1] "121"
```

## Continuation

At any given point in a program, the part of the code that’s yet to be
executed is known as a continuation.

``` r
print_as_string <- \(num) print(paste("Given", num))

add_one_and_continue <- \(num, cc) {
  result <- num + 1
  cc(result)
}

add_one_and_continue(2, print_as_string) 
#> [1] "Given 3"
```

Continuations are often seen in asynchronous programming when the
program needs to wait to receive data before it can continue. The
response is often passed off to the rest of the program, which is the
continuation, once it’s been received.

``` r
continue_program_with <- \(data) {
  # Continues program with data
}

tryCatch(read.csv("path/to/file"), error = \(e) e) |> # handle error
  continue_program_with()
```

## Purity

A function is pure if the return value is only determined by its input
values, and does not produce side effects.

``` r
greet <- \(name) paste("Hi,", name)

greet("Brianne")
#> [1] "Hi, Brianne"
```

As opposed to each of the following:

``` r
name <- "Brianne"

greet <- \() paste("Hi,", name)

greet()
#> [1] "Hi, Brianne"
```

The above example’s output is based on data stored outside of the
function…

``` r
greeting <- NULL

greet <- \(name) {
  greeting <<- paste("Hi,", name)
}

greet("Brianne")
greeting 
#> [1] "Hi, Brianne"
```

… and this one modifies state outside of the function.

## Side effects

A function or expression is said to have a side effect if apart from
returning a value, it interacts with (reads from or writes to) external
mutable state.

``` r
different_every_time = Sys.time()
```

``` r
print("IO is a side effect!")
#> [1] "IO is a side effect!"
```

## Idempotent

A function is idempotent if reapplying it to its result does not produce
a different result.

``` r
f(f(x)) == f(x)
```

``` r
abs(abs(10))
#> [1] 10
```

``` r
sort(sort(sort(c(2, 1))))
#> [1] 1 2
```

## Point-Free Style

Writing functions where the definition does not explicitly identify the
arguments used. This style usually requires currying or other
Higher-Order functions. A.K.A Tacit programming.

``` r
# Given
map <- \(fn) \(list) lapply(list, fn)
add <- \(a) \(b) a + b

# Then

# Not points-free - `numbers` is an explicit argument
increment_all <- \(numbers) map(add(1))(numbers)

# Points-free - The list is an implicit argument
increment_all_2 = map(add(1))
```

## Predicate

A predicate is a function that returns true or false for a given value.
A common use of a predicate is as the callback for array filter.

``` r
predicate <- \(a) a > 2

Filter(predicate, c(1, 2, 3, 4))
#> [1] 3 4
```

## Contracts

A contract specifies the obligations and guarantees of the behavior from
a function or expression at runtime. This acts as a set of rules that
are expected from the input and output of a function or expression, and
errors are generally reported whenever a contract is violated.

``` r
# Define our contract : integer -> logical
contract <- \(input) {
  if (is.integer(input)) 
    TRUE
  else
    stop("Contract violated: expected integer -> logical")
}

add_one <- \(num) { contract(num); num + 1 }

add_one(2L)
#> [1] 3
add_one("some string") |> tryCatch(error = \(e) print(e))
#> <simpleError in contract(num): Contract violated: expected integer -> logical>
```
