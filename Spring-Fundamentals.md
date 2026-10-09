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