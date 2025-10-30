# Programming Paradigms Overview

## Programming Paradigm
- A **programming paradigm** is a set of techniques and constraints to write, read, maintain, and reuse complex software.
- Purpose: **manage complexity, improve readability, reusability, and maintainability**.
- Programming languages support paradigms via syntax and features.

## Main Paradigms Covered

### 1. Structured Programming (SP)
- Uses flow control: **if/else, loops, function calls**.
- Avoids `goto` (prevents spaghetti code).
- Languages enforce proper control flow.

### 2. Object-Oriented Programming (OOP)
- Everything is an **object** (state + behavior).
- Key concepts:
  - **Encapsulation** – group data & methods.
  - **Inheritance** – reuse code via class hierarchies.
  - **Polymorphism** – same method behaves differently in subtypes.
  - **Abstraction** – interfaces and generalization.
- Strength: works well with many things with fixed operations.
- Weakness: adding new operations can be harder.
- Languages: **Java, C++, Python, C#, Kotlin, Ruby, PHP**.

### 3. Functional Programming (FP)
- Rooted in **lambda calculus**; emphasizes **pure functions**.
- **Pure function**: depends only on input, no side effects.
- **Side effects**: modifying variables, I/O, database updates.
- Key ideas:
  - **Immutability** – create new values instead of changing existing ones.
  - **First-class functions** – functions can be assigned, passed, returned.
  - **Higher-order functions** – functions that accept/return other functions.
- Strength: excels when many operations act on fixed data.
- Weakness: adding new things can be harder.
- Languages (pure-ish): **Haskell, Lisp, Clojure**; functional-style: **Python, Java, JS, C#, Kotlin**.

## OOP vs FP

| Aspect             | OOP                        | FP                                |
|-------------------|----------------------------|----------------------------------|
| Focus             | Many things, fixed operations | Many operations, fixed things   |
| Adding things     | Easy                        | Hard                             |
| Adding operations | Hard                        | Easy                             |
| Best for          | Encapsulated systems        | Distributed / concurrent systems |

## Summary
- **Paradigms** help structure thinking and code.
- Most programs **combine multiple paradigms**.
- Structured programming is ubiquitous.
- OOP is widely used but FP has advantages in distributed systems and minimizing side effects.
