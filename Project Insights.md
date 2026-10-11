# Real Spring Boot Project Insights

- Spring Web Dependency:  
Spring Web Starter brings together several important libraries.  
| Component | Purpose |
|---|---|
| Spring MVC (`spring-webmvc`) | Handles HTTP requests and routes them to controller methods using components such as `DispatcherServlet`. |
| Embedded Tomcat | Runs your web application and accepts HTTP connections by default. |
| Jackson | Converts Java objects to JSON and JSON to Java objects. |
| Spring Web (`spring-web`) | Provides HTTP-related classes, HTTP abstractions, and web utilities. |
| Spring Boot auto-configuration | Automatically configures web-related components based on the dependencies available on the classpath. |
| Logging (typically Logback) | Provides logging functionality through the default logging setup. |