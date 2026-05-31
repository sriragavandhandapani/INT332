# Lab Activities: Module 8 (Advanced Maven)

This laboratory module focuses on automating project compilation, executing unit tests, bundling fat executables, standardizing builds via the Maven Wrapper, and containerizing the final artifact using multi-stage Docker builds.

---

## Challenge 1: Multi-Plugin orchestration in `pom.xml`
Configure a `pom.xml` that integrates the Maven Compiler Plugin, Surefire Plugin, and Shade Plugin to compile, test, and bundle a standalone executable.

### Steps:
1. Create a directory named `maven-advanced`.
2. Scaffolds folders: `mkdir -p src/main/java/com/lpu/devops` and `src/test/java/com/lpu/devops`.
3. Create `pom.xml` in `maven-advanced/` with the following configuration:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>com.lpu.devops</groupId>
       <artifactId>maven-advanced</artifactId>
       <version>1.0-SNAPSHOT</version>
       <packaging>jar</packaging>
   
       <properties>
           <maven.compiler.source>17</maven.compiler.source>
           <maven.compiler.target>17</maven.compiler.target>
           <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       </properties>
   
       <dependencies>
           <!-- Test dependencies -->
           <artifactId>junit-jupiter-api</artifactId>
           <groupId>org.junit.jupiter</groupId>
           <version>5.10.1</version>
           <scope>test</scope>
       </dependencies>
   
       <build>
           <plugins>
               <!-- Compiler Plugin -->
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-compiler-plugin</artifactId>
                   <version>3.11.0</version>
                   <configuration>
                       <source>17</source>
                       <target>17</target>
                   </configuration>
               </plugin>
               <!-- Surefire Plugin -->
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-surefire-plugin</artifactId>
                   <version>3.1.2</version>
               </plugin>
               <!-- Shade Plugin (Build Fat JAR) -->
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-shade-plugin</artifactId>
                   <version>3.5.1</version>
                   <executions>
                       <execution>
                           <phase>package</phase>
                           <goals>
                               <goal>shade</goal>
                           </goals>
                           <configuration>
                               <transformers>
                                   <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                       <mainClass>com.lpu.devops.App</mainClass>
                                   </transformer>
                               </transformers>
                           </configuration>
                       </execution>
                   </executions>
               </plugin>
           </plugins>
       </build>
   </project>
   ```

4. Create `src/main/java/com/lpu/devops/App.java` with a simple executable class:
   ```java
   package com.lpu.devops;
   
   public class App {
       public static void main(String[] args) {
           System.out.println("Executing Standalone Maven Fat JAR Successfully!");
       }
   }
   ```

---

## Challenge 2: Install and leverage Maven Wrapper (`mvnw`)
Standardize build environments by installing the Maven Wrapper directly in the repository directory.

### Steps:
1. Inside `maven-advanced/`, run the wrapper generation plugin:
   ```bash
   mvn wrapper:wrapper -Dmaven=3.9.5
   ```
2. Verify that the files `mvnw`, `mvnw.cmd`, and the directory `.mvn/` are generated.
3. Clean and package the application using the wrapper script instead of system-installed Maven:
   ```bash
   ./mvnw clean package
   ```
4. Verify the executable build by running:
   ```bash
   java -jar target/maven-advanced-1.0-SNAPSHOT.jar
   ```

---

## Challenge 3: Design a multi-stage Docker build
Package the Maven project inside an optimized Docker image, isolating build environments from production.

### Steps:
1. Create a `Dockerfile` in `maven-advanced/`:
   ```dockerfile
   # Stage 1: Build Phase
   FROM maven:3.9.5-eclipse-temurin-17 AS build-env
   WORKDIR /workspace
   COPY pom.xml .
   RUN mvn dependency:go-offline -B
   COPY src ./src
   RUN mvn clean package -DskipTests
   
   # Stage 2: Runtime JRE Phase
   FROM eclipse-temurin:17-jre-alpine
   WORKDIR /app
   COPY --from=build-env /workspace/target/maven-advanced-1.0-SNAPSHOT.jar app.jar
   EXPOSE 8080
   ENTRYPOINT ["java", "-jar", "app.jar"]
   ```

2. Build the Docker image:
   ```bash
   docker build -t lpu-maven-app:1.0 .
   ```
3. Run the container and verify its output:
   ```bash
   docker run --rm lpu-maven-app:1.0
   ```

---

## Challenge 4: Build speed diagnostics & analysis
Analyze the time difference between standard builds and Docker cached builds.

### Steps:
1. Run a cold build:
   ```bash
   docker build --no-cache -t lpu-maven-app:cold .
   ```
   *Record the duration and download times for dependencies.*

2. Make a minor text adjustment to `App.java` (e.g. modify the print statement).
3. Re-run the build without `--no-cache`:
   ```bash
   docker build -t lpu-maven-app:cached .
   ```
   *Observe how Docker utilizes cached layers for `mvn dependency:go-offline` to skip re-downloading dependencies, reducing build times from minutes to seconds.*
