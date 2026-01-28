

---

# 🌟 Spring Boot AOP (Aspect Oriented Programming) — Part 1

## 🔹 What is AOP?

**Aspect Oriented Programming (AOP)** allows us to:

> 👉 **Intercept method execution** and run logic **before, after, or around** it.

It helps separate:

| Type                       | Meaning                                               |
| -------------------------- | ----------------------------------------------------- |
| **Business Logic**         | Main functionality (e.g., fetch employee, save order) |
| **Cross-Cutting Concerns** | Logging, transactions, security, validation, etc.     |

Without AOP, we would repeat this in 100 methods:

```java
log();
startTransaction();
businessLogic();
commit();
```

With AOP → business code stays clean, and boilerplate is handled separately.

---

## 🔹 What is an **Aspect**?

An **Aspect** is a class that contains cross-cutting logic.

It improves:

* ♻️ Reusability
* 🛠 Maintainability

---

## 🔹 Dependency Required

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

---

# 🧩 Basic Example

## 🎯 Business Class

```java
@RestController
@RequestMapping(value = "/api/")
public class Employee {

    @GetMapping(path = "/fetchEmployee")
    public String fetchEmployee(){
        return "item fetched";
    }
}
```

---

## 🎯 Aspect Class

```java
@Component
@Aspect
public class LoggingAspect {

    @Before("execution(public String com.conceptandcoding.learningspringboot.Employee.fetchEmployee())")
    public void beforeMethod(){
        System.out.println("inside beforeMethod Aspect");
    }
}
```

### 🔄 Flow:

1. API called → `/api/fetchEmployee`
2. AOP intercepts
3. `beforeMethod()` runs
4. Business method runs

---

# 🧠 Important Terms

| Term         | Meaning                                      |
| ------------ | -------------------------------------------- |
| **Aspect**   | Class containing cross-cutting logic         |
| **Advice**   | Code to execute (before/after/around)        |
| **Pointcut** | Expression deciding **where** advice applies |

---

# 🎯 Pointcut Expressions

---

## 1️⃣ **execution()** Pointcut

Matches a **specific method**.

### Structure:

```
execution(accessModifier returnType package.class.method(parameters))
```

---

### ⭐ Wildcard `*` → matches **single item**

#### ✔ Match any return type

```java
@Before("execution(* com.conceptandcoding.learningspringboot.Employee.fetchEmployee())")
```

---

#### ✔ Match any method with one String parameter

```java
@Before("execution(* com.conceptandcoding.learningspringboot.Employee.*(String))")
```

---

#### ✔ Match fetchEmployee with any one parameter

```java
@Before("execution(String com.conceptandcoding.learningspringboot.Employee.fetchEmployee(*))")
```

---

### ⭐ Wildcard `..` → matches **0 or more items**

#### ✔ Match method with any parameters

```java
@Before("execution(String com.conceptandcoding.learningspringboot.Employee.fetchEmployee(..))")
```

---

#### ✔ Match method in package + subpackages

```java
@Before("execution(String com.conceptandcoding..fetchEmployee())")
```

---

#### ✔ Match all methods in package & subpackages

```java
@Before("execution(String com.conceptandcoding..*())")
```

---

## 2️⃣ **within()** Pointcut

Matches **all methods in a class or package**.

### ✔ All methods in a class

```java
@Before("within(com.conceptandcoding.learningspringboot.Employee)")
```

### ✔ All methods in package & subpackages

```java
@Before("within(com.conceptandcoding.learningspringboot..*)")
```

---

## 3️⃣ **@within** Pointcut

Matches all methods in classes having a specific annotation.

### Business Code

```java
@RestController
@RequestMapping(value = "/api/")
public class Employee {

    @Autowired
    EmployeeUtil employeeUtil;

    @GetMapping(path = "/fetchEmployee")
    public String fetchEmployee(){
        employeeUtil.employeeHelperMethod();
        return "item fetched";
    }
}
```

```java
@Service
public class EmployeeUtil {

    public void employeeHelperMethod() {
        System.out.println("employee helper method called");
    }
}
```

### Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("@within(org.springframework.stereotype.Service)")
    public void beforeMethod() {
        System.out.println("inside beforeMethod aspect");
    }
}
```

✔ Runs before methods of classes annotated with `@Service`.

---

## 4️⃣ **@annotation** Pointcut

Matches methods with a specific annotation.

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("@annotation(org.springframework.web.bind.annotation.GetMapping)")
    public void beforeMethod() {
        System.out.println("inside beforeMethod aspect");
    }
}
```

✔ Runs before any method having `@GetMapping`.

---

## 5️⃣ **args()** Pointcut

Matches methods based on parameter types.

### Business Code

```java
@RestController
@RequestMapping(value = "/api/")
public class Employee {

    @Autowired
    EmployeeUtil employeeUtil;

    @GetMapping(path = "/fetchEmployee")
    public String fetchEmployee(){
        employeeUtil.employeeHelperMethod("xyz", 123);
        return "item fetched";
    }
}
```

```java
@Service
public class EmployeeUtil {

    public void employeeHelperMethod(String str, int val) {
        System.out.println("employee helper method called");
    }
}
```

### Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("args(String, int)")
    public void beforeMethod() {
        System.out.println("inside beforeMethod aspect");
    }
}
```

✔ Runs for any method with parameters `(String, int)`.

---

### ✔ Using Object Type

```java
@Before("args(com.conceptandcoding.learningspringboot.Employee)")
```

---

# 🔥 Advice Types (Quick View)

| Annotation        | Runs                          |
| ----------------- | ----------------------------- |
| `@Before`         | Before method                 |
| `@After`          | After method                  |
| `@AfterReturning` | After successful return       |
| `@AfterThrowing`  | If exception occurs           |
| `@Around`         | Before & after (full control) |

---

# 🧠 Big Picture

AOP allows:

```
Client → AOP Proxy → Advice Runs → Business Method Runs
```

So your business class stays clean:

```java
public String fetchEmployee(){
    return "item fetched";
}
```

While logging, transactions, etc., are handled separately.

---
