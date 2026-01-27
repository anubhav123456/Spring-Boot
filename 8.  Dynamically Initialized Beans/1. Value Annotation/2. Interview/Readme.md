# Dynamically Initialized Beans | Value Annotation

---

# 🧩 BASIC LEVEL

### **1. What is Unsatisfied Dependency in Spring?**

**Answer:**
Unsatisfied dependency occurs when Spring cannot decide which bean to inject for a dependency.
It usually happens when **one interface has multiple implementations** and no qualifier or primary bean is specified.

---

### **2. Why does this code fail?**

```java
@Autowired
Order order;
```

**Answer:**
Because Spring finds:

```
Order → OnlineOrder
Order → OfflineOrder
```

Spring doesn't know which bean to inject → **NoUniqueBeanDefinitionException**.

---

### **3. How does `@Qualifier` solve this problem?**

**Answer:**
`@Qualifier` tells Spring **exactly which bean** to inject by name.

```java
@Autowired
@Qualifier("onlineOrderObject")
Order order;
```

---

### **4. Difference between `@Primary` and `@Qualifier`**

| @Primary            | @Qualifier              |
| ------------------- | ----------------------- |
| Marks default bean  | Selects specific bean   |
| Used at class level | Used at injection point |
| Only one allowed    | Many allowed            |

---

### **5. Does using `@Qualifier` break Dependency Inversion Principle?**

**Answer:**
No. We still depend on the interface, not implementation.
But it **reduces flexibility** because bean choice becomes static.

---

# ⚙️ INTERMEDIATE LEVEL

### **6. How can we make bean selection dynamic using Qualifiers?**

**Answer:**
Inject both beans and decide at runtime.

```java
@Autowired @Qualifier("onlineOrderObject")
Order online;

@Autowired @Qualifier("offlineOrderObject")
Order offline;

if(isOnline) online.createOrder();
else offline.createOrder();
```

---

### **7. Are both beans created in the above approach?**

**Answer:**
Yes. Both beans are **singleton** and created at startup.
We only decide **which one to use**, not which one to create.

---

### **8. How can we create only one bean dynamically?**

**Answer:**
Use `@Bean` + `@Value` in configuration class.

```java
@Bean
public Order order(@Value("${isOnlineOrder}") boolean flag) {
    return flag ? new OnlineOrder() : new OfflineOrder();
}
```

---

### **9. What is `@Value` annotation used for?**

**Answer:**
Used to inject values from:

* application.properties
* environment variables
* literals

---

### **10. When is configuration-based bean creation preferred?**

**Answer:**
When behavior depends on **environment/config**, not user input.

Example:

* Dev → OfflineOrder
* Prod → OnlineOrder

---

# 🚀 ADVANCED LEVEL

### **11. What is the difference between runtime switching vs configuration switching?**

| Runtime                | Config                    |
| ---------------------- | ------------------------- |
| Based on request input | Based on property file    |
| Both beans created     | Only one bean created     |
| Used in business logic | Used in environment setup |

---

### **12. How does Spring resolve dependency when `@Bean` is used?**

**Answer:**
Spring sees `@Bean` method returning `Order`.
That method becomes the **bean factory** and is used to resolve injection.

---

### **13. What happens if property is missing for `@Value("${isOnlineOrder}")`?**

**Answer:**
Application fails to start with **IllegalArgumentException** unless default value is provided:

```java
@Value("${isOnlineOrder:false}")
```

---

### **14. Can we use `@Conditional` instead of `@Value`?**

**Answer:**
Yes. `@Conditional`, `@Profile`, and `@ConditionalOnProperty` are more scalable for environment-based bean loading.

---

### **15. Why is injecting multiple beans sometimes better than creating one dynamically?**

**Answer:**

| Multiple Beans           | Single Dynamic Bean           |
| ------------------------ | ----------------------------- |
| Faster runtime switching | Cleaner configuration         |
| Used when user decides   | Used when environment decides |

---

# 🧠 SCENARIO QUESTIONS

### **16. Interviewer:**

“You have Payment interface with Card, UPI, NetBanking. User chooses payment type in request. How will you design?”

**Answer:**
Use multiple beans + qualifiers OR Map injection:

```java
@Autowired
Map<String, Payment> payments;

payments.get(type).pay();
```

---

### **17. Interviewer:**

“How to load OnlineOrder only in Production?”

**Answer:**

```java
@Profile("prod")
@Component
class OnlineOrder implements Order {}
```

---

### **18. Interviewer:**

“What design principle is used here?”

**Answer:**

* Dependency Injection
* Inversion of Control
* Strategy Pattern

---

### **19. What pattern does dynamic implementation selection resemble?**

**Answer:**
**Strategy Pattern** — behavior changes at runtime.

---

### **20. When should you avoid `@Qualifier`?**

**Answer:**
When bean selection depends on:

* Environment → Use `@Profile`
* Property → Use `@ConditionalOnProperty`
* Complex logic → Use factory/service pattern

---

# 🏁 PERFECT INTERVIEW SUMMARY LINE

> “If multiple implementations exist, Spring throws NoUniqueBeanDefinitionException. We resolve using @Qualifier or @Primary. For dynamic selection, we inject multiple beans and choose at runtime, or create beans conditionally using @Value, @Profile, or @Conditional. This follows DI and resembles the Strategy Pattern.”

---

