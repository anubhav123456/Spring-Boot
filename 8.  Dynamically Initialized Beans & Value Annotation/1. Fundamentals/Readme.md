# Dynamically Initialized Beans | Value Annotation

---

# 🧠 Problem: Unsatisfied Dependency in Spring

### Situation

```java
public interface Order {
    void createOrder();
}
```

Two implementations:

```java
@Component
public class OnlineOrder implements Order {
    public OnlineOrder() {
        System.out.println("Online Order Initialized");
    }

    public void createOrder() {
        System.out.println("created Online Order");
    }
}
```

```java
@Component
public class OfflineOrder implements Order {
    public OfflineOrder() {
        System.out.println("Offline Order initialized");
    }

    public void createOrder() {
        System.out.println("created Offline Order");
    }
}
```

Controller:

```java
@RestController
@RequestMapping(value = "/api")
public class User {

    @Autowired
    Order order;

    @PostMapping("/createOrder")
    public ResponseEntity<String> createOrder() {
        order.createOrder();
        return ResponseEntity.ok("");
    }
}
```

### ❌ What happens?

Spring sees:

```
Order → OnlineOrder
Order → OfflineOrder
```

But controller asks for:

```
@Autowired Order order;
```

👉 Spring gets confused: **Which bean?**
➡️ Application fails with **Unsatisfied Dependency Exception**

---

# ✅ Solution 1: Using `@Qualifier` (Static Injection)

```java
@RestController
@RequestMapping(value = "/api")
public class User {

    @Qualifier("onlineOrderObject")
    @Autowired
    Order order;

    @PostMapping("/createOrder")
    public ResponseEntity<String> createOrder() {
        order.createOrder();
        return ResponseEntity.ok("");
    }
}
```

Beans:

```java
@Qualifier("onlineOrderObject")
@Component
public class OnlineOrder implements Order {
    public OnlineOrder() {
        System.out.println("Online Order Initialized");
    }

    public void createOrder() {
        System.out.println("created Online Order");
    }
}
```

```java
@Qualifier("offlineOrderObject")
@Component
public class OfflineOrder implements Order {
    public OfflineOrder() {
        System.out.println("Offline Order initialized");
    }

    public void createOrder() {
        System.out.println("created Offline Order");
    }
}
```

### 🔹 Issue

This is **hardcoded**.

```
Order = OnlineOrder only
```

➡️ Not dynamic
➡️ People say it *breaks Dependency Inversion Principle* because behavior is fixed at compile time.

---

# ✅ Solution 1 (Improved): Dynamic using Multiple Qualifiers

```java
@RestController
@RequestMapping(value = "/api")
public class User {

    @Qualifier("onlineOrderObject")
    @Autowired
    Order onlineOrderObj;

    @Qualifier("offlineOrderObject")
    @Autowired
    Order offlineOrderObj;

    @PostMapping("/createOrder")
    public ResponseEntity<String> createOrder(@RequestParam boolean isOnlineOrder) {

        if (isOnlineOrder) {
            onlineOrderObj.createOrder();
        } else {
            offlineOrderObj.createOrder();
        }

        return ResponseEntity.ok("");
    }
}
```

### 🔥 Key Points

✔ Both beans are created (Singleton)
✔ Injection happens using qualifiers
✔ Runtime decision based on **client input**

👉 Industry-standard approach

---

# ✅ Solution 2: Dynamic Bean Creation using `@Bean` + `@Value`

Here we **remove @Component** and use configuration.

### Interface

```java
public interface Order {
    void createOrder();
}
```

### Implementations (Plain Classes)

```java
public class OnlineOrder implements Order {

    public OnlineOrder() {
        System.out.println("Online Order Initialized");
    }

    public void createOrder() {
        System.out.println("created Online Order");
    }
}
```

```java
public class OfflineOrder implements Order {

    public OfflineOrder() {
        System.out.println("Offline Order initialized");
    }

    public void createOrder() {
        System.out.println("created Offline Order");
    }
}
```

---

### Controller

```java
@RestController
@RequestMapping(value = "/api")
public class User {

    @Autowired
    Order order;

    @PostMapping("/createOrder")
    public ResponseEntity<String> createOrder() {

        order.createOrder();
        return ResponseEntity.ok("");
    }
}
```

---

### Configuration Class

```java
@Configuration
public class AppConfig {

    @Bean
    public Order createOrderBean(@Value("${isOnlineOrder}") boolean isOnlineOrder) {
        if(isOnlineOrder) {
            return new OnlineOrder();
        } else {
            return new OfflineOrder();
        }
    }
}
```

---

### application.properties

```
isOnlineOrder=false
```

---

## 🔥 How this works

1. Spring starts
2. Sees `User` needs `Order`
3. Finds a bean definition:

```
@Bean public Order createOrderBean(...)
```

4. Injects value from properties:

```
@Value("${isOnlineOrder}")
```

5. If false → OfflineOrder
   If true → OnlineOrder

---

# 🎯 @Value Annotation

Used to inject values from:

| Source               | Example            |
| -------------------- | ------------------ |
| Properties file      | `${isOnlineOrder}` |
| Environment variable | `${JAVA_HOME}`     |
| Literal              | `@Value("true")`   |

---

# ⚖️ Comparison

| Method                        | Dynamic Based On             | Use Case                      |
| ----------------------------- | ---------------------------- | ----------------------------- |
| `@Qualifier` (multiple beans) | Client input (request param) | Runtime switching             |
| `@Bean + @Value`              | Config file                  | Environment-based behavior    |
| `@Primary`                    | Default bean                 | When 1 bean should be default |

---

# 🏁 Final Understanding

Unsatisfied dependency happens when:

```
1 Interface → Multiple Beans
```

You can resolve dynamically using:

1️⃣ **Multiple qualifiers + runtime logic**

2️⃣ **Configuration-based bean creation using @Value**

Both maintain dependency inversion if used properly.

---

Agar interview me pucha:

> "How to dynamically choose implementation in Spring?"

Answer:

> Use multiple beans with qualifiers and select at runtime OR define conditional bean creation using @Value / @Conditional.

---
