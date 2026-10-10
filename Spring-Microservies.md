
# Spring Microservices:  
Before learning Microservices, first understand why Microservices came into existence.  

**1. Monolithic Architecture**  
Suppose you're building a Loan Management System with Features:  
```java
   Customer Module
   Loan Module
   EMI Module
   Payment Module
   Notification Module
   Authentication Module
```  
Everything exists in a single application.  
LoanApplication.jar  

**Architecture:**  
```java
   +----------------------+
   | Loan Application     |
   |                      |
   | Customer Module      |
   | Loan Module          |
   | Payment Module       |
   | EMI Module           |
   | Notification Module  |
   +----------------------+
```  
This is called Monolithic Architecture.  


- **Problems With Monolith**
Imagine your application grows.  
```java
   10 Developers
   50 Developers
   100 Developers
```  

**Problems:**  
1. Huge Codebase: Finding bugs becomes difficult.
2. Deployment Issue: Suppose only SMS module changed, Still need to deploy Entire Application.
3. Scaling Problem: Suppose, Payment Module is heavily used. With Monolith, Scale Entire Application.
4. Single Point of Failure: If Notification Module crashes whole application can get affected.  

**Solution: Microservices**  
Break large application into small independent services. Instead of One Large Application, we create Many Small Applications.  

Loan Management Example:  
**Architecture:**  
```java

                  Client
                  |
                  |
         +--------+--------+
                  |
            API Gateway
                  |
      --------------------------------
      |        |        |           |
      |        |        |           |
   Customer  Loan   Payment   Notification
   Service   Service Service    Service
```  

Each service:  
```java
	Independent Project
	Independent Deployment
	Independent Database
```  

# How services are communicated?  

Imagine We Have These Microservices and Each service runs independently:  
```java
   Customer Service  -> Port 8081
   Loan Service      -> Port 8082
   Payment Service   -> Port 8083
   Notification      -> Port 8084
```  

Suppose customer applies for a loan. Now, Loan Service needs customer details.  
```java
   @PostMapping("/apply")
   public void applyLoan(Long customerId) {

      // Need customer details

   }
```  
Loan Service must call: Customer Service to get: Customer Name, Mobile Number, KYC Status  

**Approach 1: Using RestTemplate**  
```java
   String url =
      "http://localhost:8081/customers/100";

   Customer customer =
      restTemplate.getForObject(
         url,
         Customer.class);
```  
It Works But there are problems. Imagine production,
```java
   * Today: Customer Service -> 10.50.20.90:8081
   * Tomorrow: Customer Service -> 10.50.20.100:8081
```  
Your code breaks, Because URL is hardcoded. This creates maintenance issues.  

**1.OpenFeign:**:  
OpenFeign solves this. Instead of writing HTTP code. You write:  
Ex.
```java
   @FeignClient(name = "customer-service")
   public interface CustomerClient {

      @GetMapping("/customers/{id}")
      Customer getCustomer(
            @PathVariable Long id);
   }
```  
Use:  
```java
   Customer customer =
            customerClient.getCustomer(100L);
```  
Looks like: Normal Java Method But internally:
```java
	HTTP Request Sent
	JSON Received
	Object Created
```  
automatically.

- **Without Feign:**
```java
	URL Construction
	Headers
	Response Parsing
	Error Handling
```  
must be coded.

- **With Feign:**  
```java
	customerClient.getCustomer(id);
```  
Very clean.

But Here, there is one problem  
```java
   @FeignClient(
         name = "customer-service")
```  
Where is customer-service running? Means Which server? Which IP? Which port?  

**2.Service Discovery:**  
Service Discovery solves this. Think about Zomato. When you search: McDonalds, Zomato finds nearest branch. Similarly,  
 
Loan Service asks: Where is Customer Service? and gets the latest location.  


**3.Eureka Server:**  
Spring Cloud provides:  
```java
   Netflix Eureka
```  
for service discovery.  

**Architecture:**  
```java
                  Eureka
                     |
         -------------------------
         |           |           |
   Customer      Loan      Payment
   Service      Service     Service
```  
Every service registers.  

**Startup Process:**  

Customer Service starts:  
```java
	I am CUSTOMER-SERVICE
	Running at:
	10.10.20.5:8081
```  
Registers with Eureka.  


Loan Service starts:
```java
	I am LOAN-SERVICE 
	Running at:
	10.10.20.10:8082
```
Registers with Eureka.  


**Communication:**  
Loan Service needs Customer Service.  
Asks Eureka:  
Where is CUSTOMER-SERVICE?  

Eureka replies:  
```java
   10.10.20.5:8081
```  
Now request is routed.  

Without Eureka:  
```java
   Hardcoded URLs
```  

With Eureka:
```java
   Dynamic Service Discovery
```  

**Problem 3: Too Many URLs**  

Without Gateway:  
```java  
   Client
   |
   +--> Customer Service
   |
   +--> Loan Service
   |
   +--> Payment Service
   |
   +--> Auth Service
```  
Frontend must know: 8081, 8082, 8083, 8084, Very bad.  


**4.API Gateway**  
Gateway becomes single entry point.  
```java
   Client
      |
      |
   API Gateway
      |
   -----------------------
   |          |          |
   Loan    Customer   Payment
```  
Client talks only to: Gateway  

# Why API Gateway?
Because it centralizes:  
- Routing: Request Routing
- Authentication: JWT Validation
- Security: Unauthorized Access Block
- Logging: Request Logs
- Rate Limiting: Maximum 100 requests/minute
- Monitoring: Track API Calls  

**5.Spring Cloud Gateway**  
Most modern Spring projects use: Spring Cloud Gateway instead of Zuul.  

Example Gateway Configuration:  
```java
spring:
  cloud:
    gateway:
      routes:

      - id: customer-service

        uri: lb://CUSTOMER-SERVICE

        predicates:
          - Path=/customers/**
```  

Meaning:  
If request starts with:  
```java
   /customers
```  
send to CUSTOMER-SERVICE


# When To Use What?
- OpenFeign: One service needs data from another service.
- Eureka Server: Many services run dynamically and IP/Ports change.
- API Gateway: Multiple Microservices + Single Entry Point Needed.
- Spring Cloud Gateway: You need a modern API Gateway in Spring Boot.  

**Enterprise Flow:**  
```java  
   Client
      |
   Spring Cloud Gateway
      |
      +--------------------+
      |                    |
   Loan Service       Customer Service
      |                    |
      +----OpenFeign-------+
            |
            v
         Eureka
```      
This architecture is used in many real-world applications because it provides scalability, maintainability, centralized security, and dynamic service communication.  

 