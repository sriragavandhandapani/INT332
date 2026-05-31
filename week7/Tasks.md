# Lab Activities: Module 7 (Maven Basics)

This laboratory module focuses on scaffolding a Java project using Maven's Standard Directory Layout, managing dependencies with explicit scopes, and running lifecycle phases.

---

## Challenge 1: Scaffold standard directory structure manually
To fully internalize Maven's **Convention over Configuration**, you will build a Maven project folder structure from scratch without automated tools.

### Steps:
1. Create a root directory named `maven-lab`.
2. Inside `maven-lab`, create the following nested folders:
   ```bash
   mkdir -p src/main/java/com/lpu/devops
   mkdir -p src/main/resources
   mkdir -p src/test/java/com/lpu/devops
   mkdir -p src/test/resources
   ```
3. Verify that your directory layout matches the standard Maven structure using `tree`:
   ```bash
   tree src/
   ```

---

## Challenge 2: Define your core Project Object Model (`pom.xml`)
In the root directory of your project (`maven-lab`), create a `pom.xml` file configured to build a Java 17 application with JUnit 5 dependencies.

### Steps:
1. Create the `pom.xml` file in `maven-lab/`.
2. Populate it with the following clean code block:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>com.lpu.devops</groupId>
       <artifactId>maven-lab</artifactId>
       <version>1.0-SNAPSHOT</version>
       <packaging>jar</packaging>
   
       <properties>
           <maven.compiler.source>17</maven.compiler.source>
           <maven.compiler.target>17</maven.compiler.target>
           <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
           <junit.version>5.10.1</junit.version>
       </properties>
   
       <dependencies>
           <!-- JUnit 5 Engine (Test Scope) -->
           <dependency>
               <groupId>org.junit.jupiter</groupId>
               <artifactId>junit-jupiter-api</artifactId>
               <version>${junit.version}</version>
               <scope>test</scope>
           </dependency>
           <dependency>
               <groupId>org.junit.jupiter</groupId>
               <artifactId>junit-jupiter-engine</artifactId>
               <version>${junit.version}</version>
               <scope>test</scope>
           </dependency>
       </dependencies>
   </project>
   ```

---

## Challenge 3: Write production & test code
Create a simple calculator class in your production code folder and a corresponding unit test in your test folder.

### 1. Production Code:
Create `src/main/java/com/lpu/devops/Calculator.java`:
```java
package com.lpu.devops;

public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}
```

### 2. Test Code:
Create `src/test/java/com/lpu/devops/CalculatorTest.java`:
```java
package com.lpu.devops;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class CalculatorTest {
    @Test
    public void testAddition() {
        Calculator calculator = new Calculator();
        assertEquals(5, calculator.add(2, 3), "2 + 3 should equal 5");
    }
}
```

---

## Challenge 4: Transition through the build lifecycle
Run the Maven CLI commands sequentially to inspect compilation artifacts and test executions.

### Commands to Run & Document:
1. **Compilation Phase:**
   ```bash
   mvn compile
   ```
   *Observation:* Maven creates a `target/` directory and compiles `Calculator.java` into `target/classes/com/lpu/devops/Calculator.class`.

2. **Test Phase:**
   ```bash
   mvn test
   ```
   *Observation:* Maven compiles the test file into `target/test-classes/` and runs the unit test. JUnit outputs the status to your console.

3. **Packaging Phase:**
   ```bash
   mvn package
   ```
   *Observation:* Maven packages your application bytecode into `target/maven-lab-1.0-SNAPSHOT.jar`.

4. **Cleaning Phase:**
   ```bash
   mvn clean
   ```
   *Observation:* The `target/` directory is completely removed, returning the workspace to a pristine source-only state.

---

## Challenge 5: Run dependency diagnostics
Add a new runtime dependency and inspect the full dependency graph to understand transitive dependencies.

### Steps:
1. Open `pom.xml` and add a new dependency:
   ```xml
   <dependency>
       <groupId>com.google.guava</groupId>
       <artifactId>guava</artifactId>
       <version>32.1.3-jre</version>
       <scope>compile</scope>
   </dependency>
   ```
2. Execute the dependency tree plugin command to view what other libraries are fetched transitively (dependencies of Guava):
   ```bash
   mvn dependency:tree
   ```
3. Document how Guava pulls dependencies like `failureaccess`, `listenablefuture`, and `jsr305` automatically.
