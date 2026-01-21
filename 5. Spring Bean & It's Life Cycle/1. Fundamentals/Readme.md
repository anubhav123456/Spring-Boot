# Spring Boot Beans & IoC

---

## 1. What is a Bean?

**Definition:**
A **Bean** is a **Java object managed by the Spring IoC (Inversion of Control) container**.

* It is no different from a normal Java object
* The only difference is **Spring creates it, manages it, and controls its lifecycle**

---

## 2. What is IoC Container?

* IoC container is responsible for:

  * Creating beans
  * Initializing beans
  * Injecting dependencies
  * Managing the complete lifecycle of beans

In Spring Boot, the **ApplicationContext** is the IoC container implementation.

---

## 3. Ways to Create a Bean

There are **two main ways** to create a bean:

1. `@Component`
2. `@Bean`

---

## 4. Creating Bean using `@Component`

### Example 1: Default Constructor (Works Fine)

```java
@Component
public class User {

    String username;
    String email;

    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
```

### Explanation

* `@Component` tells Spring:

  > Create this object and manage its lifecycle
* Spring uses **auto-configuration**
* Auto-configuration assumes:

  * A **default constructor is available**

This approach follows **Convention over Configuration**.

---

## 5. Problem with `@Component` when Default Constructor is Missing

### Example 2: Parameterized Constructor (Fails)

```java
@Component
public class User {

    String username;
    String email;

    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }
}
```

### Why it fails?

* Java does **not** generate a default constructor
* Spring tries to call a default constructor
* Spring doesn’t know what values to pass
* **Application fails at startup**

---

## 6. Solution: Using `@Bean`

When **external configuration is required**, use `@Bean`.

### User Class (No `@Component`)

```java
public class User {

    String username;
    String email;

    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }
}
```

### Configuration Class

```java
@Configuration
public class AppConfig {

    @Bean
    public User createUserBean() {
        return new User("defaultusername", "defaultemail");
    }
}
```

### Key Points

* `@Configuration` tells Spring this class contains bean definitions
* `@Bean` provides **explicit instructions** on how to create an object
* Spring **will not use auto-configuration** here

---

## 7. Priority: `@Bean` vs `@Component`

If both are present:

* **`@Bean` takes priority over `@Component`**
* External configuration always wins over auto-configuration

---

## 8. Multiple Beans of Same Type

### Example

```java
@Configuration
public class AppConfig {

    @Bean
    public User createUserBean() {
        return new User("defaultusername", "defaultemail");
    }

    @Bean
    public User createAnotherUserBean() {
        return new User("anotherUsername", "anotheremail");
    }
}
```

### Result

* Spring creates **two separate User beans**
* Both are managed by IoC
* To choose one, use:

  * Bean name
  * `@Qualifier`

---

## 9. How Does Spring Find Beans?

### Method 1: `@ComponentScan`

```java
@SpringBootApplication
@ComponentScan(basePackages = "com.conceptandcoding.learningspringboot")
public class SpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }
}
```

Spring scans:

* Given base package
* All sub-packages
* Finds:

  * `@Component`
  * `@Service`
  * `@Repository`
  * `@Controller`

> All these annotations are internally `@Component`

---

### Method 2: `@Configuration`

```java
@Configuration
public class AppConfig {

    @Bean
    public User createUserBean() {
        return new User("defaultusername", "defaultemail");
    }
}
```

* `@Configuration` itself is also a `@Component`
* Spring scans it and loads all `@Bean` methods

---

## 10. When Are Beans Created?

### Two Types of Initialization

1. **Eager Initialization**
2. **Lazy Initialization**

---

### Eager Initialization

* Beans are created **at application startup**
* Default behavior for **Singleton scope**

---

### Lazy Initialization

* Bean is created **only when needed**

```java
@Lazy
@Component
public class Order {

    public Order() {
        System.out.println("initializing Order");
    }
}
```

---

## 11. Bean Lifecycle Overview

### Lifecycle Steps

1. Application starts
2. IoC container initializes
3. Beans are scanned
4. Bean objects are created
5. Dependencies are injected
6. `@PostConstruct` is called
7. Bean is ready to use
8. `@PreDestroy` is called
9. Bean is destroyed

---

## 12. Example: Bean Construction (Eager)

```java
@Component
public class User {

    public User() {
        System.out.println("initializing user");
    }
}
```

Output at startup:

```
initializing user
```

---

## 13. Dependency Injection Example (Lazy + Autowire)

### User Class

```java
@Component
public class User {

    @Autowired
    private Order order;

    public User() {
        System.out.println("initializing user");
    }
}
```

### Order Class

```java
@Lazy
@Component
public class Order {

    public Order() {
        System.out.println("initializing order");
    }
}
```

### Behavior

* User is created at startup
* Order is created **only when required**
* Dependency injection forces Order creation

---

## 14. `@PostConstruct`

```java
@Component
public class User {

    @PostConstruct
    public void init() {
        System.out.println("Bean constructed & dependencies injected");
    }
}
```

### Called After:

* Constructor execution
* Dependency injection

---

## 15. `@PreDestroy`

```java
@Component
public class User {

    @PreDestroy
    public void destroy() {
        System.out.println("Bean is about to be destroyed");
    }
}
```

Used to:

* Release DB connections
* Close resources

---

## 16. Full Lifecycle Demo (Manual Context Close)

```java
@SpringBootApplication
public class SpringbootApplication {

    public static void main(String[] args) {
        ConfigurableApplicationContext context =
                SpringApplication.run(SpringbootApplication.class, args);

        context.close();
    }
}
```

### Execution Order

1. Constructor
2. `@PostConstruct`
3. Application runs
4. Context closes
5. `@PreDestroy`

---


