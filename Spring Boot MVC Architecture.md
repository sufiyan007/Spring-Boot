# 🌱 Spring Boot MVC Architecture

> A practical guide to understanding **Spring MVC**, **Controller → Service → Repository**, **DTO vs Entity**, and the complete **HTTP request/response flow** in a Spring Boot REST API.

---

## **🎯 The Big Picture**

When you build a Spring Boot REST API, two related ideas are working together:

**1. Spring MVC request processing** — explains **how an HTTP request enters the application and reaches the correct controller method**.

**2. Layered architecture** — explains **how your application organizes the work after the request reaches the controller**.

Put them together:

```text
Client
  ↓
Tomcat
  ↓
DispatcherServlet
  ↓
HandlerMapping
  ↓
HandlerAdapter
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
```

Then the response comes back:

```text
Database
  ↓
Repository
  ↓
Service
  ↓
Controller
  ↓
HttpMessageConverter
  ↓
JSON
  ↓
DispatcherServlet
  ↓
Tomcat
  ↓
Client
```

This is the main mental model to remember.

---

## **🌐 What does MVC mean?**

MVC stands for:

```text
M = Model
V = View
C = Controller
```

### **Controller**

The **Controller** handles incoming HTTP requests and produces HTTP responses.

In Spring Boot, this is commonly:

```java
@RestController
public class StudentController {
}
```

### **Model**

In the traditional MVC meaning, the Model represents the application's data/state.

In a modern Spring Boot REST application, the data side can contain things such as:

```text
Entity
DTO
Domain objects
Request/response objects
```

So do not think:

```text
Model = only Entity
```

It is broader than that.

### **View**

A View is normally something such as an HTML page rendered by the server.

Traditional Spring MVC can use a template engine such as Thymeleaf:

```text
Controller
   ↓
View
   ↓
HTML
```

But a REST API normally returns JSON instead:

```text
Controller
   ↓
JSON
   ↓
Client
```

Therefore, in a Spring Boot REST API, you may have **no server-side View at all**.

---

## **🧩 Spring MVC vs Layered Architecture**

This distinction is very important.

### **Spring MVC request flow**

```text
Client
  ↓
Tomcat
  ↓
DispatcherServlet
  ↓
HandlerMapping
  ↓
Controller
```

This answers:

> **"How does the HTTP request get to my endpoint?"**

### **Layered application flow**

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

This answers:

> **"How does my application organize the work?"**

Your Spring Boot project can use both at the same time:

```text
Client
  ↓
Tomcat
  ↓
DispatcherServlet
  ↓
Controller       ← Presentation Layer
  ↓
Service          ← Service / Business Layer
  ↓
Repository       ← Persistence Layer
  ↓
Database
```

---

## **🏗️ Mapping Your IntelliJ Folder Structure**

A project like this:

```text
com.example.demo
│
├── controller
├── service
├── repository
├── entity
├── dto
└── DemoApplication
```

can be understood as:

```text
controller  → Presentation / HTTP layer
service     → Business logic
repository  → Database access
entity      → Persisted/domain data
dto         → Data transfer
```

### **Your folders are not literally "M / V / C"**

Do not force the structure into:

```text
M = entity
V = something
C = controller
```

Instead, understand the responsibilities.

The `controller`, `service`, `repository`, `entity`, and `dto` packages represent a **layered application design**, while the web request itself is processed using **Spring MVC**.

---

## **🌐 Controller — Presentation Layer**

The Controller is the **entry point for your API endpoint**.

Example:

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @GetMapping("/{id}")
    public StudentDto getStudent(@PathVariable Long id) {
        return studentService.getStudent(id);
    }
}
```

The Controller mainly deals with **HTTP concerns**:

```text
Receive request
Read request data
Validate request input
Call service
Return response
```

A Controller should generally not contain all the application's business logic or database code.

A good mental model is:

> **Controller = API/front desk**

The client tells the Controller what it wants. The Controller passes the actual business work to the Service.

---

## **⚙️ Service — Business Layer**

The Service contains **business logic**.

Example:

```java
@Service
public class StudentService {

    private final StudentRepository studentRepository;

    public StudentService(StudentRepository studentRepository) {
        this.studentRepository = studentRepository;
    }

    public StudentDto getStudent(Long id) {

        Student student = studentRepository.findById(id)
                .orElseThrow();

        return new StudentDto(
                student.getId(),
                student.getName(),
                student.getEmail()
        );
    }
}
```

Examples of business logic:

```text
Can this order be cancelled?
Does this customer have enough balance?
What discount should apply?
Can this payment be processed?
```

Think:

> **Service = business brain**

---

## **🗄️ Repository — Persistence Layer**

The Repository is the layer used to perform database operations.

Example:

```java
@Repository
public interface StudentRepository
        extends JpaRepository<Student, Long> {
}
```

With Spring Data JPA, common repository methods are available without manually writing the basic CRUD implementation:

```java
findAll()
findById()
save()
deleteById()
```

For example:

```java
studentRepository.findAll();
```

roughly means:

> **"Get all student records from the database."**

The Repository is your application's **database assistant**.

Think:

```text
Service
   ↓
"Get student 101"

Repository
   ↓
"Okay, I'll interact with the database."

Database
   ↓
Student data
```

---

## **🧱 Entity — Database Representation**

An Entity is a Java class mapped to persistent data.

Example:

```java
@Entity
public class Student {

    @Id
    @GeneratedValue
    private Long id;

    private String name;
    private String email;
    private String password;
}
```

JPA/Hibernate can use the mapping to represent persistent data.

Conceptually:

```text
Java                           Database

Student class           →      student table
id                      →      id
name                    →      name
email                   →      email
password                →      password
```

Think:

> **Entity = Java representation of persisted/database data.**

`@Entity` tells JPA that this class is intended to be persisted.

---

## **📦 DTO — Data Transfer Object**

DTO means **Data Transfer Object**.

A DTO is simply an object used to **carry data between boundaries/layers**.

In Spring Boot REST applications, DTOs are commonly used for:

```text
Request data
Response data
API contracts
```

Example:

```java
public class StudentDto {

    private Long id;
    private String name;
    private String email;
}
```

The DTO is not automatically a database table.

Instead, it says:

> **"This is the shape of the data I want to transfer."**

---

## **🔐 Why do Entity and DTO need to be separate?**

Suppose your database contains:

```text
student
--------------------------------
id | name | email | password
--------------------------------
1  | Raju | ...   | secret123
```

But the API should return:

```json
{
  "id": 1,
  "name": "Raju",
  "email": "raju@gmail.com"
}
```

You do not necessarily want every database field exposed to the client.

So you can have:

```text
Entity
   ↓
represents persisted/database data

DTO
   ↓
represents data you want to transfer through the API
```

For example:

```java
@Entity
public class Student {

    private Long id;
    private String name;
    private String email;
    private String password;
}
```

and:

```java
public class StudentDto {

    private Long id;
    private String name;
    private String email;
}
```

The Entity contains:

```text
id
name
email
password
```

The DTO contains:

```text
id
name
email
```

This gives you control over the API contract and helps prevent accidentally exposing internal fields.

---

## **⚠️ Do not think "DTO = JSON"**

A DTO is a **Java object**.

For example:

```java
StudentDto student = new StudentDto(
        41,
        "Raju",
        "aa.gmail.com"
);
```

That is a Java object.

The client usually does not receive the Java object itself.

Instead, Spring's HTTP message conversion infrastructure can convert it into JSON:

```text
StudentDto Java object
        ↓
HttpMessageConverter
        ↓
JSON
```

Result:

```json
{
  "id": 41,
  "name": "Raju",
  "email": "aa.gmail.com"
}
```

---

## **🌐 Your Exact `StudentDto` Example**

Your code:

```java
@RestController
public class StudentController {

    @GetMapping("/student")
    public StudentDto getStudent() {
        return new StudentDto(
                41,
                "Raju",
                "aa.gmail.com"
        );
    }
}
```

DTO:

```java
package com.example.demo.studentDto;

import lombok.AllArgsConstructor;
import lombok.Data;

@Data
@AllArgsConstructor
public class StudentDto {

    private long id;
    private String name;
    private String email;
}
```

When the client sends:

```http
GET http://localhost:8080/student
```

your method:

```java
getStudent()
```

runs and creates:

```java
new StudentDto(41, "Raju", "aa.gmail.com")
```

So, in **this exact example**, there is no Service or Repository involved.

The Controller itself creates the DTO.

The flow is:

```text
Client
  ↓
Tomcat
  ↓
DispatcherServlet
  ↓
HandlerMapping
  ↓
StudentController
  ↓
getStudent()
  ↓
new StudentDto(...)
  ↓
HttpMessageConverter
  ↓
JSON
  ↓
Client
```

---

## **🔄 What happens to the StudentDto after the Controller returns it?**

The Controller returns:

```java
StudentDto
```

Spring MVC then handles the response.

Conceptually:

```text
StudentDto Java object
       ↓
HttpMessageConverter
       ↓
JSON
```

For JSON, a typical Spring Boot web application uses **Jackson** through the relevant Spring Boot dependencies.

The client can therefore receive:

```json
{
  "id": 41,
  "name": "Raju",
  "email": "aa.gmail.com"
}
```

The important distinction is:

```text
StudentDto
    ↓
Java object

JSON
    ↓
Data format sent over HTTP
```

---

## **🧠 Serialization vs Deserialization**

These two words are worth remembering.

### **Serialization**

Java object → JSON

```text
StudentDto
    ↓
JSON
```

This commonly happens when your Controller returns an object.

### **Deserialization**

JSON → Java object

```text
JSON
    ↓
CreateStudentRequest
```

For example, a client sends:

```json
{
  "name": "Raju",
  "email": "raju@gmail.com"
}
```

and your Controller has:

```java
@PostMapping("/student")
public StudentDto createStudent(
        @RequestBody CreateStudentRequest request) {
    ...
}
```

Spring converts the incoming JSON into the Java request object.

---

## **📨 `@RequestBody`**

`@RequestBody` is commonly used when data comes inside the HTTP request body, especially JSON.

Client sends:

```json
{
  "name": "Raju",
  "email": "raju@gmail.com"
}
```

Controller:

```java
@PostMapping("/student")
public StudentDto createStudent(
        @RequestBody CreateStudentRequest request) {
    ...
}
```

Conceptually:

```text
JSON
 ↓
HttpMessageConverter
 ↓
CreateStudentRequest
 ↓
Controller
```

---

## **📤 `@ResponseBody` and `@RestController`**

`@ResponseBody` means the return value should be written to the HTTP response body rather than treated as a view name.

`@RestController` is commonly used for REST APIs and is effectively:

```text
@Controller + @ResponseBody
```

So:

```java
@RestController
public class StudentController {
}
```

is a convenient way to build a controller whose methods normally return response bodies.

Example:

```java
@GetMapping("/student")
public StudentDto getStudent() {
    return new StudentDto(41, "Raju", "aa.gmail.com");
}
```

---

## **🗺️ `@GetMapping`, `@PostMapping`, etc.**

Spring MVC uses request mappings to connect HTTP requests to controller methods.

```text
@GetMapping     → GET
@PostMapping    → POST
@PutMapping     → PUT
@PatchMapping   → PATCH
@DeleteMapping  → DELETE
```

Example:

```java
@GetMapping("/students")
public List<StudentDto> getStudents() {
    ...
}
```

The client sends:

```http
GET /students
```

Spring finds the matching method.

---

## **📍 `@PathVariable`**

Use `@PathVariable` when the value is part of the URL path.

Request:

```http
GET /students/101
```

Controller:

```java
@GetMapping("/students/{id}")
public StudentDto getStudent(@PathVariable Long id) {
    ...
}
```

Here:

```text
{id}
```

matches:

```text
101
```

So:

```text
URL path
   ↓
@PathVariable
```

---

## **🔎 `@RequestParam`**

Use `@RequestParam` when the value is provided as a query parameter.

Request:

```http
GET /students?page=2&size=10
```

Controller:

```java
@GetMapping("/students")
public List<StudentDto> getStudents(
        @RequestParam int page,
        @RequestParam int size) {
    ...
}
```

So:

```text
?page=2
&size=10
   ↓
@RequestParam
```

---

## **📑 `@RequestHeader`**

A value can also come from an HTTP header.

Example:

```java
@GetMapping("/student")
public String test(
        @RequestHeader("Authorization") String token) {
    ...
}
```

This is useful for things such as:

```text
Authorization
Content-Type
Correlation-ID
Custom headers
```

---

## **🍪 `@CookieValue`**

You can read a cookie using:

```java
@CookieValue("sessionId")
String sessionId
```

Cookies are another place where HTTP request information can come from.

---

## **✅ Validation**

Request data often needs validation before business processing.

DTO:

```java
public class CreateStudentRequest {

    @NotBlank
    private String name;

    @Email
    @NotBlank
    private String email;
}
```

Controller:

```java
@PostMapping("/students")
public StudentDto createStudent(
        @Valid @RequestBody CreateStudentRequest request) {

    return studentService.createStudent(request);
}
```

Here:

```text
@Valid
```

tells Spring to perform Bean Validation on the request object.

A useful distinction is:

```text
Input validation
→ Is this input structurally valid?

Business rule
→ Is this operation allowed according to business rules?
```

For example:

```text
Invalid email format
→ validation

Customer does not have enough balance
→ business rule
```

---

## **⚙️ Business Logic Should Usually Be in the Service**

Avoid putting everything inside a Controller.

Instead of:

```java
@PostMapping("/orders")
public Order createOrder(...) {

    // validation
    // discount calculation
    // inventory check
    // database query
    // payment logic
    // email sending
}
```

prefer:

```java
@PostMapping("/orders")
public OrderResponse createOrder(
        @RequestBody CreateOrderRequest request) {

    return orderService.createOrder(request);
}
```

Then:

```text
Controller
   ↓
Service
   ↓
business rules
```

This keeps responsibilities separated.

---

## **🗄️ Service → Repository → Database**

A realistic flow could look like:

```text
Controller
   ↓
UserService
   ↓
UserRepository
   ↓
Database
```

Example:

```java
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User getUser(Long id) {

        return userRepository.findById(id)
                .orElseThrow();
    }
}
```

The Controller asks the Service.

The Service asks the Repository.

The Repository interacts with persistence.

---

## **🔁 A More Realistic DTO Flow**

A common application flow is:

```text
Client
   ↓
JSON request
   ↓
Controller
   ↓
Request DTO
   ↓
Service
   ↓
Entity
   ↓
Repository
   ↓
Database
```

On the way back:

```text
Database
   ↓
Entity
   ↓
Service
   ↓
Response DTO
   ↓
Controller
   ↓
HttpMessageConverter
   ↓
JSON
   ↓
Client
```

This shows why DTO and Entity are often separated.

The client/API does not need to know exactly how the database is represented.

---

## **🧠 Controller vs Service vs Repository**

Use this simple rule:

```text
Controller
→ HTTP / API work

Service
→ Business logic

Repository
→ Database interaction
```

For example:

```text
"Read studentId from URL"
→ Controller

"Can this student perform this operation?"
→ Service

"Find student record with ID 41"
→ Repository
```

This separation is called **Separation of Concerns** — keeping different types of work in different places.

---

## **🚨 Exception Handling**

Instead of putting large try/catch blocks in every Controller, Spring applications commonly use centralized exception handling.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(StudentNotFoundException.class)
    public ResponseEntity<?> handleStudentNotFound(
            StudentNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(...);
    }
}
```

The flow becomes:

```text
Controller
   ↓
Service
   ↓
Exception
   ↓
Global Exception Handler
   ↓
HTTP Error Response
```

This keeps Controller code cleaner and gives the API a consistent error format.

---

## **🛡️ Filters, Interceptors and Controllers**

These are often confused.

A simplified conceptual flow is:

```text
Request
  ↓
Filter
  ↓
DispatcherServlet / Spring MVC
  ↓
Interceptor
  ↓
Controller
```

### **Filter**

Works at the servlet/container level.

Common uses include request-level processing, logging, CORS-related handling, and security infrastructure.

### **Interceptor**

Works around Spring MVC controller processing.

Common callback methods include:

```java
preHandle()
postHandle()
afterCompletion()
```

### **Controller**

Handles the actual application endpoint.

For example:

```text
GET /students/101
       ↓
StudentController
```

For a 3-year developer, understand the responsibilities and relative position of these components.

---

## **🔐 Authentication vs Authorization**

In real Spring applications, security processing can happen before the Controller logic.

Think:

```text
Authentication
→ Who are you?

Authorization
→ What are you allowed to do?
```

A simplified flow may look like:

```text
Client
   ↓
Tomcat
   ↓
Security Filters
   ↓
DispatcherServlet
   ↓
Controller
```

---

## **🌍 CORS**

CORS stands for **Cross-Origin Resource Sharing**.

For example:

```text
Frontend
http://localhost:3000

Backend
http://localhost:8080
```

These are different origins.

Browsers apply cross-origin rules, and Spring can be configured to allow appropriate cross-origin requests.

The important concept is:

> **CORS is mainly a browser security mechanism controlling cross-origin browser requests.**

---

## **📄 `ResponseEntity`**

`ResponseEntity` gives you explicit control over the HTTP response.

You can control:

```text
Status
Headers
Body
```

Example:

```java
@GetMapping("/{id}")
public ResponseEntity<StudentDto> getStudent(
        @PathVariable Long id) {

    StudentDto student = studentService.getStudent(id);

    return ResponseEntity.ok(student);
}
```

For a create operation:

```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(studentDto);
```

Use it when explicit response control is useful. You do not need to wrap every method in `ResponseEntity` just because you are using Spring MVC.

---

## **🚦 HTTP Status Codes**

A Spring Boot developer should know the common HTTP status codes:

```text
200 OK
201 Created
204 No Content
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
500 Internal Server Error
```

The status code communicates what happened to the request.

---

## **📊 Pagination and Sorting**

Real APIs often need:

```http
GET /students?page=0&size=20&sort=name
```

Spring Data commonly provides concepts such as:

```text
Page<T>
Pageable
Sort
```

The important idea is:

> **Do not return thousands of database rows when the client only needs one page.**

---

## **📎 File Uploads**

Not every request is JSON.

A file upload commonly uses:

```text
multipart/form-data
```

Example:

```java
@PostMapping("/upload")
public String upload(@RequestParam MultipartFile file) {
    ...
}
```

So remember that HTTP request bodies can have different content types.

---

## **📌 Content-Type and Accept**

### **Content-Type**

Tells the server what format the request body uses.

Example:

```http
Content-Type: application/json
```

Meaning:

> "The body I am sending is JSON."

### **Accept**

Tells the server what response format the client wants.

Example:

```http
Accept: application/json
```

Meaning:

> "I would like JSON in the response."

---

## **♻️ Idempotency**

At a high level, an operation is **idempotent** when repeating the same request has the same intended effect as performing it once.

This matters when clients or infrastructure retry requests.

For API design, understand the HTTP semantics of:

```text
GET
PUT
DELETE
POST
```

especially when working with payments, orders, and distributed systems.

---

## **🏷️ REST Endpoint Design**

A clean resource-oriented API might look like:

```text
GET    /students
GET    /students/{id}
POST   /students
PUT    /students/{id}
PATCH  /students/{id}
DELETE /students/{id}
```

The idea is to design around resources and HTTP methods rather than putting the action itself into every URL.

---

## **🧭 Request Data: The Quick Memory Trick**

Whenever you see an endpoint, ask where the data came from.

```text
/students/101
      ↓
@PathVariable

/students?page=2
      ↓
@RequestParam

JSON body
      ↓
@RequestBody

HTTP header
      ↓
@RequestHeader

Cookie
      ↓
@CookieValue
```

This makes Spring MVC annotations much easier to remember.

---

## **🏢 Simple Company Analogy**

Imagine an office.

```text
Client
  ↓
Receptionist
  ↓
Manager
  ↓
Database Clerk
  ↓
Database
```

Map it to Spring:

```text
Client
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
```

And:

```text
Entity
→ Internal/persisted data representation

DTO
→ Data prepared for transfer to/from a boundary
```

The Controller is the front desk, the Service is the business brain, and the Repository is the database specialist.

---

## **🏁 Complete Spring MVC Request Flow**

This is the flow worth being able to explain in an interview:

```text
Browser / Postman
        ↓
HTTP Request
        ↓
Embedded Tomcat
        ↓
DispatcherServlet
        ↓
HandlerMapping
        ↓
HandlerAdapter
        ↓
Controller method
        ↓
Service
        ↓
Repository
        ↓
Database
```

And the response:

```text
Database
        ↓
Repository
        ↓
Service
        ↓
Controller
        ↓
HttpMessageConverter
        ↓
JSON
        ↓
DispatcherServlet
        ↓
Tomcat
        ↓
HTTP Response
        ↓
Browser / Postman
```

For a simple endpoint where the Controller directly creates a DTO, the Service/Repository/Database part is absent:

```text
Browser / Postman
        ↓
Embedded Tomcat
        ↓
DispatcherServlet
        ↓
HandlerMapping
        ↓
HandlerAdapter
        ↓
StudentController
        ↓
StudentDto
        ↓
HttpMessageConverter
        ↓
JSON
        ↓
Client
```

---

## **🔬 What Each Internal MVC Component Does**

| Component | Simple meaning |
|---|---|
| **Tomcat** | Receives the HTTP request at the web-server/servlet-container level |
| **DispatcherServlet** | Central Spring MVC request dispatcher/coordinator |
| **HandlerMapping** | Finds the handler/controller method matching the request |
| **HandlerAdapter** | Invokes the selected handler |
| **Controller** | Handles the API/HTTP request |
| **Service** | Contains business logic |
| **Repository** | Performs persistence/database operations |
| **Entity** | Represents persisted/domain data |
| **DTO** | Carries data across boundaries |
| **HttpMessageConverter** | Converts HTTP body data ↔ Java objects |
| **Jackson** | Common JSON serialization/deserialization library in Spring Boot web applications |

---

## **🎯 What a 3-Year Spring Boot Developer Should Master**

You should be comfortable with:

```text
HTTP request / response
Tomcat
DispatcherServlet
HandlerMapping
HandlerAdapter
@Controller
@RestController
@RequestMapping
@GetMapping
@PostMapping
@PutMapping
@PatchMapping
@DeleteMapping
@PathVariable
@RequestParam
@RequestBody
@RequestHeader
@ResponseBody
ResponseEntity

DTOs
Entities
Services
Repositories

Validation
@Valid
Exception handling
@RestControllerAdvice

JSON serialization/deserialization
HttpMessageConverter
Jackson

HTTP status codes
Content-Type
Accept

Filters
Interceptors
CORS
Authentication / authorization flow
Pagination
Sorting
File upload
REST API design
Idempotency
```

You do **not** need to memorize every Spring internal implementation class.

For example, understanding these concepts is enough for most normal development:

```text
HandlerMapping
→ finds the matching handler

HandlerAdapter
→ invokes the handler

HttpMessageConverter
→ converts HTTP body ↔ Java object
```

---

## **🎤 Interview-Level Answer**

If an interviewer asks:

> **"How does a request get processed in Spring Boot?"**

A strong answer is:

> When a client sends an HTTP request, it first reaches the embedded web server such as Tomcat. The request then enters Spring MVC through the `DispatcherServlet`. Spring uses handler mapping to find the controller method that matches the request URL and HTTP method, and a handler adapter invokes that method. The Controller handles the HTTP-level request and usually delegates business logic to the Service layer. The Service can use a Repository to access the database. The result is returned through the Controller, and Spring's HTTP message conversion infrastructure, commonly using Jackson for JSON, serializes the Java object into the HTTP response body before it is sent back to the client.

---

## **🔥 Final Mental Model**

Remember these responsibilities:

```text
Tomcat
→ receives HTTP request

DispatcherServlet
→ central Spring MVC dispatcher

HandlerMapping
→ "Which controller/handler?"

HandlerAdapter
→ "Invoke that handler"

Controller
→ HTTP / API work

Service
→ Business logic

Repository
→ Database work

Entity
→ Persisted/domain representation

DTO
→ Data transfer representation

HttpMessageConverter
→ Java object ↔ HTTP body

Jackson
→ Common JSON converter used by Spring Boot
```

The most important flow is:

```text
Client
 ↓
Tomcat
 ↓
DispatcherServlet
 ↓
HandlerMapping
 ↓
HandlerAdapter
 ↓
Controller
 ↓
Service
 ↓
Repository
 ↓
Database
```

And back:

```text
Database
 ↓
Repository
 ↓
Service
 ↓
Controller
 ↓
DTO / Java Object
 ↓
HttpMessageConverter / Jackson
 ↓
JSON
 ↓
Client
```

> **The goal is not to memorize the boxes. The goal is to understand what each box is responsible for and why the request moves through it.**
