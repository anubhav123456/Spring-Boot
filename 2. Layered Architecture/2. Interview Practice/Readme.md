
---

# 🔹 Layered Architecture – Interview Questions & Answers

---

## 1. What is layered architecture in Spring Boot?

**Answer:**
Layered architecture is a design pattern where an application is divided into logical layers such as **Controller, Service, and Repository**, each having a **single responsibility**.
It improves **maintainability, scalability, testability, and separation of concerns**.

---

## 2. Why is layered architecture widely used in MNCs?

**Answer:**
Because it:

* Enforces clean separation of responsibilities
* Makes large codebases easier to maintain
* Allows parallel development
* Simplifies testing and debugging
* Supports long-term scalability

---

## 3. What are the main layers in Spring Boot?

**Answer:**

1. **Controller Layer** – Handles HTTP requests & responses
2. **Service Layer** – Contains business logic
3. **Repository Layer** – Interacts with the database

Supporting packages:

* DTO
* Entity
* Utility
* Configuration

---

## 4. What is the responsibility of the Controller layer?

**Answer:**

* Exposes REST APIs
* Accepts client input
* Maps request → DTO
* Returns response to client

❌ Should not contain business logic
❌ Should not access database

---

## 5. What annotations are used in Controller layer?

**Answer:**

* `@RestController`
* `@Controller`
* `@RequestMapping`
* `@GetMapping`, `@PostMapping`, etc.

---

## 6. What is the Service layer responsible for?

**Answer:**

* Contains **business logic**
* Orchestrates calls between repositories
* Performs validations
* Handles entity ↔ DTO mapping

---

## 7. Why should business logic NOT be in Controller?

**Answer:**

* Violates separation of concerns
* Makes code difficult to test
* Leads to fat controllers
* Reduces reusability

👉 Controllers should remain **thin**

---

## 8. What is Repository layer?

**Answer:**

* Handles database operations
* Executes queries
* Fetches and persists entities

It acts as a **data access abstraction**.

---

## 9. Why should only Repository layer access DB?

**Answer:**

* Keeps DB logic centralized
* Prevents duplication
* Makes switching DB easier
* Improves maintainability

---

## 10. Can Service layer directly access DB?

**Answer:**
Technically yes, but **it’s a bad practice**.

Correct approach:

```
Service → Repository → Database
```

---

## 11. What is DTO?

**Answer:**
DTO (Data Transfer Object) is used to transfer data between layers without exposing internal implementation or DB schema.

---

## 12. Why do we need DTO when we already have Entity?

**Answer:**

| Entity                | DTO                     |
| --------------------- | ----------------------- |
| Represents DB table   | Represents API contract |
| Used internally       | Exposed externally      |
| Tightly coupled to DB | Flexible & safe         |

👉 **Never expose Entity directly to client**

---

## 13. Difference between Request DTO and Response DTO?

**Answer:**

* **Request DTO** → Accepts client input
* **Response DTO** → Sends filtered, formatted data to client

---

## 14. Where should DTO ↔ Entity mapping be done?

**Answer:**
👉 **Service layer**

Controller → Request DTO
Service → Entity ↔ Response DTO

---

## 15. What happens if DB column name changes?

**Answer:**

* Entity changes
* Mapping changes in Service
* **Client remains unaffected**

This is why DTOs are important.

---

## 16. What is an Entity?

**Answer:**
Entity is a POJO class that represents a **database table** where:

* Fields = columns
* Each row = object instance

---

## 17. Why are entities mostly used in Repository?

**Answer:**
Because repositories deal directly with database records, and entities map directly to DB tables.

---

## 18. What is the role of Utility package?

**Answer:**
Contains **common reusable helper methods** used across multiple classes.

Examples:

* Date utilities
* String utilities
* Validation helpers

---

## 19. Why avoid hard-coded values in code?

**Answer:**

* Requires code changes for simple updates
* Risky in production
* Not environment-friendly

---

## 20. Where should configuration be stored?

**Answer:**

* `application.properties`
* `application.yml`

Example:

```properties
timeout.value=10
```

---

## 21. How does layered architecture improve testability?

**Answer:**

* Each layer can be tested independently
* Mock lower layers easily
* Unit testing becomes simpler

---

## 22. What is tight coupling and how does layered architecture help?

**Answer:**
Tight coupling means changes in one layer affect others.

Layered architecture reduces coupling by:

* Using interfaces
* DTO abstraction
* Clear boundaries

---

## 23. What happens if you expose Entity directly in API?

**Answer:**
❌ DB structure exposed
❌ Security risks
❌ Breaking changes when DB schema changes

---

## 24. How does Spring manage dependencies between layers?

**Answer:**
Using **Dependency Injection (DI)** via:

* `@Autowired`
* Constructor injection

---

## 25. What design principle does layered architecture follow?

**Answer:**

* **Single Responsibility Principle (SRP)**
* **Separation of Concerns**
* **Dependency Inversion Principle**

---

## 26. Can layered architecture be used in microservices?

**Answer:**
Yes.
Each microservice internally follows layered architecture.

---

## 27. What are common mistakes in layered architecture?

**Answer:**
❌ Fat controllers
❌ Business logic in repositories
❌ Exposing entities directly
❌ Skipping DTOs
❌ Hard-coded values

---

## 28. How would you explain layered architecture in one line?

**Answer:**
“Layered architecture separates request handling, business logic, and data access to build clean, maintainable, and scalable applications.”

---

## 29. Follow-up Question Interviewers Ask

> **“Explain request flow in layered architecture”**

**Answer:**

```
Client
 → Controller
 → Service
 → Repository
 → Database
 → Repository
 → Service
 → Controller
 → Client
```

---

## 30. Bonus (Senior Level)

**Q:** How do you avoid boilerplate mapping code?
**A:** Use **MapStruct** or **ModelMapper**

---
