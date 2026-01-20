
---

# 📘 Maven

---

## 1️⃣ What is Maven?

* **Maven is a Project Management Tool**, not just a build tool.
* It helps with:

  * Build generation
  * Dependency resolution
  * Documentation
  * Testing
  * Packaging
  * Deployment
* **Build management is only one part of Maven**, not the whole thing.

👉 Maven works completely based on **POM (Project Object Model)**.

---

## 2️⃣ Role of `pom.xml`

* Maven is **highly dependent on `pom.xml`**
* Whenever you run any Maven command:

  ```bash
  mvn install
  mvn compile
  mvn test
  ```
* Maven first:

  1. Looks for `pom.xml` in the **current directory**
  2. Reads configurations
  3. Decides what actions to perform

📌 Without `pom.xml`, Maven cannot work.

---

## 3️⃣ Maven vs ANT

### 🔴 ANT (Old Tool)

* Developer must specify:

  * **What to do**
  * **How to do**
* Example:

  ```xml
  <javac srcdir="src" destdir="classes"/>
  ```
* You explicitly tell:

  * Compiler (`javac`)
  * Source directory
  * Destination directory

* ❌ More manual work
* ❌ No standard project structure

---

### 🟢 Maven (Modern Tool)

* Developer tells only:

  * **What to do**
* Maven decides:

  * **How to do**

Example:

```bash
mvn compile
```

✔ Maven internally knows:

* Which compiler to use
* Where source code exists
* Where compiled classes go

* ✅ Less configuration
* ✅ Convention over configuration
* ✅ Standard project structure

---

## 4️⃣ Why Maven Exists? (Advantages)

* Simplifies:

  * Build generation
  * Dependency management
  * Documentation
  * Testing
  * Packaging
* Standard project structure
* Plug-and-play plugin system
* Only specify **what you want**, Maven handles **how**

---

## 5️⃣ Maven Project Structure

Standard Maven structure:

```
project-name/
│── pom.xml
│
└── src/
    ├── main/
    │   ├── java/
    │   │   └── com/company/project/
    │   │       └── Application.java
    │   └── resources/
    │
    └── test/
        └── java/
            └── com/company/project/
                └── ApplicationTest.java
```

### Explanation:

* `src/main/java` → Production code
* `src/test/java` → Unit tests
* `pom.xml` → Maven configuration file

📌 Spring Boot Maven projects follow this structure by default.

---

## 6️⃣ Understanding `pom.xml`

### 6.1 XML Schema Definition

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
```

* Ensures:

  * Correct XML structure
  * Only valid Maven tags allowed
* Random tags are **not allowed**

---

### 6.2 Parent POM

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>
```

* Defines **parent project**
* Current POM becomes **child**
* Inherits:

  * Dependencies
  * Plugin versions
  * Configurations

📌 If no parent is defined → Maven uses **Super POM**

---

### 6.3 Super POM

* Top-level POM in Maven hierarchy
* Every POM ultimately inherits from **Super POM**
* Contains:

  * Default repositories
  * Default plugins
  * Default build lifecycle configs

---

### 6.4 Project Coordinates

```xml
<groupId>com.company</groupId>
<artifactId>learning-springboot</artifactId>
<version>1.0.0</version>
```

* Uniquely identifies a project in Maven
* Used while:

  * Downloading dependencies
  * Publishing artifacts

---

### 6.5 Properties

```xml
<properties>
    <java.version>17</java.version>
</properties>
```

Usage:

```xml
<version>${java.version}</version>
```

* Key-value pairs
* Can be reused anywhere in POM
* Improves maintainability

---

### 6.6 Repositories

```xml
<repositories>
    <repository>
        <id>central</id>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
</repositories>
```

* Defines **where dependencies are downloaded from**
* Usually not required because:

  * Maven Central is already defined in Super POM

---

## 7️⃣ Maven Build Lifecycle

### 🔁 Lifecycle Phases (Sequential)

1. **validate**
2. **compile**
3. **test**
4. **package**
5. **verify**
6. **install**
7. **deploy**

📌 Running any phase automatically runs **all previous phases**

---

## 8️⃣ Phase-wise Explanation

---

### 8.1 Validate

```bash
mvn validate
```

* Validates project structure
* No default tasks
* Can add custom validations using plugins

Example (Checkstyle):

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-checkstyle-plugin</artifactId>
      <executions>
        <execution>
          <phase>validate</phase>
          <goals>
            <goal>check</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

---

### 8.2 Compile

```bash
mvn compile
```

* Converts `.java` → `.class`
* Uses `javac` internally
* Output:

```
target/classes/
```

---

### 8.3 Test

```bash
mvn test
```

* Runs unit tests from:

```
src/test/java
```

* Uses JUnit / TestNG
* Fails build if test fails

---

### 8.4 Package

```bash
mvn package
```

* Packages compiled code into:

  * `.jar` or `.war`
* Output:

```
target/app-name.jar
```

---

### 8.5 Verify

```bash
mvn verify
```

* Verifies package integrity
* Used for:

  * Static code analysis
  * Security checks
  * PMD, SpotBugs, etc.

Example (PMD):

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-pmd-plugin</artifactId>
  <executions>
    <execution>
      <phase>verify</phase>
      <goals>
        <goal>check</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

---

### 8.6 Install

```bash
mvn install
```

* Installs JAR into **local repository**
* Default location:

```
~/.m2/repository
```

* Used so other local projects can use this artifact

---

### 8.7 Local Repository

* Stored on developer machine
* Path configurable in:

```
~/.m2/settings.xml
```

```xml
<localRepository>/custom/path</localRepository>
```

📌 Maven checks **local repo first**, then remote.

---

### 8.8 Deploy

```bash
mvn deploy
```

* Uploads artifact to **remote repository**
* Example:

  * Company Nexus / Artifactory
  * Maven Central

Config:

```xml
<distributionManagement>
  <repository>
    <id>company-repo</id>
    <url>https://repo.company.com/maven</url>
  </repository>
</distributionManagement>
```

Credentials in `settings.xml`:

```xml
<servers>
  <server>
    <id>company-repo</id>
    <username>user</username>
    <password>pass</password>
  </server>
</servers>
```

---

## 🔚 Final Summary

* Maven = **Project Management Tool**
* Works via **pom.xml**
* Follows **standard structure**
* Uses **lifecycle → phases → goals**
* Plugins extend functionality
* Local & remote repositories manage artifacts

---
