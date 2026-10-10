# Spring Security:  
Its primary job is:  
**Authentication**  → Who are you?  
**Authorization**   → What are you allowed to do?  

- What Problems Does Spring Security Solve?  

**Without security:**  
- Anyone can access APIs
- Anyone can view data
- Anyone can modify records
- Passwords aren't protected
- Session can be hijacked  

**With Spring Security:**
- Login Authentication
- Role Based Access
- Password Encryption
- CSRF Protection
- Session Management
- JWT Authentication
- OAuth2 Login
- Authentication: Means Are you really who you claim to be?  

Ex:  
**Login Screen**  
    Username : Ankit  
    Password : ******  

**System verifies**  
    Valid User ?  

If yes:  
Authenticated  

- **Authorization:** Means What actions can you perform?  

Ex: Customer logged in  

Can customer approve loans? No  
Can manager approve loans? Yes  

This is called Authorization.  

**Spring Security Request Flow:**
```java
	Client
	  |
	  v
	Security Filter Chain
	  |
	  v
	Authentication
	  |
	  v
	Authorization
	  |
	  v
	Controller
	  |
	  v
	Response
```  
Every request first passes through Spring Security before reaching controller.  

- **Internal FLow:**  

1. Security Filter Chain:   

**What is Filter?**  
A filter intercepts requests.  
Ex: Security Gate at Office Entrance, Employee enters and Security Guard checks Id cards, If Valid then Enter Otherwise Access Denied.    

Basic Security Example  

**Dependency: spring-boot-starter-security**  

Add dependency and start app, Now access: localhost:8080/users. You see: Login Page, Spring Security auto-configures security.  

- **User Configuration:**
```java
@Configuration
public class SecurityConfig {

    @Bean
    public UserDetailsService users() {

        UserDetails user =
                User.builder()
                        .username("ankit")
                        .password("{noop}1234")
                        .roles("USER")
                        .build();

        return new InMemoryUserDetailsManager(user);
    }
}
```  

- How Login Works Internally:  

**User enters:**  
    ankit  
    1234  

**Spring Security:**  
1. Find User
2. Compare Password
3. Create Authentication Object
4. Store in Security Context  

If correct:  
Login Success  


- **UserDetailsService:**  
Spring uses UserDetailsService to fetch user information.  

Ex:
```java
@Service
public class CustomUserDetailsService
       implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(
              String username) {

        return userRepository
                .findByUsername(username);
    }
}
```  

**Real Project Flow:**  
```java
	Login Request
		  |
		  v
	UserDetailsService
		  |
		  v
	UserRepository
		  |
		  v
	Database
```  

- **Password Encoding:**  
Never store password inside database.  
```java
    ID   USER    PASSWORD

    1    ankit   123456
```  

**Use hashing**  
```java
@Bean
public PasswordEncoder passwordEncoder() {

    return new BCryptPasswordEncoder();
}

Password:
123456
 
stored as:
$2a$10$AbCdEfGhIj...
```  
Now database leak won't expose actual passwords.  


- **Roles and Authorization:**  

Suppose:
```java
    USER
    ADMIN
    MANAGER
```  

1. **Create Users:**  
```java
UserDetails admin =
    User.builder()
      .username("admin")
      .password("{noop}1234")
      .roles("ADMIN")
      .build();
```  	  
	  
2. **Security Filter Chain:**  
Modern Spring Security uses:  
Ex:  
```java
@Bean
SecurityFilterChain securityFilterChain(
      HttpSecurity http)
      throws Exception {

    http
       .authorizeHttpRequests(auth ->
          auth
             .requestMatchers("/admin/**")
             .hasRole("ADMIN")

             .requestMatchers("/user/**")
             .hasRole("USER")

             .anyRequest()
             .authenticated()
       )
       .formLogin();

    return http.build();
}
```  

**Meaning:**  
```java
        /admin/** → ADMIN only

        /user/** → USER only

        Everything else → Login required
```  

- **Real Loan Project Example:**

**Endpoints:**  
```java
    /loan/apply
    /loan/view
    /loan/approve
    /admin/users
```  

**Rules:**
```java
    Customer -> Apply Loan

    Employee -> View Loan

    Manager -> Approve Loan

    Admin -> Manage Users
```  

**Configuration:**
```java
    .authorizeHttpRequests(auth -> auth

    .requestMatchers("/loan/apply")
    .hasRole("CUSTOMER")

    .requestMatchers("/loan/view")
    .hasAnyRole(
        "EMPLOYEE",
        "MANAGER")

    .requestMatchers("/loan/approve")
    .hasRole("MANAGER")

    .requestMatchers("/admin/**")
    .hasRole("ADMIN")

    .anyRequest()
    .authenticated())
```  

- **Method Level Security:**  

Protect methods directly.   
@EnableMethodSecurity  
```java
@PreAuthorize(
      "hasRole('MANAGER')")
public void approveLoan() {

}
```  
Only managers can execute.  


- **Session Based Authentication:**  

Traditional websites use:
```java
    Session Authentication
```  

After login:
```java
    Session Created
```  

Server stores:  
```java
    SESSION_ID
```  

Browser stores:  
```java
    JSESSIONID Cookie
```  

Every request:  
```java
    Cookie sent
```  
Server identifies user.  


- **JWT Authentication:**   

Microservices generally use JWT(JSON Web Token) instead of sessions.  `

**Login:**
```java
    POST /login
```  

**Response:**  
```java
    JWT Token
```  

Example:
```java
    eyJhbGciOiJIUzI1Ni...
```  
Client stores token.  

**Next Request:**  
Authorization:
```java
    Bearer eyJhbGciOi...
```  
Spring validates token.  

If valid:  
Request Allowed  


**JWT Flow**
```java  

	Login
	  |
	  v
	Username + Password
	  |
	  v
	Generate JWT
	  |
	  v
	Client Stores JWT
	  |
	  v
	Send JWT in Header
	  |
	  v
	Validate JWT
	  |
	  v
	Access Granted
```  

- **Real Banking Project Example:**  

**Customer login:**  
```java
    Mobile Number
    OTP
```  

**Backend generates JWT:**  
```java
    token
```  

**Customer requests:**  
```java
    GET /loan/details
```  

**Header:**  
```java
    Authorization:
    Bearer token
```  

**Spring Security:**
Validate Token
Check User
Check Role
Return Data

**Common Security Filters**
Behind the scenes Spring executes many filters:

	UsernamePasswordAuthenticationFilter

	BasicAuthenticationFilter

	JwtAuthenticationFilter

	ExceptionTranslationFilter

	AuthorizationFilter

These execute before controller.


- SecurityContext

**After successful login:**
Authentication auth =
		SecurityContextHolder
		.getContext()
		.getAuthentication();
 

**Contains:**
Username
Roles
Authorities
Current logged-in user information lives here.

