# Comprehensive Java Interview Questions (3+ Years Experience)

## Table of Contents
1. [Core Java](#1-core-java)
2. [Advanced Java](#2-advanced-java)
3. [Spring Boot](#3-spring-boot)
4. [SQL](#4-sql)
5. [NoSQL](#5-nosql)
6. [Multithreading](#6-multithreading)
7. [Design Patterns](#7-design-patterns)
8. [System Design & Architecture](#8-system-design--architecture)
9. [Performance & Optimization](#9-performance--optimization)
10. [Testing](#10-testing)

---

## 1. Core Java

### **Q1:** What is the difference between `==` and `.equals()` in Java?
**Answer:** `==` compares references (memory addresses), while `.equals()` compares object content.
```java
String a = new String("test");
String b = new String("test");
String c = "test";
String d = "test";

System.out.println(a == b);      // false (different objects)
System.out.println(a.equals(b)); // true (same content)
System.out.println(c == d);      // true (string pool)
System.out.println(c.equals(d)); // true (same content)


### **Q2:** Explain method overloading vs method overriding with examples.
**Answer:**
```java
// Method Overloading (Compile-time polymorphism)
class Calculator {
    public int add(int a, int b) { return a + b; }
    public double add(double a, double b) { return a + b; }
    public int add(int a, int b, int c) { return a + b + c; }
}

// Method Overriding (Runtime polymorphism)
class Animal {
    public void makeSound() { System.out.println("Some sound"); }
}
class Dog extends Animal {
    @Override
    public void makeSound() { System.out.println("Woof!"); }
}
```

### **Q3:** What are the different types of memory areas in JVM?
**Answer:**
- **Heap**: Stores objects and instance variables
- **Stack**: Stores method calls and local variables
- **Method Area**: Stores class-level data, constants
- **PC Register**: Program Counter
- **Native Method Stack**: For native method calls

```java
public class MemoryExample {
    private static String classVariable = "I'm in Method Area"; // Method Area
    private String instanceVariable = "I'm in Heap"; // Heap
    
    public void method() {
        int localVariable = 10; // Stack
        String localObject = new String("I'm in Heap"); // Reference in Stack, Object in Heap
    }
}
```

### **Q4:** Explain the Collections Framework hierarchy.
**Answer:**
```java
// List implementations
List<String> arrayList = new ArrayList<>();     // Resizable array
List<String> linkedList = new LinkedList<>();   // Doubly-linked list
List<String> vector = new Vector<>();           // Synchronized ArrayList

// Set implementations
Set<String> hashSet = new HashSet<>();          // Hash table, no order
Set<String> linkedHashSet = new LinkedHashSet<>(); // Hash table + linked list
Set<String> treeSet = new TreeSet<>();          // Red-black tree, sorted

// Map implementations
Map<String, Integer> hashMap = new HashMap<>();
Map<String, Integer> linkedHashMap = new LinkedHashMap<>();
Map<String, Integer> treeMap = new TreeMap<>();
Map<String, Integer> concurrentHashMap = new ConcurrentHashMap<>();
```
### **Q5:** What is the difference between checked and unchecked exceptions?
**Answer:**
```java
// Checked Exception - Must be handled or declared
public void readFile(String fileName) throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader(fileName));
    // Must handle IOException
}

// Unchecked Exception - Runtime exceptions
public void divide(int a, int b) {
    int result = a / b; // May throw ArithmeticException
}

// Custom checked exception
class BusinessException extends Exception {
    public BusinessException(String message) { super(message); }
}

// Custom unchecked exception
class ValidationException extends RuntimeException {
    public ValidationException(String message) { super(message); }
}
```

### **Q6:** Explain Java 8 features with examples.
**Answer:**
```java
// Lambda expressions
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
numbers.forEach(n -> System.out.println(n));

// Stream API
List<Integer> evenNumbers = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());

// Optional
Optional<String> optional = Optional.ofNullable(getName());
optional.ifPresent(System.out::println);
String result = optional.orElse("Default Name");

// Method references
numbers.stream().forEach(System.out::println);

// Default methods in interfaces
interface Drawable {
    void draw();
    default void print() { System.out.println("Printing..."); }
}
```

---
## 2. Advanced Java

### **Q1:** Explain the Servlet lifecycle with example.
**Answer:**
```java
@WebServlet("/user")
public class UserServlet extends HttpServlet {
    
    @Override
    public void init() throws ServletException {
        // Called once when servlet is first loaded
        System.out.println("Servlet initialized");
    }
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // Called for each GET request
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<h1>User Data</h1>");
    }
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // Called for each POST request
        String userName = request.getParameter("name");
        // Process user data
    }
    
    @Override
    public void destroy() {
        // Called when servlet is being destroyed
        System.out.println("Servlet destroyed");
    }
}
```

### **Q2:** What is JDBC and show connection pooling example?
**Answer:**
```java
// Basic JDBC connection
public class DatabaseConnection {
    private static final String URL = "jdbc:mysql://localhost:3306/mydb";
    private static final String USERNAME = "user";
    private static final String PASSWORD = "password";
    
    public Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USERNAME, PASSWORD);
    }
    
    // Using try-with-resources
    public List<User> getAllUsers() {
        List<User> users = new ArrayList<>();
        String sql = "SELECT * FROM users";
        
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql);
             ResultSet rs = stmt.executeQuery()) {
            
            while (rs.next()) {
                User user = new User();
                user.setId(rs.getLong("id"));
                user.setName(rs.getString("name"));
                users.add(user);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return users;
    }
}

// Connection Pooling with HikariCP
@Configuration
public class DatabaseConfig {
    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        config.setUsername("user");
        config.setPassword("password");
        config.setMaximumPoolSize(20);
        config.setMinimumIdle(5);
        return new HikariDataSource(config);
    }
}
```

### **Q3:** Explain JSP lifecycle and implicit objects.
**Answer:**
```jsp
<%-- JSP Example --%>
<%@ page language="java" contentType="text/html; charset=UTF-8" %>
<html>
<head><title>User Profile</title></head>
<body>
    <%
        // Implicit objects
        String userName = request.getParameter("name");
        session.setAttribute("user", userName);
        application.setAttribute("totalUsers", 100);
        
        out.println("<h1>Welcome " + userName + "</h1>");
        out.println("<p>Session ID: " + session.getId() + "</p>");
    %>
    
    <!-- EL and JSTL -->
    <p>User: ${sessionScope.user}</p>
    <p>Total Users: ${applicationScope.totalUsers}</p>
    
    <c:if test="${not empty param.name}">
        <p>Hello, ${param.name}!</p>
    </c:if>
</body>
</html>
```

---
## 3. Spring Boot

### **Q1:** Create a complete REST API with validation and exception handling.
**Answer:**
```java
// Entity
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @NotBlank(message = "Name is required")
    @Size(min = 2, max = 50, message = "Name must be between 2 and 50 characters")
    private String name;
    
    @Email(message = "Invalid email format")
    @NotBlank(message = "Email is required")
    private String email;
    
    @Min(value = 18, message = "Age must be at least 18")
    private Integer age;
    
    // Constructors, getters, setters
}

// Repository
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    List<User> findByAgeGreaterThan(Integer age);
}

// Service
@Service
@Transactional
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public User createUser(User user) {
        if (userRepository.findByEmail(user.getEmail()).isPresent()) {
            throw new BusinessException("User with email already exists");
        }
        return userRepository.save(user);
    }
    
    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException("User not found with id: " + id));
    }
    
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    public User updateUser(Long id, User userDetails) {
        User user = getUserById(id);
        user.setName(userDetails.getName());
        user.setEmail(userDetails.getEmail());
        user.setAge(userDetails.getAge());
        return userRepository.save(user);
    }
    
    public void deleteUser(Long id) {
        User user = getUserById(id);
        userRepository.delete(user);
    }
}

// Controller
@RestController
@RequestMapping("/api/users")
@CrossOrigin(origins = "*")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @PostMapping
    public ResponseEntity<User> createUser(@Valid @RequestBody User user) {
        User createdUser = userService.createUser(user);
        return ResponseEntity.status(HttpStatus.CREATED).body(createdUser);
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        User user = userService.getUserById(id);
        return ResponseEntity.ok(user);
    }
    
    @GetMapping
    public ResponseEntity<List<User>> getAllUsers(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size) {
        Pageable pageable = PageRequest.of(page, size);
        Page<User> users = userRepository.findAll(pageable);
        return ResponseEntity.ok(users.getContent());
    }
    
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, 
                                         @Valid @RequestBody User userDetails) {
        User updatedUser = userService.updateUser(id, userDetails);
        return ResponseEntity.ok(updatedUser);
    }
    
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}

// Exception Handling
@ControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse("USER_NOT_FOUND", ex.getMessage());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(
            MethodArgumentNotValidException ex) {
        List<String> errors = ex.getBindingResult().getFieldErrors()
            .stream()
            .map(FieldError::getDefaultMessage)
            .collect(Collectors.toList());
        
        ErrorResponse error = new ErrorResponse("VALIDATION_ERROR", "Validation failed", errors);
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }
}

// Custom Exception
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}
```

### **Q2:** Explain Spring Boot auto-configuration and profiles.
**Answer:**
```java
// Custom Auto-configuration
@Configuration
@ConditionalOnClass(DataSource.class)
@ConditionalOnProperty(prefix = "app.database", name = "enabled", havingValue = "true")
@EnableConfigurationProperties(DatabaseProperties.class)
public class DatabaseAutoConfiguration {
    
    @Autowired
    private DatabaseProperties properties;
    
    @Bean
    @ConditionalOnMissingBean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl(properties.getUrl());
        config.setUsername(properties.getUsername());
        config.setPassword(properties.getPassword());
        return new HikariDataSource(config);
    }
}

@ConfigurationProperties(prefix = "app.database")
@Data
public class DatabaseProperties {
    private String url;
    private String username;
    private String password;
    private boolean enabled = true;
}
```

```yaml
# application.yml
spring:
  profiles:
    active: dev

---
# Development profile
spring:
  profiles: dev
  datasource:
    url: jdbc:h2:mem:testdb
    username: sa
    password: 
  h2:
    console:
      enabled: true

---
# Production profile
spring:
  profiles: prod
  datasource:
    url: jdbc:mysql://prod-server:3306/myapp
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
```

### **Q3:** What is dependency injection and its types in Spring?
**Answer:**
Dependency Injection is a design pattern where dependencies are provided to an object rather than the object creating them.

```java
// Constructor Injection (Recommended)
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

// Setter Injection
@Service
public class UserService {
    private UserRepository userRepository;
    
    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

// Field Injection (Not recommended)
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
}
```

### **Q4:** Explain the four pillars of OOP with examples.
**Answer:**
```java
// 1. Encapsulation - Data hiding
class BankAccount {
    private double balance;
    
    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }
    
    public double getBalance() { return balance; }
}

// 2. Inheritance - Code reusability
abstract class Vehicle {
    protected String brand;
    abstract void start();
}

class Car extends Vehicle {
    void start() { System.out.println("Car starts with key"); }
}

// 3. Polymorphism - One interface, multiple implementations
interface Shape {
    double calculateArea();
}

class Circle implements Shape {
    private double radius;
    public double calculateArea() { return Math.PI * radius * radius; }
}

class Rectangle implements Shape {
    private double length, width;
    public double calculateArea() { return length * width; }
}

// 4. Abstraction - Hide implementation details
abstract class DatabaseConnection {
    abstract void connect();
    abstract void disconnect();
    
    public void executeQuery(String sql) {
        connect();
        // Execute query logic
        disconnect();
    }
}
```

### **Q5:** What is the difference between String, StringBuilder, and StringBuffer?
**Answer:**
```java
// String - Immutable, creates new object for each operation
String str = "Hello";
str += " World"; // Creates new String object

// StringBuilder - Mutable, not thread-safe, faster
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World"); // Modifies existing object

// StringBuffer - Mutable, thread-safe, slower
StringBuffer sbf = new StringBuffer("Hello");
sbf.append(" World"); // Thread-safe operations

// Performance comparison
public void stringPerformanceTest() {
    // Slow - creates 1000 String objects
    String str = "";
    for (int i = 0; i < 1000; i++) {
        str += "a";
    }
    
    // Fast - modifies single StringBuilder
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < 1000; i++) {
        sb.append("a");
    }
}
```

---
## 4. SQL

### **Q1:** What is the difference between INNER JOIN and LEFT JOIN?
**Answer:**
```sql
-- INNER JOIN - Returns only matching records from both tables
SELECT u.name, o.order_date
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

-- LEFT JOIN - Returns all records from left table and matching from right
SELECT u.name, o.order_date
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;

-- Example with sample data
-- Users: {1: "John", 2: "Jane", 3: "Bob"}
-- Orders: {101: user_id=1, 102: user_id=1, 103: user_id=2}

-- INNER JOIN Result: John (2 rows), Jane (1 row)
-- LEFT JOIN Result: John (2 rows), Jane (1 row), Bob (1 row with null order)
```

### **Q2:** Write a query to find the 2nd highest salary.
**Answer:**
```sql
-- Method 1: Using subquery
SELECT MAX(salary) as second_highest
FROM employees 
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: Using LIMIT (MySQL)
SELECT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- Method 3: Using ROW_NUMBER()
SELECT salary
FROM (
    SELECT salary, ROW_NUMBER() OVER (ORDER BY salary DESC) as rn
    FROM employees
) ranked
WHERE rn = 2;

-- Method 4: Find Nth highest salary
SELECT salary
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) as rank
    FROM employees
) ranked
WHERE rank = 2;
```

### **Q3:** Write a query to find duplicate records in a table.
**Answer:**
```sql
-- Find duplicate emails
SELECT email, COUNT(*) as count
FROM users
GROUP BY email
HAVING COUNT(*) > 1;

-- Find all records with duplicate emails
SELECT u1.*
FROM users u1
INNER JOIN (
    SELECT email
    FROM users
    GROUP BY email
    HAVING COUNT(*) > 1
) u2 ON u1.email = u2.email
ORDER BY u1.email;

-- Delete duplicates, keep only one record
DELETE u1 FROM users u1
INNER JOIN users u2
WHERE u1.id > u2.id AND u1.email = u2.email;
```

### **Q4:** Difference between WHERE and HAVING clauses.
**Answer:**
```sql
-- WHERE - Filters rows before grouping
SELECT department, AVG(salary)
FROM employees
WHERE salary > 50000  -- Filters individual records
GROUP BY department;

-- HAVING - Filters groups after grouping
SELECT department, AVG(salary)
FROM employees
GROUP BY department
HAVING AVG(salary) > 60000;  -- Filters grouped results

-- Combined usage
SELECT department, COUNT(*) as emp_count, AVG(salary) as avg_salary
FROM employees
WHERE hire_date > '2020-01-01'  -- Filter before grouping
GROUP BY department
HAVING COUNT(*) > 5;  -- Filter after grouping
```

---
## 5. NoSQL

### **Q1:** What is NoSQL? How is it different from SQL databases?
**Answer:**

| Aspect | SQL (RDBMS) | NoSQL |
|--------|-------------|-------|
| Schema | Fixed schema | Schema-less/flexible |
| Scalability | Vertical scaling | Horizontal scaling |
| ACID | Full ACID compliance | Eventual consistency |
| Query Language | SQL | Varies (MongoDB: JSON-like) |
| Use Cases | Complex transactions | Big data, real-time apps |

```java
// MongoDB Example
@Document(collection = "users")
public class User {
    @Id
    private String id;
    private String name;
    private List<String> skills;
    private Address address;
    
    // Nested document
    public static class Address {
        private String city;
        private String country;
    }
}

@Repository
public interface UserRepository extends MongoRepository<User, String> {
    List<User> findBySkillsContaining(String skill);
    List<User> findByAddressCity(String city);
}
```

### **Q2:** Design a MongoDB schema for a chat application.
**Answer:**
```javascript
// Chat Room Collection
{
  "_id": ObjectId("..."),
  "name": "General Discussion",
  "type": "group", // "direct" or "group"
  "participants": [
    {
      "userId": ObjectId("..."),
      "role": "admin",
      "joinedAt": ISODate("2024-01-01")
    }
  ],
  "createdAt": ISODate("2024-01-01"),
  "updatedAt": ISODate("2024-08-22")
}

// Messages Collection
{
  "_id": ObjectId("..."),
  "chatId": ObjectId("..."),
  "senderId": ObjectId("..."),
  "messageType": "text", // "text", "image", "file"
  "content": "Hello everyone!",
  "metadata": {
    "fileName": "document.pdf",
    "fileSize": 1024,
    "imageUrl": "https://..."
  },
  "replyTo": ObjectId("..."), // For message replies
  "reactions": [
    {
      "userId": ObjectId("..."),
      "emoji": "👍",
      "timestamp": ISODate("...")
    }
  ],
  "isDeleted": false,
  "createdAt": ISODate("2024-08-22"),
  "updatedAt": ISODate("2024-08-22")
}

// Users Collection
{
  "_id": ObjectId("..."),
  "username": "john_doe",
  "email": "john@example.com",
  "profile": {
    "firstName": "John",
    "lastName": "Doe",
    "avatar": "https://...",
    "status": "online" // "online", "offline", "away"
  },
  "lastSeen": ISODate("2024-08-22")
}
```

### **Q3:** Redis use cases and examples.
**Answer:**
```java
// Caching with Redis
@Service
public class UserService {
    
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    @Autowired
    private UserRepository userRepository;
    
    public User getUserById(Long id) {
        String cacheKey = "user:" + id;
        
        // Check cache first
        User cachedUser = (User) redisTemplate.opsForValue().get(cacheKey);
        if (cachedUser != null) {
            return cachedUser;
        }
        
        // Fetch from database
        User user = userRepository.findById(id).orElse(null);
        if (user != null) {
            // Cache for 1 hour
            redisTemplate.opsForValue().set(cacheKey, user, 3600, TimeUnit.SECONDS);
        }
        
        return user;
    }
    
    // Session management
    public void saveSession(String sessionId, UserSession session) {
        redisTemplate.opsForValue().set("session:" + sessionId, session, 30, TimeUnit.MINUTES);
    }
    
    // Rate limiting
    public boolean isRateLimited(String clientId) {
        String key = "rate_limit:" + clientId;
        String count = (String) redisTemplate.opsForValue().get(key);
        
        if (count == null) {
            redisTemplate.opsForValue().set(key, "1", 60, TimeUnit.SECONDS);
            return false;
        }
        
        int currentCount = Integer.parseInt(count);
        if (currentCount >= 100) { // 100 requests per minute
            return true;
        }
        
        redisTemplate.opsForValue().increment(key);
        return false;
    }
}
```

---
## 6. Multithreading

### **Q1:** Difference between Runnable and Callable.
**Answer:**
```java
// Runnable - No return value, no checked exceptions
class PrintTask implements Runnable {
    private String message;
    
    public PrintTask(String message) {
        this.message = message;
    }
    
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + ": " + message);
    }
}

// Callable - Returns value, can throw checked exceptions
class CalculateTask implements Callable<Integer> {
    private int number;
    
    public CalculateTask(int number) {
        this.number = number;
    }
    
    @Override
    public Integer call() throws Exception {
        if (number < 0) {
            throw new IllegalArgumentException("Number must be positive");
        }
        
        Thread.sleep(1000); // Simulate work
        return number * number;
    }
}

// Usage example
public class ThreadExample {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        // Submit Runnable
        executor.submit(new PrintTask("Hello World"));
        
        // Submit Callable and get result
        Future<Integer> future = executor.submit(new CalculateTask(5));
        Integer result = future.get(); // Blocking call
        System.out.println("Result: " + result);
        
        executor.shutdown();
    }
}
```
### **Q2:** Explain synchronization and its types.
**Answer:**
```java
// 1. Synchronized method
class Counter {
    private int count = 0;
    
    public synchronized void increment() {
        count++; // Thread-safe
    }
    
    public synchronized int getCount() {
        return count;
    }
}

// 2. Synchronized block
class BankAccount {
    private double balance = 0;
    private final Object lock = new Object();
    
    public void deposit(double amount) {
        synchronized (lock) {
            balance += amount;
        }
    }
    
    public void withdraw(double amount) {
        synchronized (lock) {
            if (balance >= amount) {
                balance -= amount;
            }
        }
    }
}

// 3. ReentrantLock (more flexible)
class AdvancedCounter {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();
    
    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
    
    public boolean tryIncrement() {
        if (lock.tryLock()) {
            try {
                count++;
                return true;
            } finally {
                lock.unlock();
            }
        }
        return false;
    }
}

// 4. Atomic classes (lock-free)
class AtomicCounter {
    private AtomicInteger count = new AtomicInteger(0);
    
    public void increment() {
        count.incrementAndGet(); // Atomic operation
    }
    
    public int getCount() {
        return count.get();
    }
}
```
### **Q3:** Producer-Consumer problem using BlockingQueue.
**Answer:**



### **Q4:** What is deadlock and how to prevent it?
**Answer:**
```java
// Deadlock example - DON'T DO THIS
class DeadlockExample {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void method1() {
        synchronized (lock1) {
            System.out.println("Method1: Acquired lock1");
            try { Thread.sleep(100); } catch (InterruptedException e) {}
            
            synchronized (lock2) { // Waits for lock2
                System.out.println("Method1: Acquired lock2");
            }
        }
    }
    
    public void method2() {
        synchronized (lock2) {
            System.out.println("Method2: Acquired lock2");
            try { Thread.sleep(100); } catch (InterruptedException e) {}
            
            synchronized (lock1) { // Waits for lock1
                System.out.println("Method2: Acquired lock1");
            }
        }
    }
}

// Solution 1: Ordered locking
class DeadlockPrevention {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void method1() {
        synchronized (lock1) { // Always acquire lock1 first
            synchronized (lock2) {
                // Do work
            }
        }
    }
    
    public void method2() {
        synchronized (lock1) { // Always acquire lock1 first
            synchronized (lock2) {
                // Do work
            }
        }
    }
}

// Solution 2: Using tryLock with timeout
class DeadlockPreventionWithTimeout {
    private final ReentrantLock lock1 = new ReentrantLock();
    private final ReentrantLock lock2 = new ReentrantLock();
    
    public void method1() throws InterruptedException {
        if (lock1.tryLock(5, TimeUnit.SECONDS)) {
            try {
                if (lock2.tryLock(5, TimeUnit.SECONDS)) {
                    try {
                        // Do work
                    } finally {
                        lock2.unlock();
                    }
                }
            } finally {
                lock1.unlock();
            }
        }
    }
}
```

## 7. Design Patterns

### **Q1:** Singleton Pattern with thread safety.
**Answer:**
```java
// 1. Eager initialization (thread-safe but may waste memory)
class EagerSingleton {
    private static final EagerSingleton INSTANCE = new EagerSingleton();
    
    private EagerSingleton() {}
    
    public static EagerSingleton getInstance() {
        return INSTANCE;
    }
}

// 2. Double-checked locking (thread-safe and lazy)
class LazyThreadSafeSingleton {
    private static volatile LazyThreadSafeSingleton instance;
    
    private LazyThreadSafeSingleton() {}
    
    public static LazyThreadSafeSingleton getInstance() {
        if (instance == null) {
            synchronized (LazyThreadSafeSingleton.class) {
                if (instance == null) {
                    instance = new LazyThreadSafeSingleton();
                }
            }
        }
        return instance;
    }
}

// 3. Enum singleton (best approach)
enum EnumSingleton {
    INSTANCE;
    
    public void doSomething() {
        System.out.println("Doing something...");
    }
}

// 4. Bill Pugh solution (using inner static class)
class BillPughSingleton {
    private BillPughSingleton() {}
    
    private static class SingletonHelper {
        private static final BillPughSingleton INSTANCE = new BillPughSingleton();
    }
    
    public static BillPughSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```
### **Q2:** Factory Pattern vs Abstract Factory Pattern.
**Answer:**
```java
// Factory Pattern - Creates objects of a single family
interface Shape {
    void draw();
}

class Circle implements Shape {
    public void draw() { System.out.println("Drawing Circle"); }
}

class Rectangle implements Shape {
    public void draw() { System.out.println("Drawing Rectangle"); }
}

class ShapeFactory {
    public static Shape createShape(String type) {
        switch (type.toLowerCase()) {
            case "circle": return new Circle();
            case "rectangle": return new Rectangle();
            default: throw new IllegalArgumentException("Unknown shape: " + type);
        }
    }
}

// Abstract Factory Pattern - Creates families of related objects
interface GUIFactory {
    Button createButton();
    TextField createTextField();
}

interface Button {
    void click();
}

interface TextField {
    void type(String text);
}

// Windows implementation
class WindowsButton implements Button {
    public void click() { System.out.println("Windows button clicked"); }
}

class WindowsTextField implements TextField {
    public void type(String text) { System.out.println("Windows text: " + text); }
}

class WindowsFactory implements GUIFactory {
    public Button createButton() { return new WindowsButton(); }
    public TextField createTextField() { return new WindowsTextField(); }
}

// Mac implementation
class MacButton implements Button {
    public void click() { System.out.println("Mac button clicked"); }
}

class MacTextField implements TextField {
    public void type(String text) { System.out.println("Mac text: " + text); }
}

class MacFactory implements GUIFactory {
    public Button createButton() { return new MacButton(); }
    public TextField createTextField() { return new MacTextField(); }
}

// Usage
public class FactoryExample {
    public static void main(String[] args) {
        // Factory Pattern
        Shape circle = ShapeFactory.createShape("circle");
        circle.draw();
        
        // Abstract Factory Pattern
        GUIFactory factory = new WindowsFactory();
        Button button = factory.createButton();
        TextField textField = factory.createTextField();
        
        button.click();
        textField.type("Hello World");
    }
}
```

### **Q3:** Observer Pattern example.
**Answer:**

## 8. System Design & Architecture

### **Q1:** How do you handle millions of requests in a Spring Boot application?
**Answer:**

```java
// 1. Connection Pooling
@Configuration
public class DatabaseConfig {
    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        config.setMaximumPoolSize(50); // Adjust based on load
        config.setMinimumIdle(10);
        config.setConnectionTimeout(30000);
        config.setIdleTimeout(600000);
        return new HikariDataSource(config);
    }
}

// 2. Caching Strategy
@Service
public class ProductService {
    
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    @Cacheable(value = "products", key = "#id")
    public Product getProduct(Long id) {
        return productRepository.findById(id).orElse(null);
    }
    
    @CacheEvict(value = "products", key = "#product.id")
    public Product updateProduct(Product product) {
        return productRepository.save(product);
    }
}

// 3. Async Processing
@Service
public class OrderService {
    
    @Async
    public CompletableFuture<Void> processOrderAsync(Order order) {
        // Heavy processing
        sendEmailNotification(order);
        updateInventory(order);
        return CompletableFuture.completedFuture(null);
    }
    
    @EventListener
    public void handleOrderCreated(OrderCreatedEvent event) {
        processOrderAsync(event.getOrder());
    }
}

// 4. Rate Limiting
@Component
public class RateLimitingFilter implements Filter {
    
    @Autowired
    private RedisTemplate<String, String> redisTemplate;
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) throws IOException, ServletException {
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        String clientId = getClientId(httpRequest);
        
        if (isRateLimited(clientId)) {
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            httpResponse.setStatus(429); // Too Many Requests
            return;
        }
        
        chain.doFilter(request, response);
    }
    
    private boolean isRateLimited(String clientId) {
        String key = "rate_limit:" + clientId;
        String count = redisTemplate.opsForValue().get(key);
        
        if (count == null) {
            redisTemplate.opsForValue().set(key, "1", Duration.ofMinutes(1));
            return false;
        }
        
        int currentCount = Integer.parseInt(count);
        if (currentCount >= 1000) { // 1000 requests per minute
            return true;
        }
        
        redisTemplate.opsForValue().increment(key);
        return false;
    }
}
```

**Architecture Strategies:**
1. **Load Balancing**: Use Nginx, HAProxy, or cloud load balancers
2. **Microservices**: Break monolith into smaller services
3. **Database Optimization**: Read replicas, sharding, indexing
4. **CDN**: Cache static content globally
5. **Message Queues**: Use RabbitMQ, Kafka for async processing

---

## 9. Performance & Optimization

### **Q1:** How do you optimize database queries?
**Answer:**
```sql
-- 1. Use indexes
CREATE INDEX idx_user_email ON users(email);
CREATE INDEX idx_order_date ON orders(order_date);
CREATE COMPOSITE INDEX idx_user_status ON users(status, created_date);

-- 2. Avoid N+1 queries - Use JOIN instead
-- Bad: N+1 problem
SELECT * FROM users; -- 1 query
-- Then for each user: SELECT * FROM orders WHERE user_id = ?; -- N queries

-- Good: Single query with JOIN
SELECT u.*, o.* 
FROM users u 
LEFT JOIN orders o ON u.id = o.user_id;

-- 3. Use LIMIT for pagination
SELECT * FROM products 
ORDER BY created_date DESC 
LIMIT 20 OFFSET 0;

-- 4. Use WHERE clause to filter early
SELECT * FROM orders 
WHERE status = 'pending' 
  AND created_date >= '2024-01-01'
ORDER BY created_date DESC;
```

```java
// JPA Optimization
@Entity
public class User {
    @OneToMany(mappedBy = "user", fetch = FetchType.LAZY) // Lazy loading
    private List<Order> orders;
}

// Use @Query for complex queries
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    @Query("SELECT u FROM User u LEFT JOIN FETCH u.orders WHERE u.status = :status")
    List<User> findUsersWithOrders(@Param("status") String status);
    
    // Pagination
    @Query("SELECT u FROM User u WHERE u.name LIKE %:name%")
    Page<User> findByNameContaining(@Param("name") String name, Pageable pageable);
}
```

### **Q2:** JVM tuning for production applications.
**Answer:**
```bash
# JVM Memory Tuning
java -Xms4g -Xmx8g \
     -XX:NewRatio=2 \
     -XX:+UseG1GC \
     -XX:MaxGCPauseMillis=200 \
     -XX:+PrintGC \
     -XX:+PrintGCDetails \
     -XX:+HeapDumpOnOutOfMemoryError \
     -XX:HeapDumpPath=/var/log/heapdumps/ \
     -jar myapp.jar

# Explanation:
# -Xms4g: Initial heap size 4GB
# -Xmx8g: Maximum heap size 8GB
# -XX:NewRatio=2: Old gen is 2x size of young gen
# -XX:+UseG1GC: Use G1 garbage collector
# -XX:MaxGCPauseMillis=200: Target GC pause time
```

---
## 10. Testing

### **Q1:** Write unit tests for a service class.
**Answer:**
```java
// Service to test
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    @Autowired
    private EmailService emailService;
    
    public User createUser(User user) {
        if (userRepository.existsByEmail(user.getEmail())) {
            throw new UserAlreadyExistsException("User already exists");
        }
        
        User savedUser = userRepository.save(user);
        emailService.sendWelcomeEmail(savedUser.getEmail());
        
        return savedUser;
    }
    
    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException("User not found"));
    }
}

// Unit Tests
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    
    @Mock
    private UserRepository userRepository;
    
    @Mock
    private EmailService emailService;
    
    @InjectMocks
    private UserService userService;
    
    @Test
    void createUser_Success() {
        // Given
        User user = new User("john@example.com", "John Doe");
        when(userRepository.existsByEmail(user.getEmail())).thenReturn(false);
        when(userRepository.save(user)).thenReturn(user);
        
        // When
        User result = userService.createUser(user);
        
        // Then
        assertThat(result).isEqualTo(user);
        verify(userRepository).save(user);
        verify(emailService).sendWelcomeEmail(user.getEmail());
    }
    
    @Test
    void createUser_UserAlreadyExists_ThrowsException() {
        // Given
        User user = new User("john@example.com", "John Doe");
        when(userRepository.existsByEmail(user.getEmail())).thenReturn(true);
        
        // When & Then
        assertThrows(UserAlreadyExistsException.class, () -> {
            userService.createUser(user);
        });
        
        verify(userRepository, never()).save(any());
        verify(emailService, never()).sendWelcomeEmail(any());
    }
    
    @Test
    void getUserById_UserExists_ReturnsUser() {
        // Given
        Long userId = 1L;
        User user = new User("john@example.com", "John Doe");
        when(userRepository.findById(userId)).thenReturn(Optional.of(user));
        
        // When
        User result = userService.getUserById(userId);
        
        // Then
        assertThat(result).isEqualTo(user);
    }
    
    @Test
    void getUserById_UserNotFound_ThrowsException() {
        // Given
        Long userId = 1L;
        when(userRepository.findById(userId)).thenReturn(Optional.empty());
        
        // When & Then
        assertThrows(UserNotFoundException.class, () -> {
            userService.getUserById(userId);
        });
    }
}
```

### **Q2:** Integration testing with TestContainers.
**Answer:**
```java
@SpringBootTest
@Testcontainers
class UserRepositoryIntegrationTest {
    
    @Container
    static MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.0")
            .withDatabaseName("testdb")
            .withUsername("test")
            .withPassword("test");
    
    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", mysql::getJdbcUrl);
        registry.add("spring.datasource.username", mysql::getUsername);
        registry.add("spring.datasource.password", mysql::getPassword);
    }
    
    @Autowired
    private TestEntityManager entityManager;
    
    @Autowired
    private UserRepository userRepository;
    
    @Test
    void findByEmail_UserExists_ReturnsUser() {
        // Given
        User user = new User("test@example.com", "Test User");
        entityManager.persistAndFlush(user);
        
        // When
        Optional<User> found = userRepository.findByEmail("test@example.com");
        
        // Then
        assertThat(found).isPresent();
        assertThat(found.get().getName()).isEqualTo("Test User");
    }
}
```

---

## 11. Coding Problems (Frequently Asked)

### **Q1:** Find duplicate elements in an array using HashSet.
**Answer:**
```java
public class FindDuplicates {
    
    // Method 1: Using HashSet
    public static List<Integer> findDuplicates(int[] arr) {
        Set<Integer> seen = new HashSet<>();
        Set<Integer> duplicates = new HashSet<>();
        
        for (int num : arr) {
            if (!seen.add(num)) { // add() returns false if element already exists
                duplicates.add(num);
            }
        }
        
        return new ArrayList<>(duplicates);
    }
    
    // Method 2: Using frequency map
    public static List<Integer> findDuplicatesWithCount(int[] arr) {
        Map<Integer, Integer> frequency = new HashMap<>();
        
        // Count frequencies
        for (int num : arr) {
            frequency.put(num, frequency.getOrDefault(num, 0) + 1);
        }
        
        // Find duplicates
        return frequency.entrySet().stream()
                .filter(entry -> entry.getValue() > 1)
                .map(Map.Entry::getKey)
                .collect(Collectors.toList());
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 2, 4, 1, 5, 1};
        System.out.println(findDuplicates(arr)); // [1, 2]
    }
}
```
### **Q2:** Reverse a string without using library functions.
**Answer:**
```java
public class StringReversal {
    
    // Method 1: Using character array
    public static String reverse(String str) {
        if (str == null || str.length() <= 1) {
            return str;
        }
        
        char[] chars = str.toCharArray();
        int left = 0;
        int right = chars.length - 1;
        
        while (left < right) {
            // Swap characters
            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;
            
            left++;
            right--;
        }
        
        return new String(chars);
    }
    
    // Method 2: Using recursion
    public static String reverseRecursive(String str) {
        if (str == null || str.length() <= 1) {
            return str;
        }
        
        return reverseRecursive(str.substring(1)) + str.charAt(0);
    }
    
    // Method 3: Using StringBuilder
    public static String reverseWithStringBuilder(String str) {
        if (str == null) return null;
        
        StringBuilder sb = new StringBuilder();
        for (int i = str.length() - 1; i >= 0; i--) {
            sb.append(str.charAt(i));
        }
        return sb.toString();
    }
    
    public static void main(String[] args) {
        String input = "Hello World";
        System.out.println(reverse(input)); // "dlroW olleH"
        System.out.println(reverseRecursive(input)); // "dlroW olleH"
    }
}
```
### **Q3:** Implement LRU Cache using LinkedHashMap.
**Answer:**

### **Q4:** Print even and odd numbers alternatively using two threads.
**Answer:**
```java
public class EvenOddPrinter {
    private int counter = 1;
    private final int max;
    private final Object lock = new Object();
    
    public EvenOddPrinter(int max) {
        this.max = max;
    }
    
    public void printEven() {
        while (counter <= max) {
            synchronized (lock) {
                while (counter % 2 != 0 && counter <= max) {
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                        return;
                    }
                }
                
                if (counter <= max) {
                    System.out.println("Even: " + counter + " - " + Thread.currentThread().getName());
                    counter++;
                    lock.notify();
                }
            }
        }
    }
    
    public void printOdd() {
        while (counter <= max) {
            synchronized (lock) {
                while (counter % 2 == 0 && counter <= max) {
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                        return;
                    }
                }
                
                if (counter <= max) {
                    System.out.println("Odd: " + counter + " - " + Thread.currentThread().getName());
                    counter++;
                    lock.notify();
                }
            }
        }
    }
    
    public static void main(String[] args) {
        EvenOddPrinter printer = new EvenOddPrinter(10);
        
        Thread oddThread = new Thread(printer::printOdd, "OddThread");
        Thread evenThread = new Thread(printer::printEven, "EvenThread");
        
        oddThread.start();
        evenThread.start();
        
        try {
            oddThread.join();
            evenThread.join();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

// Alternative implementation using Semaphore
class EvenOddPrinterSemaphore {
    private int counter = 1;
    private final int max;
    private final Semaphore oddSemaphore = new Semaphore(1);
    private final Semaphore evenSemaphore = new Semaphore(0);
    
    public EvenOddPrinterSemaphore(int max) {
        this.max = max;
    }
    
    public void printOdd() {
        while (counter <= max) {
            try {
                oddSemaphore.acquire();
                if (counter <= max) {
                    System.out.println("Odd: " + counter++);
                }
                evenSemaphore.release();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                break;
            }
        }
    }
    
    public void printEven() {
        while (counter <= max) {
            try {
                evenSemaphore.acquire();
                if (counter <= max) {
                    System.out.println("Even: " + counter++);
                }
                oddSemaphore.release();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                break;
            }
        }
    }
}
```

---
### **Q1:** Your REST API is responding slowly. How do you debug and optimize it?
**Answer:**

**Step 1: Identify the bottleneck**
```java
// Add request/response logging
@Component
public class RequestResponseLoggingFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) throws IOException, ServletException {
        
        long startTime = System.currentTimeMillis();
        
        try {
            chain.doFilter(request, response);
        } finally {
            long duration = System.currentTimeMillis() - startTime;
            HttpServletRequest httpRequest = (HttpServletRequest) request;
            
            if (duration > 1000) { // Log slow requests (>1s)
                log.warn("Slow request: {} {} took {}ms", 
                    httpRequest.getMethod(), 
                    httpRequest.getRequestURI(), 
                    duration);
            }
        }
    }
}

// Add method-level timing
@Component
@Aspect
public class PerformanceAspect {
    
    @Around("@annotation(org.springframework.web.bind.annotation.RequestMapping)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        
        Object proceed = joinPoint.proceed();
        
        long executionTime = System.currentTimeMillis() - start;
        log.info("{} executed in {} ms", joinPoint.getSignature(), executionTime);
        
        return proceed;
    }
}
```

**Step 2: Database optimization**
```java
// Enable query logging
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.generate_statistics=true

// Monitor slow queries
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    // Add index hints
    @Query(value = "SELECT * FROM users USE INDEX (idx_email) WHERE email = ?1", 
           nativeQuery = true)
    Optional<User> findByEmailOptimized(String email);
    
    // Use projection for large result sets
    @Query("SELECT new com.example.dto.UserSummaryDTO(u.id, u.name, u.email) FROM User u")
    List<UserSummaryDTO> findAllSummaries();
}
```

**Step 3: Add caching**
```java
@Service
public class UserService {
    
    @Cacheable(value = "users", key = "#id")
    public User findById(Long id) {
        return userRepository.findById(id).orElse(null);
    }
    
    @Cacheable(value = "userList")
    public List<User> findAll() {
        return userRepository.findAll();
    }
}

// Configure cache
@Configuration
@EnableCaching
public class CacheConfig {
    
    @Bean
    public CacheManager cacheManager() {
        RedisCacheManager.Builder builder = RedisCacheManager
            .RedisCacheManagerBuilder
            .fromConnectionFactory(jedisConnectionFactory())
            .cacheDefaults(cacheConfiguration(Duration.ofMinutes(10)));
        
        return builder.build();
    }
}
```
### **Q2:** How do you handle high concurrent users modifying the same resource?
**Answer:**

```java
// 1. Optimistic Locking with JPA
@Entity
public class Product {
    @Id
    private Long id;
    
    @Version // Hibernate will automatically manage this
    private Long version;
    
    private String name;
    private Integer quantity;
    
    // getters and setters
}

@Service
public class ProductService {
    
    @Transactional
    public Product updateQuantity(Long productId, Integer newQuantity) {
        try {
            Product product = productRepository.findById(productId)
                .orElseThrow(() -> new ProductNotFoundException("Product not found"));
                
            product.setQuantity(newQuantity);
            return productRepository.save(product);
            
        } catch (OptimisticLockingFailureException e) {
            // Retry logic or return error to client
            throw new ConcurrentModificationException("Product was modified by another user");
        }
    }
}

// 2. Pessimistic Locking
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("SELECT p FROM Product p WHERE p.id = :id")
    Optional<Product> findByIdWithLock(@Param("id") Long id);
}

// 3. Distributed Locking with Redis
@Service
public class InventoryService {
    
    @Autowired
    private RedisTemplate<String, String> redisTemplate;
    
    public boolean updateInventory(Long productId, Integer quantity) {
        String lockKey = "lock:product:" + productId;
        String lockValue = UUID.randomUUID().toString();
        
        try {
            // Try to acquire lock for 10 seconds
            Boolean lockAcquired = redisTemplate.opsForValue()
                .setIfAbsent(lockKey, lockValue, Duration.ofSeconds(10));
                
            if (!lockAcquired) {
                return false; // Could not acquire lock
            }
            
            // Critical section - update inventory
            Product product = productRepository.findById(productId).orElse(null);
            if (product != null) {
                product.setQuantity(quantity);
                productRepository.save(product);
                return true;
            }
            
        } finally {
            // Release lock only if we own it
            String script = 
                "if redis.call('get', KEYS[1]) == ARGV[1] then " +
                "    return redis.call('del', KEYS[1]) " +
                "else " +
                "    return 0 " +
                "end";
                
            redisTemplate.execute(new DefaultRedisScript<>(script, Long.class), 
                Arrays.asList(lockKey), lockValue);
        }
        
        return false;
    }
}

// 4. Queue-based approach for high concurrency
@Component
public class InventoryUpdateProcessor {
    
    @RabbitListener(queues = "inventory.update.queue")
    public void processInventoryUpdate(InventoryUpdateMessage message) {
        // Process updates sequentially
        productService.updateQuantity(message.getProductId(), message.getQuantity());
    }
}

@RestController
public class InventoryController {
    
    @Autowired
    private RabbitTemplate rabbitTemplate;
    
    @PostMapping("/inventory/{productId}")
    public ResponseEntity<String> updateInventory(@PathVariable Long productId, 
                                                @RequestBody InventoryRequest request) {
        
        // Instead of direct update, queue the request
        InventoryUpdateMessage message = new InventoryUpdateMessage(productId, request.getQuantity());
        rabbitTemplate.convertAndSend("inventory.update.queue", message);
        
        return ResponseEntity.ok("Update queued");
    }
}
```

---
## 13. Hands-On Coding Exercises (Practice Like Real Interview)

### **Level 1: Core Java Exercises**

### **Q1:** Reverse a String (without library functions)
**Answer:**
```java
public class ReverseString {
    
    // Method 1: Using character array (most efficient)
    public static String reverse(String str) {
        if (str == null || str.length() <= 1) {
            return str;
        }
        
        char[] arr = str.toCharArray();
        int left = 0;
        int right = arr.length - 1;
        
        while (left < right) {
            // Swap characters
            char temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left++;
            right--;
        }
        
        return new String(arr);
    }
    
    // Method 2: Using StringBuilder (better than String concatenation)
    public static String reverseWithStringBuilder(String str) {
        if (str == null) return null;
        
        StringBuilder sb = new StringBuilder();
        for (int i = str.length() - 1; i >= 0; i--) {
            sb.append(str.charAt(i));
        }
        return sb.toString();
    }
    
    public static void main(String[] args) {
        String str = "interview";
        System.out.println("Original: " + str);
        System.out.println("Reversed: " + reverse(str));
        System.out.println("Reversed (StringBuilder): " + reverseWithStringBuilder(str));
    }
}
```
**✅ Expected Output:**
```
Original: interview
Reversed: weivretni
Reversed (StringBuilder): weivretni
```

### **Q2:** Find the missing number in array (1…n)
**Answer:**
```java
public class MissingNumber {
    
    // Method 1: Using sum formula
    public static int findMissingNumber(int[] arr, int n) {
        int expectedSum = n * (n + 1) / 2;
        int actualSum = 0;
        
        for (int num : arr) {
            actualSum += num;
        }
        
        return expectedSum - actualSum;
    }
    
    // Method 2: Using XOR (more efficient for large numbers)
    public static int findMissingNumberXOR(int[] arr, int n) {
        int xor1 = 0; // XOR of all elements from 1 to n
        int xor2 = 0; // XOR of all elements in array
        
        for (int i = 1; i <= n; i++) {
            xor1 ^= i;
        }
        
        for (int num : arr) {
            xor2 ^= num;
        }
        
        return xor1 ^ xor2;
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 2, 4, 5, 6};
        int n = 6;
        
        System.out.println("Array: " + Arrays.toString(arr));
        System.out.println("Missing number (Sum method): " + findMissingNumber(arr, n));
        System.out.println("Missing number (XOR method): " + findMissingNumberXOR(arr, n));
    }
}
```
**✅ Expected Output:**
```
Array: [1, 2, 4, 5, 6]
Missing number (Sum method): 3
Missing number (XOR method): 3
```
### **Q3:** Count character frequency



### **Q4:** Check if string is palindrome
**Answer:**
```java
public class PalindromeChecker {
    
    // Method 1: Simple approach
    public static boolean isPalindrome(String str) {
        if (str == null) return false;
        
        str = str.toLowerCase().replaceAll("[^a-zA-Z0-9]", "");
        int left = 0;
        int right = str.length() - 1;
        
        while (left < right) {
            if (str.charAt(left) != str.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        
        return true;
    }
    
    // Method 2: Using StringBuilder
    public static boolean isPalindromeReverse(String str) {
        if (str == null) return false;
        
        str = str.toLowerCase().replaceAll("[^a-zA-Z0-9]", "");
        return str.equals(new StringBuilder(str).reverse().toString());
    }
    
    public static void main(String[] args) {
        String[] testStrings = {"racecar", "A man a plan a canal Panama", "race a car", "hello"};
        
        for (String test : testStrings) {
            System.out.println("'" + test + "' is palindrome: " + isPalindrome(test));
        }
    }
}
```
**✅ Expected Output:**
```
'racecar' is palindrome: true
'A man a plan a canal Panama' is palindrome: true
'race a car' is palindrome: false
'hello' is palindrome: false
```

---
### **Level 2: Advanced Java (Multithreading)**

### **Q5:** Print even and odd numbers using two threads
**Answer:**
```java
class NumberPrinter {
    private int number = 1;
    private final int maxNumber;
    private final Object lock = new Object();
    
    public NumberPrinter(int maxNumber) {
        this.maxNumber = maxNumber;
    }
    
    public void printOdd() {
        while (number <= maxNumber) {
            synchronized (lock) {
                // Wait if current number is even
                while (number % 2 == 0 && number <= maxNumber) {
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                        return;
                    }
                }
                
                if (number <= maxNumber) {
                    System.out.println("Odd: " + number + " - " + Thread.currentThread().getName());
                    number++;
                    lock.notify(); // Wake up the other thread
                }
            }
        }
    }
    
    public void printEven() {
        while (number <= maxNumber) {
            synchronized (lock) {
                // Wait if current number is odd
                while (number % 2 == 1 && number <= maxNumber) {
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                        return;
                    }
                }
                
                if (number <= maxNumber) {
                    System.out.println("Even: " + number + " - " + Thread.currentThread().getName());
                    number++;
                    lock.notify(); // Wake up the other thread
                }
            }
        }
    }
}

public class EvenOddThreads {
    public static void main(String[] args) {
        NumberPrinter printer = new NumberPrinter(10);
        
        Thread oddThread = new Thread(printer::printOdd, "OddThread");
        Thread evenThread = new Thread(printer::printEven, "EvenThread");
        
        oddThread.start();
        evenThread.start();
        
        try {
            oddThread.join();
            evenThread.join();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        System.out.println("Finished printing numbers");
    }
}
```
**✅ Expected Output:**
```
Odd: 1 - OddThread
Even: 2 - EvenThread
Odd: 3 - OddThread
Even: 4 - EvenThread
Odd: 5 - OddThread
Even: 6 - EvenThread
Odd: 7 - OddThread
Even: 8 - EvenThread
Odd: 9 - OddThread
Even: 10 - EvenThread
Finished printing numbers
```
### **Level 3: Spring Boot Exercises**

### **Q7:** Complete REST API with CRUD operations
**Answer:**
```java
// Entity
@Entity
@Table(name = "products")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @NotBlank(message = "Name is required")
    private String name;
    
    @NotNull(message = "Price is required")
    @DecimalMin(value = "0.0", inclusive = false, message = "Price must be greater than 0")
    private BigDecimal price;
    
    @NotNull(message = "Category is required")
    private String category;
    
    // Constructors, getters, setters
    public Product() {}
    
    public Product(String name, BigDecimal price, String category) {
        this.name = name;
        this.price = price;
        this.category = category;
    }
    
    // Getters and setters...
}

// Repository
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    List<Product> findByCategory(String category);
    List<Product> findByPriceBetween(BigDecimal minPrice, BigDecimal maxPrice);
    
    @Query("SELECT p FROM Product p WHERE p.name LIKE %:name%")
    List<Product> findByNameContaining(@Param("name") String name);
}

// Service
@Service
@Transactional
public class ProductService {
    
    @Autowired
    private ProductRepository productRepository;
    
    public List<Product> getAllProducts() {
        return productRepository.findAll();
    }
    
    public Product getProductById(Long id) {
        return productRepository.findById(id)
            .orElseThrow(() -> new ProductNotFoundException("Product not found with id: " + id));
    }
    
    public Product createProduct(Product product) {
        return productRepository.save(product);
    }
    
    public Product updateProduct(Long id, Product productDetails) {
        Product product = getProductById(id);
        
        product.setName(productDetails.getName());
        product.setPrice(productDetails.getPrice());
        product.setCategory(productDetails.getCategory());
        
        return productRepository.save(product);
    }
    
    public void deleteProduct(Long id) {
        Product product = getProductById(id);
        productRepository.delete(product);
    }
    
    public List<Product> getProductsByCategory(String category) {
        return productRepository.findByCategory(category);
    }
}

// Controller
@RestController
@RequestMapping("/api/products")
@CrossOrigin(origins = "*")
public class ProductController {
    
    @Autowired
    private ProductService productService;
    
    @GetMapping
    public ResponseEntity<List<Product>> getAllProducts() {
        List<Product> products = productService.getAllProducts();
        return ResponseEntity.ok(products);
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<Product> getProductById(@PathVariable Long id) {
        Product product = productService.getProductById(id);
        return ResponseEntity.ok(product);
    }
    
    @PostMapping
    public ResponseEntity<Product> createProduct(@Valid @RequestBody Product product) {
        Product createdProduct = productService.createProduct(product);
        return ResponseEntity.status(HttpStatus.CREATED).body(createdProduct);
    }
    
    @PutMapping("/{id}")
    public ResponseEntity<Product> updateProduct(@PathVariable Long id, 
                                               @Valid @RequestBody Product productDetails) {
        Product updatedProduct = productService.updateProduct(id, productDetails);
        return ResponseEntity.ok(updatedProduct);
    }
    
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteProduct(@PathVariable Long id) {
        productService.deleteProduct(id);
        return ResponseEntity.noContent().build();
    }
    
    @GetMapping("/category/{category}")
    public ResponseEntity<List<Product>> getProductsByCategory(@PathVariable String category) {
        List<Product> products = productService.getProductsByCategory(category);
        return ResponseEntity.ok(products);
    }
}

// Exception Handler
@ControllerAdvice
public class ProductExceptionHandler {
    
    @ExceptionHandler(ProductNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleProductNotFound(ProductNotFoundException ex) {
        ErrorResponse error = new ErrorResponse("PRODUCT_NOT_FOUND", ex.getMessage());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(MethodArgumentNotValidException ex) {
        List<String> errors = ex.getBindingResult().getFieldErrors()
            .stream()
            .map(FieldError::getDefaultMessage)
            .collect(Collectors.toList());
            
        ErrorResponse error = new ErrorResponse("VALIDATION_ERROR", "Validation failed", errors);
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }
}
```
**✅ API Endpoints:**
```bash
GET    /api/products           # Get all products
GET    /api/products/{id}      # Get product by ID
POST   /api/products           # Create new product
PUT    /api/products/{id}      # Update product
DELETE /api/products/{id}      # Delete product
GET    /api/products/category/{category} # Get products by category
```

---
### **Level 4: SQL Exercises**

### **Q8:** Find duplicate rows in a table
**Answer:**
```sql
-- Create sample table
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    email VARCHAR(100),
    department VARCHAR(50),
    salary DECIMAL(10,2)
);

-- Insert sample data with duplicates
INSERT INTO employees VALUES 
(1, 'John Doe', 'john@company.com', 'IT', 75000),
(2, 'Jane Smith', 'jane@company.com', 'HR', 65000),
(3, 'John Doe', 'john2@company.com', 'IT', 75000),
(4, 'Bob Wilson', 'bob@company.com', 'Finance', 70000),
(5, 'Jane Smith', 'jane2@company.com', 'HR', 65000);

-- Find duplicate names
SELECT name, COUNT(*) as duplicate_count
FROM employees
GROUP BY name
HAVING COUNT(*) > 1;

-- Find all records with duplicate names
SELECT e1.*
FROM employees e1
INNER JOIN (
    SELECT name
    FROM employees
    GROUP BY name
    HAVING COUNT(*) > 1
) e2 ON e1.name = e2.name
ORDER BY e1.name;

-- Find duplicates based on multiple columns (name + department + salary)
SELECT name, department, salary, COUNT(*) as duplicate_count
FROM employees
GROUP BY name, department, salary
HAVING COUNT(*) > 1;

-- Delete duplicates, keep only the record with lowest ID
DELETE e1 FROM employees e1
INNER JOIN employees e2
WHERE e1.id > e2.id 
AND e1.name = e2.name 
AND e1.department = e2.department 
AND e1.salary = e2.salary;
```
**✅ Expected Output:**
```
name        duplicate_count
John Doe    2
Jane Smith  2
```
### **Q9:** Find 2nd, 3rd, Nth highest salary
**Answer:**
```sql
-- Sample data
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    department VARCHAR(50),
    salary DECIMAL(10,2)
);

INSERT INTO employees VALUES 
(1, 'Alice', 'IT', 90000),
(2, 'Bob', 'HR', 75000),
(3, 'Charlie', 'IT', 85000),
(4, 'David', 'Finance', 95000),
(5, 'Eve', 'IT', 80000);

-- Method 1: Using subquery (2nd highest)
SELECT MAX(salary) as second_highest_salary
FROM employees 
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: Using LIMIT and OFFSET (MySQL)
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- Method 3: Using ROW_NUMBER() for Nth highest
SELECT salary, name
FROM (
    SELECT salary, name, 
           ROW_NUMBER() OVER (ORDER BY salary DESC) as salary_rank
    FROM employees
) ranked
WHERE salary_rank = 2;

-- Method 4: Using DENSE_RANK() (handles ties better)
SELECT salary, name
FROM (
    SELECT salary, name,
           DENSE_RANK() OVER (ORDER BY salary DESC) as salary_rank
    FROM employees
) ranked
WHERE salary_rank = 2;

-- Top 3 highest salaries by department
SELECT department, name, salary, dept_rank
FROM (
    SELECT department, name, salary,
           ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as dept_rank
    FROM employees
) ranked
WHERE dept_rank <= 3
ORDER BY department, dept_rank;
```
**✅ Expected Output:**
```
second_highest_salary
90000

salary  name
90000   Alice

department  name     salary  dept_rank
Finance     David    95000   1
HR          Bob      75000   1
IT          Alice    90000   1
IT          Charlie  85000   2
IT          Eve      80000   3
```

---
### **Level 5: Algorithm & Data Structure Exercises**
### **Q10:** Implement LRU Cache with detailed explanation
### **Q11:** Producer-Consumer with BlockingQueue


### **Level 6: Mixed Problem Solving**
### **Q12:** Find intersection of two arrays
### **Q13:** Implement a Thread-Safe Singleton with performance testing
