# Spring Events:  
Spring Events provide a way for different parts of your application to communicate without being tightly coupled.  

Instead of:  
```java
    Service A directly calls Service B
    Service A directly calls Service C
    Service A directly calls Service D
```
you can publish an event and interested components can react to it.  

This follows the Publisher-Subscriber (Pub/Sub) pattern.  

Complete Example:  

**Event**  
```java
public class UserRegisteredEvent {

    private String email;

    public UserRegisteredEvent(String email) {
        this.email = email;
    }

    public String getEmail() {
        return email;
    }
}
```  

**Publisher**
```java
@Service
public class UserService {

    @Autowired
    private ApplicationEventPublisher publisher;

    public void registerUser() {

        System.out.println("User Saved");

        publisher.publishEvent(
               new UserRegisteredEvent(
                       "abc@gmail.com"));
    }
}
```  

**Listener 1**  
```java
@Component
public class EmailListener {

    @EventListener
    public void sendWelcomeEmail(
          UserRegisteredEvent event) {

        System.out.println(
          "Welcome email sent to "
           + event.getEmail());
    }
}
```  

**Listener 2**  
```java
@Component
public class AuditListener {

    @EventListener
    public void createAudit(
          UserRegisteredEvent event) {

        System.out.println(
          "Audit Created");
    }
}
```  

**Output:**  
```java
    User Saved

    Welcome email sent to abc@gmail.com

    Audit Created
```  

**Notice:**  
UserService knows nothing  
about EmailListener or AuditListener.  

This is loose coupling.  
