
---

# 🔥 `@ConditionalOnProperty` in Spring Boot

## ✅ What is `@ConditionalOnProperty`?

`@ConditionalOnProperty` is a **Spring Boot conditional annotation** used to **control whether a bean should be created or not based on a property value** in `application.properties` / `application.yml`.

👉 In simple words:
**Bean will be created only if a specific property matches a given condition.**

---

## ❓ Why Do We Need It?

In large applications:

* Thousands of beans exist
* All singleton beans are **eagerly initialized at startup**
* Many beans might be **unnecessary for a given deployment**

This causes:

* ❌ Cluttered ApplicationContext
* ❌ More memory usage
* ❌ Slower startup

👉 `@ConditionalOnProperty` helps **create only required beans**.

---

# 🧠 Default Behavior (Without Condition)

### Classes:

```java
@Component
public class DBConnection {

    @Autowired
    MySQLConnection mySQLConnection;

    @Autowired
    NoSQLConnection noSQLConnection;

    @PostConstruct
    public void init(){
        System.out.println("DB Connection Bean Created with dependencies below:");
        System.out.println("is MySQLConnection object Null: " + Objects.isNull(mySQLConnection));
        System.out.println("is NoSQLConnection object Null: " + Objects.isNull(noSQLConnection));
    }
}
```

```java
@Component
public class NoSQLConnection {

    NoSQLConnection() {
        System.out.println("initialization of NoSQLConnection Bean");
    }
}
```

```java
@Component
public class MySQLConnection {

    MySQLConnection(){
        System.out.println("initialization of MySQLConnection Bean");
    }
}
```

### 🔹 What happens at startup?

1. All 3 beans created (Singleton + Eager init)
2. Both dependencies injected
3. Output:

```
initialization of NoSQLConnection Bean
initialization of MySQLConnection Bean
DB Connection Bean Created with dependencies below:
is MySQLConnection object Null: false
is NoSQLConnection object Null: false
```

---

# 🎯 Real Interview Use Cases

### ✅ Use Case 1: Database Migration

Company migrating from **MySQL → NoSQL**
Need to enable only one at a time using config.

### ✅ Use Case 2: Common Library Used by Multiple Apps

| App   | Needs |
| ----- | ----- |
| App 1 | NoSQL |
| App 2 | MySQL |

Same codebase, different bean creation.

---

# 🧩 Solution: `@ConditionalOnProperty`

---

## 🔹 Step 1: Make Dependencies Optional

```java
@Component
public class DBConnection {

    @Autowired(required = false)
    MySQLConnection mySQLConnection;

    @Autowired(required = false)
    NoSQLConnection noSQLConnection;

    @PostConstruct
    public void init(){
        System.out.println("DB Connection Bean Created with dependencies below:");
        System.out.println("is MySQLConnection object Null: " + Objects.isNull(mySQLConnection));
        System.out.println("is NoSQLConnection object Null: " + Objects.isNull(noSQLConnection));
    }
}
```

⚠ Why `required = false`?
Because bean might not be created due to condition. Otherwise app fails to start.

---

## 🔹 Step 2: Add `@ConditionalOnProperty`

### MySQL Bean

```java
@Component
@ConditionalOnProperty(
    prefix = "sqlconnection",
    value = "enabled",
    havingValue = "true",
    matchIfMissing = false
)
public class MySQLConnection {

    MySQLConnection(){
        System.out.println("initialization of MySQLConnection Bean");
    }
}
```

### NoSQL Bean

```java
@Component
@ConditionalOnProperty(
    prefix = "nosqlconnection",
    value = "enabled",
    havingValue = "true",
    matchIfMissing = false
)
public class NoSQLConnection {

    NoSQLConnection() {
        System.out.println("initialization of NoSQLConnection Bean");
    }
}
```

---

## 🔹 Step 3: `application.properties`

```properties
sqlconnection.enabled=true
```

---

# 🧠 How `@ConditionalOnProperty` Works

| Parameter        | Meaning                                           |
| ---------------- | ------------------------------------------------- |
| `prefix`         | First part of property key                        |
| `value`          | Second part of key                                |
| 👉 Key Formed    | `prefix.value` → `sqlconnection.enabled`          |
| `havingValue`    | Bean created only if property value equals this   |
| `matchIfMissing` | If property absent, should bean still be created? |

---

### 🔍 Condition Evaluation

| Property                          | Result                              |
| --------------------------------- | ----------------------------------- |
| `sqlconnection.enabled=true`      | MySQL bean created                  |
| `nosqlconnection.enabled` missing | NoSQL bean NOT created              |
| `matchIfMissing=false`            | Missing property = Bean not created |

---

### 🔹 Final Output

```
initialization of MySQLConnection Bean
DB Connection Bean Created with dependencies below:
is MySQLConnection object Null: false
is NoSQLConnection object Null: true
```

---

# 🟢 Advantages

### ✅ 1. Feature Toggle

Switch features without code change.

### ✅ 2. Prevents Bean Clutter

Only required beans exist.

### ✅ 3. Saves Memory

Fewer objects in context.

### ✅ 4. Faster Startup

Less bean initialization.

### ✅ 5. Environment Based Config

Different beans in Dev / Prod.

---

# 🔴 Disadvantages

### ❌ 1. Misconfiguration Risk

Wrong property value → bean not created.

### ❌ 2. Code Complexity

Hard to track which bean loads.

### ❌ 3. Property Management Overhead

Need strict config discipline.

### ❌ 4. Confusion if Same Property Controls Multiple Beans

---

# 💡 Interview One-Liner

> "`@ConditionalOnProperty` is used to conditionally create Spring beans based on configuration properties, commonly used for feature toggling, environment-based configuration, and reducing unnecessary bean initialization."

---

