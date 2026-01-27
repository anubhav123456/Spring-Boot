
---

# 🎯 `@ConditionalOnProperty` — Interview Questions & Answers

---

## 🟢 1. What is `@ConditionalOnProperty`?

**Answer:**

`@ConditionalOnProperty` is a Spring Boot annotation used to **conditionally create a bean** based on the value of a property defined in `application.properties` or `application.yml`.

If the property matches the specified condition, the bean is loaded; otherwise, it is not created.

---

## 🟢 2. Why do we need `@ConditionalOnProperty`?

**Answer:**

In large-scale applications:

* Many beans are created at startup (singleton, eager init)
* Some beans may not be needed in all environments

This annotation helps to:

* Avoid unnecessary beans in ApplicationContext
* Reduce memory usage
* Improve startup performance
* Enable feature toggling

---

## 🟢 3. How does `@ConditionalOnProperty` work internally?

**Answer:**

Spring Boot:

1. Reads property from `application.properties`
2. Builds key using `prefix + "." + value`
3. Compares the property value with `havingValue`
4. If matched → bean created
   If not matched → bean skipped
5. If property is missing → `matchIfMissing` decides behavior

---

## 🟢 4. What are the parameters of `@ConditionalOnProperty`?

**Answer:**

```java
@ConditionalOnProperty(
    prefix = "sqlconnection",
    value = "enabled",
    havingValue = "true",
    matchIfMissing = false
)
```

| Parameter        | Meaning                                         |
| ---------------- | ----------------------------------------------- |
| `prefix`         | First part of property key                      |
| `value`          | Second part of key                              |
| `havingValue`    | Bean loads only if property value equals this   |
| `matchIfMissing` | If property not present, should bean still load |

👉 Property key formed = `sqlconnection.enabled`

---

## 🟢 5. What happens if `matchIfMissing = true`?

**Answer:**

If the property is not defined in config:

* Bean will still be created
* Spring treats missing property as a match

---

## 🟢 6. Why do we use `@Autowired(required = false)` with it?

**Answer:**

Because the bean might not be created due to the condition.

If `required=true` (default), Spring will throw:

> `NoSuchBeanDefinitionException`

Setting `required=false` allows Spring to inject `null` if the bean doesn't exist.

---

## 🟢 7. What problem does this solve in microservices?

**Answer:**

Different services can use:

* Same shared library
* Different features enabled via config

So services can selectively enable beans without changing code.

---

## 🟢 8. Real-world use cases?

**Answer:**

1. Database migration (MySQL ↔ NoSQL)
2. Feature toggling
3. Dev vs Prod configuration
4. Optional integrations (Redis, Kafka, S3)
5. Multi-tenant system configurations

---

## 🟢 9. What happens at startup when condition fails?

**Answer:**

Spring **does not create the bean definition**.
The bean never enters the ApplicationContext.

---

## 🟢 10. Difference between `@ConditionalOnProperty` and `@Profile`?

| `@Profile`                       | `@ConditionalOnProperty` |
| -------------------------------- | ------------------------ |
| Based on environment (dev, prod) | Based on property value  |
| Coarse-grained control           | Fine-grained control     |
| Less flexible                    | Highly flexible          |

---

## 🟢 11. Can we use string values other than true/false?

**Answer:**

Yes. `havingValue` is a **string comparison**.

Example:

```properties
db.type=mysql
```

```java
@ConditionalOnProperty(prefix="db", value="type", havingValue="mysql")
```

---

## 🟢 12. What are the advantages?

**Answer:**

* Feature toggling
* Avoids unnecessary beans
* Saves memory
* Faster startup
* Environment-based behavior

---

## 🟢 13. What are the disadvantages?

**Answer:**

* Misconfiguration risk
* Hard to track bean loading
* Increased config management
* Can cause confusion if overused

---

## 🟢 14. What happens if property value doesn’t match `havingValue`?

**Answer:**

Condition fails → Bean is not created.

---

## 🟢 15. Can multiple beans use the same property?

**Answer:**

Yes, but not recommended.
It can create confusion and accidental bean loading.

---

## 🟢 16. Is this Spring or Spring Boot feature?

**Answer:**

`@ConditionalOnProperty` is a **Spring Boot** feature (not core Spring).

---

## 🟢 17. What type of beans benefit most from this?

**Answer:**

* Database connections
* Messaging systems (Kafka, RabbitMQ)
* Caching systems (Redis)
* External integrations

---

## 🟢 18. Interview Summary Answer

> "`@ConditionalOnProperty` allows us to conditionally create beans based on configuration properties, helping with feature toggling, environment-specific behavior, and reducing unnecessary bean initialization in large Spring Boot applications."

---
