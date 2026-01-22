# Spring Boot Bean Scopes

---

## 1️⃣ What is a Bean Scope in Spring?

**Answer:**
Bean scope defines **the lifecycle and visibility of a bean**, i.e., **how many instances** of a bean Spring creates and **when** they are created and destroyed.

---

## 2️⃣ How many types of bean scopes are available in Spring?

**Answer:**
Spring supports **five main scopes**:

1. Singleton
2. Prototype
3. Request
4. Session
5. Application

---

## 3️⃣ What is the default scope of a Spring bean?

**Answer:**
The default scope is **Singleton**.

If no `@Scope` annotation is specified, Spring creates **one instance per IOC container**.

---

## 4️⃣ What does Singleton scope mean?

**Answer:**
Singleton means:

* Only **one object per IOC container**
* Same instance shared across the application
* Bean is **eagerly initialized** by default

---

## 5️⃣ Is Singleton the same as Java Singleton pattern?

**Answer:**
❌ No.

* Java Singleton → one object per JVM
* Spring Singleton → one object per **IOC container**

Multiple IOC containers can still create multiple instances.

---

## 6️⃣ Are Singleton beans thread-safe?

**Answer:**
❌ Not automatically.

Spring does **not** provide thread safety.
Thread safety depends on **how you write the code**.

Stateless beans are generally safe; stateful beans need synchronization.

---

## 7️⃣ What is Prototype scope?

**Answer:**
Prototype scope means:

* A **new object is created every time** the bean is requested
* Bean is **lazily initialized**
* Spring does not manage full lifecycle after creation

---

## 8️⃣ When is a Prototype bean created?

**Answer:**
A Prototype bean is created:

* When it is **autowired**
* When `getBean()` is called
* Not during application startup

---

## 9️⃣ What happens when a Singleton bean depends on a Prototype bean?

**Answer:**
Only **one Prototype instance** is created and injected into the Singleton.

To get a new instance every time, we must use:

* `ObjectFactory`
* `Provider`
* Lookup method injection

---

## 🔟 What is Request scope?

**Answer:**
Request scope means:

* One bean instance per **HTTP request**
* Same instance reused within the same request
* New instance for a new request

---

## 11️⃣ When are Request scoped beans created?

**Answer:**
Request scoped beans are:

* **Lazily initialized**
* Created only when an HTTP request arrives

---

## 12️⃣ Can we use Request scope in a non-web application?

**Answer:**
❌ No.

Request scope is only available in **web-aware Spring applications**.

---

## 13️⃣ What problem occurs when Singleton depends on Request scope?

**Answer:**
Application fails at startup because:

* Singleton beans are eagerly initialized
* No active HTTP request exists during startup

Error: **BeanCreationException**

---

## 14️⃣ How do you resolve Singleton → Request scope dependency?

**Answer:**
By using **Scoped Proxy**:

```java
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
```

This creates a **proxy object** at startup and the real bean during HTTP request.

---

## 15️⃣ What is Scoped Proxy?

**Answer:**
Scoped Proxy is a **dummy placeholder object** injected during initialization.

Actual bean is created:

* When the scope becomes active (HTTP request/session)

---

## 16️⃣ What is Session scope?

**Answer:**
Session scope means:

* One bean instance per **HTTP session**
* Same object reused across multiple requests
* New instance created after session expiration or logout

---

## 17️⃣ Difference between Request and Session scope?

**Answer:**

| Feature                | Request          | Session           |
| ---------------------- | ---------------- | ----------------- |
| Object lifetime        | One HTTP request | One HTTP session  |
| Reused across requests | ❌ No             | ✅ Yes             |
| Use case               | Request data     | User session data |

---

## 18️⃣ When is a Session scoped bean destroyed?

**Answer:**

* When session expires
* When session is invalidated manually

---

## 19️⃣ What is Application scope?

**Answer:**
Application scope means:

* One bean shared across **multiple IOC containers**
* Very rarely used in real projects

---

## 20️⃣ Difference between Singleton and Application scope?

**Answer:**

| Scope       | Visibility              |
| ----------- | ----------------------- |
| Singleton   | One IOC container       |
| Application | Multiple IOC containers |

---

## 21️⃣ Which scopes are eagerly initialized?

**Answer:**

* Singleton (default)

All other scopes are **lazy**.

---

## 22️⃣ Which scopes are web-aware?

**Answer:**

* Request
* Session
* Application

---

## 23️⃣ Can Prototype beans have @PreDestroy?

**Answer:**
❌ No.

Spring does not manage destruction of Prototype beans.

---

## 24️⃣ Real-world use cases of each scope

**Answer:**

* Singleton → Services, Repositories
* Prototype → Temporary objects
* Request → Request metadata
* Session → Logged-in user info
* Application → Rare global state

---

## 25️⃣ Most Common Interview Trap Question

**Q:** Singleton is thread-safe, right?

**Answer:** ❌ No.
Thread safety depends on implementation, not scope.

---

## ✅ Final Interview Tip

Most interview questions revolve around:

* Singleton vs Prototype
* Request scope + Proxy
* Lifecycle understanding

If you understand **bean lifecycle + scope interaction**, you can answer any question.

---
