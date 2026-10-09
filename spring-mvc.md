
# Spring MVC:  
Spring Boot's web layer is built on top of Spring MVC.  

Spring MVC is a web framework provided by Spring for building, MVC stands for: Model-View-Controller.  
- Web Applications
- REST APIs
- Dynamic Websites  
 
**Think of a real-world banking application:**
```java
	User opens account details page
			↓
	Controller receives request
			↓
	Service processes business logic
			↓
	Repository fetches data
			↓
	Controller returns response
			↓
	View displays data
```  

# Without MVC:
```java
public class UserServlet {

    public void doGet() {

        // request handling

        // business logic

        // database query

        // html generation
    }
}
```  
Everything is mixed together.  

**Problems:** 
* Huge classes
* Difficult maintenance
* Difficult testing
* Difficult debugging
* MVC separates responsibilities.  


# Understanding MVC Components:  

1. **Model:** It represents application data.  
Ex:
```java
public class User {

    private Long id;
    private String name;

    // Getter Setter
}
```  

Data coming from:
```java
	Database
	External API
	File
```  
is usually represented using Model classes.  

2. **View:** It is what user sees.  
```java
    HTML
    JSP
    Thymeleaf
```  

3. **Controller:** Controller handles incoming requests.  


# Spring MVC Architecture:
```java  
	Browser
	   |
	   v
	DispatcherServlet
	   |
	   v
	Controller
	   |
	   v
	Service
	   |
	   v
	Repository
	   |
	   v
	Database
```  

- **DispatcherServlet (Heart of Spring MVC):** This is the Front Controller. All requests first come here. And it decides Which controller should handle request?  

Example: Let's build a small application.  

**Model:**  
```java
public class User {

    private Long id;
    private String name;

    public User(Long id, String name) {
        this.id = id;
        this.name = name;
    }
}
```  

**Repository:**  
```java
@Repository
public class UserRepository {

    public User getUser(Long id) {

        return new User(id, "Ankit");
    }
}
```  

**Service:**  
```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    public User getUser(Long id) {

        return repository.getUser(id);
    }
}
```  

**Controller:**  
```java
@Controller
public class UserController {

    private final UserService service;

    public UserController(UserService service) {
        this.service = service;
    }

    @GetMapping("/users/{id}")
    public String getUser(
            @PathVariable Long id,
            Model model) {

        User user = service.getUser(id);

        model.addAttribute("user", user);

        return "user";
    }
}
```  

**Thymeleaf View:**  
```java
    <html>
    <body>

    <h1>User Details</h1>

    <p th:text="${user.name}"></p>

    </body>
    </html>
```