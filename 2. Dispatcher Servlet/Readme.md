
---

# 🌐 Java Web Flow: Servlet → Spring MVC → Spring Boot

---

## 1️⃣ Servlet Container & Tomcat (Basics)

### 🔹 What is Tomcat?

* **Tomcat = Servlet Container**
* ❌ NOT service container
* It **manages servlets** (life cycle, request/response handling)

### 🔹 Deployment

* Java web app is converted into a **WAR file**
* WAR is deployed into **Tomcat**
* Tomcat hosts and runs the application

---

## 2️⃣ How Request Works in Pure Servlet (Without Spring)

### 🔁 Request Flow

```
Client Request
   ↓
Tomcat (Servlet Container)
   ↓
web.xml
   ↓
Mapped Servlet
   ↓
doGet / doPost
   ↓
Response
```

---

### 📄 web.xml (Servlet Mapping)

```xml
<web-app>
    <servlet>
        <servlet-name>DemoServletOne</servlet-name>
        <servlet-class>com.example.DemoServletOne</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>DemoServletOne</servlet-name>
        <url-pattern>/first</url-pattern>
    </servlet-mapping>
</web-app>
```

---

### 🧩 Servlet Class

```java
public class DemoServletOne extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {

        resp.getWriter().write("Hello from Servlet");
    }
}
```

---

### ❌ Problems with Servlets

1. **Huge web.xml**

   * Hundreds of servlets
   * Filters, listeners, mappings → unreadable

2. **Tight Coupling**

   * `new Class()` everywhere
   * Hard to mock dependencies

3. **Difficult Unit Testing**

   * Cannot mock objects easily

4. **Poor REST API Management**

   * `if-else` inside `doGet()`
   * One servlet → many endpoints → messy code

---

## 3️⃣ Spring MVC Comes to Rescue 🚀

Spring MVC solves **Servlet limitations**

---

## 4️⃣ Spring MVC Request Flow (High Level)

```
Client Request
   ↓
Tomcat (Servlet Container)
   ↓
DispatcherServlet (Front Controller)
   ↓
HandlerMapping
   ↓
Controller Method
   ↓
Response
```

📌 **DispatcherServlet = First Controller**

---

## 5️⃣ DispatcherServlet (Heart of Spring MVC)

### Responsibilities:

1. Receives **all requests**
2. Uses **HandlerMapping**
3. Finds **Controller**
4. Creates Controller instance
5. Resolves dependencies using **IOC**
6. Invokes correct method
7. Returns response

---

## 6️⃣ Controller in Spring MVC

### Example Controller

```java
@Controller
@RequestMapping("/payment")
public class PaymentController {

    @GetMapping("/status")
    public String getPaymentStatus() {
        return "Payment Successful";
    }

    @GetMapping("/details")
    public String getPaymentDetails() {
        return "Payment Details";
    }
}
```

### ✅ Advantages

* No `web.xml`
* No `if-else`
* Clean, readable APIs
* One method = one endpoint


---

## 7️⃣ Summary: Servlet vs Spring MVC

| Feature         | Servlet | Spring MVC     |
| --------------- | ------- | -------------- |
| Config          | web.xml | Annotations    |
| Coupling        | Tight   | Loose          |
| Testing         | Hard    | Easy           |
| REST APIs       | if-else | Clean mappings |
| Object Creation | Manual  | IOC            |
| Scalability     | Poor    | Excellent      |

---

