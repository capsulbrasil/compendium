---
author: Gabriel Augusto (@gabrielolivrp)
tags: exceptions programming
---

# Why exception is bad?

In this article I want to try to show why using exceptions for error handling may not be a good option.

Exceptions are a signal that indicates that some error has occurred in the execution of a program. The author of this [article](https://reflectoring.io/business-exceptions/) says that there are 2 types of exceptions, **technical exceptions** and **commercial exceptions**. Let's take a closer look at what each one is.

## Technical Exceptions

Technical exceptions are errors that cannot be recovered, so they must be fixed directly in the source code. Let me give you an example.

Imagine that you are building a library, and the programmer who is using it passes invalid arguments to his function, notice that we cannot recover this error, so we throw an exception indicating that the parameters are wrong so that the programmer can correct it.

## Business Exceptions

Business exceptions are the opposite of technical exceptions, they are errors caused by the end users of our software. This type of error can be easily recovered, we inform the user through an error message, so that he can correct it and continue the flow of the program.


## Why shouldn't we use exceptions?

Technical exceptions should never be an expected value, they are something that should occur exceptionally, only when something occurs that is not foreseen in the flow of our software, using exceptions to handle business rules can cause a lot of complexity to the software, because for every exception thrown we have to deal with it later. Each time we use a `try/catch` block the compiler allocates resources to handle a possible exception, so when we use too many exceptions it is a waste of resources.

## Alternatives

There are good alternatives for handling business exceptions, such as the `Either` structure, which is widely used in many programming languages. This returns two types, `Left` to represent an error and `Right` to represent a correct value

```typescript
// Types
type Left<A, B> = {
  readonly $: "left";
  readonly value: A;
};

type Right<A, B> = {
  readonly $: "right";
  readonly value: B;
};

type Either<A, B> = Left<A, B> | Right<A, B>;

// Type Constructors
const right = <A, B>(value: B): Either<A, B> => ({
  $: "right",
  value,
});
const left = <A, B>(value: A): Either<A, B> => ({
  $: "left",
  value,
});
```

Example of use:

```typescript
const safeDiv = (x: number, y: number): Either<string, number> => {
  if (y == 0) {
    return left("Divison by zero");
  }
  return right(x / y);
};
```

Another very popular structure is `Option`, used to represent the presence or absence of a value.

```typescript
// Maybe types
type Some<A> = {
  readonly $: "some";
  readonly value: A;
};
type None<A> = {
  readonly $: "none";
};
type Option<A> = Some<A> | None<A>;

// Type Constructors
const some = <A>(value: A): Option<A> => ({ $: "some", value });
const none = <A>(): Option<A> => ({ $: "none" });
```

Example of use:

```typescript
const getUserByEmail = (email: string): Option<User> => {
  const user = userRepository.findByEmail(email);
  if (!user) {
    return none();
  }
  return some(user);
};
```

## References

* [Why is exception handling bad?](https://stackoverflow.com/questions/1736146/why-is-exception-handling-bad#:~:text=Exceptions%20make%20it%20really%20easy,be%20tricky%20and%20counter%2Dintuitive.)

* [5 Reasons Why Business Exceptions Are a Bad Idea](https://reflectoring.io/business-exceptions/)
