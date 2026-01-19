
---

# 🔥 Scenario-Based Questions on Layered Architecture

---

## 1️⃣ Scenario: Business logic written in Controller

**Question:**
You join a project where controllers contain heavy business logic. What problems do you see and how will you fix it?

**Expected Answer:**
Problems:

* Violates separation of concerns
* Controllers become hard to test
* Business logic cannot be reused
* Difficult to maintain

Fix:

* Move all business logic to **Service layer**
* Keep controllers thin
* Controller should only:

  * Accept request
  * Call service
  * Return response

---

## 2️⃣ Scenario: Service directly calling DB

**Question:**
You see JDBC queries written inside the Service layer. Is it acceptable?

**Expected Answer:**
Technically possible, but **not recommended**.

Why?

* Mixes business logic and data access
* Hard to replace DB later
* Breaks layered architecture

Correct approach:

```
Service → Repository → DB
```

---

## 3️⃣ Scenario: Entity returned directly in API response

**Question:**
The API is returning JPA entities directly to the client. What are the risks?

**Expected Answer:**
Risks:

* DB schema exposure
* Security issues (extra fields)
* Breaking API on schema change
* Lazy loading issues

Solution:

* Introduce **Response DTO**
* Map entity → DTO in Service layer

---

## 4️⃣ Scenario: DB column name change

**Question:**
DB column `payment_amt` is renamed to `amount`. How should your application handle this?

**Expected Answer:**

* Update Entity mapping
* Update Service mapping
* **No change in Controller or API contract**

Reason:

* DTO decouples DB schema from API

---

## 5️⃣ Scenario: API schema change from client

**Question:**
Client changes request field from `id` to `paymentId`. How do you minimize impact?

**Expected Answer:**

* Change mapping only in Controller
* Map new field to existing Request DTO
* Service & Repository remain unchanged

---

## 6️⃣ Scenario: Multiple Services need same logic

**Question:**
Three services use the same date-calculation logic. Where should it go?

**Expected Answer:**

* Create a **Utility / Helper class**
* Avoid duplication
* Keeps services clean

---

## 7️⃣ Scenario: Hard-coded values found in Service

**Question:**
You see hard-coded timeout values inside Service layer. What will you do?

**Expected Answer:**

* Move values to `application.properties`
* Inject using configuration
* Avoid code change for config updates

---

## 8️⃣ Scenario: One API needs data from multiple tables

**Question:**
Your API needs payment + user data. How do you design it?

**Expected Answer:**

* Service layer orchestrates:

  * Call PaymentRepository
  * Call UserRepository
* Combine data
* Return a **single Response DTO**

Controller should not do this aggregation.

---

## 9️⃣ Scenario: Controller calling Repository directly

**Question:**
Is it okay for Controller to call Repository directly?

**Expected Answer:**
❌ No.

Why:

* Skips business logic
* Harder to enforce validations
* Breaks layered design

Correct:

```
Controller → Service → Repository
```

---

## 🔟 Scenario: Validation logic location

**Question:**
Where should validation logic be placed?

**Expected Answer:**

* Basic request validation → Controller / DTO
* Business validation → Service
* DB constraints → Database

---

## 1️⃣1️⃣ Scenario: Reusable logic across microservices

**Question:**
Same logic needed across multiple microservices. How do you handle it?

**Expected Answer:**

* Extract logic into:

  * Common library (shared module)
  * Or Utility service
* Avoid copy-paste

---

## 1️⃣2️⃣ Scenario: Transaction management

**Question:**
Where should `@Transactional` be placed?

**Expected Answer:**
👉 **Service layer**

Why:

* Business operation boundary
* Multiple repository calls in one transaction

---

## 1️⃣3️⃣ Scenario: Logging placement

**Question:**
Where should logging be done?

**Expected Answer:**

* Controller → request/response logs
* Service → business flow logs
* Repository → DB query logs

---

## 1️⃣4️⃣ Scenario: Performance issue due to heavy mapping

**Question:**
Service layer has heavy DTO ↔ Entity mapping affecting performance. What do you do?

**Expected Answer:**

* Use **MapStruct**
* Reduce unnecessary fields
* Avoid nested object mapping
* Use projections if needed

---

## 1️⃣5️⃣ Scenario: Circular dependency between services

**Question:**
Two services depend on each other. How do you fix it?

**Expected Answer:**

* Extract common logic into third service
* Redesign responsibilities
* Avoid circular dependencies

---

## 1️⃣6️⃣ Scenario: API versioning

**Question:**
You need to support v1 and v2 of API. How layered architecture helps?

**Expected Answer:**

* Separate Controllers (v1, v2)
* Reuse Service & Repository
* Different DTOs per version

---

## 1️⃣7️⃣ Scenario: Unit testing layers

**Question:**
How would you unit test Service layer?

**Expected Answer:**

* Mock Repository
* Test business logic only
* No DB dependency

---

## 1️⃣8️⃣ Scenario: Switching DB (MySQL → MongoDB)

**Question:**
How layered architecture helps in DB migration?

**Expected Answer:**

* Only Repository changes
* Service & Controller unaffected
* Entities adjusted if needed

---

## 1️⃣9️⃣ Scenario: Fat Service class

**Question:**
Service class has 2000+ lines. What do you do?

**Expected Answer:**

* Break into smaller services
* Follow single responsibility
* Extract helper methods

---

## 2️⃣0️⃣ Final Interview Killer Answer

**Q:** Why layered architecture is preferred?

**Answer:**
“Layered architecture enforces separation of concerns, improves maintainability, enables independent testing, and protects the system from cascading changes.”

---

