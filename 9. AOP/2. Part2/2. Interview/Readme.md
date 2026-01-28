

---

# 🎯 Spring AOP Interview Questions & Answers

---

## 🧠 1. What is AOP in Spring?

**Answer:**
AOP (Aspect-Oriented Programming) is used to separate **cross-cutting concerns** (logging, security, transactions, auditing, etc.) from business logic.

Instead of writing logging/security code inside every method, we define:

* **Aspect** → contains cross-cutting logic
* **Pointcut** → where to apply logic
* **Advice** → what logic to run

Spring applies this using **proxies** at runtime.

---

## 🧠 2. What is the difference between Pointcut and Advice?

| Term         | Meaning                                              |
| ------------ | ---------------------------------------------------- |
| **Pointcut** | Expression that selects which method(s) to intercept |
| **Advice**   | Action that runs when pointcut matches               |

Example:

```java
@Before("execution(* com.app.service.*.*(..))")
public void logBefore() { }
```

* `execution(...)` → Pointcut
* `logBefore()` → Advice

---

## 🧠 3. What does `@args` pointcut do?

**Answer:**
`@args` matches methods where the **runtime type of the argument** is annotated with a specific annotation.

```java
@Before("@args(org.springframework.stereotype.Service)")
```

✔ Matches methods where the **object passed as parameter** belongs to a class annotated with `@Service`.

It checks the **annotation on argument’s class**, not on method or calling class.

---

## 🧠 4. Difference between `args()` and `@args()`?

|         | `args()`       | `@args()`                      |
| ------- | -------------- | ------------------------------ |
| Checks  | Argument type  | Annotation on argument’s class |
| Example | `args(String)` | `@args(Service)`               |

---

## 🧠 5. What does `target()` pointcut mean?

**Answer:**
`target()` matches methods executed on a particular **object type (instance)**.

```java
@Before("target(com.app.EmployeeUtil)")
```

It means:

> If any method is called on an object of `EmployeeUtil`, run advice.

---

## 🧠 6. Can `target()` use an interface?

**Yes.**

```java
@Before("target(com.app.IEmployee)")
```

✔ Matches methods invoked on any object implementing `IEmployee`.

Even if actual object is `TempEmployee` or `PermanentEmployee`, it matches.

---

## 🧠 7. What is `@annotation` pointcut?

**Answer:**
Matches methods annotated with a specific annotation.

```java
@Before("@annotation(org.springframework.web.bind.annotation.GetMapping)")
```

✔ Advice runs for all methods having `@GetMapping`.

---

## 🧠 8. What is the difference between `within` and `@within`?

|         | `within`                    | `@within`                     |
| ------- | --------------------------- | ----------------------------- |
| Matches | Classes in a package        | Classes with given annotation |
| Example | `within(com.app.service.*)` | `@within(RestController)`     |

---

## 🧠 9. How do you combine pointcuts?

Using boolean operators:

```java
@Before("execution(* com.app.Controller.*()) && @within(RestController)")
```

| Operator | Meaning         |   |               |
| -------- | --------------- | - | ------------- |
| `&&`     | Both must match |   |               |
| `        |                 | ` | Any one match |

---

## 🧠 10. What are Named Pointcuts?

Used to avoid repeating long expressions.

```java
@Pointcut("execution(* com.app.EmployeeController.*())")
public void controllerMethods() {}

@Before("controllerMethods()")
public void log() {}
```

Improves readability and reuse.

---

## 🧠 11. What is `@Around` advice?

**Answer:**
Runs **before and after** method execution.

```java
@Around("execution(* com.app.EmployeeUtil.*())")
public Object around(ProceedingJoinPoint pjp) throws Throwable {
    System.out.println("Before");
    Object result = pjp.proceed();  // actual method call
    System.out.println("After");
    return result;
}
```

👉 You must call `proceed()` to execute the method.

---

## 🧠 12. What is `ProceedingJoinPoint`?

Represents the method being executed.

Used only in `@Around` advice to:

* Execute the method → `proceed()`
* Get method name, args, target object

---

## 🧠 13. What is a Join Point?

A **point in execution** of the program where AOP can be applied.
In Spring AOP, join points are **method executions**.

---

## 🧠 14. How does Spring AOP work internally?

### Steps:

1. Spring scans for `@Aspect`
2. Parses pointcut expressions
3. Checks beans for interception eligibility
4. Creates **proxy objects**
5. Proxy intercepts method calls
6. Advice chain executed
7. Actual method invoked

---

## 🧠 15. What is a Proxy in AOP?

A **wrapper class** created by Spring that:

* Overrides target methods
* Executes advice
* Calls actual method

Your original bean is replaced with proxy bean.

---

## 🧠 16. JDK Proxy vs CGLIB Proxy

|             | JDK Proxy                                 | CGLIB Proxy                      |
| ----------- | ----------------------------------------- | -------------------------------- |
| Requirement | Must implement interface                  | Works without interface          |
| How         | Creates proxy implementing same interface | Creates subclass of target class |

Spring chooses automatically.

---

## 🧠 17. Execution order in AOP?

```
@Before
@Around (before part)
Actual Method
@Around (after part)
@After
```

---

## 🧠 18. Why is AOP efficient even with many pointcuts?

Because:

* Pointcuts are **parsed at startup**
* Stored in optimized structure
* Proxy created only for eligible beans
* No runtime scanning of all pointcuts

---

## 🧠 19. Can we intercept private methods?

❌ No. Spring AOP works on **proxy-based method calls**, so:

* Only **public methods**
* Called from outside the class

---

## 🧠 20. When should we use `@Around` instead of `@Before/@After`?

Use `@Around` when:

* Need to control method execution
* Modify return value
* Handle exceptions
* Measure execution time

---

# 🏆 Pro-Level Interview Question

**Q: Why doesn’t AOP work when a method inside the same class calls another method?**

**Answer:**
Because Spring AOP uses proxies.
Internal method calls bypass proxy → advice not applied.
This is called **self-invocation problem**.

---
