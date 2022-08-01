---
author: Gabriel Augusto (@gabrielolivrp)
tags: functional programming, lambda calculus
---

# Lambda Calculus

The lambda calculus is used to express computation, created by Alonzo Church in the 1930s.
It is a very simple but very powerful system, representing the whole basis of functional programming.

Let's look at its syntax, rules, and finally a JavaScript example.

## Syntax

The lambda calculus has a very simple syntax, having only **variables**, **functions** and **applications**.

### Variables

Variables are represented by a character and are used to represent function parameters.

```javascript
a
b
.
z
```

### Lambda Functions

You can imagine lambda functions like the javascript functions `() => {}`, but they take only one parameter and return another lambda expression

In formal notation, you declare a lambda function using the symbol `λ` and then the name of the variable that represents the parameter, the body of the function is everything after the `.`

```javascript
// Formal notation
// λx.x
// λf.λx. f x        => lambda function that takes an `f` parameter and returns another lambda function that takes another `x` parameter

// JavaScript
(f) => (x) => f(y)
```

### Application

An application means calling a lambda function by passing an arguments. The applications of arguments occur from left to right, i.e. they are left-associative.

```javascript
// Formal notation
// f x
// f x y         => is the same as `(f x) y`, we pass the `x` into `f` and then apply the `y` to the return of the function `f`

// JavaScript
f(x)
f(x)(y)
```

## Reduction rules

Now that we know the syntax we just need to know how to compute. This is very simple, we basically have two rules.

### Alpha reduction

This rule says that basically you can rename the variable names of a function.

```javascript
// Formal notation
// λx.x

// JavaScript
(x) => x
```

After applying the **alpha reduction** rule, renaming the `x` to `a`, the function becomes:

```javascript
// Formal notation
// λa.a

// JavaScript
(a) => a
```

Note that it is the same function, the only thing that has changed is the variable name, so we say they are **equivalent functions**.

### Beta reduction

The **beta reduction** rule, says that you can replace a variable that is in the body of a function with a lambda expression.

```javascript
// Formal notation
// (λx.x) y

// JavaScript
((x) => x)(y) // Example of an identity function, which takes as argument an `y` value.
```

To apply reduction to the above function the first step is to remove the parameter, and replace all occurrences of `x` in the function with the value `y`, so the result of reducing `(x) => x` by applying `y` is `y` itself

Let's go to a more complicated example:

The lambda function below that takes two parameters, `f` and `x`, and receives two arguments `((a) => a)` and `b`.

```javascript
// Formal notation
// (λf.λx.f x) (λa. a) b

// JavaScript
((f) => (x) => f(x)) ((a) => a) (b)
```

The first step is to replace all occurrences of `f` with `((a) => a)`

```javascript
// Formal notation
// (λx.(λa. a) x) b

// JavaScript
((x) => ((a) => a)(x)) (b)
```

Now just repeat the same thing for `x` and replace it with `b`.

```javascript
// Formal notation
// (λa. a) b

// JavaScript
((a) => a)(b)
```

Note that we have one more application to do, replace all occurrences of `a` with `b`

```javascript
b
```

The result of the reduction is just `b`.

## JavaScript example

Now that we know how lambda calculus works, let's go to a real example, let's make a sum function, which takes two numbers and returns the sum.

### Church Numbers

Natural numbers are defined using `zero` and its successors. Thus, `0` is represented by `zero`, `1` is represented by the successor of zero, `successor(zero)` and so on.

Using this same analogy, we can write the numbers in the lambda calculus as follows

```javascript
// λs.λx. x
const zero = (s) => (x) => x

// λs.λx.s x
const one = (s) => (x) => s(x)

// λs.λx.s (s x)
const two = (s) => (x) => s(s(x))

// λs.λx.s (s x)
const three = (s) => (x) => s(s(s(x)))
```

### Função de adição

Now we just need a function for the sum, which is defined by the following expression below:

```javascript
// λm.λn.λf.λx. ((m f) (((n f) x)))
const plus = m => n => f => x => m(f)(n(f)(x))
```

Now we have all the functions defined to perform a sum. The complete code is below

```javascript
// λs.λx. x
const zero = (s) => (x) => x

// λs.λx.s x
const one = (s) => (x) => s(x)

// λs.λx.s (s x)
const two = (s) => (x) => s(s(x))

// λs.λx.s (s x)
const three = (s) => (x) => s(s(s(x)))

// λm.λn.λf.λx. ((m f) (((n f) x)))
const plus = (m) => (n) => (f) => (x) => m(f)(n(f)(x))

// Takes a number from church and returns an integer
const toInt = (n) => n((x) => x + 1)(0)

// 1 + 1
// (λm.λn.λf.λx. m f (n f x)) (λs.λx.s x) (λs.λx.s x) => Replace `m` with `(λs.λx.s x)`
// (λn.λf.λx. (λs.λx.s x) f (n f x)) (λs.λx.s x)      => Replace `n` with `(λs.λx.s x)`
// λf.λx. (λs.λx.s x) f ((λs.λx.s x) f x)             => Replace `s` with `f`
// λf.λx. (λx.f x) ((λs.λx.s x) f x)                  => Replace `x` with `((λs.λx.s x) f x)`
// λf.λx. f ((λs.λx.s x) f x)                         => Replace `s` with `f`
// λf.λx. f ((λx.f x) x)                              => Replace `x` with `x`
// λf.λx. f (f x)

const result = plus(one)(one)

console.log(toInt(result))
```

## References

* [Lambda Calculus](https://en.wikipedia.org/wiki/Lambda_calculus)
