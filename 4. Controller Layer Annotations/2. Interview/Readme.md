# Interview Q&A

---

### Q1️⃣ What is the difference between `@Controller` and `@RestController`?

**Answer:**

* `@Controller` is used in Spring MVC for returning **views** (JSP/Thymeleaf).
* `@RestController` is used for REST APIs and returns **HTTP response body** directly.

```java
@RestController = @Controller + @ResponseBody
```

---

### Q2️⃣ In `@Controller`, how does Spring treat the return value by default?

**Answer:**
By default, Spring treats the return value as a **view name**, not as response data.

```java
@Controller
public class TestController {

    @GetMapping("/hello")
    public String hello() {
        return "hello"; // treated as view name
    }
}
```

Spring will try to resolve:

```
hello.jsp / hello.html
```

---

### Q3️⃣ What is the role of `@ResponseBody`?

**Answer:**
`@ResponseBody` tells Spring that the return value of the method should be written **directly into the HTTP response body**, not resolved as a view.

```java
@Controller
public class TestController {

    @GetMapping("/hello")
    @ResponseBody
    public String hello() {
        return "hello";
    }
}
```

Output:

```
hello
```

---

### Q4️⃣ Why don’t we need `@ResponseBody` in `@RestController`?

**Answer:**
Because `@RestController` already includes `@ResponseBody` internally.

```java
@RestController
public class TestController {

    @GetMapping("/hello")
    public String hello() {
        return "hello";
    }
}
```

---

### Q5️⃣ What happens if `@ResponseBody` is missing in `@Controller`?

**Answer:**
Spring assumes the return value is a **view name** and tries to render it using a `ViewResolver`. If no view is found, it results in an error.

Common Error:

* `404 Not Found`
* `Whitelabel Error Page`

---

### Q6️⃣ How does Spring decide whether to return a view or HTTP response?

**Answer:**
Spring checks:

1. Is the controller annotated with `@RestController`?
2. Is the method annotated with `@ResponseBody`?

* If **yes** → HTTP response body
* If **no** → View resolution

---

### Q7️⃣ What is ViewResolver and when is it used?

**Answer:**
ViewResolver is used only when:

* `@Controller` is used
* `@ResponseBody` is NOT present

It maps the returned string to an actual view file like JSP or HTML.

---

### Q8️⃣ Can a single project have both `@Controller` and `@RestController`?

**Answer:**
Yes.

* `@Controller` → Web pages (MVC)
* `@RestController` → REST APIs

---

### Q9️⃣ What does Spring internally return when we return a String from `@RestController`?

**Answer:**
Spring internally wraps the response in a `ResponseEntity` with:

* Status: `200 OK`
* Body: returned value

---

### Q🔟 Give a one-line interview explanation

**Answer:**

> "In Spring, `@Controller` treats return values as view names by default, whereas `@RestController` returns data directly as HTTP response body."

---