# Spring Boot Beans & IoC

---

## 1. What is a Spring Bean?

**Answer:**
A Spring Bean is a **Java object that is created, managed, and destroyed by the Spring IoC container**. The lifecycle of the bean is fully controlled by Spring.

---

## 2. What is IoC (Inversion of Control)?

**Answer:**
IoC is a design principle where **the control of object creation and dependency management is transferred from the programmer to the Spring container**.

Instead of creating objects using `new`, Spring creates and injects them.

---

## 3. What is the IoC container in Spring Boot?

**Answer:**
The IoC container in Spring Boot is **ApplicationContext**. It is responsible for:

* Creating beans
* Injecting dependencies
* Managing bean lifecycle

---

## 4. How many ways can we create a bean in Spring Boot?

**Answer:**
There are **two primary ways**:

1. Using `@Component`
2. Using `@Bean`

---

## 5. What is `@Component`?

**Answer:**
`@Component` tells Spring to **automatically create and manage an object of this class** using auto-configuration.

It follows **Convention over Configuration**.

---

## 6. What annotations are internally `@Component`?

**Answer:**

* `@Controller`
* `@Service`
* `@Repository`
* `@Configuration`

All of them instruct Spring to create a bean.

---

## 7. Why does `@Component` fail when there is no default constructor?

**Answer:**
Spring uses auto-configuration and tries to call the **default constructor**.
If only a parameterized constructor exists, Spring doesn’t know what values to pass, causing application startup failure.

---

## 8. When should we use `@Bean` instead of `@Component`?

**Answer:**
We should use `@Bean` when:

* No default constructor is available
* External configuration is required
* We want full control over object creation

---

## 9. What is `@Configuration`?

**Answer:**
`@Configuration` is a class-level annotation that tells Spring this class contains **bean definition methods**.

It is internally a `@Component`.

---

## 10. Difference between `@Component` and `@Bean`

**Answer:**

| Feature             | @Component | @Bean    |
| ------------------- | ---------- | -------- |
| Configuration type  | Auto       | Explicit |
| Used on             | Class      | Method   |
| Constructor control | No         | Yes      |
| Priority            | Lower      | Higher   |

---

## 11. If both `@Component` and `@Bean` exist for the same class, which one takes priority?

**Answer:**
`@Bean` takes priority because **external configuration always overrides auto-configuration**.

---

## 12. What happens if multiple beans of the same type exist?

**Answer:**
Spring creates **multiple objects**, one for each `@Bean` or `@Component`.

To resolve ambiguity, we use:

* `@Qualifier`
* Bean name

---

## 13. How does Spring Boot find beans?

**Answer:**
Spring Boot finds beans using:

1. `@ComponentScan`
2. `@Configuration`

---

## 14. What is `@ComponentScan`?

**Answer:**
`@ComponentScan` tells Spring **which package to scan** for components and its sub-packages.

If not specified, Spring Boot scans from the package of the main application class.

---

## 15. Does `@SpringBootApplication` include `@ComponentScan`?

**Answer:**
Yes. `@SpringBootApplication` internally includes:

* `@ComponentScan`
* `@EnableAutoConfiguration`
* `@Configuration`

---

## 16. When are beans created in Spring Boot?

**Answer:**
Beans are created:

* At application startup (Eager)
* When required (Lazy)

---

## 17. What is Eager Initialization?

**Answer:**
Eager initialization means **beans are created during application startup**.

Singleton beans are eagerly initialized by default.

---

## 18. What is Lazy Initialization?

**Answer:**
Lazy initialization means **beans are created only when they are first requested**.

It can be enabled using `@Lazy`.

---

## 19. Are singleton beans always eagerly initialized?

**Answer:**
Yes, by default.
But eager behavior can be overridden using `@Lazy`.

---

## 20. What is the default scope of a Spring bean?

**Answer:**
The default scope is **Singleton**.

---

## 21. Explain the Spring Bean Lifecycle.

**Answer:**

1. Application starts
2. IoC container initializes
3. Bean scanning
4. Bean instantiation
5. Dependency injection
6. `@PostConstruct`
7. Bean ready to use
8. `@PreDestroy`
9. Bean destroyed

---

## 22. What is dependency injection?

**Answer:**
Dependency Injection is a mechanism where **Spring injects dependent objects automatically** instead of the class creating them.

---

## 23. What does `@Autowired` do?

**Answer:**
`@Autowired` tells Spring to **inject the required dependency from the IoC container**.

Spring first checks if the bean exists:

* If yes → inject
* If no → create and inject

---

## 24. What is `@PostConstruct`?

**Answer:**
`@PostConstruct` is used to execute logic **after bean creation and dependency injection**.

Used for:

* Initialization logic
* Pre-processing

---

## 25. What is `@PreDestroy`?

**Answer:**
`@PreDestroy` is executed **just before the bean is destroyed**.

Used for:

* Closing resources
* Releasing DB connections

---

## 26. When is `@PreDestroy` called?

**Answer:**
When:

* Application context is closed
* Application shuts down gracefully

---

## 27. What happens when ApplicationContext is closed?

**Answer:**

* All managed beans are destroyed
* `@PreDestroy` methods are invoked
* Resources are released

---

## 28. Can lazy beans be initialized eagerly?

**Answer:**
Yes. If a lazy bean is required by an eagerly initialized bean, Spring will initialize it.

---

## 29. Why is Constructor Injection preferred in industry?

**Answer:**
Because it:

* Makes dependencies mandatory
* Improves immutability
* Is easier to test
* Avoids NullPointerExceptions

---

## 30. Key Interview Takeaways

* `@Bean` > `@Component`
* Singleton → eager by default
* `@Lazy` overrides eager behavior
* `@Configuration` is also a component
* Lifecycle hooks are frequently asked

---
