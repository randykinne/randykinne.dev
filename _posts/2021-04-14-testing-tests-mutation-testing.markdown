---
title: "Using Mutation Testing to Test Your Tests"
layout: post
date: 2021-04-14 10:00 PM
tag:
- testing
hidden: false # don't count this post in blog pagination
description: "How do you know if your tests are really testing your code?"
category: blog
author: randykinne
externalLink: false
---

# Context

Oops, a bug in a function with nested loops, recursion, and more than one switch-case statement. It's okay, it happens. It might not be ideal, but the reality is that code like this produces business value. What is good code anyways? Completely subjective?

Following the gospel of [Martin Fowler](https://refactoring.com/), we decide to refactor before trying to find the bug. This would be terrifying: A function that does so much can't even fit on the screen, let alone in your head. How can we be sure that when we make a change, our code is still going to work as intended?

We could use tests! We'll run our tests after every change. If no tests fail, everything is fine! Right?

Right?

## Wrong

This is a simplified example. Our entire test suite could look like this.

```java
@Test
public void testAllTheCode() {
    assertTrue(1 == 1);
}
```

If we run our tests after every change, they will pass. We could probably delete all of our code and run the tests and they would pass. 

So how can we validate that the tests do what they're supposed to? How can we test the tests?

We could use code coverage! If 100% of our code was executed during our tests, surely that means that we have perfect tests. 

Right?

## Wrong Again

Let's use an example. 

```java
public boolean greaterThanTen(int x) {
  if (x >= 10) {
    return true;
  } else {
    return false;
  }
}
```

```java
@Test
public boolean greaterThanTenTest() {
  assertTrue(greaterThanTen(11));
  assertFalse(greaterThanTen(9));
}
```

100% test coverage! We've done it. Perfect code, great job. 

Hopefully, you caught the problem: we never checked `10`. 

## The Solution

*Mutation testing*: the concept of making small changes to the code (mutations) and running the test suite against the mutated code. 

We can directly measure the quality of our tests this way: if there is a single failure, the mutant is killed and our tests cover that mutation. If all of our existing tests pass against mutated code, we have a live mutant, able to show us exactly where our tests aren't up to par.

This may or may not be valid - there are *equivalent mutants*, or mutations that do not functionally change the code.

We can actually do mutation testing really easily for a single function, manually, ensuring that we're not creating an equivalent mutant.

Once we've established that all of the lines of the function don't have any significant mutants, we can refactor using our test suite without worrying about breaking the code, and we can be confident that a breaking change will cause a test failure.

## Example

Original Code
```java
public boolean greaterThanTen(int x) {
  if (x > 10) {
    return true;
  } else {
    return false;
  }
}
```

Mutated Code
```java
public boolean greaterThanTen(int x) {
  if (x < 10) {
    return true;
  } else {
    return false;
  }
}
```

Hopefully, our tests catch that the `>` and `<` signs switched. 

If we run the mutated code against the original test suite, we should see that calling `greaterThanTen(5)` returns `true` when it should return `false`, and our test would fail.

## Other Fun Ways to Mutate

It's actually quite fun coming up with ways of breaking code.

- Commenting out a full line
- If a function has multiple return statements, swap their return values
- Replace a variable with a constant
- Changing operators (`5*5` turns into `5+5`)
- Removing if/else statements completely
- Setting loops to loop a specific amount of times
- Changing `==` with `!=`


## Pitfalls

Manual mutation testing is fine for a single function, but even testing a full class would be extremely time consuming to create many mutants for. Fortunately, there are automation tools available for different languages.

## Additional Sources
https://www.guru99.com/mutation-testing.html

https://testing.googleblog.com/2021/04/mutation-testing.html

Correlation between bugs and live mutants https://dl.acm.org/doi/abs/10.1145/2635868.2635929