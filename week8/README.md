# Module 8: Advanced Maven & Container Integration

## Core Curriculum
- Core Plugins: `maven-compiler-plugin`, `maven-surefire-plugin`, `maven-shade-plugin` (Creating Fat JARs)
- The Maven Wrapper (`mvnw` and `.mvn/`)
- Multi-Stage Dockerization for Java Applications
- Optimization of Docker cache with Maven builds

---

## 1. Advanced Maven Plugins
While Maven has standard lifecycle phases, the actual work is done by **Plugins**. Plugins bind to lifecycle phases to execute specific tasks.

### A. The Compiler Plugin (`maven-compiler-plugin`)
This plugin compiles your Java source files. You can specify the Java release target and enable detailed compiler warnings.
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.11.0</version>
    <configuration>
        <source>17</source>
        <target>17</target>
        <compilerArgs>
            <arg>-Xlint:all</arg>
        </compilerArgs>
    </configuration>
</plugin>
```

### B. The Surefire Plugin (`maven-surefire-plugin`)
The Surefire plugin executes unit tests. It is bound to the `test` phase of the default lifecycle.
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.1.2</version>
    <configuration>
        <includes>
            <include>**/*Test.java</include>
            <include>**/*Spec.java</include>
        </includes>
    </configuration>
</plugin>
```

### C. The Shade Plugin (`maven-shade-plugin`)
By default, `mvn package` creates a "thin" JAR containing only your compiled classes. If your application depends on Guava or Jackson, running `java -jar app.jar` will crash with `NoClassDefFoundError`. 

The **Shade Plugin** packages your classes *and* all transient dependency classes into a single, executable **"Fat" or "Uber" JAR**.
```xml
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
```

---

## 2. The Maven Wrapper (`mvnw`)
A recurring problem in CI/CD pipelines and developer setups is mismatched Maven versions. If a project requires Maven 3.9 but a developer has Maven 3.6, the build might fail or behave unpredictably.

The **Maven Wrapper** solves this by bundling a lightweight shell script (`mvnw` on Linux/macOS, `mvnw.cmd` on Windows) and configuration directory (`.mvn/`) in the project root.

- When you run `./mvnw clean install`, it checks if the specified Maven version is installed in `~/.m2/wrapper/`.
- If not, it automatically downloads the correct Maven binary and runs the build with it.
- **To initialize Maven Wrapper in a project:**
  ```bash
  mvn wrapper:wrapper -Dmaven=3.9.5
  ```

---

## 3. Multi-Stage Dockerization
Deploying raw Java applications inside containers requires a Java Runtime Environment (JRE). However, the JDK (Java Development Kit) needed to *build* the app is massive and insecure to ship to production.

We use **Multi-Stage Builds** to compile in a JDK-dense environment, and copy only the final Fat JAR into a lightweight JRE/JRE-slim container.

```dockerfile
# Stage 1: Build Environment (JDK)
FROM maven:3.9.5-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
# Prefetch dependencies to cache them in Docker layer
RUN mvn dependency:go-offline -B
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2: Runtime Environment (JRE)
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/maven-advanced-1.0-SNAPSHOT.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## Summary
Advanced Maven involves leveraging plugins to build production-ready artifacts (such as Fat JARs via `maven-shade-plugin`), establishing consistent environments with `mvnw`, and employing multi-stage Dockerfiles to construct highly secure, optimized container images.
