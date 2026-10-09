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

1. **Inversion of Control (IoC):**  
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