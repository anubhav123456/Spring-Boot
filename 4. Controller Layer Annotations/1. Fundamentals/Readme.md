# Spring Boot Controller, Request Mapping & Data Binding

---

## 1. Dispatcher Servlet & Handler Mapping

* **Dispatcher Servlet** is the front controller in Spring Boot.
* Every incoming HTTP request first goes to the DispatcherServlet.
* Dispatcher Servlet uses **HandlerMapping** to:

  1. Identify *which controller* can handle the request
  2. Identify *which method* inside that controller should be invoked
* Annotations like `@Controller`, `@RestController`, `@RequestMapping`, `@GetMapping`, etc. help Spring build this mapping.

---

## 2. @Controller vs @RestController

### @Controller

* Marks a class as a **Spring MVC Controller**.
* By default, return values are treated as **view names** (JSP, Thymeleaf, etc.).
* To return raw HTTP response data, you must use `@ResponseBody` on each method.

```java
@Controller
public class SampleController {

    @RequestMapping(path = "/fetchUser", method = RequestMethod.GET)
    @ResponseBody
    public String getUserDetails() {
        return "fetching and returning user details";
    }

    @RequestMapping(path = "/saveUser", method = RequestMethod.POST)
    @ResponseBody
    public String saveUserDetails() {
        return "Successfully saved the user details";
    }
}
```

---

### @RestController

* Combination of:

  * `@Controller`
  * `@ResponseBody`
* Every method returns **HTTP response body by default**.
* No need to write `@ResponseBody` on each method.
* Preferred for REST APIs.

```java
@RestController
@RequestMapping(value = "/api/")
public class SampleController {

    @GetMapping(path = "/fetchUser")
    public String getUserDetails() {
        return "fetching and returning user details";
    }

    @PostMapping(path = "/saveUser")
    public String saveUserDetails() {
        return "Successfully saved the user details";
    }
}
```

---

## One-Liner

🔥 **Important Note**:

> “In `@Controller`, return value is treated as view name unless `@ResponseBody` is used.
> `@RestController` avoids view resolution and directly returns response body.”

---

## 3. @RequestMapping & Specialized Mappings

### @RequestMapping

* Used to map an HTTP request to a controller method.
* Can be applied at:

  * **Class level** (common base path)
  * **Method level** (specific API)

Important attributes:

* `path` / `value` → URL path (aliases)
* `method` → HTTP method (GET, POST, PUT, DELETE, etc.)

```java
@RequestMapping(path = "/fetchUser", method = RequestMethod.GET)
```

---

### @GetMapping / @PostMapping

* Shortcuts over `@RequestMapping`.
* Improve readability.
* Default HTTP method is fixed.

```java
@GetMapping(path = "/fetchUser")
@PostMapping(path = "/saveUser")
```

Internally, they are still `@RequestMapping` annotations.

---

## 4. Class-Level Request Mapping

Used to avoid repeating common path prefixes.

```java
@RestController
@RequestMapping(value = "/api/")
public class SampleController {

    @GetMapping(path = "/fetchUser")
    public String getUserDetails() {
        return "fetching and returning user details";
    }
}
```

Final API path:

```
/api/fetchUser
```

---

## 5. @RequestParam

### Purpose

* Binds **query parameters** from the URL to method parameters.
* Query parameters come after `?` in the URL.

### Example URL

```
http://localhost:8080/api/fetchUser?firstName=SHRAYANSH&lastName=JAIN&age=32
```

### Code Example

```java
@RestController
@RequestMapping(value = "/api/")
public class SampleController {

    @GetMapping(path = "/fetchUser")
    public String getUserDetails(
            @RequestParam(name = "firstName") String firstName,
            @RequestParam(name = "lastName", required = false) String lastName,
            @RequestParam(name = "age") int age) {

        return "fetching and returning user details based on first name = " + firstName +
                " , lastName = " + lastName + " and age is = " + age;
    }
}
```

### Key Points

* `required = true` (default): parameter must be present
* `required = false`: parameter optional (value becomes `null`)
* Automatic type conversion supported (String → int, long, enums, etc.)

---

## 6. Custom Data Binding using @InitBinder

* Used for **pre-processing request parameters** before binding.
* Helpful for trimming, formatting, normalization, etc.

### Example Use Case

* Convert `firstName` to lowercase and trim spaces before binding.

```java
@InitBinder
public void initBinder(WebDataBinder binder) {
    binder.registerCustomEditor(String.class, "firstName",
            new FirstNamePropertyEditor());
}
```

### Custom Property Editor

```java
public class FirstNamePropertyEditor extends PropertyEditorSupport {

    @Override
    public void setAsText(String text) {
        setValue(text.trim().toLowerCase());
    }
}
```

---

## 7. @PathVariable

### Purpose

* Extracts dynamic values directly from the URL path.
* Used when data is part of the path, not query params.

### Example

```
/api/fetchUser/SHRAYANSH
```

### Code

```java
@RestController
@RequestMapping(value = "/api/")
public class SampleController {

    @GetMapping(path = "/fetchUser/{firstName}")
    public String getUserDetails(@PathVariable(value = "firstName") String firstName) {
        return "fetching and returning user details based on first name = " + firstName;
    }
}
```

---

## 8. @RequestBody

### Purpose

* Binds **HTTP request body** (JSON/XML) to a Java object.
* Commonly used in POST / PUT APIs.

### Controller Code

```java
@RestController
@RequestMapping(value = "/api/")
public class SampleController {

    @PostMapping(path = "/saveUser")
    public String getUserDetails(@RequestBody User user) {
        return "User created " + user.getUsername() + ":" + user.getEmail();
    }
}
```

### User POJO

```java
public class User {

    @JsonProperty("user_name")
    String username;
    String email;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

### CURL Request

```bash
curl --location --request POST 'http://localhost:8080/api/saveUser' \
--header 'Content-Type: application/json' \
--data-raw '{
    "user_name": "Shrayansh",
    "email": "sjxyztest@gmail.com"
}'
```

---

## 9. @JsonProperty

* Used when JSON field names don’t match Java field names.
* Provided by **Jackson** library.

```java
@JsonProperty("user_name")
String username;
```

---

## 10. ResponseEntity

### Purpose

* Represents **entire HTTP response**:

  * Status code
  * Headers
  * Response body

### Example

```java
@RestController
@RequestMapping(value = "/api/")
public class SampleController {

    @GetMapping(path = "/fetchUser")
    public ResponseEntity<String> getUserDetails(
            @RequestParam(value = "firstName") String firstName) {

        String output = "fetched User details of " + firstName;
        return ResponseEntity.status(HttpStatus.OK).body(output);
    }
}
```

### Important Notes

* When returning plain `String` from `@RestController`, Spring internally creates a `ResponseEntity`.
* With `@Controller` (without `@ResponseBody`), Spring treats return value as **view name**, not response body.

---

