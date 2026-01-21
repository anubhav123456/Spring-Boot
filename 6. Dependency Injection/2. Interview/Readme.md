# Spring Boot Dependency Injection – Interview Questions & Answers

---

## 1. What is Dependency Injection?

**Answer:**
Dependency Injection (DI) is a design pattern where the dependencies of a class are **provided by an external source** rather than being created by the class itself. In Spring Boot, the **Spring container** injects required dependencies, enabling **loose coupling** and better maintainability.

---

## 2. Why do we need Dependency Injection?

**Answer:**

* To reduce **tight coupling** between classes
* To follow **Dependency Inversion Principle (SOLID)**
* To make code **easier to test, extend, and maintain**
* To shift object creation responsibility to Spring

---

## 3. What problem does Dependency Injection solve?

**Answer:**
DI solves the problem of **tight coupling**, where one class directly creates the object of another class using `new`. Any change in the dependent class then forces changes in the caller class.

---

## 4. Explain Dependency Inversion Principle.

**Answer:**
High-level modules should not depend on low-level modules. Both should depend on **abstractions (interfaces)**, not concrete implementations.

---

## 5. How does Spring achieve Dependency Injection?

**Answer:**
Spring uses:

* `@Component` to create beans
* `@Autowired` to resolve dependencies
* Reflection internally to inject dependencies

---

## 6. What are the types of Dependency Injection in Spring?

**Answer:**

1. Field Injection
2. Setter Injection
3. Constructor Injection

---

## 7. What is Field Injection?

**Answer:**
Field injection injects dependencies directly into class fields using `@Autowired`.

```java
@Autowired
Order order;
```

---

## 8. Advantages of Field Injection

**Answer:**

* Simple and easy to write
* Less boilerplate code

---

## 9. Disadvantages of Field Injection

**Answer:**

* Cannot make fields `final`
* Breaks immutability
* Risk of `NullPointerException`
* Difficult unit testing
* Hidden dependencies

---

## 10. What is Setter Injection?

**Answer:**
Dependencies are injected via setter methods annotated with `@Autowired`.

```java
@Autowired
public void setOrder(Order order) {
    this.order = order;
}
```

---

## 11. Advantages of Setter Injection

**Answer:**

* Dependency can be changed after object creation
* Easier unit testing than field injection

---

## 12. Disadvantages of Setter Injection

**Answer:**

* Cannot make fields immutable
* Object can be in partially initialized state
* Less readable and harder to maintain

---

## 13. What is Constructor Injection?

**Answer:**
Dependencies are injected through the constructor of the class.

```java
public User(Order order) {
    this.order = order;
}
```

---

## 14. Why is Constructor Injection recommended?

**Answer:**

* Ensures **mandatory dependencies** are available
* Allows `final` fields (immutability)
* Fail-fast behavior
* Easier unit testing
* Object is fully initialized

---

## 15. Is `@Autowired` mandatory on constructors?

**Answer:**
From **Spring 4.3+**, if a class has only **one constructor**, `@Autowired` is optional. If multiple constructors exist, `@Autowired` is mandatory.

---

## 16. What happens if a class has multiple constructors?

**Answer:**
Spring throws an exception unless one constructor is explicitly marked with `@Autowired`.

---

## 17. What is Fail-Fast in Constructor Injection?

**Answer:**
If a dependency is missing, Spring fails during **application startup**, not at runtime, preventing hidden errors.

---

## 18. How does Constructor Injection help in Unit Testing?

**Answer:**
Mock objects can be passed directly via the constructor without using reflection or `@InjectMocks`.

---

## 19. What is Circular Dependency?

**Answer:**
When two or more beans depend on each other directly or indirectly, forming a cycle.

```text
Order → Invoice → Order
```

---

## 20. How does Spring behave with Circular Dependency?

**Answer:**
Spring fails to start the application and throws a **BeanCurrentlyInCreationException**.

---

## 21. How can Circular Dependency be resolved?

**Answer:**

1. **Refactor code** (best solution)
2. Use `@Lazy`
3. Use `@PostConstruct` (hacky, not recommended)

---

## 22. How does `@Lazy` resolve Circular Dependency?

**Answer:**
`@Lazy` delays bean creation and injects a **proxy object** instead of the actual bean. The real object is created only when first used.

---

## 23. What is Unsatisfied Dependency?

**Answer:**
Occurs when Spring finds **multiple implementations** of an interface and cannot decide which one to inject.

---

## 24. How do you resolve Unsatisfied Dependency?

**Answer:**

* Using `@Primary`
* Using `@Qualifier`

---

## 25. What is `@Primary`?

**Answer:**
`@Primary` gives **higher priority** to a bean when multiple candidates are available.

---

## 26. What is `@Qualifier`?

**Answer:**
`@Qualifier` explicitly tells Spring **which bean to inject** by name.

```java
@Autowired
@Qualifier("onlineOrder")
Order order;
```

---

## 27. Difference between `@Primary` and `@Qualifier`

| Aspect      | @Primary     | @Qualifier    |
| ----------- | ------------ | ------------- |
| Control     | Global       | Local         |
| Flexibility | Low          | High          |
| Usage       | Default bean | Explicit bean |

---

## 28. Why is Field Injection discouraged in production?

**Answer:**

* Breaks encapsulation
* Hard to test
* Hidden dependencies
* Encourages poor design

---

## 29. What happens internally when `@Autowired` is used?

**Answer:**
Spring uses **reflection** to scan fields, constructors, and methods annotated with `@Autowired` and injects dependencies accordingly.

---

## 30. Final Interview Recommendation

**Answer:**

> Always use **Constructor Injection**.
> If constructors become too large, **refactor the design**, not the injection style.

---
