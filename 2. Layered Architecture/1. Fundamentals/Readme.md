
---

# Spring Boot Layered Architecture – Complete Notes

## 1. Why Layered Architecture?

* **Most commonly used architecture** in Spring Boot
* Followed by **big MNCs & enterprise applications**
* Provides:

  * Clear separation of concerns
  * Maintainability
  * Scalability
  * Testability
  * Easy future changes

---

## 2. What is Layered Architecture?

A typical Spring Boot application is divided into **layers**, where **each layer has a single responsibility**.

### Common Layers:

1. **Controller Layer**
2. **Service Layer**
3. **Repository Layer**

### Supporting Packages (not layers):

* DTO
* Entity
* Utility
* Configuration

---

## 3. Overall Request Flow

```
Client
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
```

Response flows back in reverse order.

---

## 4. Controller Layer

### Purpose

* Entry point of the application
* Hosts REST APIs
* Handles:

  * Request mapping
  * Request → DTO mapping
  * Response returning

### Annotations Used

* `@RestController`
* `@Controller`
* `@RequestMapping`
* `@GetMapping`, `@PostMapping`

### Responsibilities

✅ Accept client input
❌ No business logic
❌ No DB access

---

### Controller Code Example

```java
@RestController
@RequestMapping("/payments")
public class PaymentController {

    @Autowired
    PaymentService paymentService;

    @GetMapping("/{id}")
    public ResponseEntity<PaymentResponse> getPaymentById(@PathVariable Long id) {

        // map incoming data to internal request DTO
        PaymentRequest internalRequestObj = new PaymentRequest();
        internalRequestObj.setPaymentId(id);

        // pass request DTO to service layer
        PaymentResponse payment = paymentService.getPaymentDetailsById(internalRequestObj);

        // return response DTO
        return ResponseEntity.ok(payment);
    }
}
```

---

## 5. DTO (Data Transfer Object)

### What is DTO?

* Used to **transfer data between layers**
* Shields internal layers from:

  * API schema changes
  * DB column exposure

### Types

1. **Request DTO**
2. **Response DTO**

---

### Why Use DTO?

❌ Without DTO

* Schema changes impact multiple layers

✅ With DTO

* Only controller mapping changes
* Service & repository remain untouched

---

### Request DTO Example

```java
public class PaymentRequest {
    private long paymentId;

    public long getPaymentId() {
        return paymentId;
    }

    public void setPaymentId(long paymentId) {
        this.paymentId = paymentId;
    }
}
```

---

### Response DTO Example

```java
public class PaymentResponse {
    private Long paymentId;
    private double amount;
    private String currency;

    public Long getPaymentId() {
        return paymentId;
    }

    public void setPaymentId(Long paymentId) {
        this.paymentId = paymentId;
    }

    public double getAmount() {
        return amount;
    }

    public void setAmount(double amount) {
        this.amount = amount;
    }

    public String getCurrency() {
        return currency;
    }

    public void setCurrency(String currency) {
        this.currency = currency;
    }
}
```

---

## 6. Service Layer

### Purpose

* Contains **business logic**
* Acts as a **bridge** between Controller & Repository

### Annotations Used

* `@Service`

### Responsibilities

✅ Business rules
✅ Data transformation
❌ No direct DB access

---

### Service Code Example

```java
@Service
public class PaymentService {

    @Autowired
    PaymentRepository paymentRepository;

    public PaymentResponse getPaymentDetailsById(PaymentRequest internalRequestObj) {

        PaymentEntity paymentModel =
                paymentRepository.getPaymentById(internalRequestObj);

        // map entity to response DTO
        PaymentResponse paymentResponse =
                mapModelToResponseDTO(paymentModel);

        return paymentResponse;
    }

    private PaymentResponse mapModelToResponseDTO(PaymentEntity paymentEntity) {

        PaymentResponse response = new PaymentResponse();
        response.setPaymentId(paymentEntity.getId());
        response.setAmount(paymentEntity.getPaymentAmount());
        response.setCurrency(paymentEntity.getPaymentCurrency());
        return response;
    }
}
```

---

## 7. Repository Layer

### Purpose

* Interacts with the **database**
* Executes queries

### Annotations Used

* `@Repository`

### Responsibilities

✅ DB access
❌ No business logic

---

### Repository Code Example

```java
@Repository
public class PaymentRepository {

    public PaymentEntity getPaymentById(PaymentRequest request) {
        PaymentEntity paymentModel = executeQuery(request);
        return paymentModel;
    }

    private PaymentEntity executeQuery(PaymentRequest request) {
        // Simulating DB fetch
        PaymentEntity payment = new PaymentEntity();
        payment.setId(request.getPaymentId());
        payment.setPaymentCurrency("INR");
        payment.setPaymentAmount(100.00);
        return payment;
    }
}
```

---

## 8. Entity Layer

### What is an Entity?

* Represents **DB table**
* Fields = Column names
* Used mainly by repositories

### Annotations Used

* `@Entity` (conceptually)

---

### Entity Code Example

```java
public class PaymentEntity {

    private Long id;
    private double paymentAmount;
    private String paymentCurrency;
    private String userEmail;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public double getPaymentAmount() {
        return paymentAmount;
    }

    public void setPaymentAmount(double paymentAmount) {
        this.paymentAmount = paymentAmount;
    }

    public String getPaymentCurrency() {
        return paymentCurrency;
    }

    public void setPaymentCurrency(String paymentCurrency) {
        this.paymentCurrency = paymentCurrency;
    }

    public String getUserEmail() {
        return userEmail;
    }

    public void setUserEmail(String userEmail) {
        this.userEmail = userEmail;
    }
}
```

---

## 9. Utility Package

### Purpose

* Holds **common helper methods**
* Avoids code duplication

### Examples

* Date formatting
* Validation logic
* Common calculations

---

## 10. Configuration

### Why Configuration?

* Avoid hard-coded values
* Allow changes without code modification

### Stored In

* `application.properties`
* `application.yml`

### Example Concept

```properties
timeout.value=10
```

Used in code via configuration instead of hardcoding.

---

## 11. Key Takeaways (Interview Ready)

✔ Controller → only request/response handling
✔ Service → business logic + mappings
✔ Repository → DB interaction only
✔ DTO → protects layers from schema changes
✔ Entity → DB table representation
✔ Clean separation = enterprise-grade code

---
