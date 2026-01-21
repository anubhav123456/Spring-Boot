# Spring Boot Dependency Injection

---

## 1. Prerequisite for Dependency Injection

Before understanding **Dependency Injection (DI)**, you must understand:

* **Spring Beans**
* **Bean Lifecycle**

Dependency Injection fits into the **bean lifecycle**, specifically **after bean instantiation and before usage**.

---

## 2. Problem Without Dependency Injection (Tight Coupling)

### Basic Example

```java
public class User {
    Order order = new Order();

    public User() {
        System.out.println("initializing user");
    }
}

public class Order {
    public Order() {
        System.out.println("initializing Order");
    }
}
```

### Issues

* `User` **directly creates** `Order`
* **Tightly coupled** classes
* Any change in `Order` impacts `User`

---

## 3. Problem After Introducing Interface

```java
public interface Order {}

public class OnlineOrder implements Order {}
public class OfflineOrder implements Order {}

public class User {
    Order order = new OnlineOrder();
}
```

### Issues

* Violates **Dependency Inversion Principle (D in SOLID)**
* High-level module (`User`) depends on low-level module (`OnlineOrder`)

---

## 4. Dependency Inversion Principle (SOLID)

> **Do not depend on concrete classes, depend on abstractions**

Correct approach:

```java
public class User {
    Order order;

    public User(Order orderObj) {
        this.order = orderObj;
    }
}
```

Dependency is now **injected from outside**.

---

## 5. Dependency Injection Using Spring

```java
@Component
public class User {
    @Autowired
    Order order;
}

@Component
public class Order {}
```

### What Spring Does

* Spring creates beans
* Finds dependencies using `@Autowired`
* Injects dependency automatically
* Achieves **loose coupling**

---

## 6. Types of Dependency Injection in Spring

1. Field Injection
2. Setter Injection
3. Constructor Injection (**Recommended**)

---

## 7. Field Injection

### Example

```java
@Component
public class User {

    @Autowired
    Order order;

    public User() {
        System.out.println("User initialized");
    }
}

@Component
@Lazy
public class Order {
    public Order() {
        System.out.println("order initialized");
    }
}
```

### How It Works

* Spring creates `User`
* Uses **reflection** to find `@Autowired` fields
* Creates `Order` if required
* Injects dependency

### Advantages

* Simple
* Easy to read

### Disadvantages

❌ Cannot make fields `final`

```java
@Autowired
public final Order order; // Not allowed
```

❌ Breaks immutability (reflection ignores `final`)

❌ Risk of `NullPointerException`

```java
User user = new User();
user.process(); // order is null
```

❌ Difficult unit testing

* Requires `@Mock` and `@InjectMocks`
* Uses reflection internally

---

## 8. Setter Injection

### Example

```java
@Component
public class User {

    private Order order;

    public User() {
        System.out.println("User initialized");
    }

    @Autowired
    public void setOrderDependency(Order order) {
        this.order = order;
    }
}

@Component
@Lazy
public class Order {
    public Order() {
        System.out.println("order initialized");
    }
}
```

### Advantages

✔ Dependency can be changed later

✔ Easier unit testing (no reflection needed)

### Disadvantages

❌ Cannot make fields `final`

❌ Dependency not obvious (poor readability)

❌ Object can exist in partially initialized state

---

## 9. Constructor Injection (Recommended)

### Example

```java
@Component
public class User {

    private final Order order;

    @Autowired
    public User(Order order) {
        this.order = order;
        System.out.println("User initialized");
    }
}

@Component
@Lazy
public class Order {
    public Order() {
        System.out.println("order initialized");
    }
}
```

### Spring 4.3+ Feature

If only **one constructor exists**, `@Autowired` is optional:

```java
@Component
public class User {
    private final Order order;

    public User(Order order) {
        this.order = order;
    }
}
```

---

## 10. Multiple Constructors Scenario

### Problem

```java
@Component
public class User {

    Order order;
    Invoice invoice;

    public User(Order order) {
        this.order = order;
    }

    public User(Invoice invoice) {
        this.invoice = invoice;
    }
}
```

❌ Spring fails: *Which constructor to use?*

### Solution

```java
@Autowired
public User(Invoice invoice) {
    this.invoice = invoice;
}
```

---

## 11. Advantages of Constructor Injection

✔ Mandatory dependencies enforced
✔ Fields can be `final` (immutable)
✔ Fail-fast (startup failure if dependency missing)
✔ Easier unit testing
✔ Fully initialized object

### Disadvantage (Actually an Advantage)

* Large constructor → indicates poor design
* Encourages refactoring

---

## 12. Common DI Issues

### 12.1 Circular Dependency

```java
@Component
public class Order {
    @Autowired
    Invoice invoice;
}

@Component
public class Invoice {
    @Autowired
    Order order;
}
```

❌ Application fails to start

### Solutions

#### 1️⃣ Refactor (Best)

* Extract common logic
* Use separate service

#### 2️⃣ Use `@Lazy`

```java
@Autowired
@Lazy
Order order;
```

* Spring injects proxy
* Actual object created when first used

#### 3️⃣ `@PostConstruct` (Hacky)

```java
@Component
public class Invoice {

    private Order order;

    @PostConstruct
    public void init() {
        this.order = new Order();
    }
}
```

⚠️ Not recommended

---

## 13. Unsatisfied Dependency

### Scenario

```java
public interface Order {}

@Component
public class OnlineOrder implements Order {}

@Component
public class OfflineOrder implements Order {}

@Component
public class User {
    @Autowired
    Order order;
}
```

❌ Spring doesn't know which implementation to inject

---

## 14. Solution 1: `@Primary`

```java
@Component
@Primary
public class OnlineOrder implements Order {}
```

Spring gives priority to `@Primary`

---

## 15. Solution 2: `@Qualifier`

```java
@Component
@Qualifier("onlineOrder")
public class OnlineOrder implements Order {}

@Component
@Qualifier("offlineOrder")
public class OfflineOrder implements Order {}

@Component
public class User {

    @Autowired
    @Qualifier("offlineOrder")
    Order order;
}
```

✔ Explicit dependency resolution

---

## 16. Final Summary

| Injection Type | Recommended | Immutable | Fail Fast | Test Friendly |
| -------------- | ----------- | --------- | --------- | ------------- |
| Field          | ❌ No        | ❌ No      | ❌ No      | ❌ Hard        |
| Setter         | ⚠️ Rare     | ❌ No      | ❌ No      | ✔ Yes         |
| Constructor    | ✅ Yes       | ✔ Yes     | ✔ Yes     | ✔ Yes         |

---

## 17. Final Recommendation

> **Always prefer Constructor Injection**

If you have doubts → refactor design, not DI type.

---
