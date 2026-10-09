# OOPS — Complete Revision Notes

> Covers: Classes & objects → 4 pillars → Constructors & copies → Abstract vs Interface → Overloading vs Overriding → Relationships → SOLID → Design Patterns → Exceptions → Language notes → Cheat sheet

## Table of Contents
1. [Why OOP?](#1-why-oop)
2. [The Four Pillars](#2-the-four-pillars)
3. [Classes Deep-Dive: Constructors, Copies, Static](#3-classes-deep-dive-constructors-copies-static)
4. [Abstract Class vs Interface](#4-abstract-class-vs-interface)
5. [Overloading vs Overriding](#5-overloading-vs-overriding)
6. [Relationships Between Classes](#6-relationships-between-classes)
7. [SOLID Principles](#7-solid-principles)
8. [Design Patterns](#8-design-patterns)
9. [Exception Handling](#9-exception-handling)
10. [Language-Specific Notes (C++/Java/Python)](#10-language-specific-notes)
11. [Cheat Sheet](#11-cheat-sheet)

---

## 1. Why OOP?

- **Procedural programming**: functions operating on shared data; fine for small programs, but data and behavior drift apart as systems grow.
- **OOP**: bundles **data + behavior into objects**; models the real world; enables reuse (inheritance/composition), controlled access (encapsulation), and interchangeable parts (polymorphism).
- **Class**: blueprint (no memory until instantiated). **Object**: instance with its own state (fields) + behavior (methods) + identity.

---

## 2. The Four Pillars

### Encapsulation — *hiding data, protecting state*
Wrap data + methods; expose only what's needed via access modifiers (`private`/`protected`/`public`/`internal`). Access through getters/setters that can validate.
```java
class Account {
    private double balance;                 // hidden
    public void deposit(double amt) {
        if (amt > 0) balance += amt;        // invariant protected
    }
}
```

### Abstraction — *hiding complexity, exposing intent*
Show **what** an object does, not **how**: abstract classes and interfaces define contracts; callers depend on them. (Driving a car = steering/accelerator API, not the fuel-injection details.)

> **Encapsulation vs Abstraction (the classic question)**: abstraction is a *design-level* idea — decide what to expose (the contract); encapsulation is a *language mechanism* to enforce it (private fields, access modifiers). Abstraction hides **complexity**; encapsulation hides **data**.

### Inheritance — *reuse & extension ("is-a")*
Child class acquires parent's members, adds/overrides behavior. Types: single, multilevel (A→B→C), hierarchical (one parent, many children), multiple (many parents — C++ yes, Java classes no), hybrid. Promotes code reuse; can tightly couple classes (fragile base-class problem).

### Polymorphism — *one interface, many behaviors*
- **Compile-time (static binding)**: method overloading, operator overloading (C++), templates/generics.
- **Runtime (dynamic binding)**: method overriding — the actual object's method runs even via a parent reference (implemented with vtables/virtual dispatch).

---

## 3. Classes Deep-Dive: Constructors, Copies, Static

### Constructors
| Type | Purpose |
|---|---|
| Default (no-arg) | Compiler-provided if you define none; lost once you define any constructor |
| Parameterized | Initialize with values |
| Copy constructor | Build from another object (`Object(const Object &o)` in C++; Java uses clone/copy ctor idiom) |
| Constructor overloading | Multiple ctors, often chained with `this(...)`/`: this(...)` |

Rules: same name as class, no return type, called automatically, can't be abstract/static/final (Java), can be **private** (Singleton, factory-only creation).
**Destructor** (C++/RAII): releases resources; **make it virtual in polymorphic base classes** — else deleting via base pointer skips derived cleanup (undefined behavior/memory leak).

### Shallow vs Deep Copy
- **Shallow**: copies field values; pointers/references still point to the *same* underlying objects (mutations leak across copies).
- **Deep**: recursively duplicates referenced objects — full independence. Java default is always shallow (hence defensive copies / clone overrides).

### Static members
- Belong to the **class**, not instances; one copy shared (counters, constants, caches).
- Static methods can't use `this`, can't directly access instance members, aren't overridden (they're *hidden* — resolved by reference type).
- Static blocks (Java) initialize statics at class-load; static classes (C#) / utility classes (private ctor) group stateless helpers.

### `this` / `self`
Reference to the current object — disambiguates fields from parameters, enables ctor chaining, returning the object (builder pattern). C++ `friend` functions/classes can touch privates (operators, tightly-coupled pairs).

---

## 4. Abstract Class vs Interface

| | Abstract class | Interface |
|---|---|---|
| Relationship | "is-a" with shared **state & partial impl** | Pure **contract** ("can-do") |
| Fields | Any (state allowed) | Constants only (Java: `public static final`) |
| Constructors | Yes | No |
| Method impl | Mix of concrete + abstract | Java 8+: `default`/`static` methods allowed; traditionally abstract only |
| Multiple inheritance | One class only (Java/C#) | Implement many |
| Access modifiers | Any | Members implicitly public |
| Use when | Sharing code/state among closely related classes | Capability across unrelated classes (`Comparable`, `Serializable`) |

**Diamond problem**: class D inherits the same method from B and C (both inheriting A) — which implementation runs?
- **C++**: multiple inheritance allowed → fix with **virtual inheritance** (A shared once).
- **Java**: no multiple *class* inheritance; interfaces with default methods can still conflict → compiler forces D to **override** and may call `B.super.method()` explicitly.

---

## 5. Overloading vs Overriding

| | Overloading (compile-time) | Overriding (runtime) |
|---|---|---|
| Where | Same class (or inherited set) | Parent→child |
| Signature | **Must differ** (params) — return type alone isn't enough | **Must match** (or covariant return) |
| Binding | Static (by reference/declared type) | Dynamic (by actual object type) |
| Access | Any | Child can't **narrow** parent's access |
| Exceptions | — | Child can't throw broader checked exceptions |
| `static` | Can overload | Can't truly override — statics are **hidden**, not overridden |
| `final`/`private` (Java) | — | Can't be overridden |
| Keywords | — | `virtual` (C++), `@Override` (Java — always annotate!) |

```java
class Shape { double area() { return 0; } } // overridden
class Circle extends Shape {
    @Override double area() { return Math.PI * r * r; }
}
Shape s = new Circle(2); s.area(); // Circle's area runs
```

---

## 6. Relationships Between Classes

| Relationship | Meaning | UML | Example | Lifecycle |
|---|---|---|---|---|
| **Association** | Uses/knows | solid line | Doctor ↔ Patient | Independent |
| **Aggregation** | "has-a", weak (shared parts) | hollow diamond ◇ | Team ◇ Player (players outlive team) | Independent |
| **Composition** | "has-a", strong (owned parts) | filled diamond ◆ | House ◆ Room (rooms die with house) | Bound to owner |
| **Dependency** | Temporary use | dashed arrow | Method parameter | Transient |

- **is-a (inheritance) vs has-a (composition)**: prefer **composition over inheritance** when the relationship isn't truly "is-a" — composition is flexible, avoids fragile base classes, and keeps coupling low (decorator/strategy patterns rely on it).

---

## 7. SOLID Principles

| Letter | Principle | One-liner | Smell it fixes |
|---|---|---|---|
| **S** | Single Responsibility | One class = one reason to change | "God" classes doing parsing+DB+email |
| **O** | Open/Closed | Open for extension, closed for modification | `if/else` chains per new type (use polymorphism/strategy) |
| **L** | Liskov Substitution | Subtypes must be usable wherever the parent is | Subclass that throws `UnsupportedOperationException` |
| **I** | Interface Segregation | Many small client-specific interfaces > one fat one | Interfaces forcing empty method impls |
| **D** | Dependency Inversion | Depend on abstractions, not concretions; inject dependencies | `new`ing concrete classes deep inside business logic |

```java
// DIP example
interface Notifier { void send(String msg); } // abstraction
class OrderService {
    private final Notifier notifier;                  // injected
    OrderService(Notifier n) { this.notifier = n; }
}
```

Supporting cast: **DRY** (don't repeat yourself), **KISS**, **YAGNI** (build what's needed now), **Law of Demeter** (talk only to direct collaborators — `a.getB().getC().doX()` is a smell), high **cohesion** + low **coupling** as the eternal goal.

---

## 8. Design Patterns

Proven, reusable solutions to recurring design problems. Three classic families (GoF):

| Family | Patterns |
|---|---|
| **Creational** (object creation) | Singleton, Factory Method, Abstract Factory, Builder, Prototype |
| **Structural** (composition) | Adapter, Facade, Decorator, Proxy, Composite, Bridge, Flyweight |
| **Behavioral** (interaction) | Observer, Strategy, Command, Iterator, Template Method, State, Chain of Responsibility, Mediator, Memento, Visitor |

### Singleton — one instance, global access
```java
public class Config {
    private static volatile Config instance;             // volatile = safe publication
    private Config() {}                                   // no external construction
    public static Config get() {
        if (instance == null) {                           // 1st check (no lock)
            synchronized (Config.class) {
                if (instance == null) instance = new Config();  // 2nd check
            }
        }
        return instance;
    }
}
```
Alternatives: Java `enum` singleton (simplest, serialization-safe) or a static holder class. Watch-outs: hidden global state, hard to test, thread-safety.

### Factory Method — let subclasses/one place decide what to create
```java
interface Button { void render(); }
class Dialog {
    abstract Button createButton();   // factory method
    void show() { createButton().render(); }
}
```
Decouples callers from concrete classes; new types = new factories, no caller changes (OCP).

### Observer — publish/subscribe
Subject keeps a list of observers; on state change it notifies them all (`notifyObservers()`). Used by: event listeners, message queues, MVC, stock tickers. Push vs pull models.

### Strategy — interchangeable algorithms
Encapsulate each algorithm behind an interface; select at runtime (`PaymentService` with `CardPayment/UpiPayment/WalletPayment`). Kills `if/else` per type; classic OCP demo.

### Others worth one line each
- **Builder**: step-by-step construction of complex immutable objects (fluent `new Pizza.Builder().size(L).cheese().build()`).
- **Adapter**: converts one interface to another (card reader between SD card & laptop).
- **Facade**: simple front door over a complex subsystem (`OrderFacade.checkout()`).
- **Decorator**: wraps to add behavior dynamically (Java `BufferedReader(new FileReader(...))`).
- **Proxy**: controls access (lazy loading, caching, security, RPC stubs).
- **Template Method**: parent fixes the skeleton, children fill steps.
- **Command**: request as an object → queues, undo/redo.

---

## 9. Exception Handling

```java
try {
    risky();
} catch (IOException e) { // specific first
    log(e);
} finally { // cleanup — always runs (except System.exit / JVM crash)
    close();
}
```
- **Checked** (compile-enforced: IOException, SQLException) vs **unchecked** (RuntimeException: NPE, IllegalArgument) vs **Errors** (don't catch: OOM, StackOverflow).
- **try-with-resources** (Java): auto-close anything `AutoCloseable`.
- Best practices: don't swallow exceptions, don't use exceptions for control flow, catch specific types, log-or-rethrow not both, custom exceptions for domain errors, fail fast with meaningful messages.

---

## 10. Language-Specific Notes

### C++
- `virtual` functions → runtime polymorphism via **vtable** (per-class table of function pointers; each object carries a vptr). Cost: one indirection.
- **Virtual destructor** in base classes (see §3). Multiple inheritance + **virtual inheritance** for diamonds. Operator overloading. **RAII**: resources tied to object lifetime (smart pointers: `unique_ptr`, `shared_ptr`).
- Pass-by-value vs pass-by-reference (`&`) vs pointer; copy constructor + assignment operator; Rule of 3/5/0.

### Java
- Everything extends `Object` (`equals/hashCode/toString`); **all non-primitives are references**; GC handles memory.
- `final` (constant/method not overridable/class not extendable) vs `finally` (block) vs `finalize()` (deprecated — don't use).
- Interfaces evolved: default/static methods (Java 8), private methods (Java 9). Records (Java 16+) for data carriers. Enums are full classes.
- Generics erase at runtime (type erasure); autoboxing (`Integer` cache −128..127 — the `==` trap).

### Python
- **Duck typing**: behavior over type ("if it quacks…"); no true `private` — convention `_name` / `__name` (name mangling).
- Everything is an object; `__init__` (not a true ctor), `self` explicit; **MRO** (C3 linearization) resolves diamonds.
- `@property` for encapsulated attributes; ABCs/`Protocol` for abstract types; dataclasses for value objects.

### C#
Like Java plus: properties, `struct` (value types), events/delegates (first-class Observer), LINQ.

---

## 11. Cheat Sheet

| Concept | One-liner |
|---|---|
| Encapsulation vs abstraction | Hide **data** vs hide **complexity**; mechanism vs design goal |
| Overloading vs overriding | Same name different params (compile-time) vs same signature in child (runtime) |
| Static "override" | Method hiding — resolved by reference type, not object |
| Abstract class vs interface | Shared state+partial code vs pure capability contract |
| Diamond problem | Ambiguous inherited impl; C++ virtual inheritance, Java must-override |
| Covariant return | Overriding may return a subtype of the parent's return type |
| Shallow vs deep copy | Copy references vs copy referenced objects |
| Aggregation vs composition | Hollow vs filled diamond; parts outlive vs die with owner |
| Prefer composition | Flexible "has-a" over rigid "is-a"; avoids fragile base classes |
| LSP in practice | Subclass must not strengthen preconditions / weaken postconditions |
| DIP in practice | Constructor-inject interfaces, not concrete classes |
| Singleton thread-safe | Double-checked locking + `volatile`, enum, or holder idiom |
| Strategy vs state | Both wrap behavior; strategy = interchangeable algorithms, state = behavior per internal state |
| Observer | Subject notifies subscribers; basis of events & pub/sub |
| Virtual destructor | Without it, `delete basePtr` skips derived cleanup → leak |
| vtable | Per-class dispatch table enabling runtime polymorphism |
