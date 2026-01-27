

---

# 🔥 Spring Boot `@Profile` Annotation

---

## 1️⃣ First Understand: **What is Profiling in Spring Boot?**

In real projects, **same codebase** runs in **different environments**:

| Environment     | Example Differences                  |
| --------------- | ------------------------------------ |
| **Dev (Local)** | Dummy DB, high timeout, more logs    |
| **QA / Stage**  | Test DB, medium timeout              |
| **Prod (Live)** | Real DB, low timeout, strict configs |

So **configuration changes**, not the code.

Examples of config that change:

* DB username/password
* URLs of other services
* Port numbers
* Timeout values
* Retry counts
* Throttle limits

---

## 2️⃣ How Spring Boot Handles This?

Using **Profiles**.

Profiles = Environment-specific configuration sets.

We create multiple property files:

```
application.properties        → Default
application-dev.properties    → Dev
application-qa.properties     → QA
application-prod.properties   → Production
```

---

## 3️⃣ Basic Example (Without @Profile Annotation)

### 🧠 Component Class

```java
@Component
public class MySQLConnection {

    @Value("${username}")
    String username;

    @Value("${password}")
    String password;

    @PostConstruct
    public void init(){
        System.out.println("username: " + username + " password: " + password);
    }
}
```

---

### 📁 application.properties (Default)

```properties
username=defaultUsername
password=defaultPassword
spring.profiles.active=qa
```

---

### 📁 application-dev.properties

```properties
username=devUsername
password=devPassword
```

### 📁 application-qa.properties

```properties
username=qaUsername
password=qaPassword
```

### 📁 application-prod.properties

```properties
username=prodUsername
password=prodPassword
```

---

### ⚙️ What Happens?

Since:

```properties
spring.profiles.active=qa
```

Spring loads:

* `application.properties` (parent)
* `application-qa.properties` (child — **higher priority**)

Output:

```
username: qaUsername password: qaPassword
```

---

## 4️⃣ Priority Rule

| Location                                 | Priority  |
| ---------------------------------------- | --------- |
| Profile file (application-qa.properties) | 🔥 Higher |
| Default file (application.properties)    | Lower     |

---

## 5️⃣ Setting Profile Dynamically

### ✅ Method 1 — Using Maven Profile in `pom.xml`

```xml
<profiles>
    <profile>
        <id>local</id>
        <properties>
            <spring-boot.run.profiles>dev</spring-boot.run.profiles>
        </properties>
    </profile>
    <profile>
        <id>production</id>
        <properties>
            <spring-boot.run.profiles>prod</spring-boot.run.profiles>
        </properties>
    </profile>
    <profile>
        <id>stage</id>
        <properties>
            <spring-boot.run.profiles>qa</spring-boot.run.profiles>
        </properties>
    </profile>
</profiles>
```

Run:

```bash
mvn spring-boot:run -Pproduction
```

---

### ✅ Method 2 — Direct Command Line

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=prod
```

This overrides `spring.profiles.active`.

---

# 🎯 Now Main Topic: `@Profile` Annotation

### Definition:

**Bean is created ONLY when the specified profile is active.**

---

## 6️⃣ Example with `@Profile`

```java
@Component
@Profile("prod")
public class MySQLConnection {

    @Value("${username}")
    String username;

    @Value("${password}")
    String password;

    @PostConstruct
    public void init(){
        System.out.println("MySql username: " + username + " passowrd: " + password);
    }
}
```

```java
@Component
@Profile("dev")
public class NoSQLConnection {

    @Value("${username}")
    String username;

    @Value("${password}")
    String password;

    @PostConstruct
    public void init(){
        System.out.println("NoSQL username: " + username + " passowrd: " + password);
    }
}
```

---

### If active profile = `qa`

```properties
spring.profiles.active=qa
```

❌ Neither bean created (no profile match)

---

### If run with:

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=prod
```

✔ MySQL bean created
❌ NoSQL bean NOT created

---

## 7️⃣ Multiple Profiles Active

```properties
spring.profiles.active=prod,qa
```

Beans:

```java
@Component
@Profile("prod")
class MySQLConnection { }
```

```java
@Component
@Profile("qa")
class NoSQLConnection { }
```

✔ BOTH beans created

📌 But **properties from last profile (qa)** get higher priority.

---

## 8️⃣ VERY IMPORTANT INTERVIEW POINT

### ❓ Can we use `@Profile` to create beans for different applications?

Yes, technically.

```java
@Component
@Profile("app1")
public class NoSQLConnection { }
```

**Application 1**

```properties
spring.profiles.active=app1
```

**Application 2**

```properties
spring.profiles.active=app2
```

Bean only created in App1.

---

### 🚨 BUT THIS IS WRONG USAGE

`@Profile` is meant for:
✔ Dev
✔ QA
✔ Prod

Not:
❌ App1
❌ App2

For application-based bean creation use:

👉 **`@ConditionalOnProperty`** instead

---

## 9️⃣ Difference: `@Profile` vs `@ConditionalOnProperty`

| Feature                      | @Profile               | @ConditionalOnProperty             |
| ---------------------------- | ---------------------- | ---------------------------------- |
| Purpose                      | Environment separation | Feature toggle / app-specific bean |
| Use case                     | Dev/QA/Prod configs    | Enable/disable functionality       |
| Interview correctness        | ✅ Proper               | ✅ Proper                           |
| Using for app-specific logic | ❌ Not recommended      | ✔ Correct                          |

---

# 🧠 Final Definition (Interview Ready)

> **`@Profile` is used to register beans conditionally based on the active Spring environment profile (like dev, qa, prod). It helps manage environment-specific configurations and beans.**

---