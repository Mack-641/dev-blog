---
title: "SOLID Principles — Practical Examples"
date: '2025-10-17T17:11:08+02:00'
draft: false
tags: ["SOLID","design","architecture","patterns"]
---

A concise, practical guide to the SOLID object-oriented design principles with short examples you can copy and adapt.

<!--more-->

## Overview

SOLID is an acronym for five design principles that help produce maintainable, testable and extensible code:

- Single Responsibility Principle (SRP)
- Open/Closed Principle (OCP)
- Liskov Substitution Principle (LSP)
- Interface Segregation Principle (ISP)
- Dependency Inversion Principle (DIP)

Below are short explanations and compact TypeScript examples.

---

## 1. Single Responsibility Principle (SRP)

Each module/class should have one reason to change.

TypeScript example — separate concerns for data and presentation:

```ts
// SRP: separate fetching from rendering
class UserRepository {
  async fetchUser(id: string) { /* fetch from API / DB */ }
}

class UserPresenter {
  render(user: any) { /* format & output */ }
}

// Usage
const repo = new UserRepository();
const presenter = new UserPresenter();
const user = await repo.fetchUser("u1");
presenter.render(user);
```

Benefit: changes to fetching logic don't affect rendering code.

---

## 2. Open/Closed Principle (OCP)

Software entities should be open for extension but closed for modification.

TypeScript example — use polymorphism to extend behavior:

```ts
interface DiscountStrategy { calculate(amount: number): number }

class NoDiscount implements DiscountStrategy {
  calculate(a: number) { return a }
}

class SeasonalDiscount implements DiscountStrategy {
  calculate(a: number) { return a * 0.9 }
}

class Checkout {
  constructor(private strategy: DiscountStrategy) {}
  total(amount: number) { return this.strategy.calculate(amount) }
}
```

To add a new discount, implement DiscountStrategy — no changes to Checkout required.

---

## 3. Liskov Substitution Principle (LSP)

Subtypes must be substitutable for their base types without altering correctness.

Bad example (violates LSP):

```ts
class Rectangle { setWidth(w: number) {}; setHeight(h: number) {} }
class Square extends Rectangle {
  setWidth(w: number) { /* forces height = w */ }
  setHeight(h: number) { /* forces width = h */ }
}
// Client expecting independent width/height breaks when given a Square
```

Better: use separate abstractions or immutable value objects instead of inheritance.

---

## 4. Interface Segregation Principle (ISP)

Clients should not be forced to depend on interfaces they do not use — prefer small, focused interfaces.

TypeScript example:

```ts
interface Printer { print(doc: string): void }
interface Scanner { scan(): string }

class AllInOne implements Printer, Scanner {
  print(d: string) { /* ... */ }
  scan() { return "..." }
}

class SimplePrinter implements Printer {
  print(d: string) { /* ... */ }
}
```

Clients that only need printing depend only on Printer, not scanner methods.

---

## 5. Dependency Inversion Principle (DIP)

High-level modules should not depend on low-level modules; both should depend on abstractions.

TypeScript example — inject abstraction:

```ts
interface Logger { log(msg: string): void }

class ConsoleLogger implements Logger { log(m: string) { console.log(m) } }
class FileLogger implements Logger { log(m: string) { /* write to file */ } }

class OrderService {
  constructor(private logger: Logger) {}
  placeOrder(o: any) {
    // process order...
    this.logger.log("order placed")
  }
}

// Usage: inject desired logger implementation
const svc = new OrderService(new ConsoleLogger())
```

This makes OrderService independent from concrete logging implementations and easier to test.

---

## Quick checklist when applying SOLID

- SRP: can each class be described with one responsibility?
- OCP: can you add new behavior without modifying existing code?
- LSP: can a subclass replace its parent without surprises?
- ISP: are interfaces small and focused for their clients?
- DIP: do high-level modules depend on abstractions (not concrete classes)?

Further reading: Robert C. Martin's writings and practical refactorings on each principle.

That's it — use these patterns as guidelines, not rigid rules; prefer simplicity and refactor when the code signals need.