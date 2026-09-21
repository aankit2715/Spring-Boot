## What is Spring Boot?
Spring Boot is a Java-based framework built on top of the Spring ecosystem that simplifies application development by providing auto-configuration, embedded servers, and production-ready features with minimal setup. 

## Comparison: Spring vs Spring Boot:

| Aspect        | Spring Framework                                                                 | Spring Boot                                                                 |
|---------------|----------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| Configuration | Requires extensive XML or Java-based configuration. Developers had to manually define beans, dependencies, and application context. | Provides auto-configuration — detects dependencies and configures them automatically, reducing boilerplate. |
| Setup         | Complex project setup; developers had to choose and integrate dependencies individually. | Offers starter dependencies — pre-packaged sets for web, JPA, security, etc. |
| Deployment    | Needed external servers (Tomcat, Jetty). WAR files had to be deployed manually.  | Ships with embedded servers — apps run directly as JAR files (`java -jar app.jar`). |
