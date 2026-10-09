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

- Step 1: 
```java 
Spring scans packages.  
@ComponentScan
 
Finds: Engine and Car
```  

- Step 2
```java
Creates Engine Bean.
Engine engine = new Engine();
```  

- Step 3
```java
Creates Car Bean.
Car car = new Car();
```  

- Step 4
```java
Injects Engine into Car.
car.engine = engine;
```

- Step 5
```java
Stores in IoC Container.
Map<String, Object> beanContainer;
 
Conceptually:
{
    "engine" : EngineObject,
    "car" : CarObject
}
```
