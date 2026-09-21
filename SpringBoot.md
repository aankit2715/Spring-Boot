## What is Spring Boot?
Spring Boot is a Java-based framework built on top of the Spring ecosystem that simplifies application development by providing auto-configuration, embedded servers, and production-ready features with minimal setup. 

## Auto-Configuration:
- Spring Framework: You had to manually configure beans in XML or Java classes. For example, setting up a DataSource for a database required writing explicit bean definitions.
- Spring Boot: If you add spring-boot-starter-data-jpa and H2 database dependency, Spring Boot auto-configures the DataSource, EntityManagerFactory, and TransactionManager without you writing a single line of config.

```java
// In Spring Boot, just add dependency in pom.xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
</dependency>

// Then write your repository
public interface UserRepository extends JpaRepository<User, Long> {}
```
No XML, no manual bean creation — Boot configures it automatically.

## Embedded Servers:
- Spring Framework: You had to deploy your WAR file into an external Tomcat/Jetty server.
- Spring Boot: Comes with embedded Tomcat/Jetty/Undertow. You just run java -jar app.jar and the server starts inside your application.

```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```
Running this starts an embedded Tomcat server on port 8080 — no external setup needed.

## Production-Ready Features:
- Spring Framework: Monitoring, metrics, and health checks required third-party tools.
- Spring Boot: Includes Actuator out of the box. It exposes endpoints like /actuator/health, /actuator/metrics, /actuator/info.

```java
<!-- Add Actuator dependency -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
Now you can hit:
http://localhost:8080/actuator/health → shows app health status
http://localhost:8080/actuator/metrics → shows JVM memory, CPU usage, etc.
This makes apps production-ready without extra setup.

## Comparison: Spring vs Spring Boot:

| Aspect        | Spring Framework                                                                 | Spring Boot                                                                 |
|---------------|----------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| Configuration | Requires extensive XML or Java-based configuration. Developers had to manually define beans, dependencies, and application context. | Provides auto-configuration — detects dependencies and configures them automatically, reducing boilerplate. |
| Setup         | Complex project setup; developers had to choose and integrate dependencies individually. | Offers starter dependencies — pre-packaged sets for web, JPA, security, etc. |
| Deployment    | Needed external servers (Tomcat, Jetty). WAR files had to be deployed manually.  | Ships with embedded servers — apps run directly as JAR files (`java -jar app.jar`). |
