# Spring Core:  
Spring Core is the heart of the Spring Framework.  

It provides:  
- IoC (Inversion of Control)
- Dependency Injection (DI)
- Bean Management
- Spring Container
- Bean Lifecycle
- Scopes
- Autowiring
- Configuration (XML, Java, Annotations)  

# Problem Before Spring:  
Ex: Imagine a simple application:  
```java
public class Engine {

    public void start() {
        System.out.println("Engine started");
    }
}

public class Car {

    private Engine engine = new Engine();

    public void drive() {
        engine.start();
        System.out.println("Car running");
    }
}

public class Main {
    public static void main(String[] args) {
        Car car = new Car();
        car.drive();
    }
}
```  

**What's wrong?**  
Car is tightly coupled with Engine. If suppose there is changes in Engine class like PatrolEngine, DiesileEngne, ElectricEngine. Everytime you'll need to modify Car.

# 1. Inversion of Control (IoC):  
Inversion of Control means transferring the responsibility of object creation and management from application code to Spring Container.  

- Traditional Way:  
Application creates objects.  
Ex:  
```java
Engine engine = new Engine();
Car car = new Car(engine);
 ```  
Application controls object creation.  

- IoC Way:  
Spring controls object creation.  
Ex:
```java
Car car = context.getBean(Car.class);
```  
Now Spring creates object and injects dependencies automatically.

# Spring Container:  
This is the brain of Spring.  

**Responsibilities:**  
    - Create Beans
    - Inject Dependencies
    - Manage Lifecycle
    - Destroy Beans  

**Two implementations:**  

1. BeanFactory:  
Basic container.  
Ex:
```java
BeanFactory factory;
```  
Lazy loading.  
Bean created only when needed.  

2. ApplicationContext:  
Most commonly used.  
Ex:
```java
ApplicationContext context;
```  
Spring Boot internally uses ApplicationContext.  

# What is a Bean?  
A bean is simply an object managed by Spring.  

- Normal Object:  
Ex: 
```java
Engine engine = new Engine();
```  

- Spring Bean:  
Ex:
```java
@Component
public class Engine {
}
```  
Spring creates and manages this object.  

# 2. Dependency Injection (DI):  
Ex: Car depends on Engine. Spring injects Engine into Car.  

- Types of Dependency Injection:  

**1. Constructor Injection (Recommended):**  
Ex:
```java
@Component
public class Car {

    private final Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }
}

Spring:
Engine engine = new Engine();
Car car = new Car(engine);
```  

**Benefits:**  
	- Immutable
	- Thread-safe
	- Easy Unit Testing
	- Recommended in Spring Boot  

**Why Constructor Injection?**  
Because mandatory dependencies become explicit and objects can never be created in an invalid state. Means When someone looks at your class, they can immediately understand what objects are required for that class to work correctly.  

**2. Setter Injection:**  
In Setter Injection, Spring first creates the object and then injects dependencies using setter methods. **It Used for optional dependencies.**  
Ex:
```java
@Component
public class Car {

    private Engine engine;

    @Autowired
    public void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```  

**What happens internally?**  
Spring roughly does:  
```java
Engine engine = new Engine();
Car car = new Car();      // Step 1: Create object
car.setEngine(engine);    // Step 2: Inject dependency
```  

**Why is it called Optional Dependency?**  
Ex:
```java
public class ReportService {

    private EmailService emailService;

    public void generateReport() {
        System.out.println("Report Generated");
    }

    @Autowired
    public void setEmailService(EmailService emailService) {
        this.emailService = emailService;
    }
}
```  
Here, **Core functionality:- Generate Report** and **Optional functionality:- Email Report**. Even if EmailService is unavailable, still reportservice will work. Hence EmailService is optional.  
 

**3. Field Injection:**  
Ex:
```java
@Component
public class Car {

    @Autowired
    private Engine engine;
}
```  
Works but not recommended.  

# 3. Bean Management:  
Ex:
```java
@Component
class Engine {}
 
@Component
class Car {
 
	@Autowired
	Engine engine;
}
```  
Application startup:  

**Step 1:**   
Spring scans packages.  
```java 
@ComponentScan
``` 
Finds: Engine and Car.  
  

**Step 2:**  
Creates Engine Bean.  
```java
Engine engine = new Engine();
```  

**Step 3:**  
Creates Car Bean.  
```java
Car car = new Car();
```  

**Step 4:**  
Injects Engine into Car.  
```java
car.engine = engine;
```  

**Step 5:**  
Stores in IoC Container.  
```java
Map<String, Object> beanContainer;
 
Conceptually:
{
    "engine" : EngineObject,
    "car" : CarObject
}
```

# Stereotype Annotations:  
A stereotype annotation tells Spring: "This class is a Spring-managed bean and has a specific role in the application."  
**All of these:**  
    - @Component
    - @Service
    - @Repository
    - @Controller
    - @RestController  
Ultimately create beans in the Spring IoC Container.   

- First Understand @Component:  
```java
@Component
public class Engine {
}
```  
**Explaination:**  
When Spring starts:  
```java
@ComponentScan
```  
scans the package.  

And If it finds:
```java
@Component
public class Engine {}
```  
then creates a bean.  

Internally:  
```java
Engine engine = new Engine();
```  
and stores it in the IoC Container.  

You can inject it anywhere by:  
```java
@Autowired
private Engine engine;
```  

- Why Do We Have @Service, @Repository, etc.?  
Technically: @Service, @Repository, @Controller are specialized forms of: @Component.  
For example:  
```java
@Service
public class UserService {
}
```
is almost equivalent to:  

```java
@Component
public class UserService {
}
```  
Spring creates a bean in both cases. The difference is: Readability, Purpose and Additional Features.  

    - @Service Used for: Business Logic Layer.
    - @Repository Used for: Database Access Layer.
    - @Controller Used in: Spring MVC Applications and Returns: HTML Pages / JSP / Thymeleaf Views.  

Ex:
```java
@Controller
public class UserController {

    @GetMapping("/home")
    public String home() {
        return "home";
    }
} 

Response:
<html>
   ....
</html>
```  

- @RestController, Most commonly used nowadays. Used for:  
```java
	REST APIs
	JSON Response
	Microservices
```  
Ex:
```java
@RestController
public class UserController {

    @GetMapping("/user")
    public String getUser() {
        return "Ankit";
    }
}

Response: 
"Ankit"
```  
Sent directly in HTTP response body. and This conversion is performed by Jackson Library.  

- Why @RestController Was Introduced?  
Before Spring 4.0, we wrote:  
Ex:
```java
@Controller
public class UserController {

    @ResponseBody
    @GetMapping("/user")
    public User getUser() {

        return new User(1L, "Ankit");
    }
}
```  
Notice:  
```java
@ResponseBody
```  
required on every method.  

- What Does @ResponseBody Do?  
It tells Spring:  
	- Don't look for HTML page.  
	- Write return value directly to HTTP response body.  
So, @RestController is equivalent to: @Controller and @ResponseBody
