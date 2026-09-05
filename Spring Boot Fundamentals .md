# 🌱 **Spring Boot** Fundamentals

> **Level:** Beginner-friendly explanation with the depth expected from a developer with around 3 years of experience.  
> **Focus:** Understand the *why*, not just the annotations. Each topic includes the idea, practical example, when to use it, common doubts, and the relationship with the other Spring concepts.

---

##  **🧭 1. The Big Picture — What **Spring Boot** Is Actually Doing**
The easiest way to understand **Spring Boot** is to stop thinking of it as "a set of annotations" and instead think of it as a framework that **takes responsibility for starting your application, creating/managing objects, wiring those objects together, loading configuration, and setting up common infrastructure**.

In plain Java, you normally control object creation yourself:

```java
PaymentService paymentService = new PaymentService();
OrderService orderService = new OrderService(paymentService);
```

You decide **what gets created, when it gets created, and which object gets passed where**.

With Spring, you describe what your application needs and Spring takes over much of that responsibility:

```text
Your application
      ↓
Spring Boot
      ↓
Spring Container / ApplicationContext
      ↓
Creates and manages Beans
      ↓
Injects their dependencies
      ↓
Configures required infrastructure
      ↓
Application becomes ready
```

So the high-level startup flow is:

```text
JVM starts
   ↓
main()
   ↓
SpringApplication.run(...)
   ↓
Spring Boot starts
   ↓
ApplicationContext is created
   ↓
Configuration is prepared
   ↓
Components are scanned
   ↓
Beans are registered
   ↓
Auto-configuration is applied
   ↓
Beans are created
   ↓
Dependencies are injected
   ↓
Embedded server starts (web application)
   ↓
CommandLineRunner / ApplicationRunner runs
   ↓
Application is ready
```

**Core idea:** **Spring Boot** is doing the infrastructure and object-management work so your code can focus more on business logic.

---

##  **🔄 2. **IoC** — Inversion of Control**
**IoC means Spring takes control of creating and managing application objects instead of your application code doing it directly.**

Without Spring:

```java
PaymentService paymentService = new PaymentService();
```

You are saying:

> "I will create and manage this object."

With Spring:

```java
@Component
public class PaymentService {
}
```

you are telling Spring that this class can be managed as a **Bean**. Spring can then create the object and manage its lifecycle.

A useful comparison is:

```text
Without IoC
    ↓
You create objects
    ↓
You connect objects
    ↓
You manage them


With IoC
    ↓
Spring creates objects
    ↓
Spring connects objects
    ↓
Spring manages them
```

### Why do we need **IoC**?

For a tiny program, manual `new` statements are fine. In a real application, you might have:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

and dozens or hundreds of classes.

If every class creates its own dependencies, the application becomes **tightly coupled** (classes depend directly on concrete implementations and know too much about object creation).

Spring takes that responsibility out of your business classes.

### Easy analogy

Think of an office.

Without **IoC**:

> Every employee has to arrange their own laptop, desk, internet connection, and equipment.

With **IoC**:

> The office infrastructure team provides those things. Employees simply use them.

Spring is acting like that infrastructure team.

**Remember:**  
**IoC = Spring takes control of object creation and management.**

---

##  **📦 3. **Spring Container** & ApplicationContext**
The **Spring Container** is the system that creates and manages Spring Beans. It knows which Beans exist, what they depend on, and when they should be created or destroyed.

A simplified view:

```text
                 Spring Container
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
 PaymentService    EmailService    OrderService
     Bean              Bean             Bean
```

The commonly used Spring container abstraction is the **`**ApplicationContext**`**.

For learning, think of:

```text
Spring Container
      ↓
ApplicationContext
      ↓
Central place where Spring manages Beans
```

It is responsible for things such as:

- Creating and managing Beans
- Wiring dependencies
- Reading application configuration
- Applying conditions
- Managing **Bean** lifecycle
- Supporting application events and other framework services

### Why does this matter?

Suppose you write:

```java
public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

You did not create the `PaymentService`.

Spring can look at the **ApplicationContext**, find the appropriate `PaymentService` **Bean**, and provide it to the `OrderService` constructor.

**Important distinction:**

```text
ApplicationContext
    → manages Spring Beans

Java heap
    → contains Java objects in general
```

Not every Java object is necessarily a **Spring Bean**.

---

##  **🫘 4. What Is a **Spring Bean**?**
A **Bean is simply an object managed by Spring**.

For example:

```java
@Component
public class PaymentService {
}
```

Spring can create an instance of `PaymentService` and register it in its container.

Conceptually:

```text
PaymentService class
        ↓
Spring creates object
        ↓
Spring manages object
        ↓
PaymentService Bean
```

### Is every Java object a **Bean**?

**No.**

This:

```java
PaymentService service = new PaymentService();
```

creates a normal Java object.

That object is not automatically a Spring-managed **Bean** just because it is a Java object.

A useful rule is:

> **Bean = object that Spring knows about and manages.**

---

##  **🔎 5. How Does Spring Know Which Classes to Manage?**
Spring commonly uses **component scanning**.

Classes annotated with Spring stereotypes can be discovered:

```java
@Component
@Service
@Repository
@Controller
@RestController
```

The simplified flow is:

```text
@SpringBootApplication
        ↓
Component Scanning
        ↓
Search application packages
        ↓
Find Spring components
        ↓
Register them as Beans
```

### Why does package location matter?

Suppose your main class is in:

```text
com.example.demo
```

and your classes are in:

```text
com.example.demo.controller
com.example.demo.service
com.example.demo.repository
```

These are subpackages of the application package, so Spring can discover them through **component scanning**.

That's why a common structure is:

```text
com.example.demo
├── DemoApplication
├── controller/
├── service/
└── repository/
```

### Easy rule

Keep the main **Spring Boot** application class at the appropriate **root package** so your application components are naturally inside the scanning area.

**Remember:**  
**Component scanning finds candidate classes; Spring then registers/creates the corresponding Beans.**

---

##  **🚀 6. `**@SpringBootApplication**` and Application Startup**
A typical **Spring Boot** application starts like this:

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

`**@SpringBootApplication**` is a convenience annotation that brings together major **Spring Boot** functionality. For learning purposes, remember it conceptually as:

```text
@Configuration
+
@ComponentScan
+
@EnableAutoConfiguration
```

That gives the application three major capabilities:

```text
Configuration
    → application configuration can be defined

Component Scan
    → discover Spring components

Auto-Configuration
    → Spring Boot configures common infrastructure automatically
```

### `main()` vs `SpringApplication.run()`

Java starts with:

```java
public static void main(String[] args)
```

Then:

```java
SpringApplication.run(DemoApplication.class, args);
```

bootstraps the Spring application.

Think:

```text
JVM
 ↓
main()
 ↓
SpringApplication.run()
 ↓
Spring Boot startup
```

---

##  **🏗️ 7. **Spring Boot** Startup Flow — What Happens After You Click Run?**
When you click **Run** in IntelliJ, Java executes `main()` first.

The detailed high-level flow is:

```text
1. JVM starts
        ↓
2. main() executes
        ↓
3. SpringApplication.run(...) is called
        ↓
4. Spring Boot prepares the application
        ↓
5. ApplicationContext is created
        ↓
6. Environment is prepared
   - application.properties / application.yml
   - command-line arguments
   - active profiles
        ↓
7. Components are discovered
        ↓
8. Bean definitions are registered
        ↓
9. Auto-configuration is applied
        ↓
10. ApplicationContext is refreshed
        ↓
11. Beans are created
        ↓
12. Dependencies are injected
        ↓
13. Bean lifecycle callbacks happen
        ↓
14. Embedded web server starts
        ↓
15. CommandLineRunner / ApplicationRunner executes
        ↓
16. Application is fully started
```

### What does "necessary startup work" mean?

It simply means **the work Spring needs to perform to make your application operational**, such as:

```text
Read configuration
Create ApplicationContext
Discover components
Register Beans
Apply auto-configuration
Create Beans
Inject dependencies
Initialize lifecycle callbacks
Start web infrastructure
Run startup callbacks
```

One important correction:

> Spring does **not** simply "execute all dependencies."

A dependency is a library your application uses. Spring uses the relevant classes and infrastructure from those libraries during startup.

---

##  **⚙️ 8. Auto-Configuration**
**Auto-Configuration means **Spring Boot** automatically configures common infrastructure based on the application's classpath, dependencies, configuration, and conditions.**

For example, when web support is present:

```text
Web dependencies
      ↓
Spring Boot recognizes web application
      ↓
Common MVC/web infrastructure is configured
      ↓
Embedded server can be started
```

Instead of manually configuring every piece of the web stack, **Spring Boot** provides sensible defaults.

### Why is **Auto-Configuration** useful?

Without **Spring Boot**, you would have to configure much more framework infrastructure yourself.

**Spring Boot** follows the idea:

```text
You declare what you need
        ↓
Spring Boot provides sensible defaults
```

This is one of the biggest productivity benefits of **Spring Boot**.

### Simple analogy

You say:

> "I am building a web application."

**Spring Boot** says:

> "Okay, I will configure the common web infrastructure for you."

---

##  **🔌 9. **Dependency Injection** (**DI**)**
**Dependency Injection means an object receives the objects it needs instead of creating those objects itself.**

Suppose:

```text
OrderService
    needs
PaymentService
```

Without **DI**:

```java
public class OrderService {

    private PaymentService paymentService =
            new PaymentService();
}
```

`OrderService` is responsible for creating its dependency.

With **DI**:

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

Now Spring can provide the dependency.

The mental model is:

```text
OrderService
      ↓
"I need PaymentService."
      ↓
Spring provides PaymentService
```

### Why is **DI** useful?

**DI** reduces the responsibility of classes.

Instead of:

> "I need a PaymentService, so I will create one."

the class says:

> "I need a PaymentService. Someone else can provide it."

That makes the application easier to test, replace, and maintain.

---

##  **🔁 10. **IoC** vs **DI** — Do Not Mix Them Up**
These concepts are related, but they are not identical.

### **IoC**

**IoC** is the **broader principle**:

> Spring takes control of object creation and management.

### **DI**

**DI** is one of the main mechanisms used to implement that idea:

> Spring provides dependencies to an object.

Easy memory:

```text
IoC
 ↓
WHO controls object creation?
 ↓
Spring


DI
 ↓
HOW are dependencies provided?
 ↓
Spring injects them
```

So:

```text
IoC = broader principle
DI  = dependency-provision mechanism
```

---

##  **🏗️ 11. Constructor Injection**
**Constructor Injection means the dependency enters the class through its constructor.**

Example:

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

Spring conceptually does something similar to:

```text
Create PaymentService
        ↓
Create OrderService
        ↓
Pass PaymentService into OrderService constructor
```

You did not write:

```java
new PaymentService()
```

inside `OrderService`.

Spring provides the dependency.

### Why is **Constructor Injection** preferred?

It makes required dependencies explicit.

If the class cannot work without `PaymentService`, the constructor shows that immediately:

```java
public OrderService(PaymentService paymentService)
```

It also works naturally with `**final**` fields and makes unit testing straightforward because the test can provide the dependency directly.

### Good default pattern

```java
private final PaymentService paymentService;

public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

---

##  **🔒 12. `**private final**` — Why Do We Usually Use It?**
In constructor injection, you will very often see:

```java
private final PaymentService paymentService;
```

These two keywords do different jobs.

### `private`

Only the class itself directly accesses the field.

### `**final**`

The field/reference cannot be reassigned after initialization.

Example:

```java
public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

After that, this is not allowed:

```java
this.paymentService = anotherPaymentService;
```

because the field is `**final**`.

### Why is `**final**` a good fit for dependencies?

Because a **required dependency** is usually part of the object's identity and should not suddenly be replaced after construction.

The pattern becomes:

```text
Required dependency
        ↓
Constructor Injection
        ↓
private final field
```

This is why you see this pattern so often in professional Spring applications.

### When should I NOT use `**final**`?

Do not use `**final**` when you intentionally need the field to be reassigned.

Example:

```java
private String status;

public void changeStatus(String status) {
    this.status = status;
}
```

Here the value changes:

```text
PENDING
   ↓
PROCESSING
   ↓
COMPLETED
```

So `**final**` would not make sense.

### Important distinction

```java
private final PaymentService paymentService;
```

does **not** mean the `PaymentService` object itself can never change internally.

It means the field cannot be reassigned to a different object.

Think:

```text
final
    ↓
"Do not replace this reference."
```

not:

```text
"The entire object is immutable."
```

---

##  **🪝 13. `**@Autowired**` — What It Actually Does**
`**@Autowired**` identifies an **injection point** for Spring.

Example:

```java
@Autowired
public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

Keep the concepts separate:

```text
Constructor Injection
    → HOW the dependency enters

@Autowired
    → explicitly marks WHERE Spring should inject
```

### Do we need `**@Autowired**` on a constructor?

If the class has **only one constructor**, usually **no**.

Both work:

```java
public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

and:

```java
@Autowired
public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

So in this situation:

```text
With @Autowired
    → works

Without @Autowired
    → works

Practical result
    → same
```

### Why did your experiment show no difference?

Because your class had one constructor. Spring already knows that this is the constructor it should use.

### When is `**@Autowired**` useful on a constructor?

When there are multiple constructors and you want to clearly identify the one Spring should use.

Example:

```java
@Service
public class OrderService {

    public OrderService() {
    }

    @Autowired
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

Here:

```text
Multiple constructors
        ↓
Spring needs an injection constructor
        ↓
@Autowired identifies it
```

### Important: Java DOES allow multiple constructors

This is valid Java:

```java
public class Car {

    public Car() {
    }

    public Car(String color) {
    }
}
```

The issue is not that Java forbids multiple constructors.

The issue is:

> **When Spring creates the **Bean**, which constructor should Spring use?**

---

##  **🧩 14. Three Main **Dependency Injection** Styles**
### **Constructor Injection**

```java
private final PaymentService paymentService;

public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

**Best default for required dependencies.**

### **Field Injection**

```java
@Autowired
private PaymentService paymentService;
```

Spring creates the object and then injects the field.

It is supported and simple, but generally not the preferred default for required dependencies because the dependency is less explicit and the field cannot naturally be `**final**`.

### **Setter Injection**

```java
private PaymentService paymentService;

@Autowired
public void setPaymentService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

Spring creates the object and then calls the setter.

This can make sense when a dependency is optional or intentionally replaceable.

### Quick decision

```text
Required dependency
    → Constructor Injection

Optional/changeable dependency
    → Setter Injection can make sense

Field Injection
    → Supported, but generally avoid as your default
```

---

##  **🏷️ 15. `**@Component**`, `**@Service**`, `**@Repository**`, `**@Controller**`, `**@RestController**`**
These annotations help Spring identify classes that should participate in component management and communicate their responsibility.

### `**@Component**`

General-purpose Spring-managed component:

```java
@Component
public class PaymentService {
}
```

### `**@Service**`

Commonly used for business/service logic:

```java
@Service
public class OrderService {
}
```

### `**@Repository**`

Commonly used for persistence/database access:

```java
@Repository
public class OrderRepository {
}
```

### `**@Controller**`

Used for traditional Spring MVC controllers.

### `**@RestController**`

Used for REST APIs:

```java
@RestController
public class PaymentController {
}
```

A useful way to think about them:

```text
@Component
    → general component

@Service
    → business/service layer

@Repository
    → persistence layer

@Controller
    → MVC controller

@RestController
    → REST API controller
```

---

##  **🌐 16. `**@RestController**` — Your Earlier Doubt**
You asked:

> "Why are we writing `**@RestController**` when I am not calling through an endpoint?"

The answer is that `**@RestController**` does two conceptual things:

```text
1. Makes the class a Spring-managed component
2. Gives it REST controller semantics
```

But **it does not automatically execute your methods**.

Example:

```java
@RestController
public class PaymentController {

    @GetMapping("/pay")
    public String pay() {
        return "Payment successful";
    }
}
```

Only when a request arrives:

```text
GET /pay
   ↓
PaymentController.pay()
   ↓
"Payment successful"
```

So these are separate ideas:

```text
@RestController
    → this class is a REST controller

@GetMapping("/pay")
    → this method handles GET /pay

HTTP request
    → actually triggers the method
```

### Why did **Tomcat** start even before you called an endpoint?

Because the web dependency tells **Spring Boot** that the application is a web application.

Conceptually:

```text
Web dependency
      ↓
Spring Boot web app
      ↓
Embedded Tomcat starts
      ↓
Tomcat listens for HTTP requests
```

Your application can therefore be:

```text
Started ✅
Tomcat running ✅
No endpoint called yet ✅
```

---

##  **🛠️ 17. Manual **Bean** Creation — `**@Configuration**` + `**@Bean**`**
Sometimes you want Spring to manage an object, but you don't want or cannot put `**@Component**` on the class.

Example:

```java
public class PaymentService {

    private final String gateway;

    public PaymentService(String gateway) {
        this.gateway = gateway;
    }
}
```

You can explicitly register it:

```java
@Configuration
public class AppConfig {

    @Bean
    public PaymentService paymentService() {
        return new PaymentService("Stripe");
    }
}
```

### What exactly is happening?

This line:

```java
new PaymentService("Stripe")
```

is **manual object creation**.

But because the method is annotated:

```java
@Bean
```

Spring takes the returned object and registers it as a **Spring Bean**.

So:

```text
new PaymentService("Stripe")
        ↓
object created
        ↓
@Bean
        ↓
Spring Container
        ↓
managed Bean
```

### Why do we need `**@Configuration**`?

`**@Configuration**` tells Spring:

> "This class contains configuration instructions."

A common use is to define Beans.

---

##  **🆚 18. `**@Component**` vs `**@Bean**`**
These solve the same broad problem—getting an object into the **Spring Container**—but they are used differently.

### `**@Component**`

Put it directly on the class:

```java
@Component
public class PaymentService {
}
```

Think:

> "Spring, discover this class and manage it."

### `**@Bean**`

Put it on a method inside configuration:

```java
@Configuration
public class AppConfig {

    @Bean
    public PaymentService paymentService() {
        return new PaymentService("Stripe");
    }
}
```

Think:

> "Spring, use this method to create/register this object."

### When should I use which?

Use `**@Component**` / `**@Service**` when:

```text
You control the class
+
it naturally belongs as a Spring-managed application component
+
you don't need special creation logic
```

Use `**@Bean**` when:

```text
You need explicit creation/configuration
OR
the class is from a third-party library
OR
you need custom constructor arguments
OR
you need special setup
```

---

##  **📚 19. Real-World `**@Bean**` Example — Third-Party Class**
Suppose you need a class from a library:

```java
ObjectMapper
```

You don't own that class, so you cannot simply modify it and add:

```java
@Component
```

Instead, register it from your application:

```java
@Configuration
public class AppConfig {

    @Bean
    public ObjectMapper objectMapper() {
        return new ObjectMapper();
    }
}
```

Now another Spring-managed class can receive it:

```java
@Service
public class UserService {

    private final ObjectMapper objectMapper;

    public UserService(ObjectMapper objectMapper) {
        this.objectMapper = objectMapper;
    }
}
```

The complete flow is:

```text
Third-party class
      ↓
@Configuration
      ↓
@Bean
      ↓
Spring Container
      ↓
Dependency Injection
```

---

##  **🎛️ 20. `**@Primary**` — Which **Bean** Should Be Preferred?**
Suppose you have:

```text
PaymentService
      ↑
 ┌────┴──────────┐
 ↓               ↓
Stripe          Razorpay
```

Both are Spring Beans.

Now another class requests:

```java
PaymentService paymentService;
```

Spring sees multiple candidates.

`**@Primary**` says:

> **"Prefer this **Bean** when multiple matching Beans exist."**

Example:

```java
@Service
@Primary
public class StripePaymentService implements PaymentService {
}
```

and:

```java
@Service
public class RazorpayPaymentService implements PaymentService {
}
```

Now, when Spring needs a `PaymentService`, Stripe is the preferred candidate.

### Simple meaning

```text
Multiple matching Beans
        ↓
@Primary
        ↓
Choose this one by default
```

---

##  **🎯 21. `**@Qualifier**` — Which Exact **Bean** Do I Want?**
Sometimes you don't want a default. You want a specific implementation.

Example:

```java
@Service("stripePaymentService")
public class StripePaymentService implements PaymentService {
}
```

```java
@Service("razorpayPaymentService")
public class RazorpayPaymentService implements PaymentService {
}
```

Then:

```java
public OrderService(
        @Qualifier("stripePaymentService")
        PaymentService paymentService) {

    this.paymentService = paymentService;
}
```

This says:

> **"Give me the Stripe PaymentService here."**

### `**@Primary**` vs `**@Qualifier**`

```text
@Primary
    → preferred/default candidate

@Qualifier
    → exact candidate I explicitly want
```

Use `**@Primary**` when one implementation should normally win.

Use `**@Qualifier**` when the choice should be explicit at the injection point.

---

##  **🔀 22. `**@ConditionalOnProperty**` — Configuration Decides Which **Bean** Exists**
This is useful when configuration should decide **whether a **Bean** is created**.

Suppose:

```text
PaymentService
      ↑
 ┌────┴──────────┐
 ↓               ↓
Stripe          Razorpay
```

Stripe:

```java
@Component
@ConditionalOnProperty(
    name = "payment.provider",
    havingValue = "stripe"
)
public class StripePaymentService implements PaymentService {
}
```

Razorpay:

```java
@Component
@ConditionalOnProperty(
    name = "payment.provider",
    havingValue = "razorpay"
)
public class RazorpayPaymentService implements PaymentService {
}
```

Configuration:

```properties
payment.provider=stripe
```

Spring checks the property during startup.

```text
payment.provider = stripe
        ↓
Stripe condition = TRUE
        ↓
Stripe Bean exists

Razorpay condition = FALSE
        ↓
Razorpay Bean does not exist
```

Change the configuration to:

```properties
payment.provider=razorpay
```

and now Razorpay becomes the active **Bean** instead.

### Why is this useful?

It is very useful for:

```text
Payment provider
Storage provider
Notification provider
Mock vs real implementation
Optional integrations
Feature switches
Third-party integrations
```

The important advantage is that the Java business code can continue using:

```java
PaymentService
```

without knowing which concrete implementation is active.

---

##  **⚙️ 23. `**@ConditionalOnProperty**` — `name`, `havingValue`, `matchIfMissing`**
Example:

```java
@ConditionalOnProperty(
    name = "payment.provider",
    havingValue = "stripe"
)
```

### `name`

Which property should Spring check?

```text
payment.provider
```

### `havingValue`

What value should the property contain?

```text
stripe
```

So the condition is basically:

```text
payment.provider == "stripe"
```

### `matchIfMissing`

You can define behavior when the property is missing:

```java
@ConditionalOnProperty(
    name = "payment.provider",
    havingValue = "stripe",
    matchIfMissing = true
)
```

This means the condition can still match when the property is not present.

Use this deliberately because it establishes a default behavior.

---

##  **📍 24. Where Do I Configure `**@ConditionalOnProperty**`?**
Usually in:

```text
src/main/resources/application.properties
```

Example:

```properties
payment.provider=stripe
```

or in:

```text
src/main/resources/application.yml
```

Example:

```yaml
payment:
  provider: stripe
```

The configuration is read during application startup.

So if you change:

```properties
payment.provider=stripe
```

to:

```properties
payment.provider=razorpay
```

you normally restart the application so Spring can rebuild the **ApplicationContext** using the new configuration.

This is not normally a live switch that immediately replaces a **Bean** while the application is already running.

---

##  **🆚 25. `**@ConditionalOnProperty**` vs `**@Primary**` vs `**@Qualifier**`**
This distinction is extremely important.

### `**@ConditionalOnProperty**`

Asks:

> **Should this **Bean** exist?**

```text
Configuration
    ↓
Which Bean is created?
```

### `**@Primary**`

Asks:

> **If multiple matching Beans exist, which one should be preferred?**

```text
Multiple Beans
    ↓
Which one is the default?
```

### `**@Qualifier**`

Asks:

> **Which exact **Bean** should be injected here?**

```text
Multiple Beans
    ↓
Give me this exact one
```

### Easy memory

```text
@ConditionalOnProperty
    → existence

@Primary
    → preference

@Qualifier
    → exact selection
```

---

##  **🌍 26. `@Profile` vs `**@ConditionalOnProperty**`**
These are related but solve different problems.

### `@Profile`

Usually used around application environments:

```text
dev
test
prod
```

Example idea:

```java
@Profile("prod")
```

### `**@ConditionalOnProperty**`

Usually used around a specific configuration decision:

```text
payment.provider=stripe
```

### Easy memory

```text
@Profile
    → Which environment?

@ConditionalOnProperty
    → Which configuration option?
```

---

##  **▶️ 27. `**CommandLineRunner**`**
`**CommandLineRunner**` is used when you want **Spring Boot** to execute custom code **automatically after the application has started**.

Example:

```java
@Component
public class StartupTask implements CommandLineRunner {

    @Override
    public void run(String... args) {
        System.out.println("Startup task executed");
    }
}
```

The high-level flow is:

```text
main()
   ↓
SpringApplication.run()
   ↓
Spring startup
   ↓
Beans created and dependencies injected
   ↓
CommandLineRunner.run()
   ↓
Startup code executes
```

### Why do we use it?

Typical use cases include:

```text
Load initial data
Initialize something
Warm up a cache
Perform a startup check
Run development-only startup logic
Print startup information
```

It is an **optional startup hook**.

You do not need `**CommandLineRunner**` just to start a **Spring Boot** application.

---

##  **🧠 28. Your `**CommandLineRunner**` Doubt — Exactly Where Does It Run?**
Suppose:

```java
public static void main(String[] args) {

    System.out.println("A");

    SpringApplication.run(Application.class, args);

    System.out.println("B");
}
```

and:

```java
@Override
public void run(String... args) {
    System.out.println("C");
}
```

The rough flow is:

```text
A
 ↓
SpringApplication.run()
 ↓
Spring startup
 ↓
CommandLineRunner.run()
 ↓
C
 ↓
SpringApplication.run() returns
 ↓
B
```

So the important point is:

> **`main()` starts first. `**CommandLineRunner**.run()` executes later, as part of **Spring Boot** startup. After it finishes, control can return to the code after `SpringApplication.run()` in `main()`.**

Also remember:

```text
SpringApplication.run()
    → starts Spring Boot

CommandLineRunner.run()
    → startup callback executed by Spring
```

They are two different `run()` methods.

---

##  **🔗 29. Full Example — How **DI**, `**@ConditionalOnProperty**`, and `**@RestController**` Work Together**
Imagine a payment system.

```text
                 PaymentService
                       ↑
              ┌────────┴────────┐
              ↓                 ↓
           Stripe             Razorpay
```

### Interface

```java
public interface PaymentService {

    String pay();
}
```

### Stripe implementation

```java
@Component
@ConditionalOnProperty(
    name = "payment.provider",
    havingValue = "stripe"
)
public class StripePaymentService implements PaymentService {

    @Override
    public String pay() {
        return "Paid using Stripe";
    }
}
```

### Razorpay implementation

```java
@Component
@ConditionalOnProperty(
    name = "payment.provider",
    havingValue = "razorpay"
)
public class RazorpayPaymentService implements PaymentService {

    @Override
    public String pay() {
        return "Paid using Razorpay";
    }
}
```

### Configuration

```properties
payment.provider=stripe
```

### Business service

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    public String placeOrder() {
        return paymentService.pay();
    }
}
```

### Controller

```java
@RestController
public class OrderController {

    private final OrderService orderService;

    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @GetMapping("/order")
    public String order() {
        return orderService.placeOrder();
    }
}
```

Now the runtime story is:

```text
Application starts
        ↓
Read payment.provider=stripe
        ↓
Stripe condition matches
        ↓
Stripe Bean created
        ↓
Razorpay condition does not match
        ↓
Razorpay Bean is not created
        ↓
OrderService needs PaymentService
        ↓
Spring injects StripePaymentService
        ↓
OrderController receives OrderService
        ↓
Tomcat starts
        ↓
GET /order
        ↓
OrderController.order()
        ↓
OrderService.placeOrder()
        ↓
PaymentService.pay()
        ↓
"Paid using Stripe"
```

This is a very useful real-world mental model because several Spring concepts are working together rather than separately.

---

##  **🧪 30. Common Doubts — Quick Answers**
### "Why don't I see `new` in constructor injection?"

Because **Spring creates/provides the dependency**.

You write:

```java
public OrderService(PaymentService paymentService)
```

instead of:

```java
new PaymentService()
```

---

### "Is `**@Autowired**` the same as constructor injection?"

No.

```text
Constructor Injection
    → dependency comes through constructor

@Autowired
    → marks an injection point
```

---

### "Why does my code work with or without `**@Autowired**`?"

Because your class has one constructor and Spring can normally use it automatically.

---

### "Can Java have two constructors?"

Yes.

The issue is not Java. The issue is **which constructor Spring should use to create the Bean**.

---

### "Does `**@Autowired**` create the object?"

No.

Spring creates/manages the **Bean**. `**@Autowired**` tells Spring where to inject it.

---

### "Why use `**private final**`?"

Because a required constructor-injected dependency usually should not be reassigned after construction.

---

### "Why can't I normally use `**final**` with field injection?"

Because field injection happens after object construction, while a `**final**` field needs to be initialized as part of construction (or another valid initialization path).

---

### "Why is `**@RestController**` not printing anything?"

Because it does not automatically execute methods. A matching **HTTP request** must arrive.

---

### "Why is **Tomcat** running if I did not call an endpoint?"

Because the application is configured as a web application. **Spring Boot** starts the embedded server and waits for requests.

---

### "Why use `**@Bean**` when I can use `**@Component**`?"

Use `**@Bean**` when you need explicit/custom creation or when the class is outside your control, such as a **third-party library** class.

---

### "What is the difference between `**@Primary**` and `**@Qualifier**`?"

```text
@Primary
    → choose the preferred/default Bean

@Qualifier
    → explicitly choose the exact Bean
```

---

### "What is the difference between `**@ConditionalOnProperty**` and `**@Qualifier**`?"

```text
@ConditionalOnProperty
    → decides which Bean should exist

@Qualifier
    → decides which existing Bean should be injected
```

---

##  **🧠 31. Practical Decision Guide — What Should I Use?**
When building a Spring application, think like this:

**I have a normal class that Spring should manage**

```text
@Component / @Service / @Repository / Controller stereotype
```

**My class needs another required Spring Bean**

```text
Constructor Injection
+
private final
```

**My class has one constructor**

```text
@Autowired usually not required
```

**My class has multiple constructors**

```text
Use @Autowired to identify the intended injection constructor when needed
```

**I need a dependency that is optional/changeable**

```text
Setter Injection can make sense
```

**I need to register a third-party class or custom-created object**

```text
@Configuration + @Bean
```

**I have multiple Beans and one should be the default**

```text
@Primary
```

**I have multiple Beans and need a specific one**

```text
@Qualifier
```

**Configuration should decide which **Bean** exists**

```text
@ConditionalOnProperty
```

**I need something to execute automatically after startup**

```text
CommandLineRunner
```

**I need an HTTP REST endpoint**

```text
@RestController
+
@GetMapping / @PostMapping / ...
```

---

##  **🎯 32. Interview Cheat Sheet**
**What is **IoC**?**

> **IoC** means Spring takes responsibility for creating and managing application objects instead of application code directly controlling their creation.

**What is a **Spring Bean**?**

> A **Bean** is an object managed by the **Spring Container**.

**What is the **ApplicationContext**?**

> **ApplicationContext** is the commonly used Spring container abstraction that manages Beans, configuration, dependency injection, and lifecycle.

**What is **Dependency Injection**?**

> **DI** means an object receives its dependencies from Spring instead of creating them itself.

**What is **Constructor Injection**?**

> **Constructor Injection** means required dependencies are supplied through the class constructor.

**Why is **Constructor Injection** preferred?**

> It makes dependencies explicit, works naturally with `**final**`, supports easier testing, and makes required dependencies visible at object creation time.

**What is `**@Autowired**`?**

> `**@Autowired**` identifies an injection point. With a single constructor, it is usually not required.

**What is `**@Bean**`?**

> `**@Bean**` registers the object returned by a configuration method as a Spring-managed **Bean**.

**What is `**@Configuration**`?**

> `**@Configuration**` identifies a class that contains Spring configuration, commonly including `**@Bean**` definitions.

**What is **Auto-Configuration**?**

> **Spring Boot** automatically configures common infrastructure based on the application's dependencies, configuration, and conditions.

**What is `**@ConditionalOnProperty**`?**

> It conditionally registers a **Bean** based on the value of a configuration property.

**What is `**@Primary**`?**

> It marks a preferred **Bean** when multiple Beans match a dependency.

**What is `**@Qualifier**`?**

> It identifies a specific **Bean** to inject when multiple candidates exist.

**What is `**CommandLineRunner**`?**

> It executes custom code automatically after **Spring Boot** startup.

**What is `**@RestController**`?**

> It identifies a Spring-managed REST controller whose mapped methods can handle HTTP requests.

---

##  **🗺️ 33. Final Mental Model**
Keep this single picture in your head:

```text
                         SPRING BOOT
                              │
                              ↓
                  SpringApplication.run()
                              │
                              ↓
                ApplicationContext
                 / Spring Container
                              │
                              ↓
                             IoC
                              │
                              ↓
                    Spring manages Beans
                              │
               ┌──────────────┴──────────────┐
               ↓                             ↓
       Component Scanning             Auto-Configuration
               │                             │
               ↓                             ↓
             Beans                    Infrastructure setup
               │
               ↓
     Dependency Injection
               │
        ┌──────┴─────────┐
        ↓                ↓
Constructor          @Autowired
Injection
        │
        ↓
  private final
        │
        ↓
Application objects wired together
        │
        ↓
   Conditional Beans
 (@ConditionalOnProperty)
        │
        ↓
 @Primary / @Qualifier
        │
        ↓
 Embedded Web Server
        │
        ↓
 CommandLineRunner
        │
        ↓
 Application Ready
        │
        ↓
 HTTP Request
        │
        ↓
 @RestController
        │
        ↓
      Service
        │
        ↓
 Repository / Database / Other systems
```

---

##  **✅ 34. Core Rules to Remember**
```text
IoC
→ Spring controls object creation and management.

ApplicationContext
→ Central Spring container abstraction.

Bean
→ Object managed by Spring.

DI
→ Spring provides dependencies to objects.

Constructor Injection
→ Dependency comes through constructor.

Required dependency
→ Prefer Constructor Injection.

Constructor Injection
→ Usually store required dependencies as private final.

One constructor
→ @Autowired usually not required.

Multiple constructors
→ @Autowired can identify the injection constructor.

@Autowired
→ Marks an injection point; it does not create the dependency.

@Component / @Service
→ Spring discovers and manages application classes.

@Bean
→ Explicitly register a created/configured object.

@Configuration
→ Contains Spring configuration, commonly @Bean methods.

@Primary
→ Preferred Bean when multiple candidates exist.

@Qualifier
→ Exact Bean selection.

@ConditionalOnProperty
→ Configuration controls whether a Bean exists.

CommandLineRunner
→ Runs custom code after startup.

@RestController
→ Handles REST requests through mapped endpoint methods.

Auto-Configuration
→ Spring Boot automatically configures common infrastructure.

new
→ Manual object creation; avoid manually creating Spring-managed dependencies when DI should provide them.
```

---

-> Behind the scene when we click on run button:
<img width="797" height="712" alt="image" src="https://github.com/user-attachments/assets/cb8c23b5-8939-48f5-82e5-d515f0a8a83d" />

---

> **One-line memory:**  
> **Spring Boot starts the application → Spring manages Beans → **DI** wires them together → **Auto-Configuration** sets up infrastructure → startup callbacks run → the application becomes ready to serve requests.**

---
