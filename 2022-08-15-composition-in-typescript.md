---
author: João G. Santos (@ringeringeraja)
tags: typescript functional programming
---

## Problem

Sometimes while coding in Typescript you face situations where you have to apply several transformations to an array. One intuitive way of doing that, bearing readability in mind, is as following, chaining multiple map's together to avoid writing several attributions.

```typescript
const getSanitizedStuff = () => {
  const stuff: Array<Stuff> = getStuff()
  const sanitizedStuff = stuff
    .map(transformName)
    .map(transformQuantity)
    .map((t) => transformTimestamps(t, () => new Date))

  return sanitizedStuff
}
```

This is problematic since the target array is being iterated several times needlessly, only for the sake of having good indentation and small lines. Functional Programming, though, has a very good approach for this kind of situation (not being pedantic here, I just happen to know it comes from FP). Of course I'm talking about Function Composition.

## Solution

In this case we'll be using Ramda library. It's prefered as Savitri already leverages it internally, so the npm install should be faster. So, R.pipe will compose all functions received as arguments into a single function, left-to-right. Instead of chaining maps one after another, we rather create a "sanitizeStuff" pipe (I don't know if this term is accurate FP-wise) then map our array throughout it. This way we preserve the indenting and readbility, with the enormous plus of accomplishing *maximum computing efficiency*, something one should care when deploying commercial systems to production.

```typescript
import * as R from 'ramda'

const getSanitizedStuff = () => {
  const stuff: Array<Stuff> = getStuff()
  const sanitizeStuff = R.pipe(
    transformName,
    transformQuantity,
    (t) => transformTimestamps(t, () => new Date)
  )

  const sanitizedStuff = stuff.map(sanitizeStuff)
  return sanitizedStuff
}
```

## The library

Ramda has a handful of neat functions that are supposed to make Typescript just feel like Haskell. Jokes aside, I've been incrementally adopting it to accomplish things that otherwise would require laborous in-loco implementation (mergeDeepWithKey is an example). It's surelly worthwhile to take a look onto the documentation.

## References
* https://ramdajs.com/docs/
* https://en.wikipedia.org/wiki/Function_composition_(computer_science)
