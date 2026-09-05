# Spring Boot Project Structure

This document explains the standard Spring Boot project structure from top to bottom.

The goal is to understand:

- What every folder/file is
- Why it exists
- What normally goes inside it
- What happens during application startup
- Where to write code
- Where to run the application
- What `pom.xml` does
- Difference between source code, configuration, build output, and project metadata
- How the structure looks from a real-world / 3-year developer perspective

---

# 1. Complete Project Structure

The project shown in IntelliJ looks approximately like this:

```text
demo/
│
├── .idea/
│
├── demo/
│   ├── .mvn/
│   │
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── com.example.demo/
│   │   │   │       ├── DemoApplication.java
│   │   │   │       └── HelloWorld.java
│   │   │   │
│   │   │   └── resources/
│   │   │
│   │   └── test/
│   │
│   ├── target/
│   │
│   ├── .gitattributes
│   ├── .gitignore
│   ├── HELP.md
│   ├── mvnw
│   ├── mvnw.cmd
│   └── pom.xml
│
├── target/
│
├── External Libraries
│
└── Scratches and Consoles
```

IMPORTANT:

Your screenshot shows a `demo` folder inside another `demo` folder.

That means IntelliJ is currently showing something similar to:

```text
C:\Users\...\Downloads\demo
        |
        └── demo
             ├── src
             ├── pom.xml
             └── ...
```

The inner `demo` is the actual Maven/Spring Boot project.

The outer `demo` is the directory that contains that project.

---

# 2. `.idea/`

```text
.idea/
```

## What is it?

`.idea` is an IntelliJ IDEA configuration folder.

It contains project-specific settings used by IntelliJ.

For example, IntelliJ may store information related to:

- Project configuration
- Module configuration
- Run configurations
- Code style settings
- IDE-specific metadata
- Which SDK/JDK the project is using
- IntelliJ project preferences

## Important point

`.idea` is NOT a Spring Boot folder.

It belongs to IntelliJ IDEA.

Spring Boot does not need `.idea` to run.

```text
Spring Boot
    ↓
doesn't care about .idea
```

But IntelliJ uses it.

If you open the same Maven project in another IDE, such as Eclipse, `.idea` is not what runs the application.

## Should it be committed to Git?

Usually, teams do not blindly commit all IntelliJ-specific settings.

Some `.idea` files may be useful in a team, while others are machine/user specific.

This is normally handled using `.gitignore`.

---

# 3. The actual project folder — `demo/`

```text
demo/
```

This is the actual Maven project.

This folder contains the things required to:

- Build the application
- Run the application
- Store source code
- Store configuration
- Store tests
- Define dependencies
- Produce the final application artifact

Think of it as:

```text
demo/
    ↓
Complete Spring Boot application
```

Everything important for the application is inside this project folder.

---

# 4. `.mvn/`

```text
.mvn/
```

This is Maven-related configuration.

It is associated with the Maven Wrapper.

The Maven Wrapper allows a project to use a specified Maven version without requiring the developer to manually install/configure Maven globally.

This becomes useful when:

- You clone the project on another machine
- You join a new team
- CI/CD builds the project
- Different developers have different Maven installations

The wrapper helps standardize the build environment.

---

# 5. Why do we have Maven Wrapper?

Imagine this situation.

Developer A has:

```text
Maven 3.8.x
```

Developer B has:

```text
Maven 4.x
```

Your project may have been designed/tested with a particular Maven version.

Instead of saying:

> "Everyone manually install exactly this Maven version."

the project can provide a Maven Wrapper.

Then the project itself controls which Maven version should be used.

---

# 6. Files related to Maven Wrapper

You can see:

```text
.mvn/
mvnw
mvnw.cmd
```

These are related.

## `mvnw`

```text
mvnw
```

This is the Maven Wrapper script for Unix-like systems such as:

- Linux
- macOS

Example:

```bash
./mvnw spring-boot:run
```

---

# 7. `mvnw.cmd`

```text
mvnw.cmd
```

This is the Windows version of the Maven Wrapper script.

Since you are using Windows, you can use:

```cmd
mvnw.cmd spring-boot:run
```

or from a terminal:

```cmd
.\mvnw.cmd spring-boot:run
```

Depending on the terminal, you may also see:

```cmd
mvnw.cmd
```

---

# 8. Why use `mvnw` instead of `mvn`?

There are two common commands:

```bash
mvn
```

and:

```bash
./mvnw
```

`mvn` means:

> Use Maven installed on the machine.

`mvnw` means approximately:

> Use the Maven version configured for this project.

For a team project, the wrapper gives you more predictable builds.

---

# 9. `src/`

```text
src/
```

`src` means:

> Source

This is where the actual application source and tests live.

This is one of the most important folders.

Inside `src`, Spring Boot/Maven generally separates application code from test code.

```text
src/
│
├── main/
│
└── test/
```

Think:

```text
main = application code

test = test code
```

---

# 10. `src/main/`

```text
src/
└── main/
```

`main` contains the code/resources required for the actual application.

Everything under:

```text
src/main/
```

is considered production application content.

This means:

> This is what belongs to the actual application we want to build and run.

---

# 11. Why is it called `main`?

Because Maven uses a standard directory convention.

The standard Maven structure is:

```text
src/main/
src/test/
```

This makes the build lifecycle predictable.

Instead of every project inventing its own structure:

```text
code/
application/
production/
tests/
stuff/
```

Maven establishes conventions.

This is one of the important ideas behind Maven:

> Convention over configuration.

---

# 12. `src/main/java/`

```text
src/
└── main/
    └── java/
```

This folder contains Java source code.

For example:

```text
src/main/java/
```

may contain:

```text
Controller
Service
Repository
Entity
DTO
Configuration
Exception
Utility
etc.
```

In a Spring Boot project, most of your Java application code eventually lives here.

---

# 13. Why exactly `src/main/java`?

This is a Maven standard.

Maven understands:

```text
src/main/java
```

as:

> Production Java source code.

When Maven builds the project, it knows where to find Java files.

For example:

```text
src/main/java/com/example/demo/DemoApplication.java
```

will be recognized as Java production code.

---

# 14. Package: `com.example.demo`

Inside `java` you have:

```text
com.example.demo
```

This is a Java package.

The folder structure physically looks like:

```text
com/
└── example/
    └── demo/
```

IntelliJ displays it as:

```text
com.example.demo
```

because package folders can be compacted in the Project view.

---

# 15. What is a package?

A package is a way to organize Java classes.

For example:

```text
com.example.demo
```

can be thought of as a namespace/group.

If your application becomes large, you don't want 300 classes in one folder.

You may organize them like:

```text
com.example.demo
│
├── controller
├── service
├── repository
├── entity
├── dto
├── config
└── exception
```

This makes the codebase easier to manage.

---

# 16. Why does the package name look like a website?

You may wonder why we use:

```text
com.example.demo
```

rather than:

```text
myproject
```

Java projects traditionally use reverse-domain naming.

For example, if a company owns:

```text
example.com
```

its Java package may be:

```text
com.example
```

Then an application may use:

```text
com.example.orders
```

or:

```text
com.example.payment
```

This reduces the chance of package-name collisions.

---

# 17. Real-world package example

A real Spring Boot application might look like:

```text
com.company.payment
│
├── controller
│   └── PaymentController.java
│
├── service
│   └── PaymentService.java
│
├── repository
│   └── PaymentRepository.java
│
├── entity
│   └── Payment.java
│
├── dto
│   └── PaymentRequest.java
│
├── exception
│   └── PaymentException.java
│
└── config
    └── SecurityConfig.java
```

This is where your application grows beyond a tutorial.

---

# 18. `DemoApplication.java`

Your project contains:

```text
DemoApplication.java
```

This is normally the main Spring Boot application class.

Typical code looks like:

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

This class is extremely important.

---

# 19. What does `main()` do?

Java applications start from:

```java
public static void main(String[] args)
```

This is the Java entry point.

When you run:

```text
DemoApplication
```

Java starts from:

```java
main()
```

Then this line executes:

```java
SpringApplication.run(DemoApplication.class, args);
```

This starts the Spring Boot application.

---

# 20. What does `SpringApplication.run()` do?

Conceptually:

```text
main()
   ↓
SpringApplication.run()
   ↓
Spring Boot starts
   ↓
Spring Container starts
   ↓
Beans are created
   ↓
Configuration is loaded
   ↓
Embedded server starts
   ↓
Application is ready
```

This is a simplified view, but it is the correct mental model.

---

# 21. What is `@SpringBootApplication`?

You will usually see:

```java
@SpringBootApplication
```

This annotation tells Spring Boot that this is the primary application configuration class.

It effectively combines several Spring capabilities.

Conceptually, it includes functionality related to:

```text
@Configuration
@EnableAutoConfiguration
@ComponentScan
```

The exact mechanics are more detailed, but as a beginner this mental model is enough.

---

# 22. Why is `DemoApplication` usually at the top package?

Suppose:

```text
com.example.demo
    DemoApplication.java
```

and:

```text
com.example.demo.controller
    HelloController.java
```

Spring Boot's component scanning can discover classes below the application's package.

Conceptually:

```text
com.example.demo
       ↓
       ├── controller
       ├── service
       ├── repository
       └── other subpackages
```

This is one reason package structure matters.

---

# 23. `HelloWorld.java`

Your screenshot also shows:

```text
HelloWorld
```

This is your own Java class.

Unlike:

```text
DemoApplication.java
```

which was generated as the Spring Boot entry point,

`HelloWorld.java` is an application class you created.

For example:

```java
package com.example.demo;

public class HelloWorld {

    public String sayHello() {
        return "Hello World";
    }
}
```

This is simply a Java class unless you add Spring annotations or use it through another Spring component.

---

# 24. Important distinction: Java class vs Spring Bean

Having a Java class does NOT automatically mean it is a Spring Bean.

For example:

```java
public class HelloWorld {
}
```

is simply a Java class.

If you write:

```java
@Component
public class HelloWorld {
}
```

then Spring can discover it through component scanning and manage its lifecycle as a Spring Bean.

This distinction becomes very important in real Spring Boot development.

---

# 25. `src/main/resources/`

Now we move to:

```text
src/
└── main/
    ├── java/
    └── resources/
```

`resources` is for non-Java application resources.

Examples include:

```text
application.properties
application.yml
SQL files
templates
static files
messages.properties
JSON files
XML files
certificates/config files
```

The exact contents depend on the project.

---

# 26. Why don't we put `application.properties` inside Java?

Because it isn't Java source code.

For example:

```text
.java
```

files contain Java code.

Whereas:

```text
application.properties
```

contains configuration.

So Maven/Spring separates them:

```text
src/main/java
    → Java source

src/main/resources
    → application resources/configuration
```

---

# 27. `application.properties`

A typical Spring Boot project may eventually contain:

```text
src/main/resources/
└── application.properties
```

Example:

```properties
server.port=8081

spring.datasource.url=jdbc:mysql://localhost:3306/testdb
spring.datasource.username=root
spring.datasource.password=secret
```

Spring Boot reads these properties during application startup.

---

# 28. `application.yml`

Instead of:

```text
application.properties
```

you can also use:

```text
application.yml
```

Example:

```yaml
server:
  port: 8081

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/testdb
    username: root
```

Both are commonly used configuration formats.

---

# 29. `resources` is more than just configuration

Do not think:

```text
resources = application.properties only
```

That's incorrect.

It is a general resource directory.

For example:

```text
resources/
│
├── application.properties
│
├── static/
│   ├── index.html
│   ├── css/
│   └── js/
│
├── templates/
│   └── home.html
│
└── messages.properties
```

The actual folders depend on the application.

---

# 30. `src/test/`

Now look at:

```text
src/
│
├── main/
│
└── test/
```

`src/test` contains test code.

This code is generally not part of the production application.

For example:

```text
src/test/java/
```

may contain:

```text
DemoApplicationTests.java
UserServiceTest.java
PaymentControllerTest.java
```

---

# 31. Why separate `main` and `test`?

Because production code and testing code have different purposes.

```text
src/main
    ↓
Actual application

src/test
    ↓
Tests used to verify the application
```

During a normal build, Maven knows how to compile and execute tests according to its lifecycle.

---

# 32. Example test structure

A project might look like:

```text
src/
│
├── main/
│   ├── java/
│   └── resources/
│
└── test/
    └── java/
        └── com.example.demo/
            └── UserServiceTest.java
```

A test could verify:

```text
Given user ID = 10
When service is called
Then user information should be returned
```

---

# 33. `target/`

Your screenshot shows:

```text
target/
```

This is very important.

`target` is the Maven build output directory.

You generally do NOT manually write application source code here.

Maven creates it during the build process.

---

# 34. What goes into `target/`?

Depending on the build, you may see:

```text
target/
│
├── classes/
├── test-classes/
├── generated-sources/
├── generated-test-sources/
├── reports/
└── demo-0.0.1-SNAPSHOT.jar
```

The exact contents depend on the project and build.

---

# 35. `target/classes`

This contains compiled application classes.

For example:

```text
src/main/java/com/example/demo/DemoApplication.java
```

gets compiled into bytecode.

Conceptually:

```text
.java
  ↓
javac
  ↓
.class
```

So:

```text
src/main/java
       ↓
 compile
       ↓
target/classes
```

---

# 36. `target/test-classes`

Test Java files are also compiled.

For example:

```text
src/test/java/com/example/demo/UserServiceTest.java
```

can result in compiled test classes under:

```text
target/test-classes/
```

---

# 37. `target` is generated

This is the key point:

```text
src/
    → source created by developers

target/
    → build output generated by Maven
```

You normally edit:

```text
src/
```

not:

```text
target/
```

---

# 38. Should `target/` be committed to Git?

Normally:

```text
No.
```

Because it is generated build output.

Usually `.gitignore` contains something similar to:

```gitignore
/target/
```

Then each developer/build server can generate it again.

---

# 39. `.gitattributes`

Next:

```text
.gitattributes
```

This is a Git configuration file.

It defines attributes for files in a Git repository.

It can control things such as:

- Line-ending behavior
- How files are treated by Git
- Certain merge/diff behaviors

For a beginner:

```text
.gitattributes
    ↓
Git-related configuration
```

It is not a Spring Boot file.

---

# 40. `.gitignore`

Next:

```text
.gitignore
```

This tells Git:

> Do not track these files/folders.

Typical Java/Spring projects may ignore:

```text
target/
.idea/
*.class
```

and other machine-generated files.

---

# 41. Why do we need `.gitignore`?

Suppose Maven generates:

```text
target/
```

Every developer will generate their own `target`.

There is generally no value in committing that generated output.

So:

```text
.gitignore
    ↓
prevents unnecessary files from entering Git
```

---

# 42. `.gitignore` example

You might see:

```gitignore
target/
.idea/
*.iml
```

Meaning approximately:

```text
target/
    → ignore build output

.idea/
    → ignore IntelliJ-specific files

*.iml
    → ignore IntelliJ module files
```

The exact file may vary.

---

# 43. `HELP.md`

Spring Initializr often creates:

```text
HELP.md
```

This is documentation generated with the project.

It may contain information about:

- Spring Boot references
- Links to documentation
- Helpful project-related resources

It is not required for your Java application logic.

Think:

```text
HELP.md
    ↓
Documentation
```

---

# 44. `pom.xml`

This is one of the most important files in a Maven-based Spring Boot project.

```text
pom.xml
```

POM means:

> Project Object Model

This file describes the Maven project.

It tells Maven information such as:

- Project identity
- Spring Boot version
- Java version
- Dependencies
- Plugins
- Build configuration
- Packaging information

---

# 45. Think of `pom.xml` as the project's control document

A useful mental model is:

```text
pom.xml
    ↓
Defines how this Java/Maven project should be built
```

It does NOT contain your business logic.

For example, you would not normally put:

```java
public void processPayment() {
}
```

inside `pom.xml`.

That belongs in Java source code.

---

# 46. Basic POM structure

A simplified `pom.xml` looks like:

```xml
<project>

    <modelVersion>4.0.0</modelVersion>

    <parent>
        ...
    </parent>

    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        ...
    </properties>

    <dependencies>
        ...
    </dependencies>

    <build>
        ...
    </build>

</project>
```

Now let's understand each section.

---

# 47. `<project>`

The root element is:

```xml
<project>
```

Everything in the Maven POM is inside this element.

---

# 48. `<modelVersion>`

Usually:

```xml
<modelVersion>4.0.0</modelVersion>
```

This identifies the POM model version used by Maven.

For beginners:

```text
modelVersion
    ↓
Version of the Maven POM model
```

This is normally not something you modify casually.

---

# 49. `<parent>`

Spring Boot projects often use:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>...</version>
</parent>
```

The Spring Boot parent provides sensible defaults and dependency-management behavior.

One major advantage is that Spring Boot can manage compatible dependency versions for you.

---

# 50. Why is the parent useful?

Without dependency management, you could end up specifying many versions manually:

```xml
<dependency>
    <groupId>...</groupId>
    <artifactId>...</artifactId>
    <version>...</version>
</dependency>
```

Spring Boot provides coordinated versions.

This reduces version-management overhead and compatibility problems.

---

# 51. `<groupId>`

Example:

```xml
<groupId>com.example</groupId>
```

This identifies the organization/group that owns the project.

In a real company it could be:

```text
com.company
com.company.payment
com.company.trading
```

---

# 52. `<artifactId>`

Example:

```xml
<artifactId>demo</artifactId>
```

This is the name of the Maven artifact/project.

For example:

```text
payment-service
order-service
customer-service
```

The artifact name often contributes to the generated JAR name.

For example:

```text
payment-service-1.0.0.jar
```

---

# 53. `<version>`

Example:

```xml
<version>0.0.1-SNAPSHOT</version>
```

This is the project version.

`SNAPSHOT` generally indicates ongoing development rather than a final immutable release.

Example progression:

```text
0.0.1-SNAPSHOT
0.0.1
0.0.2
1.0.0
```

---

# 54. `<name>`

Example:

```xml
<name>demo</name>
```

This is the project name.

It is mainly project metadata.

---

# 55. `<description>`

Example:

```xml
<description>Demo project for Spring Boot</description>
```

This describes the project.

Again, this is metadata/documentation, not application logic.

---

# 56. `<properties>`

Example:

```xml
<properties>
    <java.version>21</java.version>
</properties>
```

Properties allow project-level values to be defined.

For example:

```xml
<java.version>21</java.version>
```

means the project is configured around Java 21.

You may also see properties for:

- Maven compiler settings
- encoding
- dependency/plugin versions
- custom build values

depending on the project.

---

# 57. `<dependencies>`

This is one of the most important sections.

Example:

```xml
<dependencies>

    <dependency>
        ...
    </dependency>

    <dependency>
        ...
    </dependency>

</dependencies>
```

A dependency means:

> My project needs this external library to compile/run/test.

---

# 58. Why do we need dependencies?

Suppose you want to create a REST API.

You could theoretically write a huge amount of infrastructure yourself.

But Spring Boot provides libraries for things like:

```text
HTTP
Spring MVC
JSON
Database access
Security
Validation
Testing
Logging
etc.
```

Instead of implementing all of that from scratch, you declare dependencies.

Then Maven downloads the required libraries.

---

# 59. Example dependency

You may see:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webmvc</artifactId>
</dependency>
```

This says:

```text
Project needs Spring Boot's Web MVC starter
```

Maven then resolves the dependency and its transitive dependencies.

---

# 60. What are transitive dependencies?

Suppose you declare:

```text
spring-boot-starter-webmvc
```

You may not have manually listed every library that Spring MVC requires.

But the starter itself depends on other libraries.

Conceptually:

```text
spring-boot-starter-webmvc
        ↓
    Spring MVC
        ↓
    other required libraries
```

Maven resolves this dependency graph automatically.

This is called:

> Transitive dependency management

---

# 61. Another dependency: testing

You may have something like:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webmvc-test</artifactId>
    <scope>test</scope>
</dependency>
```

This provides libraries needed for web/MVC testing.

---

# 62. What does `<scope>test</scope>` mean?

This is important.

```xml
<scope>test</scope>
```

means this dependency is intended for testing.

Conceptually:

```text
Production application
    ↓
doesn't need this as normal production dependency

Tests
    ↓
can use it
```

Other Maven scopes exist, such as:

```text
compile
provided
runtime
test
system
import
```

You do not need to memorize all of them initially, but understanding `test` is useful.

---

# 63. `<build>`

You may see:

```xml
<build>
    <plugins>
        ...
    </plugins>
</build>
```

This controls build-related behavior.

For example:

- Maven plugins
- Packaging behavior
- Build configuration
- Resource handling
- Compilation behavior

---

# 64. `spring-boot-maven-plugin`

A Spring Boot project commonly contains:

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

This plugin integrates Spring Boot with Maven.

It helps with Spring Boot-specific build tasks such as creating an executable JAR.

Conceptually:

```text
Java source
    ↓
Maven build
    ↓
Spring Boot Maven Plugin
    ↓
Executable application artifact
```

---

# 65. Difference between Dependency and Plugin

This is a common interview question.

## Dependency

A dependency is a library your application uses.

Example:

```text
Spring MVC
Jackson
JPA
PostgreSQL driver
JUnit
```

Your code uses classes provided by dependencies.

## Plugin

A Maven plugin performs build-related operations.

Example:

```text
compile
test
package
Spring Boot packaging
```

So:

```text
Dependency
    → used by application/build classpath

Plugin
    → used by Maven build process
```

---

# 66. Where do I run the Spring Boot application?

There are multiple ways.

The easiest method in IntelliJ is:

Open:

```text
DemoApplication.java
```

Find:

```java
public static void main(String[] args)
```

Then click the green Run button next to the class/method.

Or right-click:

```text
DemoApplication.java
```

and choose:

```text
Run 'DemoApplication.main()'
```

---

# 67. What happens when you click Run?

Conceptually:

```text
Click Run
    ↓
IntelliJ launches Java
    ↓
DemoApplication.main()
    ↓
SpringApplication.run()
    ↓
Spring Boot starts
    ↓
Spring Container starts
    ↓
Beans are discovered/created
    ↓
Configuration is loaded
    ↓
Embedded server starts
    ↓
Application becomes ready
```

---

# 68. Where do I run Maven commands?

You can use IntelliJ's Terminal.

For example:

```bash
./mvnw clean
```

or on Windows:

```cmd
.\mvnw.cmd clean
```

Run the command from the directory containing:

```text
pom.xml
```

Example:

```text
demo/
├── pom.xml
├── src/
└── mvnw.cmd
```

Open terminal in this directory.

---

# 69. Common Maven commands

## Run tests

```bash
./mvnw test
```

Windows:

```cmd
.\mvnw.cmd test
```

---

## Clean build output

```bash
./mvnw clean
```

This removes generated build output such as:

```text
target/
```

---

## Package the application

```bash
./mvnw package
```

This compiles, tests, and packages the application according to the Maven lifecycle.

---

## Clean and package

```bash
./mvnw clean package
```

This is very common.

Conceptually:

```text
delete old target
      ↓
compile
      ↓
test
      ↓
package
```

---

# 70. Running using Maven

Spring Boot also supports:

```bash
./mvnw spring-boot:run
```

On Windows:

```cmd
.\mvnw.cmd spring-boot:run
```

This starts the Spring Boot application through Maven.

---

# 71. IntelliJ Run vs Maven Run

There are two common ways.

## IntelliJ

```text
Run DemoApplication
```

IntelliJ directly launches your Java application.

## Maven

```text
mvnw spring-boot:run
```

Maven invokes the Spring Boot Maven plugin to run it.

Both can start the application.

---

# 72. What happens to `.java` files when building?

Imagine:

```text
src/main/java/com/example/demo/DemoApplication.java
```

You run:

```bash
mvnw package
```

Conceptually:

```text
.java source
      ↓
Java compiler
      ↓
.class bytecode
      ↓
target/classes
      ↓
packaging
      ↓
JAR
```

---

# 73. What is a JAR?

JAR means:

> Java ARchive

It is a packaged Java application artifact.

For example:

```text
target/demo-0.0.1-SNAPSHOT.jar
```

A Spring Boot executable JAR can contain:

- Application classes
- Resources
- Required dependencies
- Spring Boot launcher infrastructure

This allows the application to be run as a packaged application.

---

# 74. Running the packaged JAR

After packaging, you can typically run:

```bash
java -jar target/demo-0.0.1-SNAPSHOT.jar
```

Conceptually:

```text
Your source code
      ↓
Maven build
      ↓
JAR
      ↓
java -jar
      ↓
Spring Boot application starts
```

---

# 75. Outer `target` vs inner `target`

Your screenshot appears to show:

```text
demo/
│
├── demo/
│   └── target/
│
└── target/
```

This can look confusing.

The important thing is to check which folder actually contains your:

```text
pom.xml
```

The Maven project is normally the directory containing:

```text
pom.xml
src/
mvnw
mvnw.cmd
```

The `target` associated with that project is the build output for that Maven project.

The extra outer `target` can be related to how the directory/project was opened or created and is not necessarily the application source structure you should focus on.

---

# 76. External Libraries

In IntelliJ you may see:

```text
External Libraries
```

This is not a physical application folder like `src`.

IntelliJ uses this section to display libraries available to the project.

For example:

```text
JDK
Spring libraries
Jackson
JUnit
Tomcat
etc.
```

Many of these are downloaded/resolved through Maven.

---

# 77. Where do these external libraries come from?

Your `pom.xml` declares dependencies.

For example:

```xml
<dependency>
    ...
</dependency>
```

Maven resolves them from configured repositories.

Typically Maven downloads artifacts into the local Maven repository.

Then IntelliJ adds them to the project's classpath.

Conceptually:

```text
pom.xml
   ↓
Maven
   ↓
Repository
   ↓
Download dependency
   ↓
Local Maven repository
   ↓
IntelliJ classpath
   ↓
External Libraries
```

---

# 78. What is the Classpath?

The classpath is essentially the set of locations/libraries Java uses to find classes/resources required by the application.

For example, if your code says:

```java
import org.springframework.boot.SpringApplication;
```

Java needs to find the class:

```text
org.springframework.boot.SpringApplication
```

That class comes from a Spring Boot library available through the project classpath.

---

# 79. What if I remove the dependency?

Suppose you remove the dependency that provides:

```java
SpringApplication
```

Then IntelliJ may show:

```text
Cannot resolve symbol SpringApplication
```

and Maven compilation can fail.

This is why dependencies matter.

---

# 80. Scratches and Consoles

IntelliJ also displays:

```text
Scratches and Consoles
```

This is an IntelliJ feature.

It is useful for temporary work such as:

- Testing code snippets
- SQL experiments
- Temporary scripts
- Quick notes
- Temporary files

It is not part of your Spring Boot application's production structure.

---

# 81. Most Important Folder Distinction

Memorize this:

```text
src/main/java
    ↓
Java production code

src/main/resources
    ↓
Production configuration/resources

src/test
    ↓
Test code

target
    ↓
Maven-generated build output

pom.xml
    ↓
Maven project configuration

.idea
    ↓
IntelliJ configuration

.gitignore
    ↓
files Git should ignore
```

---

# 82. How everything connects

This is the most important mental model.

```text
                    pom.xml
                       │
                       ↓
                     Maven
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
     Dependencies     Plugins     Build config
          │
          ↓
     External Libraries
          │
          │
          ↓
src/main/java ───────────────┐
          │                  │
          ↓                  │
     Java source             │
                             ↓
src/main/resources ───→ Spring Boot Application
                             │
                             ↓
                        Compilation
                             │
                             ↓
                         target/
                             │
                             ↓
                            JAR
```

---

# 83. Application startup flow

When you run:

```text
DemoApplication
```

the high-level flow is:

```text
DemoApplication.main()
          ↓
SpringApplication.run()
          ↓
Create Spring Application
          ↓
Create/prepare Spring Environment
          ↓
Load configuration
          ↓
Create ApplicationContext
          ↓
Component scanning
          ↓
Bean definitions
          ↓
Bean creation
          ↓
Dependency injection
          ↓
Auto-configuration
          ↓
Embedded server startup
          ↓
Application ready
```

This is simplified but is a good architecture-level understanding.

---

# 84. Real-world Spring Boot package structure

As your project becomes bigger, it may evolve from:

```text
com.example.demo
├── DemoApplication.java
└── HelloWorld.java
```

into:

```text
com.company.payment
│
├── PaymentApplication.java
│
├── controller/
│   └── PaymentController.java
│
├── service/
│   └── PaymentService.java
│
├── repository/
│   └── PaymentRepository.java
│
├── entity/
│   └── Payment.java
│
├── dto/
│   ├── PaymentRequest.java
│   └── PaymentResponse.java
│
├── exception/
│   ├── PaymentException.java
│   └── GlobalExceptionHandler.java
│
├── config/
│   └── SecurityConfig.java
│
└── util/
    └── PaymentUtil.java
```

And:

```text
src/main/resources/
├── application.yml
├── static/
├── templates/
└── messages.properties
```

Tests may be:

```text
src/test/java/com/company/payment/
├── controller/
├── service/
└── repository/
```

---

# 85. A practical rule for where to put files

When creating a new file, ask:

## Is it Java code?

Put it under:

```text
src/main/java
```

or for tests:

```text
src/test/java
```

## Is it configuration/resource?

Put it under:

```text
src/main/resources
```

## Is it generated build output?

It belongs under:

```text
target
```

but you normally DON'T manually create/edit it.

## Is it Maven configuration?

Put it in:

```text
pom.xml
```

## Is it IntelliJ-specific?

It belongs to:

```text
.idea
```

---

# 86. What should I personally edit?

As a developer, you will frequently edit:

```text
src/main/java/
src/main/resources/
src/test/java/
pom.xml
```

You may occasionally edit:

```text
.gitignore
application.yml
```

You generally should NOT manually edit:

```text
target/
```

And you generally don't need to manually manage:

```text
.idea/
```

unless you specifically need IDE configuration.

---

# 87. Beginner mental model

Think of the project like a company.

```text
pom.xml
    = Company operating/build rules

src/main/java
    = Actual employees doing the business work

src/main/resources
    = Company configuration/resources

src/test
    = Quality Assurance department

target
    = Finished/generated output

.idea
    = Your personal office/IDE setup

.gitignore
    = "Do not put these things into Git"
```

This is not technically exact, but it is a useful mental model.

---

# 88. 3-Year Developer mental model

At a professional level, think in terms of responsibilities:

```text
Source
    → maintainable application code

Configuration
    → environment/application behavior

Dependencies
    → external capabilities

Build
    → reproducible artifact generation

Tests
    → quality verification

Artifact
    → deployable application

IDE metadata
    → developer tooling

Version control metadata
    → source-management behavior
```

This separation becomes important in large enterprise systems.

---

# 89. Final project map

The easiest version to memorize is:

```text
demo/
│
├── .idea/
│       → IntelliJ configuration
│
├── src/
│   │
│   ├── main/
│   │   │
│   │   ├── java/
│   │   │      → Java application code
│   │   │
│   │   └── resources/
│   │          → configuration + resources
│   │
│   └── test/
│          → test code
│
├── target/
│       → Maven-generated build output
│
├── .gitattributes
│       → Git file attributes
│
├── .gitignore
│       → files Git should ignore
│
├── HELP.md
│       → generated help/documentation
│
├── .mvn/
│       → Maven Wrapper configuration
│
├── mvnw
│       → Maven Wrapper for Linux/macOS
│
├── mvnw.cmd
│       → Maven Wrapper for Windows
│
└── pom.xml
        → Maven project configuration
```

---

# 90. Most important files in your current project

For your current beginner project, focus primarily on these:

```text
DemoApplication.java
HelloWorld.java
pom.xml
src/main/resources/
src/test/
```

Understand these thoroughly before worrying too much about:

```text
.idea
.gitattributes
HELP.md
target
```

---

# 91. Quick Interview Questions

## Q1. Where does production Java code go?

```text
src/main/java
```

## Q2. Where does configuration go?

Usually:

```text
src/main/resources
```

## Q3. Where do tests go?

```text
src/test
```

## Q4. What is `pom.xml`?

```text
Maven Project Object Model
```

It defines project/build/dependency configuration.

## Q5. What is `target`?

Generated Maven build output.

## Q6. What is `.idea`?

IntelliJ IDEA project metadata/configuration.

## Q7. What does `mvnw` do?

Runs Maven through the project's Maven Wrapper.

## Q8. What starts the Spring Boot application?

Usually:

```java
public static void main(String[] args)
```

which calls:

```java
SpringApplication.run(...)
```

## Q9. Where do dependencies come from?

They are declared in:

```text
pom.xml
```

and resolved by Maven.

## Q10. What is a dependency?

An external library/capability required by the project.

---

# 92. One final mental picture

Always visualize your Spring Boot project like this:

```text
                    YOUR SPRING BOOT PROJECT
                              │
                              │
                         ┌────┴────┐
                         │ pom.xml │
                         └────┬────┘
                              │
                           Maven
                              │
            ┌─────────────────┼─────────────────┐
            │                 │                 │
            ↓                 ↓                 ↓
      Dependencies          Build             Plugins
            │
            ↓
    External Libraries


src/
 │
 ├── main/
 │    │
 │    ├── java/
 │    │      └── Your Java application
 │    │
 │    └── resources/
 │           └── Configuration/resources
 │
 └── test/
        └── Tests


Java Application
      ↓
SpringApplication.run()
      ↓
Spring Boot
      ↓
Spring Container
      ↓
Beans + Configuration
      ↓
Embedded Server
      ↓
Running Application
```

This is the core structure you should keep in your head while learning Spring Boot.
