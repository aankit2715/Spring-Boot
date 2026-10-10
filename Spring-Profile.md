# Spring Profiles:  
Spring Profiles are used to load different configurations for different environments.  

In real projects, we usually have:  
```java
    Development (DEV)  
    Testing (UAT/QA)  
    Production (PROD)
```
Each environment needs different settings.  

Example:
```java
    DEV -> Local Database
    UAT -> Testing Database
    PROD -> Production Database
```  
You cannot use the same configuration everywhere.  
This is where Spring Profiles help.  

- **Why Do We Need Profiles?**  

Suppose your application runs in three environments.  

**Development**  
```java
    spring.datasource.url=jdbc:mysql://localhost:3306/devdb
```  

**UAT**
```java
    spring.datasource.url=jdbc:mysql://uat-server:3306/uatdb
```  

**Production**
```java
    spring.datasource.url=jdbc:mysql://prod-server:3306/proddb
```  

Without Profiles, you would constantly change:
```java
    spring.datasource.url
```  
before deployment which is very risky.  


**Real Project Example**: Loan Management Application  

- **DEV**
```java
    Database : Local MySQL
    Logging  : DEBUG
    Email    : Mock Email Service
```  

- **PROD**
```java
    Database : Production Oracle
    Logging  : ERROR
    Email    : Real SMTP Service
```  
Different beans and properties are needed.


# Profile-Specific Properties Files:  

**Spring Boot supports:**  
```java
    application.properties
    application-dev.properties
    application-test.properties
    application-prod.properties
```  

Ex:  
```java
    application-dev.properties
    spring.datasource.url=jdbc:mysql://localhost:3306/devdb
    logging.level.root=DEBUG
    
    application-prod.properties
    spring.datasource.url=jdbc:mysql://prod-server:3306/proddb
    logging.level.root=ERROR
```  

- **Activate a Profile:**

Method 1:  
```java
    spring.profiles.active=dev  

    Spring loads:  
    application.properties
    +
    application-dev.properties
```  

Method 2:
```java
    Command Line
    java -jar app.jar \
    --spring.profiles.active=prod
```  

Method 3:
```java
    Environment Variable
    SPRING_PROFILES_ACTIVE=prod  // Common in Docker/Kubernetes deployments.
```  

- **How Spring Loads Properties?**  

Suppose:  

**application.properties**
```java
    app.name=Loan Application
    server.port=8080
```  

**application-dev.properties**
```java
    server.port=9090
```  

Active Profile:  
```java
    spring.profiles.active=dev
```  

Result:  
```java
    app.name=Loan Application
    server.port=9090
```  
Profile-specific properties override common properties.  


# @Profile Annotation:  
Profiles can control not only properties but also beans.  
This is extremely important.  

Example
```java
Interface:
public interface NotificationService {
	void sendMessage();
}
```  

**DEV Implementation**
```java
    @Service
    @Profile("dev")
    public class MockNotificationService
                    implements NotificationService {
    
        public void sendMessage() {
        System.out.println("Mock Notification");
        }
    }
```  

**PROD Implementation**
```java
    @Service
    @Profile("prod")
    public class EmailNotificationService
        implements NotificationService {

        public void sendMessage() {
            System.out.println(
                    "Real Email Sent");
        }
    }
```  

**Active Profile = dev**
```java
    spring.profiles.active=dev
```  

**Spring creates:**  
```java
    MockNotificationService 
```  
Only


# Using @Profile with @Bean  
Very common in projects.  

Ex:
```java
    @Configuration
    public class AppConfig {

        @Bean
        @Profile("dev")
        public DataSource devDataSource() {

            return new DriverManagerDataSource(
                "jdbc:mysql://localhost/devdb");
        }

        @Bean
        @Profile("prod")
        public DataSource prodDataSource() {

            return new DriverManagerDataSource(
                "jdbc:mysql://prod/proddb");
        }
    }
```  

# Multiple Profiles:  
A bean can belong to multiple profiles.  

Ex.
```java
    @Service
    @Profile({"dev","test"})
    public class MockPaymentService {
    }

    Bean loads for:
    DEV
    TEST
```  

# Negation Profile:  
Load a bean for all profiles except one.  

Ex:
```java
    @Service
    @Profile("!prod")
    public class DebugLogger {
    }
```  
Meaning: Load everywhere except PROD
