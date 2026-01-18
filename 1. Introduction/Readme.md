

---

# 📘 Introduction to Spring Boot

## 1️⃣ Why Spring Boot Exists?

Spring Boot exists to **simplify Java web application development** by solving problems present in:

* Servlets
* Spring MVC (traditional Spring Framework)

To understand Spring Boot properly, we must go step-by-step:

> **Servlets → Spring MVC → Spring Boot**

---

## 2️⃣ Servlet & Servlet Container (Foundation)

### 🔹 What is a Servlet?

* A **Servlet** is a Java class that:

  * Handles client requests
  * Processes them
  * Returns responses

> One servlet can handle HTTP methods like:

* `doGet()`
* `doPost()`
* `doPut()`
* `doDelete()`

---

### 🔹 What is a Servlet Container?

* A **Servlet Container**:

  * Manages servlets
  * Controls servlet lifecycle
  * Routes requests to correct servlet

📌 Example: **Apache Tomcat**

---

## 3️⃣ How Servlet-Based Applications Worked

### 🔹 Example: Servlet Code

```java
@WebServlet("/demoservletone/*")
public class DemoServlet1 extends HttpServlet 
{

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
    {

        String requestPathInfo = request.getPathInfo();

        if(requestPathInfo.equals("/")) 
        {
            //do something
        }
        else if(requestPathInfo.equals("/firstendpoint")) 
        {
            //do something
        }
        else if(requestPathInfo.equals("/secondendpoint")) 
        {
            //do something
        }
    }

    @Override
    protected void doPut(HttpServletRequest request, HttpServletResponse response) 
    {
        //do something
    }
}
```

Another servlet:

```java
@WebServlet("/demoservlettwo/*")
public class DemoServlet2 extends HttpServlet 
{

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
    {
        //do something
    }

    @Override
    protected void doPut(HttpServletRequest request, HttpServletResponse response) 
    {
        //do something
    }
}
```

---

### 🔹 web.xml – The Biggest Problem ❌

```xml
<servlet>
    <servlet-name>DemoServlet1</servlet-name>
    <servlet-class>DemoServlet1</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>DemoServlet1</servlet-name>
    <url-pattern>/demoservletone</url-pattern>
    <url-pattern>/demoservletone/firstendpoint</url-pattern>
    <url-pattern>/demoservletone/secondendpoint</url-pattern>
</servlet-mapping>

<servlet>
    <servlet-name>DemoServlet2</servlet-name>
    <servlet-class>DemoServlet2</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>DemoServlet2</servlet-name>
    <url-pattern>/demoservlettwo</url-pattern>
</servlet-mapping>
```

#### Problems with `web.xml`:

* Becomes **huge** in large applications
* Hard to read & maintain
* Contains:

  * Servlet mappings
  * Filters
  * Security rules
* One servlet handles **multiple endpoints using if-else**

```java
if (path.equals("/first")) {
    // logic
} else if (path.equals("/second")) {
    // logic
}
```

---

## 4️⃣ Problems with Servlets

❌ Tight coupling
❌ Hard unit testing
❌ Massive `web.xml`
❌ Poor REST API structure
❌ Manual server deployment

---

## 5️⃣ Spring Framework (Spring MVC) – Solution to Servlets

Spring MVC solved servlet issues using:

### ✅ Annotation-based configuration

* Removed `web.xml`

### ✅ DispatcherServlet (Front Controller)

* Central request handler

### ✅ Clean REST APIs

---

## 6️⃣ Inversion of Control (IoC) & Dependency Injection (DI)

### 🔹 Problem: Tight Coupling (Without DI)

```java
public class PaymentService {

    UserService userService = new UserService();

    public String getSenderDetails(int id) {
        return userService.getUserDetails(id);
    }
}
```

❌ Cannot mock
❌ Hard unit testing
❌ Strong dependency

---

### 🔹 Solution: Dependency Injection (Spring)

```java
@Component
public class UserService {
    public String getUserDetails(int id) {
        return "User " + id;
    }
}
```

```java
@Component
public class PaymentService {

    @Autowired
    private UserService userService;

    public String getSenderDetails(int id) {
        return userService.getUserDetails(id);
    }
}
```

✅ Loose coupling
✅ Easy mocking
✅ Easy unit testing

📌 **DI is an implementation of IoC**

---

## 7️⃣ Spring MVC Request Flow

### 🔹 Flow Diagram (Conceptual)

```
Client Request
      ↓
Servlet Container (Tomcat)
      ↓
DispatcherServlet
      ↓
Handler Mapping
      ↓
Controller
      ↓
Service / Business Logic
      ↓
Response
```

---

### 🔹 Controller Example

```java
@Controller
@RequestMapping("/payment")
public class PaymentController {

    @GetMapping("/details")
    public String getPaymentDetails() {
        return "payment-details";
    }
}
```

✔ No if-else
✔ One API = One method
✔ Clean REST structure

---

## 8️⃣ Spring MVC Configuration (Before Spring Boot)

### 🔹 pom.xml

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.9</version>
</dependency>
```

### 🔹 AppConfig Class

```java
@Configuration
@EnableWebMvc
@ComponentScan("com.example")
public class AppConfig {
}
```

### 🔹 DispatcherServlet Configuration

```java
public class MyDispatcherServlet extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return null;
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{AppConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```

❌ Too much boilerplate
❌ Manual configuration
❌ WAR deployment required

---

## 9️⃣ Why Spring Boot?

Spring Boot fixes **Spring MVC complexity**.

---

## 🔟 Problems Spring Boot Solves

### 🟢 1. Dependency Management

❌ Spring MVC:

* Manual dependencies
* Version conflicts

✅ Spring Boot:

* Starter dependencies
* Version managed automatically

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.0.0</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
</dependencies>
```

---

### 🟢 2. Auto Configuration

Spring Boot:

* Auto configures:

  * DispatcherServlet
  * Component Scan
  * MVC setup
* No need for config classes

📌 Known as **Opinionated Framework**

---

### 🟢 3. Embedded Server 🚀

❌ Earlier:

* Create WAR
* Deploy to Tomcat

✅ Spring Boot:

* Embedded Tomcat
* Run as Java app

---

## 1️⃣1️⃣ Spring Boot Minimal Application

### 🔹 Main Class

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

📌 `@SpringBootApplication` includes:

* `@EnableAutoConfiguration`
* `@ComponentScan`
* `@Configuration`

---

### 🔹 Controller

```java
@RestController
@RequestMapping("/my-api")
public class MyController {

    @GetMapping("/first-api")
    public String firstApi() {
        return "Hello from Concept and Coding";
    }
}
```

---

### 🔹 Running the App

```text
http://localhost:8080/my-api/first-api
```

✔ Embedded Tomcat starts automatically
✔ DispatcherServlet works internally
✔ Clean & fast setup

---

## 1️⃣2️⃣ Final Definition of Spring Boot

### ✅ Spring Boot:

* Builds **production-ready applications**
* Uses Spring Framework internally
* Supports **Convention over Configuration**
* Simplifies:

  * Dependency management
  * Configuration
  * Server setup

---

## 🔚 Summary Table

| Feature         | Servlets | Spring MVC | Spring Boot |
| --------------- | -------- | ---------- | ----------- |
| web.xml         | Required | Removed    | Removed     |
| Dependency Mgmt | Manual   | Manual     | Auto        |
| Configuration   | Heavy    | Medium     | Minimal     |
| Server          | External | External   | Embedded    |
| REST APIs       | Hard     | Easy       | Easiest     |

---
