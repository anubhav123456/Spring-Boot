
---

# 🔥 Maven Interview Questions & Answers

---

## 🟢 BASIC QUESTIONS

---

### 1️⃣ What is Maven?

**Answer:**
Maven is a **project management and build automation tool**.
It helps in **build generation, dependency management, testing, packaging, documentation, and deployment** using a standard project structure.

---

### 2️⃣ Is Maven only a build tool?

**Answer:**
No. Maven is a **project management tool**.
Build generation is only **one part** of Maven. It also manages dependencies, plugins, documentation, testing, and deployments.

---

### 3️⃣ What is POM in Maven?

**Answer:**
POM stands for **Project Object Model**.
`pom.xml` is the **core configuration file** in Maven that contains:

* Project metadata
* Dependencies
* Plugins
* Build configurations

---

### 4️⃣ What happens when we run a Maven command?

**Answer:**
When a Maven command is executed:

1. Maven searches for `pom.xml` in the current directory
2. Reads configurations from POM
3. Executes the requested lifecycle phase along with its previous phases

---

### 5️⃣ What is the default Maven project structure?

**Answer:**
Maven follows a **standard directory structure**:

```
src/main/java       → application code
src/test/java       → test code
pom.xml             → configuration
target/             → build output
```

---

## 🟡 INTERMEDIATE QUESTIONS

---

### 6️⃣ What is the difference between Maven and Ant?

**Answer:**

| Ant                            | Maven                           |
| ------------------------------ | ------------------------------- |
| Need to specify *what and how* | Specify only *what*             |
| No standard structure          | Standard project structure      |
| Manual dependency handling     | Automatic dependency management |
| Procedural                     | Declarative                     |

---

### 7️⃣ What are Maven coordinates?

**Answer:**
Maven coordinates uniquely identify a project:

```xml
<groupId>com.company</groupId>
<artifactId>my-app</artifactId>
<version>1.0.0</version>
```

---

### 8️⃣ What is a Maven lifecycle?

**Answer:**
A lifecycle is a **sequence of phases** that Maven executes in order to build and distribute a project.

---

### 9️⃣ Name the Maven build lifecycle phases.

**Answer:**

1. validate
2. compile
3. test
4. package
5. verify
6. install
7. deploy

---

### 🔑 10️⃣ What happens if we run `mvn package`?

**Answer:**
Maven executes:

```
validate → compile → test → package
```

And generates a **JAR/WAR file** inside the `target` directory.

---

### 11️⃣ What is a Maven goal?

**Answer:**
A goal is a **single task** performed by a Maven plugin.
Example:

```bash
mvn compiler:compile
```

---

### 12️⃣ Difference between Phase and Goal?

**Answer:**

| Phase                  | Goal                |
| ---------------------- | ------------------- |
| Lifecycle step         | Single task         |
| Executes automatically | Executes via plugin |
| Example: compile       | Example: javac      |

---

## 🔵 ADVANCED QUESTIONS

---

### 13️⃣ What is Super POM?

**Answer:**
Super POM is the **default parent POM** for all Maven projects.
If no parent is defined, Maven inherits from Super POM automatically.

---

### 14️⃣ What is Parent POM?

**Answer:**
A parent POM is used to:

* Share common configurations
* Manage dependency versions
* Centralize plugin configurations

Child POM inherits from parent.

---

### 15️⃣ What is Dependency Management?

**Answer:**
`<dependencyManagement>` defines **dependency versions centrally** without importing them automatically.

Used mainly in **parent POMs**.

---

### 16️⃣ What is the difference between `dependencies` and `dependencyManagement`?

**Answer:**

| dependencies                | dependencyManagement |
| --------------------------- | -------------------- |
| Downloads dependency        | Only manages version |
| Used in child POM           | Used in parent POM   |
| Mandatory to use dependency | Optional             |

---

### 17️⃣ What is a local repository?

**Answer:**
Local repository is a **cache on the developer machine**:

```
~/.m2/repository
```

Maven checks local repo **before remote**.

---

### 18️⃣ What is a remote repository?

**Answer:**
Remote repository is hosted externally:

* Maven Central
* Company Nexus / Artifactory

Used to share artifacts across teams.

---

### 19️⃣ What is the difference between `install` and `deploy`?

**Answer:**

| install                 | deploy                    |
| ----------------------- | ------------------------- |
| Stores artifact locally | Uploads artifact remotely |
| `.m2/repository`        | Company or Maven Central  |
| Developer level         | Organization level        |

---

### 20️⃣ Where are Maven credentials stored?

**Answer:**
In:

```
~/.m2/settings.xml
```

Used for authenticating remote repositories.

---

## 🔴 SCENARIO-BASED QUESTIONS

---

### 21️⃣ Maven is downloading dependencies again and again. Why?

**Answer:**

* Local repository was deleted
* Snapshot dependency updated
* Local repository path changed
* Corrupted `.m2` cache

---

### 22️⃣ How to skip tests in Maven?

**Answer:**

```bash
mvn install -DskipTests
```

or

```bash
mvn install -Dmaven.test.skip=true
```

---

### 23️⃣ How to add a custom task to a lifecycle phase?

**Answer:**
By using plugins inside `<build>`:

```xml
<execution>
  <phase>verify</phase>
  <goals>
    <goal>check</goal>
  </goals>
</execution>
```

---

### 24️⃣ How does Maven know how to compile Java code?

**Answer:**
Through **default plugins** defined in the **Super POM**, such as:

* `maven-compiler-plugin`

---

### 25️⃣ What happens if `pom.xml` is missing?

**Answer:**
Maven command fails because POM is mandatory.

---

### 26️⃣ Can we change the local repository location?

**Answer:**
Yes, in `settings.xml`:

```xml
<localRepository>/custom/path</localRepository>
```

---

### 27️⃣ How does Maven resolve dependencies?

**Answer:**

1. Check local repository
2. If not found → remote repository
3. Download and cache locally

---

### 28️⃣ What is SNAPSHOT version?

**Answer:**

* Indicates **development version**
* Maven checks for updates frequently
* Example:

```xml
1.0.0-SNAPSHOT
```

---

### 29️⃣ Why companies use Nexus/Artifactory?

**Answer:**

* Centralized dependency management
* Faster builds
* Security & access control
* Avoid dependency loss

---

### 30️⃣ Real-world usage of Maven?

**Answer:**

* Build automation
* CI/CD pipelines
* Dependency management
* Release management
* Enterprise-level project standardization

---

## 🧠 INTERVIEW TIP (IMPORTANT)

🔥 Always say:

> *“Maven follows Convention over Configuration and lifecycle-based build execution.”*

---

