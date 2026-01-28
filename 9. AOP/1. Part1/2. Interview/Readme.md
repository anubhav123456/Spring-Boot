

---

# 🔥 SPRING AOP

---

## 🟢 1. What is AOP?

**Answer:**

AOP (Aspect Oriented Programming) is a programming paradigm used to separate **cross-cutting concerns** from business logic.

It allows us to **intercept method execution** and run additional logic:

* Before method
* After method
* Around method

Examples of cross-cutting concerns:

* Logging
* Transaction management
* Security
* Validation

---

## 🟢 2. What problem does AOP solve?

**Answer:**

Without AOP, boilerplate code like logging and transaction handling gets repeated in many methods, causing:

* Code duplication
* Poor maintainability
* Tight coupling

AOP moves this repeated logic into a separate **aspect class**, keeping business code clean.

---

## 🟢 3. What is an Aspect?

**Answer:**

An **Aspect** is a class that contains cross-cutting logic.

```java
@Aspect
@Component
public class LoggingAspect {
}
```

It defines:

* Where code should run (Pointcut)
* What code should run (Advice)

---

## 🟢 4. What is Advice?

**Answer:**

Advice is the **actual code** that runs when a method is intercepted.

Types:

| Advice            | Runs                          |
| ----------------- | ----------------------------- |
| `@Before`         | Before method execution       |
| `@After`          | After method finishes         |
| `@AfterReturning` | After successful return       |
| `@AfterThrowing`  | If exception occurs           |
| `@Around`         | Before + after (full control) |

---

## 🟢 5. What is a Pointcut?

**Answer:**

A **Pointcut** is an expression that defines **which methods should be intercepted**.

Example:

```java
@Before("execution(* com.example.Employee.fetchEmployee())")
```

---

## 🟢 6. What does `execution()` pointcut do?

**Answer:**

It matches a **specific method execution**.

Syntax:

```
execution(returnType package.class.method(args))
```

Example:

```java
execution(String com.example.Employee.fetchEmployee())
```

---

## 🟢 7. What is the meaning of `*` in pointcut?

**Answer:**
`*` is a wildcard that matches **one item**.

Examples:

✔ Any return type

```java
execution(* com.example.Employee.fetchEmployee())
```

✔ Any method name

```java
execution(* com.example.Employee.*(String))
```

✔ Any single parameter

```java
execution(String com.example.Employee.fetchEmployee(*))
```

---

## 🟢 8. What is the meaning of `..` in pointcut?

**Answer:**
`..` matches **zero or more items**.

Examples:

✔ Any number of parameters

```java
execution(String com.example.Employee.fetchEmployee(..))
```

✔ Any subpackage

```java
execution(String com.example..fetchEmployee())
```

---

## 🟢 9. Difference between `*` and `..` ?

| Symbol | Meaning            |
| ------ | ------------------ |
| `*`    | One item           |
| `..`   | Zero or more items |

---

## 🟢 10. What does `within()` pointcut do?

**Answer:**

Matches **all methods in a class or package**.

```java
@Before("within(com.example.Employee)")
```

This runs advice for every method in `Employee` class.

---

## 🟢 11. What is `@within` pointcut?

**Answer:**

Matches all methods in classes that have a specific annotation.

```java
@Before("@within(org.springframework.stereotype.Service)")
```

Runs advice for all methods of classes annotated with `@Service`.

---

## 🟢 12. What is `@annotation` pointcut?

**Answer:**

Matches methods having a specific annotation.

```java
@Before("@annotation(org.springframework.web.bind.annotation.GetMapping)")
```

Runs advice for methods annotated with `@GetMapping`.

---

## 🟢 13. What does `args()` pointcut do?

**Answer:**

Matches methods based on parameter types.

```java
@Before("args(String, int)")
```

Matches methods that accept `(String, int)`.

---

## 🟢 14. How to match methods that take an object parameter?

```java
@Before("args(com.example.Employee)")
```

Matches methods receiving `Employee` object.

---

## 🟢 15. What dependency is required for Spring AOP?

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

---

## 🟢 16. How does Spring AOP work internally?

**Answer:**

Spring AOP uses **proxies**.

Flow:

```
Client → Proxy → Advice runs → Target method runs
```

Spring creates a proxy object instead of calling the actual bean directly.

---

## 🟢 17. What is the difference between Spring AOP and AspectJ?

| Spring AOP                 | AspectJ                 |
| -------------------------- | ----------------------- |
| Proxy-based                | Bytecode weaving        |
| Runtime weaving            | Compile-time weaving    |
| Works only on Spring beans | Works on any Java class |

---

## 🟢 18. Why must aspect class be annotated with `@Component`?

Because Spring must manage it as a bean. Otherwise, AOP will not work.

---

## 🟢 19. Can AOP work on private methods?

**Answer:**
❌ No.
Spring AOP uses proxies, and proxies intercept only **public method calls**.

---

## 🟢 20. Real-world uses of AOP?

* Logging
* Transactions (`@Transactional`)
* Security (`@PreAuthorize`)
* Exception handling
* Auditing

---

## 🎯 Most Important Interview Line

👉 **“AOP helps achieve separation of concerns by moving cross-cutting logic like logging and transactions away from business logic using aspects, pointcuts, and advice.”**

---
