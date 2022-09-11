---
author: Gabriel Augusto (@gabrielolivrp)
tags: typescript programming types
---

## Introduction

The TypeScript language has a very powerful type system, with it we can program in type level. An interesting fact is that its type system is **turing complete**, that is, it is possible to code any program that runs on the turing machine, the proof is [here](https://github.com/Microsoft/TypeScript/issues/14833).

The example below is making use of conditional types to return the type of an animal's class.

```ts
// types of animals
type Dog = "Dog";
type Giraffe = "Giraffe";

// class of animals
type Mammal = "Mammal";
type Herbivore = "Herbivore";

// returns the class
type Class<A extends Dog | Giraffe> = A extends Giraffe ? Herbivore : Mammal;

// example
type E1 = Class<Giraffe>;
type E2 = Class<Dog>;
```

## References

* [Advanced Types](https://www.typescriptlang.org/docs/handbook/advanced-types.html)
