# Spring Data JPA:  

**Many developers think:**   
Spring Data JPA = Database Framework  

**Not exactly.**  
	JPA → Specification  
	Hibernate → Implementation of JPA  
	Spring Data JPA → Wrapper on top of JPA  

- What Problem Does Spring Data JPA Solve?  

Suppose we want to fetch a user from database.  

**Using JDBC:**  
```java
Connection con = DriverManager.getConnection(...);

PreparedStatement ps =
    con.prepareStatement(
       "SELECT * FROM USER WHERE ID=?");

ps.setLong(1, id);

ResultSet rs = ps.executeQuery();
```  
Lots of boilerplate code.  

**Using JPA:**  
```java
entityManager.find(User.class, id);
```  

**Using Spring Data JPA:**  
```java
userRepository.findById(id);
```

# Evolution:
```java
	JDBC
	↓
	Hibernate
	↓
	JPA
	↓
	Spring Data JPA
```  

# What is JPA?  
Java Persistence API and Persistence means Store data permanently in database. JPA is only a specification. Think of it like, Interface.  
- JPA = Rules
- Hibernate = Implementation  


# What is Hibernate?  
Hibernate is the most popular implementation of JPA.  

**When you write:**  
```java
entityManager.persist(user);
```
actually Hibernate executes SQL.


# What is Spring Data JPA?  
Spring Data JPA provides repositories.  

**Without Spring Data JPA:**  
```java
EntityManager em;
em.persist(user);
em.find(User.class,id);
```

**With Spring Data JPA:**  
```java
userRepository.save(user);
userRepository.findById(id);
```  

**Ex: Real Project Example**  
Imagine a Loan Application. Need to store customer details.  

Database Table: CUSTOMER  
                     ID, NAME, EMAIL  

**Step 1:** Create Entity  
An Entity represents a table.  
Ex:  
```java
@Entity
@Table(name="CUSTOMER")
public class Customer {

    @Id
    private Long id;

    private String name;

    private String email;

    // getters setters
}
```  

What does @Entity mean? 
Tells Hibernate that This class maps to a DB table.  


**Step 2:** Primary Key  
```java
@Id
private Long id;  // Primary Key Column
```

**Step 3:** Repository  
```java
@Repository
public interface CustomerRepository
       extends JpaRepository<Customer, Long> {
}
```  
Now Spring generates implementation automatically.You don't write: SELECT or INSERT or UPDATE or DELETE manually.  

**What Does JpaRepository Provide?**  
Immediately you get:  
```java
	save()
	findById()
	findAll()
	deleteById()
	count()
	existsById()
```  
for free.  

Ex: Save Customer:  
```java 
Customer customer = new Customer();

customer.setId(1L);
customer.setName("Ankit");
customer.setEmail("abc@gmail.com");

repository.save(customer);
```  

Generated SQL:  
```java
INSERT INTO CUSTOMER
    (ID,NAME,EMAIL)
    VALUES(1,'Ankit','abc@gmail.com')
```  
Hibernate generates SQL automatically.  


Ex: Fetch Customer:  
```java
Customer customer =
     repository.findById(1L)
      .orElse(null);
```  

Generated SQL:  
```java
SELECT *
    FROM CUSTOMER
        WHERE ID=1;
```  

Ex: Fetch All Records  
```java
List<Customer> customers =
	repository.findAll();
```

Generated SQL:
```java
SELECT * FROM CUSTOMER;
```  

Ex: Delete  
```java
repository.deleteById(1L);
```  
Generated SQL:
```java
DELETE FROM CUSTOMER
    WHERE ID=1;
```  

**Example: Complete Flow**  

Controller:  
```java
@RestController
public class CustomerController {

    private final CustomerService service;

    public CustomerController(
           CustomerService service) {

        this.service = service;
    }

    @PostMapping("/customers")
    public String save() {

        service.saveCustomer();

        return "Saved";
    }
}
```  

Service:  
```java
@Service
public class CustomerService {

    private final CustomerRepository repository;

    public CustomerService(
       CustomerRepository repository) {

       this.repository = repository;
    }

    public void saveCustomer() {

        Customer customer =
               new Customer();

        customer.setId(1L);
        customer.setName("Ankit");

        repository.save(customer);
    }
}
```  

Repository:  
```java
public interface CustomerRepository
       extends JpaRepository<Customer,Long> {
}
```  

**Request Flow**
```java
	Controller
		↓
	Service
		↓
	Repository
		↓
	Hibernate
		↓
	Database
```  

- **Custom Query Methods:**    
One of the coolest features.  

Suppose:  
private String email;  
Need customer by email.  

Simply write:  
```java
public interface CustomerRepository
      extends JpaRepository<Customer,Long> {

    Customer findByEmail(String email);
}
```
That's it, No SQL.  


- **Pagination:**    
Suppose table has: 10 lakh records.  

Don't fetch all.  
```java
Page<Customer> customers =
    repository.findAll(
         PageRequest.of(0,10));

SQL:
LIMIT 10
OFFSET 0   // offset = pageNumber * pageSize
Useful for production applications.
```  

- **Sorting:**  
```java
repository.findAll(
	Sort.by("name"));
 

Generated: SQL
ORDER BY NAME
```  

- **@Query Annotation:**  
Sometimes method names become huge.  
```java
@Query(
 "SELECT c FROM Customer c WHERE c.email=:email"
)
Customer getCustomerByEmail(String email);
```  
Custom JPQL query.


# Relationships in JPA:  
Most important topic after basics.  
Suppose:  
```java  
    Customer
        |
        | One Customer
        |
        v
    Many Loans
```  
 
1. **One To Many**  
Ex. Table: Customer
```java
@Entity
public class Customer {

    @Id
    private Long id;

    @OneToMany
    private List<Loan> loans;
}
```  

Table: Loan  
```java
@Entity
public class Loan {

    @Id
    private Long id;
}
```  
Relationship: One customer can have many loans.  


- **@Transactional:** Provides ACID transaction management automatically means Either all operations complete successfully OR none of them should be saved.  

Ex: Suppose a customer applies for a loan.  

**Your business process is:**  
```java
Save Customer
Save Loan Details
Generate Loan Account
Send Notification

public void applyLoan() {

    customerRepository.save(customer);

    loanRepository.save(loan);

    loanAccountRepository.save(account);

    notificationService.sendMessage(customer);
}
```  

Now imagine:  
```java
customerRepository.save(customer); // Success
loanRepository.save(loan);         // Failed

Without transaction:
Customer Saved ✅
Loan Saved ❌

Database state becomes: Inconsistent
CUSTOMER Table
--------------
101 Ankit

LOAN Table
----------
No Record
```  
Customer exists but loan does not. This is called a partial update problem.  

**Solution:** Transaction  
Ex:  
```java
@Transactional
public void applyLoan() {

    customerRepository.save(customer);

    loanRepository.save(loan);

    loanAccountRepository.save(account);

    notificationService.sendMessage(customer);
}
```  
In case of any failure, everything will get rollback.  

**Common Annotations:**  
- @Entity: Marks table mapping.
- @Table(name="CUSTOMER"): Maps to table.
- @Id: Primary key.
- @GeneratedValue Auto-generated IDs.  
  Ex: 
```java
	@GeneratedValue
	private Long id;
```
- @Column(name="EMAIL"): Maps fields to columns.  
	Ex:  
```java
	@Column(name="EMAIL")
	private String email;
```
- @Transactional: Transaction management.

