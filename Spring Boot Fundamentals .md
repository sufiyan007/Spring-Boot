Spring Boot Fundamentals 

These notes cover the Spring Boot fundamentals learned after the Project Structure topic.

Style: Simple language + clear examples + internal flow + practical scenarios + common doubts + interview-ready points.

Target level: Beginner-friendly explanation with the depth expected from a developer with around 3 years of experience.

1️⃣ The Big Picture — What Spring Boot Is Actually Doing

When you start a Spring Boot application, the most important thing to understand is that Spring is taking responsibility for a lot of work that you would otherwise have to do manually.

In a normal Java application, you create objects yourself using new, connect those objects together, configure them, and decide when they should be created. In Spring Boot, you describe what your application needs, and Spring creates and manages many of those objects for you.

The overall flow is:

You click Run
      ↓
JVM starts
      ↓
main() executes
      ↓
SpringApplication.run(...)
      ↓
Spring Boot starts
      ↓
ApplicationContext / Spring Container is created
      ↓
Configuration is loaded
      ↓
Components are discovered
      ↓
Beans are registered
      ↓
Auto-configuration is applied
      ↓
Beans are created
      ↓
Dependencies are injected
      ↓
Embedded server starts
      ↓
CommandLineRunner / ApplicationRunner runs
      ↓
Application is ready

The best way to learn Spring is to understand this flow and then place each annotation/concept inside it.

2️⃣ IoC — Inversion of Control

What is IoC?

IoC means:

Spring takes control of creating and managing application objects instead of your code doing everything itself.

Without Spring:

PaymentService paymentService = new PaymentService();
OrderService orderService = new OrderService(paymentService);

Here, you control object creation.

With Spring:

@Component
public class PaymentService {
}

Spring can create and manage the PaymentService object.

So the basic idea is:

Without IoC
    ↓
You create/manage objects

With IoC
    ↓
Spring creates/manages objects

Why do we need IoC?

In a small application, manually creating objects is easy.

But imagine a real application:

OrderController
      ↓
OrderService
      ↓
PaymentService
      ↓
PaymentRepository
      ↓
Database

There may be hundreds of objects and many dependencies.

Manually managing all those new statements becomes difficult and tightly coupled.

Spring takes over this responsibility.

Easy analogy

Think of an office.

Without IoC:

Every employee arranges their own desk,
computer, internet and equipment.

With IoC:

The office management provides everything.
Employees simply use what they need.

Spring is acting like the office management.

3️⃣ Spring Container / ApplicationContext

What is the Spring Container?

The Spring Container is the system responsible for creating and managing Spring Beans.

Think of it as:

Spring's central object-management system.

It can:

Create Beans
Store/manage Beans
Inject dependencies
Manage Bean lifecycle
Read configuration
Apply conditions

A simplified view:

                Spring Container
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
 PaymentService    EmailService    OrderService
     Bean              Bean             Bean

What is ApplicationContext?

ApplicationContext is the commonly used Spring container abstraction.

For learning purposes:

Spring Container
       ↓
ApplicationContext
       ↓
manages Spring Beans

So when someone says:

"Spring Container"

or:

"ApplicationContext"

they are often talking about the central place/system where Spring manages the application's Beans.

Why is it important?

Suppose:

public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}

Spring can look into the ApplicationContext and find the PaymentService Bean it should provide.

4️⃣ What Is a Bean?

A Bean is:

An object that is created and managed by Spring.

For example:

@Component
public class PaymentService {
}

Spring can create an object of PaymentService and manage it as a Bean.

Think:

PaymentService class
        ↓
Spring creates object
        ↓
Spring manages object
        ↓
PaymentService Bean

Important doubt: Is every Java object a Bean?

No.

This:

PaymentService service = new PaymentService();

creates a normal Java object.

It does not automatically mean Spring manages that object.

A Spring Bean is specifically an object that has been registered with the Spring Container.

5️⃣ How Does Spring Know Which Classes to Manage?

Spring commonly uses component scanning.

You will see annotations such as:

@Component
@Service
@Repository
@Controller
@RestController

These make classes discoverable as Spring-managed components in the appropriate context.

The simplified flow is:

@SpringBootApplication
        ↓
Component Scanning
        ↓
Spring searches application packages
        ↓
Finds @Component / @Service / @Repository / ...
        ↓
Registers them as Beans

Why does package location matter?

Suppose your main class is:

com.example.demo

and your classes are:

com.example.demo.controller
com.example.demo.service
com.example.demo.repository

These are under the application's package, so component scanning can discover them.

That is why you often see:

com.example.demo
    ├── DemoApplication
    ├── controller
    ├── service
    └── repository

6️⃣ @SpringBootApplication

Typical application class:

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}

@SpringBootApplication is a convenience annotation that brings together major Spring Boot features.

For learning, remember it conceptually as:

@Configuration
+
@ComponentScan
+
@EnableAutoConfiguration

That means the application class participates in:

configuration
component scanning
auto-configuration

You do not need to memorize its internal source code at this stage. The important point is understanding what role it plays during startup.

7️⃣ main() → SpringApplication.run() → Spring Starts

Java always begins with:

public static void main(String[] args)

Inside a Spring Boot application you normally see:

SpringApplication.run(DemoApplication.class, args);

The sequence is:

JVM
 ↓
main()
 ↓
SpringApplication.run(...)
 ↓
Spring Boot startup

Important doubt

main() executes first.

CommandLineRunner.run() does NOT execute before main().

The CommandLineRunner.run() method is triggered later as part of the Spring startup lifecycle.

8️⃣ Spring Boot Startup Flow — Detailed but Easy

This is the flow you should be comfortable explaining in an interview:

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
14. Embedded web server starts (for a web app)
       ↓
15. CommandLineRunner / ApplicationRunner executes
       ↓
16. Application is ready

This flow connects most of the topics in this document.

9️⃣ What Does "Spring Does the Necessary Work" Mean?

You asked what "necessary" means.

It simply means:

Spring performs the setup required to make your application operational.

Examples:

Read configuration
Create ApplicationContext
Discover components
Register Beans
Apply auto-configuration
Create Beans
Inject dependencies
Initialize Beans
Start the web server
Run startup callbacks

Do not think:

"Spring executes every dependency."

A dependency is a library your project uses.

Spring uses the relevant libraries and infrastructure while building and starting the application.

🔟 Auto-Configuration

What is Auto-Configuration?

Auto-configuration means:

Spring Boot automatically configures common infrastructure based on the application's dependencies, configuration and conditions.

For example, when web support is present:

Web dependencies
      ↓
Spring Boot recognizes web application requirements
      ↓
Web/MVC infrastructure is configured
      ↓
Embedded server can be started

You do not have to manually configure every piece of common web infrastructure.

Why is this useful?

Without Spring Boot, many framework settings would have to be configured manually.

Spring Boot tries to provide sensible defaults so that you can focus more on application logic.

Simple mental model

You say:
"I am building a web application."

Spring Boot says:
"Okay, I'll configure the common web infrastructure."

1️⃣1️⃣ Dependency Injection (DI)

Dependency Injection means:

An object receives the objects it needs from Spring instead of creating those objects itself.

Suppose:

OrderService
    needs
PaymentService

Without DI:

public class OrderService {

    private PaymentService paymentService =
            new PaymentService();
}

OrderService creates the dependency itself.

With DI:

@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}

Now Spring can provide PaymentService.

The mental model is:

OrderService
      ↓
"I need PaymentService."
      ↓
Spring provides PaymentService

1️⃣2️⃣ IoC vs DI

These two are related, but they are not the same thing.

IoC

IoC is the bigger idea:

Spring takes control of object creation and management.

DI

DI is one of the main ways Spring provides that control:

Spring provides required dependencies to an object.

Easy memory:

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

So:

IoC = broader principle
DI  = mechanism/pattern used to implement it

1️⃣3️⃣ Constructor Injection

Constructor Injection means:

The dependency is supplied through the constructor.

Example:

@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}

Spring conceptually does something similar to:

Create PaymentService
        ↓
Create OrderService
        ↓
Pass PaymentService into OrderService constructor

You did not write:

new PaymentService()

inside OrderService.

Spring provides the dependency.

Why is Constructor Injection preferred?

It makes required dependencies very clear.

If the class needs:

PaymentService

then the constructor says:

public OrderService(PaymentService paymentService)

That communicates:

"OrderService needs PaymentService to be properly created."

It also works naturally with final fields and makes testing easier.

1️⃣4️⃣ private final with Constructor Injection

Common professional pattern:

private final PaymentService paymentService;

public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}

What does private mean?

Only this class directly accesses the field.

What does final mean?

The field/reference cannot be reassigned after it has been initialized.

For example:

this.paymentService = paymentService;

After initialization, you cannot normally do:

this.paymentService = anotherPaymentService;

because the field is final.

Why use private final?

For a required dependency that should remain associated with the object:

required dependency
       ↓
constructor injection
       ↓
private final field

This is one of the most common Spring patterns.

1️⃣5️⃣ When Should I NOT Use final?

Do not use final when the field is intentionally expected to be reassigned.

Example:

private String status;

public void changeStatus(String status) {
    this.status = status;
}

The value may change:

PENDING
   ↓
PROCESSING
   ↓
COMPLETED

Therefore final would not make sense here.

Similarly, if you deliberately use setter injection and want to replace the dependency later:

private PaymentService paymentService;

public void setPaymentService(PaymentService paymentService) {
    this.paymentService = paymentService;
}

the field cannot be final.

Important distinction

private final PaymentService paymentService;

does not mean the object itself can never change internally.

It means:

The field cannot be reassigned to another object.

1️⃣6️⃣ @Autowired

@Autowired is an annotation that identifies an injection point for Spring.

Example:

@Autowired
public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}

Think:

Constructor Injection
    ↓
HOW the dependency arrives

@Autowired
    ↓
Explicitly marks the injection point

They are related, but they are not the same concept.

1️⃣7️⃣ Do We Need @Autowired on a Constructor?

If a class has only one constructor, usually no.

This works:

@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}

This also works:

@Service
public class OrderService {

    private final PaymentService paymentService;

    @Autowired
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}

So in this situation:

With @Autowired
    → works

Without @Autowired
    → works

Practical result
    → same

Why did you see no difference when testing it?

Because your class had only one constructor.

Spring already knows:

"There is only one constructor. I can use it."

So adding @Autowired did not visibly change your result.

1️⃣8️⃣ Multiple Constructors and @Autowired

Java allows multiple constructors.

Example:

public class Car {

    public Car() {
    }

    public Car(String color) {
    }
}

There is nothing wrong with that.

The Spring problem is different.

If Spring sees:

Constructor 1
    ↓
no dependency

Constructor 2
    ↓
PaymentService dependency

Spring may need help deciding which constructor should be used.

You can explicitly mark the intended constructor:

@Autowired
public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}

So remember:

Java
    → multiple constructors are allowed

Spring
    → may need to know which constructor to use for injection

1️⃣9️⃣ Field Injection

Example:

@Service
public class OrderService {

    @Autowired
    private PaymentService paymentService;
}

Spring creates the OrderService object and then injects the dependency into the field.

Conceptually:

Create OrderService
       ↓
Find PaymentService Bean
       ↓
Put it into paymentService field

This works, but Constructor Injection is generally preferred for required dependencies because constructor injection makes dependencies explicit and supports immutable (final) fields.

Important

For ordinary field injection, you normally do not use:

@Autowired
private final PaymentService paymentService;

because final requires initialization through a constructor or another valid initialization mechanism, while field injection happens after construction.

2️⃣0️⃣ Setter Injection

Example:

@Service
public class OrderService {

    private PaymentService paymentService;

    @Autowired
    public void setPaymentService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}

Flow:

Create OrderService
       ↓
Call setPaymentService(...)
       ↓
PaymentService is injected

Setter Injection can be useful when a dependency is optional or intentionally replaceable.

2️⃣1️⃣ Constructor vs Field vs Setter Injection

Constructor Injection

private final PaymentService paymentService;

public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}

Use when the dependency is required.

Recommended default.

Field Injection

@Autowired
private PaymentService paymentService;

Easy to write, but generally not the preferred default for required dependencies.

Setter Injection

@Autowired
public void setPaymentService(PaymentService paymentService) {
    this.paymentService = paymentService;
}

Useful when a dependency is optional or intentionally changeable.

Quick memory

Required dependency
    → Constructor Injection

Optional/changeable dependency
    → Setter Injection can make sense

Field Injection
    → supported, but generally avoid as your default

2️⃣2️⃣ @Autowired Does NOT Create the Bean

This is a common beginner misunderstanding.

Wrong idea:

@Autowired
    ↓
creates PaymentService

Correct idea:

PaymentService is registered as a Bean
        ↓
Spring manages it
        ↓
@Autowired identifies an injection point
        ↓
Spring provides the Bean there

For example:

@Component
public class PaymentService {
}

and:

public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}

Spring provides the PaymentService.

2️⃣3️⃣ @Component, @Service, @Repository, @Controller, @RestController

These are commonly used Spring stereotypes.

@Component

General-purpose Spring-managed component:

@Component
public class PaymentService {
}

@Service

Commonly used for service/business logic:

@Service
public class OrderService {
}

@Repository

Commonly used for database/persistence layer classes:

@Repository
public class OrderRepository {
}

@Controller

Typically used for MVC controllers.

@RestController

Used for REST APIs.

Conceptually:

@Component
    → general Spring component

@Service
    → service/business logic

@Repository
    → persistence/database layer

@Controller
    → MVC web controller

@RestController
    → REST web controller

These annotations help communicate the responsibility of the class while also participating in Spring's component management.

2️⃣4️⃣ @RestController — Important Doubt

You asked earlier:

"Why are we writing @RestController when I am not calling through an endpoint?"

The answer is:

You do not need @RestController just to practice Dependency Injection.

@RestController is useful when the class is intended to receive HTTP requests.

Example:

@RestController
public class PaymentController {

    @GetMapping("/pay")
    public String pay() {
        return "Payment successful";
    }
}

Now:

GET /pay
    ↓
PaymentController.pay()

Without an HTTP request, the method is not automatically called.

However, the controller class can still be created as a Spring Bean during startup.

This gives us three separate concepts:

@RestController
    → this class is a REST controller

Dependency Injection
    → Spring provides dependencies

@GetMapping("/pay")
    → this method handles a specific HTTP request

Do not mix these three concepts.

2️⃣5️⃣ Manual Bean Creation — @Configuration + @Bean

Sometimes you want Spring to manage an object, but the class is not a component you can simply annotate.

Example:

public class PaymentService {

    private final String gateway;

    public PaymentService(String gateway) {
        this.gateway = gateway;
    }
}

Instead of:

@Component

you can explicitly create/register the Bean:

@Configuration
public class AppConfig {

    @Bean
    public PaymentService paymentService() {
        return new PaymentService("Stripe");
    }
}

What is happening?

This line:

return new PaymentService("Stripe");

manually creates the object.

But:

@Bean

tells Spring:

"Register the object returned by this method as a Spring Bean."

So:

new PaymentService("Stripe")
        ↓
object created
        ↓
@Bean
        ↓
Spring Container
        ↓
managed Bean

2️⃣6️⃣ Why Do We Need @Configuration?

Example:

@Configuration
public class AppConfig {
}

It tells Spring:

This class contains configuration instructions for the application.

A common use is defining Beans with @Bean.

Example:

@Configuration
public class AppConfig {

    @Bean
    public PaymentService paymentService() {
        return new PaymentService("Stripe");
    }
}

2️⃣7️⃣ @Component vs @Bean

@Component

You place it on the class:

@Component
public class PaymentService {
}

Think:

"Spring, discover this class and manage it."

@Bean

You place it on a method:

@Configuration
public class AppConfig {

    @Bean
    public PaymentService paymentService() {
        return new PaymentService("Stripe");
    }
}

Think:

"Spring, use this method to create/register this object."

When should I use what?

Use @Component / @Service when:

You control the class
+
it naturally belongs as a Spring-managed application component

Use @Bean when:

You need explicit creation/configuration
OR
the class comes from a third-party library
OR
you need custom constructor arguments
OR
you need special setup

2️⃣8️⃣ Real-World @Bean Example — Third-Party Class

Suppose you need a library class:

ObjectMapper

You do not own that class, so you cannot normally go into its source code and add:

@Component

You can register it from your application:

@Configuration
public class AppConfig {

    @Bean
    public ObjectMapper objectMapper() {
        return new ObjectMapper();
    }
}

Now another Spring class can receive it through DI:

@Service
public class UserService {

    private final ObjectMapper objectMapper;

    public UserService(ObjectMapper objectMapper) {
        this.objectMapper = objectMapper;
    }
}

The important chain is:

Third-party class
      ↓
@Configuration
      ↓
@Bean
      ↓
Spring Container
      ↓
Dependency Injection

2️⃣9️⃣ @Primary

Suppose you have multiple implementations:

PaymentService
      ↑
 ┌────┴──────────┐
 ↓               ↓
Stripe          Razorpay

Both may be Spring Beans.

Now another class asks:

PaymentService paymentService

Spring sees multiple candidates.

@Primary says:

Prefer this Bean when multiple matching Beans exist.

Example:

@Service
@Primary
public class StripePaymentService implements PaymentService {
}

and:

@Service
public class RazorpayPaymentService implements PaymentService {
}

Now when Spring needs a PaymentService, Stripe is the preferred candidate.

3️⃣0️⃣ @Qualifier

@Qualifier lets you specify exactly which Bean should be injected.

Example:

@Service("stripePaymentService")
public class StripePaymentService implements PaymentService {
}

@Service("razorpayPaymentService")
public class RazorpayPaymentService implements PaymentService {
}

Then:

public OrderService(
        @Qualifier("stripePaymentService")
        PaymentService paymentService) {

    this.paymentService = paymentService;
}

This means:

"I specifically want the Stripe implementation here."

Simple difference

@Primary
    → preferred/default candidate

@Qualifier
    → exact candidate

3️⃣1️⃣ @ConditionalOnProperty

This is useful when configuration should decide whether a Bean exists.

Example:

@Component
@ConditionalOnProperty(
    name = "payment.provider",
    havingValue = "stripe"
)
public class StripePaymentService implements PaymentService {
}

Configuration:

payment.provider=stripe

Result:

Condition = TRUE
        ↓
StripePaymentService Bean is created

If:

payment.provider=razorpay

then:

Condition = FALSE
        ↓
StripePaymentService Bean is not created

3️⃣2️⃣ Why Use @ConditionalOnProperty?

Imagine:

PaymentService
      ↑
 ┌────┴──────────┐
 ↓               ↓
Stripe          Razorpay

You want configuration to decide which provider is active.

Instead of changing Java code:

payment.provider=stripe

or:

payment.provider=razorpay

Spring decides which Bean should exist.

This is useful for:

Payment provider
Storage implementation
Notification provider
Optional integrations
Feature switches
Mock vs real implementation
Third-party integrations

3️⃣3️⃣ Where Do We Configure the Property?

Usually:

src/main/resources/application.properties

Example:

payment.provider=stripe

Or:

src/main/resources/application.yml

Example:

payment:
  provider: stripe

The property is externalized configuration.

3️⃣4️⃣ name, havingValue, matchIfMissing

Example:

@ConditionalOnProperty(
    name = "payment.provider",
    havingValue = "stripe"
)

name

Which configuration property should be checked?

payment.provider

havingValue

What value should it contain?

stripe

So the condition is effectively:

payment.provider == "stripe"

matchIfMissing

Example:

@ConditionalOnProperty(
    name = "payment.provider",
    havingValue = "stripe",
    matchIfMissing = true
)

This means the condition can still match if the property is missing.

Use this deliberately because it creates a default behavior.

3️⃣5️⃣ When Is @ConditionalOnProperty Checked?

It is checked during application startup while Spring is building/configuring the ApplicationContext.

The simplified flow is:

Application starts
        ↓
Configuration is read
        ↓
Condition is evaluated
        ↓
Matching Bean is registered
        ↓
Bean is created
        ↓
Dependency Injection happens
        ↓
Application continues starting

If you change:

payment.provider=stripe

to:

payment.provider=razorpay

normally restart the application so Spring can build the context again using the new configuration.

It is not normally a live switch that instantly swaps the Bean while the application is already running.

3️⃣6️⃣ @ConditionalOnProperty vs @Primary vs @Qualifier

This is very important.

@ConditionalOnProperty

Asks:

Should this Bean exist?

Config
  ↓
Which Bean gets created?

@Primary

Asks:

If multiple Beans exist, which one should be preferred?

Multiple Beans
      ↓
Which one is the default?

@Qualifier

Asks:

Which specific existing Bean do I want here?

Multiple Beans
      ↓
Give me THIS one

Easy memory:

@ConditionalOnProperty
    → existence

@Primary
    → preference

@Qualifier
    → exact selection

3️⃣7️⃣ CommandLineRunner

CommandLineRunner allows you to execute custom code automatically after Spring Boot has started.

Example:

@Component
public class StartupTask implements CommandLineRunner {

    @Override
    public void run(String... args) {
        System.out.println("Startup task executed");
    }
}

The key flow is:

main()
   ↓
SpringApplication.run()
   ↓
Spring starts
   ↓
Beans created/injected
   ↓
CommandLineRunner.run()
   ↓
startup code executes

3️⃣8️⃣ Why Use CommandLineRunner?

It is useful for code that should happen automatically after startup.

Examples:

Load initial data
Initialize a cache
Perform a startup check
Run development startup logic
Print startup information

It is an optional startup hook.

You do not need it just to start a Spring Boot application.

3️⃣9️⃣ Important CommandLineRunner Doubt

Suppose:

public static void main(String[] args) {

    System.out.println("A");

    SpringApplication.run(Application.class, args);

    System.out.println("B");
}

and:

@Override
public void run(String... args) {
    System.out.println("C");
}

The rough sequence is:

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

So:

main() starts first.

Then Spring Boot starts.

Then CommandLineRunner.run() executes as part of startup.

Then control returns from SpringApplication.run() to main().

4️⃣0️⃣ Embedded Tomcat and @RestController

If your project has web support, Spring Boot can start an embedded server such as Tomcat.

Typical logs:

Tomcat initialized with port 8080
Tomcat started on port 8080
Started DemoApplication

This means:

Web server started
Application context started
Application is ready

It does NOT mean:

Controller method was called

A controller method runs when an HTTP request matches its endpoint.

Example:

@GetMapping("/pay")
public String pay() {
    return "Payment successful";
}

Then:

GET /pay
    ↓
PaymentController.pay()

4️⃣1️⃣ Full DI Example

Suppose:

OrderController
      ↓
OrderService
      ↓
PaymentService
      ↓
StripePaymentService

Code:

@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}

And:

@Component
public class StripePaymentService implements PaymentService {
}

Spring's conceptual flow:

1. Discover StripePaymentService
        ↓
2. Register Bean
        ↓
3. Discover OrderService
        ↓
4. See constructor needs PaymentService
        ↓
5. Find matching PaymentService Bean
        ↓
6. Create OrderService
        ↓
7. Inject PaymentService
        ↓
8. Discover/Create OrderController
        ↓
9. Inject OrderService

This is the core of DI.

4️⃣2️⃣ Full Payment Provider Example

Interface:

public interface PaymentService {

    String pay();
}

Stripe:

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

Razorpay:

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

Configuration:

payment.provider=stripe

Consumer:

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

Result:

payment.provider=stripe
        ↓
Stripe condition = TRUE
        ↓
Stripe Bean exists

Razorpay condition = FALSE
        ↓
Razorpay Bean does not exist

OrderService
        ↓
needs PaymentService
        ↓
StripePaymentService injected

4️⃣3️⃣ A Practical Decision Guide

When writing Spring code, ask these questions.

"I have a normal application class. How should Spring manage it?"

Usually:

@Component
@Service
@Repository
@Controller
@RestController

depending on its responsibility.

"The class needs another Spring Bean."

Prefer:

Constructor Injection

with:

private final Dependency dependency;

"I have one constructor."

Usually:

No @Autowired required

"I have multiple constructors."

Consider:

@Autowired

on the intended injection constructor.

"I need to register/configure a third-party class."

Use:

@Configuration + @Bean

"I have multiple implementations and one should be the default."

Use:

@Primary

"I have multiple implementations and need an exact one here."

Use:

@Qualifier

"Configuration should decide whether a Bean exists."

Use:

@ConditionalOnProperty

"Something should execute automatically after startup."

Use:

CommandLineRunner

"I need an HTTP API endpoint."

Use:

@RestController
+
@GetMapping / @PostMapping / ...

4️⃣4️⃣ Common Beginner Doubts

Doubt 1: "Why don't I see new in Constructor Injection?"

Because you are not manually creating the dependency.

You write:

public OrderService(PaymentService paymentService)

and Spring provides the object.

new
    → manual creation

constructor parameter
    → dependency received from Spring

Doubt 2: "Is @Autowired the same thing as Constructor Injection?"

No.

Constructor Injection
    → how dependency is received

@Autowired
    → annotation that identifies an injection point

Doubt 3: "Why does my code work with or without @Autowired?"

Because there is only one constructor.

Spring can infer that it should use that constructor.

Doubt 4: "Can a class have two constructors?"

Yes.

Java allows it.

The issue is that Spring may need help deciding which constructor to use for dependency injection.

Doubt 5: "Does @Autowired create the dependency?"

No.

Spring creates/manages the dependency as a Bean, and @Autowired marks where it should be injected.

Doubt 6: "Why do I use final?"

Because a required constructor-injected dependency usually should not be reassigned after construction.

Doubt 7: "Why is @RestController not printing anything?"

Because @RestController does not automatically execute your methods.

The method needs an HTTP request matching its mapping.

Doubt 8: "Why is Tomcat running if I didn't call an endpoint?"

Because Spring Boot started the web server during application startup.

The server waits for HTTP requests.

Doubt 9: "Why use @Bean if I can use @Component?"

Use @Bean when you need explicit object creation/configuration or when the class is outside your control, such as a third-party library.

4️⃣5️⃣ Interview-Ready Summary

IoC

IoC means Spring takes responsibility for creating and managing application objects.

Spring Container

The Spring Container creates, manages, wires and controls the lifecycle of Spring Beans.

ApplicationContext

ApplicationContext is the commonly used Spring container abstraction that manages Beans and application configuration.

Bean

A Bean is an object managed by the Spring Container.

Dependency Injection

Dependency Injection means an object receives its dependencies from Spring instead of creating them itself.

Constructor Injection

Constructor Injection supplies dependencies through a class constructor and is generally preferred for required dependencies.

@Autowired

@Autowired marks an injection point. It is usually unnecessary when a class has only one constructor.

private final

private hides the field from outside access, while final prevents the field reference from being reassigned after initialization.

@Bean

@Bean registers the object returned by a configuration method as a Spring-managed Bean.

@Configuration

@Configuration indicates that a class contains Spring configuration, commonly including @Bean methods.

Auto-Configuration

Spring Boot automatically configures common infrastructure based on the application's dependencies, configuration and conditions.

@ConditionalOnProperty

It conditionally registers a Bean based on a configuration property value.

@Primary

It marks a preferred Bean when multiple matching Beans exist.

@Qualifier

It identifies the specific Bean to inject when multiple matching candidates exist.

CommandLineRunner

It executes custom code automatically after Spring Boot startup.

@RestController

It identifies a Spring-managed REST controller whose mapped methods can handle HTTP requests.

4️⃣6️⃣ Final Mental Model

Keep this flow in your head:

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
          ┌───────────┴───────────┐
          ↓                       ↓
     Component Scan        Auto-Configuration
          │                       │
          ↓                       ↓
        Beans              Infrastructure setup
          │
          ↓
   Dependency Injection
          │
     ┌────┴───────────┐
     ↓                ↓
Constructor        @Autowired
Injection
     │
     ↓
private final
     │
     ↓
Application objects wired together
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
Repository / Database / Other Systems

4️⃣7️⃣ Core Rules to Memorize

1. IoC
   → Spring controls object creation and management.

2. Bean
   → Object managed by Spring.

3. ApplicationContext
   → Central Spring container abstraction.

4. DI
   → Spring provides dependencies to objects.

5. Constructor Injection
   → Dependency comes through constructor.

6. Required dependency
   → Prefer Constructor Injection.

7. Constructor Injection
   → Usually use private final fields.

8. One constructor
   → @Autowired usually not required.

9. Multiple constructors
   → @Autowired can identify the constructor Spring should use.

10. @Autowired
    → Marks an injection point; it does not create the dependency.

11. @Component / @Service
    → Let Spring discover/manage application classes.

12. @Bean
    → Explicitly register a created/configured object.

13. @Configuration
    → Holds Spring configuration and commonly @Bean methods.

14. @Primary
    → Preferred Bean when multiple candidates exist.

15. @Qualifier
    → Exact Bean selection.

16. @ConditionalOnProperty
    → Configuration controls whether a Bean exists.

17. CommandLineRunner
    → Runs startup code after Spring startup.

18. @RestController
    → REST controller; methods run when matching HTTP requests arrive.

19. Auto-Configuration
    → Spring Boot automatically configures common infrastructure.

20. `new`
    → Manual object creation; avoid manually creating Spring-managed dependencies when DI should provide them.

4️⃣8️⃣ One-Line Memory Map

IoC
→ Spring takes control

Container
→ Spring manages objects

Bean
→ Object managed by Spring

DI
→ Spring provides objects

Constructor Injection
→ Dependency comes through constructor

private final
→ Common way to store required constructor dependencies

@Autowired
→ Explicit injection point

@Bean
→ Manually create + register object as Bean

@ConditionalOnProperty
→ Configuration decides whether Bean exists

@Primary
→ Preferred Bean

@Qualifier
→ Exact Bean

CommandLineRunner
→ Run code after startup

@RestController
→ Handle REST requests

Auto-Configuration
→ Spring Boot sets up common infrastructure automatically

These concepts form the core Spring Boot foundation. Once these are clear, topics such as bean scopes, bean lifecycle, configuration properties, profiles, Spring MVC, JPA, security, and transactions become much easier to understand.
