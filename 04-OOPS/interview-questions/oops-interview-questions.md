# OOPS — Top 30 Interview Questions (with Answers)

> Definitions alone won't cut it — each answer includes the "why" interviewers probe for.

---

### 1. What is OOP? Why do we use it?
A paradigm organizing code into **objects** that bundle state (fields) and behavior (methods), modeled around classes. Benefits: modularity, controlled access to state (encapsulation), reuse via inheritance/composition, interchangeable components via polymorphism, and a vocabulary that maps to real-world domains — which is why large systems default to OOP.

### 2. Class vs object vs instance?
Class: the blueprint — defines fields/methods, no memory. Object/instance: a runtime realization with its own state and identity; memory allocated on construction. One class → many objects (`Car` blueprint vs *your* car).

### 3. Explain the four pillars with real-life examples.
- **Encapsulation**: ATM — balance is private; deposits go through validated methods.
- **Abstraction**: driving a car — you use pedals/steering without knowing fuel injection.
- **Inheritance**: `Dog` and `Cat` extend `Animal` — shared `eat()/sleep()` comes free.
- **Polymorphism**: `shape.draw()` renders differently for `Circle`/`Square` — same call, behavior depends on the actual object.

### 4. Encapsulation vs abstraction?
Abstraction is the **design idea**: expose only what clients need (the "what"). Encapsulation is the **mechanism** that enforces it in language: private fields + accessor methods (hiding the data, the "how"). Every well-encapsulated class achieves abstraction, but abstraction also covers interface design, not just data hiding.

### 5. What are the types of inheritance? Which languages allow multiple inheritance?
Single, multilevel, hierarchical, multiple, hybrid. C++ allows multiple class inheritance; Java/C# allow it only through **interfaces** (classes limited to one parent) — to avoid ambiguity and the diamond problem.

### 6. What is the diamond problem and how is it solved?
D inherits from B and C, which both override a method from A — which impl should D get? **C++**: virtual inheritance makes A a single shared base. **Java**: no multiple class inheritance; if two interfaces provide conflicting default methods, the compiler forces the subclass to override (it may delegate via `B.super.m()`).

### 7. Method overloading vs overriding — full rules.
Overloading: same name, different parameter lists, resolved at compile time by the declared type; changing only the return type is invalid. Overriding: same signature (covariant returns allowed) in a subclass, resolved at runtime by the actual object; access can only widen; checked exceptions can only narrow; `static` methods are hidden (not overridden), `private`/`final` (Java) can't be overridden.

### 8. Can we override static methods? What about `main`?
No. Static methods belong to the class and are resolved statically — a "re-declared" static method in a child **hides** the parent's, chosen by reference type. Same for `main`: you can overload it, but JVM only ever calls the standard `public static void main(String[])`.

### 9. Constructor vs method?
Constructor: same name as the class, no return type, called implicitly on creation, initializes state, can't be abstract/static/final (Java), can be private. Method: any name, must have a return type, called explicitly, operates on existing objects.

### 10. Types of constructors? What is a copy constructor?
Default (compiler gives one only if you define none), parameterized, copy. **Copy constructor** builds a new object from an existing one (C++: `Foo(const Foo&)`; Java: `new Foo(other)` idiom since there's no native one). Essential to implement deep copies when objects hold references/pointers.

### 11. Shallow vs deep copy?
Shallow duplicates the top object but shares referenced objects — changes leak through. Deep duplicates the entire object graph. Default copying is shallow in most languages; deep copy via copy constructors, clone overrides, or serialization. Interview extension: how would you deep-copy a `List<List<Integer>>`?

### 12. Abstract class vs interface — when to use each?
Abstract class: shared **state + partial implementation** for closely related types (one-per-class inheritance, has constructors/fields). Interface: a **capability contract** implementable by unrelated types, allows multiple inheritance; Java 8+ default methods blur the line a little, but interfaces still can't hold per-object state. Rule: "is-a with shared code" → abstract; "can-do" → interface.

### 13. Can an abstract class have a constructor? Can it be instantiated?
Yes it can (and should, to initialize its fields — called via the subclass's constructor chain). No, it can't be instantiated directly — only concrete subclasses can be created.

### 14. Static vs instance members? Restrictions on static methods?
Instance members: one copy per object, accessible via references. Static: one copy per class, shared, loaded once. Static methods can't use `this`/`super`, can't directly touch instance fields, and are never polymorphic (hiding, not overriding). Use statics for constants, factories, pure utilities — not for mutable shared state (thread-safety!).

### 15. `final` vs `finally` vs `finalize()`?
`final`: keyword — constants, methods that can't be overridden, classes that can't be extended. `finally`: block that runs after try/catch for cleanup (almost always, except `System.exit`/JVM death; prefer try-with-resources). `finalize()`: GC hook — deprecated because timing is unpredictable and it can resurrect objects; never rely on it.

### 16. Explain access modifiers.
Java: `private` (class only) < default/package-private < `protected` (package + subclasses) < `public`. C++: private/protected/public (+ friend). Guideline: fields private, expose the narrowest API possible — visibility is how encapsulation is enforced.

### 17. Association vs aggregation vs composition?
Association: general "knows/uses" link (doctor ↔ patient). **Aggregation**: whole–part where parts **outlive** the whole (team ◇ players). **Composition**: whole–part where parts **die with** the whole (house ◆ rooms; created and destroyed by the owner). Interviewers check you attach the lifecycle difference, not just diamond shapes.

### 18. Inheritance vs composition — which do you prefer and why?
Prefer composition ("has-a") unless there's a true, substitutable "is-a": composition delegates behavior, can change at runtime, doesn't drag the parent's API into yours, and avoids the fragile-base-class problem. Inheritance is for genuine subtyping where LSP holds.

### 19. Explain SOLID with quick examples.
**S**RP: `Invoice` shouldn't also email PDFs — split printing/sending out. **O**CP: add `UpiPayment` as a new `PaymentMethod` class instead of editing an if/else chain. **L**SP: a subclass must be substitutable — `Rectangle/Square` violation is the classic example. **I**SP: split a fat `Worker` interface so a robot doesn't implement `eat()`. **D**IP: `OrderService` takes a `Notifier` interface via constructor injection rather than `new EmailNotifier()`.

### 20. What is Liskov Substitution in practice?
Wherever the parent works, any child must work — without surprises. Violations: child throws for inherited methods, strengthens preconditions, weakens postconditions, or behaves incompatibly (`Square extends Rectangle` breaks `setWidth`). Fix: redesign the hierarchy or use composition.

### 21. What is coupling and cohesion? What do we aim for?
**Cohesion**: how focused a module's responsibilities are — aim **high**. **Coupling**: how much modules know about each other — aim **low** (depend on interfaces, avoid chaining `a.getB().getC()` — Law of Demeter). High cohesion + low coupling = changeable, testable code.

### 22. What is the Singleton pattern? Make it thread-safe.
Ensures exactly one instance with a global access point (config, connection pools). Thread-safe options: (a) **double-checked locking** with a `volatile` field, (b) holder idiom (JVM class-load locking), (c) `enum`. Downsides: global mutable state, hidden dependencies, testing pain — in modern code, a DI-managed singleton scope (Spring bean) is often better.

### 23. Factory vs Abstract Factory?
Factory Method: one product — a method (often overridable) defers which concrete class to instantiate. Abstract Factory: a factory interface producing **families** of related products (UiFactory → Button + Checkbox per OS theme) so clients stay platform-agnostic. Choose abstract factory when products must be consistent as a set.

### 24. Explain Observer and Strategy patterns.
**Observer**: subject maintains subscribers; on state change it notifies all — decouples event producers from consumers (UI listeners, pub/sub systems, MVC). **Strategy**: encapsulate interchangeable algorithms behind one interface, selected at runtime (payment methods, route options, sort comparators) — removes per-type conditionals, satisfies OCP.

### 25. Builder vs Factory?
Factory answers *"which subclass?"* in one call. Builder answers *"this object needs many optional parts"* — step-by-step fluent construction, ideal for immutable objects with many fields (`new HttpRequest.Builder().url(...).header(...).build()`), and replaces telescoping constructors.

### 26. What is a virtual function? Why does a base class need a virtual destructor (C++)?
Virtual functions enable runtime polymorphism — calls dispatch via the object's **vtable**, so a base-class pointer invokes the derived override. Without a **virtual destructor**, `delete basePtr` runs only the base destructor — derived resources leak (undefined behavior). Rule: any polymorphic base gets a virtual destructor.

### 27. Pass by value vs pass by reference?
Pass-by-value copies the argument — callee changes are invisible (Java/C++ primitives; Java object *references* are passed by value, so you can mutate the object but not rebind the caller's variable). Pass-by-reference aliases the original (C++ `&`, C# `ref`); changes stick. Classic trap: swapping two Java objects' references doesn't work.

### 28. How does exception handling work? Best practices?
`try` guards code; matching `catch` handles; `finally`/try-with-resources cleans up; unhandled exceptions propagate up the stack. Checked exceptions force handling (recoverable IO), unchecked signal bugs (NPE). Best practices: catch specific types, never swallow, don't use exceptions for control flow, wrap-and-throw with context, custom domain exceptions.

### 29. What is a vtable? What's the cost of polymorphism?
Per-class table of virtual function pointers; each object stores a vptr set at construction. A virtual call = one extra indirection (vptr → vtable → function), which is why micro-hot paths sometimes avoid virtuals — but 99% of code shouldn't care. C++ opts in with `virtual`; Java methods are virtual by default (except `final`/`private`/`static`).

### 30. How would you design X (parking lot / elevator / LRU cache)?
Framework to answer LLD rounds: (1) clarify requirements & scope, (2) identify core classes and their relationships (association/composition), (3) define interfaces for varying behavior (strategy), (4) apply SOLID — mention which, (5) discuss concurrency if relevant (locks on slots/floors), (6) sketch key class diagram + walk through one flow. Practice 2–3 designs end-to-end before interviews.

---

## 💡 How to answer OOPS questions well
- Pair every definition with a one-line example — interviewers filter rote learners instantly.
- Use the language you know best, but know where Java/C++/Python differ (multiple inheritance, virtual, duck typing).
- In design rounds, say SOLID principle names out loud while justifying choices — that's what's being graded.
