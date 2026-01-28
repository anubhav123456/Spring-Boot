

---

# 🌟 Spring AOP – Part 2

---

## 🔹 1. `@args` Pointcut

### ✅ Meaning

```java
@Before("@args(org.springframework.stereotype.Service)")
```

**Reads as:**

> Match any method where the **runtime type of the argument** is a class annotated with `@Service`.

* It does **NOT** care about:

  * Method name
  * Class name
* It ONLY checks:
  👉 The **object passed as argument** → is its **class annotated** with given annotation?

---

### 🧠 Example

#### Service Classes

```java
@Service
public class EmployeeUtil {

    public void employeeHelperMethod(EmployeeDAO employeeDAO) {
        System.out.println("employee helper method called");
    }
}
```

```java
@Service
public class EmployeeDAO {
    // Class body
}
```

#### Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("@args(org.springframework.stereotype.Service)")
    public void beforeMethod() {
        System.out.println("inside beforeMethod aspect");
    }
}
```

### 🔍 What happens?

* Method: `employeeHelperMethod(EmployeeDAO employeeDAO)`
* Argument type: `EmployeeDAO`
* `EmployeeDAO` is annotated with `@Service` ✅
  ➡️ Pointcut matches → advice runs.

---

## 🔹 2. `target()` Pointcut

### ✅ Meaning

```java
@Before("target(com.conceptandcoding.learningspringboot.EmployeeUtil)")
```

> Matches any method **called on an instance** of this class.

---

### 🧠 Example

#### Controller

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

#### Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("target(com.conceptandcoding.learningspringboot.EmployeeUtil)")
    public void beforeMethod() {
        System.out.println("inside beforeMethod aspect");
    }
}
```

#### Bean

```java
@Component
public class EmployeeUtil {

    public void employeeHelperMethod() {
        System.out.println("employee helper method called");
    }
}
```

### 🔍 What happens?

* `employeeUtil.employeeHelperMethod()` is called
* Object type = `EmployeeUtil`
  ➡️ Matches `target(EmployeeUtil)` → advice runs.

---

## 🔹 3. `target()` with Interface

You can give **interface** instead of class.

### Interface

```java
public interface IEmployee {
    public void fetchEmployeeMethod();
}
```

### Implementations

```java
@Component
@Qualifier("tempEmployee")
public class TempEmployee implements IEmployee {
    @Override
    public void fetchEmployeeMethod() {
        System.out.println("in temp Employee fetch method");
    }
}
```

```java
@Component
@Qualifier("permaEmployee")
public class PermanentEmployee implements IEmployee {
    @Override
    public void fetchEmployeeMethod() {
        System.out.println("inside permanent fetch employee method");
    }
}
```

### Controller

```java
@RestController
@RequestMapping(value = "/api/")
public class EmployeeController {

    @Autowired
    @Qualifier("tempEmployee")
    IEmployee employeeObj;

    @GetMapping(path = "/fetchEmployee")
    public String fetchEmployee() {
        employeeObj.fetchEmployeeMethod();
        return "item fetched";
    }
}
```

### Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("target(com.conceptandcoding.learningspringboot.IEmployee)")
    public void beforeMethod() {
        System.out.println("inside beforeMethod aspect");
    }
}
```

### 🔍 Result

* Object = `TempEmployee`
* It **implements** `IEmployee`
  ➡️ Pointcut matches.

---

## 🔹 4. Combining Pointcuts (`&&`, `||`)

---

### AND Example

```java
@Before("execution(* com.conceptandcoding.learningspringboot.EmployeeController.*())"
        + " && @within(org.springframework.web.bind.annotation.RestController)")
public void beforeAndMethod() {
    System.out.println("inside beforeAndMethod aspect");
}
```

✔ Both must be true:

* Method in `EmployeeController`
* Class annotated with `@RestController`

---

### OR Example

```java
@Before("execution(* com.conceptandcoding.learningspringboot.EmployeeController.*())"
        + " || @within(org.springframework.stereotype.Component)")
public void beforeOrMethod() {
    System.out.println("inside beforeOrMethod aspect");
}
```

✔ Any one true is enough.

---

## 🔹 5. `@annotation` Pointcut

### Meaning

> Match methods that have a specific annotation.

### Example

```java
@RestController
@RequestMapping(value = "/api/")
public class Employee {

    @GetMapping (path = "/fetchEmployee")
    public String fetchEmployee(){
        return "item fetched";
    }
}
```

```java
@Component
@Aspect
public class LoggingAspect {

    @Before("@annotation(org.springframework.web.bind.annotation.GetMapping)")
    public void beforeMethod(){
        System.out.println("inside beforeMethod Aspect");
    }
}
```

---

## 🔹 6. Named Pointcuts

Avoid repeating long expressions.

```java
@Aspect
@Component
public class LoggingAspect {

    @Pointcut("execution(* com.conceptandcoding.learningspringboot.EmployeeController.*())")
    public void customPointcutName() {
        // empty
    }

    @Before("customPointcutName()")
    public void beforeMethod() {
        System.out.println("inside beforeMethod aspect");
    }
}
```

---

## 🔹 7. Advice Types

| Advice    | Runs                       |
| --------- | -------------------------- |
| `@Before` | Before method              |
| `@After`  | After method               |
| `@Around` | Before + After (surrounds) |

---

## 🔹 8. `@Around` Advice

### Example

```java
@Aspect
@Component
public class LoggingAspect {

    @Around("execution(* com.conceptandcoding.learningspringboot.EmployeeUtil.*())")
    public void aroundMethod(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("inside before Method aspect");
        joinPoint.proceed();  // calls actual method
        System.out.println("inside after Method aspect");
    }
}
```

```java
@Component
public class EmployeeUtil {

    public void employeeHelperMethod() {
        System.out.println("employee helper method called");
    }
}
```

---

## 🔹 9. How Spring AOP Works Internally

### Step 1: Find `@Aspect` classes

Spring scans and finds aspect classes.

### Step 2: Parse Pointcut Expressions

Expressions are parsed and stored efficiently.

### Step 3: Find Beans (`@Component`, `@Service`, etc.)

Each bean checked if eligible for interception.

### Step 4: Create Proxy

If match:

| Case                       | Proxy Type            |
| -------------------------- | --------------------- |
| Class implements interface | **JDK Dynamic Proxy** |
| No interface               | **CGLIB Proxy**       |

Proxy overrides methods and adds advice logic.

### Step 5: Method Invocation Flow

When method is called:

1. Proxy method is invoked
2. Advice chain created
3. Execution order:

```
Before Advice
→ Around (before)
→ Actual Method
→ Around (after)
→ After Advice
```

Internally handled using:

* `CglibAopProxy`
* `ReflectiveMethodInvocation`
* `ProceedingJoinPoint`

---

# 🎯 Final Big Picture

**Pointcut** = *Where to apply logic*
**Advice** = *What logic to apply*
**Proxy** = *How Spring injects that logic without changing your code*

---

