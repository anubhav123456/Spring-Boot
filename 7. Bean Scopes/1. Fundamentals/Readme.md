# Spring Boot Bean Scopes

---

## Types of Scopes in Spring

Spring provides 5 major bean scopes

1. Singleton (default)
2. Prototype
3. Request
4. Session
5. Application

---

## 1️⃣ Singleton Scope (Default)

### Key Points

 Default scope in Spring
 One object per IOC container
 Eagerly initialized (created at application startup)
 Same instance is shared everywhere

---

### Example Default Singleton

```java
@RestController
@RequestMapping(value = api)
public class TestController1 {

    @Autowired
    User user;

    public TestController1(){
        System.out.println(TestController1 instance initialization);
    }

    @PostConstruct
    public void init(){
        System.out.println(TestController1 object hashCode  + this.hashCode() +
             User object hashCode  + user.hashCode());
    }

    @GetMapping(path = fetchUser)
    public ResponseEntityString getUserDetails() {
        System.out.println(fetchUser api invoked);
        return ResponseEntity.status(HttpStatus.OK).body();
    }
}
```

---

### Explicit Singleton Scope

```java
@RestController
@RequestMapping(value = api)
@Scope(value = ConfigurableBeanFactory.SCOPE_SINGLETON)
public class TestController2 {

    @Autowired
    User user;

    public TestController2() {
        System.out.println(TestController2 instance initialization);
    }

    @PostConstruct
    public void init(){
        System.out.println(TestController2 object hashCode  + this.hashCode() +
             User object hashCode  + user.hashCode());
    }

    @GetMapping(path = fetchUser2)
    public ResponseEntityString getUserDetails() {
        System.out.println(fetchUser2 api invoked);
        return ResponseEntity.status(HttpStatus.OK).body();
    }
}
```

---

### Singleton Component

```java
@Component
@Scope(singleton)
public class User {

    public User() {
        System.out.println(User initialization);
    }

    @PostConstruct
    public void init(){
        System.out.println(User object hashCode  + this.hashCode());
    }
}
```

---

### Singleton Behavior

* All controllers share same User instance
* Objects are created before any API call
* No new object on API invocation

---

## 2️⃣ Prototype Scope

### Key Points

* New object every time it is requested
* Lazily initialized
* Spring does not manage full lifecycle after creation

---

### Prototype Example

```java
@RestController
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
@RequestMapping(value = api)
public class TestController1 {

    @Autowired
    User user;

    @Autowired
    Student student;

    public TestController1() {
        System.out.println(TestController1 instance initialization);
    }

    @PostConstruct
    public void init() {
        System.out.println(TestController1 object hashCode  + this.hashCode() +
                 User object hashCode  + user.hashCode()
                +  Student object hashCode  + student.hashCode());
    }
}
```

---

### Student (Singleton)

```java
@Component
public class Student {

    @Autowired
    User user;

    public Student() {
        System.out.println(Student instance initialization);
    }

    @PostConstruct
    public void init() {
        System.out.println(Student object hashCode  + this.hashCode() +
                 User object hashCode  + user.hashCode());
    }
}
```

---

### User (Prototype)

```java
@Component
@Scope(prototype)
public class User {

    public User() {
        System.out.println(User initialization);
    }

    @PostConstruct
    public void init() {
        System.out.println(User object hashCode  + this.hashCode());
    }
}
```

---

### Prototype Behavior

 New `User` object for every injection
 Student (singleton) keeps its own User instance
 Controller gets a new User every request

---

## 3️⃣ Request Scope

### Key Points

 One object per HTTP request
 Lazily initialized
 Same object reused within the same request

---

### Request Scope Example

```java
@RestController
@Scope(request)
@RequestMapping(value = api)
public class TestController1 {

    @Autowired
    User user;

    @Autowired
    Student student;

    public TestController1() {
        System.out.println(TestController1 instance initialization);
    }

    @PostConstruct
    public void init() {
        System.out.println(TestController1 object hashCode  + this.hashCode() +
                 User object hashCode  + user.hashCode() +
                 Student object hashCode  + student.hashCode());
    }
}
```

---

### Student (Prototype)

```java
@Component
@Scope(prototype)
public class Student {

    @Autowired
    User user;

    public Student() {
        System.out.println(Student instance initialization);
    }
}
```

---

### User (Request)

```java
@Component
@Scope(request)
public class User {

    public User() {
        System.out.println(User initialization);
    }
}
```

---

### Request Scope Behavior

 New object per HTTP request
 Same object shared within the request
 Different request → new instance

---

## ❗ Problem Singleton depending on Request Scope

### Issue

```java
@RestController
@Scope(singleton)
public class TestController1 {

    @Autowired
    User user;
}

@Component
@Scope(request)
public class User {}
```

❌ Application fails at startup because

 Singleton is eagerly initialized
 No active HTTP request exists

---

## ✅ Solution Scoped Proxy

```java
@Component
@Scope(value = request, proxyMode = ScopedProxyMode.TARGET_CLASS)
public class User {

    public User() {
        System.out.println(User initialization);
    }
}
```

### How Proxy Works

 Dummy proxy object injected at startup
 Real object created when HTTP request arrives

---

## 4️⃣ Session Scope

### Key Points

 One object per HTTP session
 Same object reused across multiple requests
 New object after session invalidation

---

### Session Scope Example

```java
@RestController
@Scope(session)
@RequestMapping(value = api)
public class TestController1 {

    @Autowired
    User user;

    @GetMapping(fetchUser)
    public ResponseEntityString fetch() {
        return ResponseEntity.ok(OK);
    }

    @GetMapping(logout)
    public ResponseEntityString logout(HttpServletRequest request) {
        request.getSession().invalidate();
        return ResponseEntity.ok(Logged out);
    }
}
```

---

### Session Behavior

 Same object for same session
 New object after logoutsession expiry

---

## 5️⃣ Application Scope

### Key Points

 One object shared across multiple IOC containers
 Rarely used
 Similar to Singleton but broader

```java
@Scope(application)
```

---

## 🔥 Scope Comparison Table

 Scope        Objects Created   Lifecycle  Use Case           
 -----------  ----------------  ---------  ------------------ 
 Singleton    One per IOC       Eager      Stateless services 
 Prototype    Every request     Lazy       Temporary objects  
 Request      Per HTTP request  Lazy       Request data       
 Session      Per session       Lazy       User loginsession 
 Application  Per application   Eager      Rare cases         

---

## ✅ Final Takeaways

 Singleton is default and eager
 Prototype always creates new objects
 Request & Session are web-aware scopes
 Use proxyMode when mixing scopes
 Always think in terms of bean lifecycle + scope

---

✅ Interview Tip Most Spring scope questions revolve around Singleton vs Prototype vs Request + Proxy

---
