
---

# 🎯 Spring Boot `@Profile`
---

### ❓ 1. What is a Profile in Spring Boot?

**Answer:**
A Profile in Spring Boot represents an **environment-specific configuration**. It allows the same codebase to run in different environments like **dev, qa, prod**, each having different configuration values such as database credentials, URLs, timeouts, etc.

---

### ❓ 2. Why do we need Profiles?

**Answer:**
Because configuration changes across environments while code remains the same. Profiles help in:

* Separating environment configs
* Avoiding hardcoded values
* Maintaining clean deployment strategy

---

### ❓ 3. How do you create environment-specific configuration files?

**Answer:**

```
application.properties        → Default
application-dev.properties
application-qa.properties
application-prod.properties
```

Spring automatically loads the profile-specific file when a profile is active.

---

### ❓ 4. How do you activate a profile in Spring Boot?

**Answer:**

**Method 1 — application.properties**

```properties
spring.profiles.active=qa
```

**Method 2 — Command Line**

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=prod
```

**Method 3 — Maven Profile**

```bash
mvn spring-boot:run -Pproduction
```

---

### ❓ 5. What is the priority when multiple property files exist?

**Answer:**
Profile-specific properties override default properties.

```
application-qa.properties  >  application.properties
```

---

### ❓ 6. What does `@Profile` annotation do?

**Answer:**
`@Profile` tells Spring to **create a bean only when a specific profile is active**.

---

### ❓ 7. Example of `@Profile`

```java
@Component
@Profile("prod")
public class MySQLConnection { }
```

Bean is created only if:

```
spring.profiles.active=prod
```

---

### ❓ 8. What happens if profile does not match?

**Answer:**
The bean is **not created** and not added to the Spring context.

---

### ❓ 9. Can multiple profiles be active?

**Answer:** Yes.

```properties
spring.profiles.active=prod,qa
```

Beans annotated with either `@Profile("prod")` or `@Profile("qa")` will be created.

---

### ❓ 10. If multiple profiles are active, which properties file is used?

**Answer:**
All profiles are active, but **last profile in the list gets higher property priority**.

---

### ❓ 11. Where can `@Profile` be used?

**Answer:**

* On `@Component` classes
* On `@Configuration` classes
* On `@Bean` methods

---

### ❓ 12. What is the difference between `@Profile` and `@ConditionalOnProperty`?

| Feature       | @Profile                 | @ConditionalOnProperty                |
| ------------- | ------------------------ | ------------------------------------- |
| Purpose       | Environment-based beans  | Feature toggle / property-based beans |
| Used for      | dev, qa, prod separation | Enabling/disabling functionality      |
| Best practice | Environment configs      | Application logic control             |

---

### ❓ 13. Can `@Profile` be used to create beans for different applications?

**Answer:**
Technically yes, but **not recommended**.
Profiles are meant for **environment separation**, not application-based logic.
Use `@ConditionalOnProperty` for app-specific beans.

---

### ❓ 14. What happens if no profile is set?

**Answer:**
Spring uses **application.properties** as the default configuration.

---

### ❓ 15. What is the internal working of `@Profile`?

**Answer:**
During context initialization, Spring checks:

```
Is active profile == profile defined in @Profile?
```

If match → bean created
Else → bean skipped

---

### ❓ 16. Real-world use case of `@Profile`

**Answer:**

* Dev → H2 Database
* Prod → MySQL/PostgreSQL

```java
@Configuration
@Profile("dev")
class H2Config { }

@Configuration
@Profile("prod")
class MySQLConfig { }
```

---

### ❓ 17. Can `@Profile` be combined with other annotations?

**Answer:** Yes.

```java
@Bean
@Profile("prod")
public DataSource dataSource() { }
```

---

### ❓ 18. Is `@Profile` checked at runtime or startup?

**Answer:**
At **application startup** during bean creation.

---

### ❓ 19. What problem does `@Profile` solve?

**Answer:**
Prevents loading unnecessary beans for a given environment, improving:

* Performance
* Maintainability
* Configuration clarity

---

### ❓ 20. Interview One-Liner Definition

> **`@Profile` is a Spring annotation used to conditionally load beans based on the active runtime environment profile such as dev, qa, or prod.**

---

