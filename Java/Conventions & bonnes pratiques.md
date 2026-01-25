# Conventions & Bonnes Pratiques Java

## 📋 Conventions de Nommage

### Classes et Interfaces

- **PascalCase** pour les classes et interfaces

```java
public class UserProfile {
    // ...
}

public interface Serializable {
    // ...
}
```

### Méthodes et Variables

- **camelCase** pour les méthodes et variables

```java
private String userName;
private int userId;

public String getUserData() {
    return userData;
}
```

### Constantes

- **UPPER_SNAKE_CASE** pour les constantes

```java
public static final int MAX_RETRY_COUNT = 3;
public static final String API_BASE_URL = "https://api.example.com";
private static final double PI = 3.14159;
```

### Packages

- **lowercase** séparé par des points

```java
package com.company.project.module;
```

### Booléens

- Préfixer avec `is`, `has`, `can`, `should`

```java
private boolean isActive;
private boolean hasPermission;
private boolean canEdit;
```

---

## 🏗️ Structure et Organisation

### Organisation d'une classe

```java
public class User {
    // 1. Constantes statiques
    public static final String DEFAULT_ROLE = "USER";

    // 2. Variables statiques
    private static int userCount = 0;

    // 3. Variables d'instance
    private String name;
    private String email;

    // 4. Constructeurs
    public User(String name, String email) {
        this.name = name;
        this.email = email;
        userCount++;
    }

    // 5. Getters et Setters
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    // 6. Méthodes publiques
    public void activate() {
        // ...
    }

    // 7. Méthodes privées
    private void validate() {
        // ...
    }

    // 8. Classes internes
    private static class Builder {
        // ...
    }
}
```

### Organisation des packages

```
com.company.project/
├── config/
├── controller/
├── service/
├── repository/
├── model/
│   ├── entity/
│   └── dto/
├── exception/
└── util/
```

---

## ✅ Bonnes Pratiques Générales

### 1. Utiliser final quand possible

```java
// ✅ Variables
public final class ImmutableUser {
    private final String name;
    private final String email;

    public ImmutableUser(String name, String email) {
        this.name = name;
        this.email = email;
    }
}

// Méthodes
public final void processData() {
    // Ne peut pas être override
}
```

### 2. Préférer les interfaces aux classes abstraites

```java
// ✅ Bon
public interface UserService {
    User findById(Long id);
    void save(User user);
}

// Pour le comportement commun
public abstract class BaseEntity {
    protected Long id;
    protected LocalDateTime createdAt;
}
```

### 3. Equals et HashCode

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    User user = (User) o;
    return Objects.equals(id, user.id) &&
           Objects.equals(email, user.email);
}

@Override
public int hashCode() {
    return Objects.hash(id, email);
}

// ✅ Ou utiliser un record (Java 14+)
public record User(Long id, String name, String email) {}
```

### 4. ToString

```java
@Override
public String toString() {
    return String.format("User{id=%d, name='%s', email='%s'}",
        id, name, email);
}
```

### 5. Try-with-resources

```java
// ✅ Bon - fermeture automatique
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line = br.readLine();
} catch (IOException e) {
    e.printStackTrace();
}

// ❌ Éviter
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("file.txt"));
    // ...
} finally {
    if (br != null) br.close();
}
```

---

## 🎯 POO et SOLID

### Single Responsibility Principle

```java
// ❌ Mauvais - fait trop de choses
public class UserManager {
    public void createUser() {}
    public void sendEmail() {}
    public void logActivity() {}
}

// ✅ Bon - responsabilités séparées
public class UserService {
    private EmailService emailService;
    private LogService logService;

    public void createUser(User user) {
        // Logique de création
        emailService.sendWelcome(user);
        logService.log("User created");
    }
}
```

### Dependency Injection

```java
// ✅ Constructor injection (préféré)
public class UserController {
    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }
}

// Avec Spring
@RestController
public class UserController {
    private final UserService userService;

    @Autowired
    public UserController(UserService userService) {
        this.userService = userService;
    }
}
```

### Interface Segregation

```java
// ❌ Interface trop large
public interface Worker {
    void work();
    void eat();
    void sleep();
}

// ✅ Interfaces spécifiques
public interface Workable {
    void work();
}

public interface Eatable {
    void eat();
}

public class Human implements Workable, Eatable {
    public void work() {}
    public void eat() {}
}
```

---

## 🔄 Collections et Streams

### Utiliser les bonnes collections

```java
// List - ordre important
List<String> names = new ArrayList<>();
List<String> linkedList = new LinkedList<>(); // Pour insertions fréquentes

// Set - unicité
Set<String> uniqueNames = new HashSet<>();
Set<String> sortedNames = new TreeSet<>();

// Map - clé-valeur
Map<Long, User> userMap = new HashMap<>();
Map<String, Integer> sortedMap = new TreeMap<>();
```

### Streams API (Java 8+)

```java
// ✅ Bon usage des streams
List<String> activeUsers = users.stream()
    .filter(user -> user.isActive())
    .map(User::getName)
    .sorted()
    .collect(Collectors.toList());

// Opérations communes
long count = users.stream()
    .filter(User::isActive)
    .count();

Optional<User> first = users.stream()
    .filter(u -> u.getAge() > 18)
    .findFirst();

int sum = numbers.stream()
    .mapToInt(Integer::intValue)
    .sum();

// Grouping
Map<String, List<User>> byRole = users.stream()
    .collect(Collectors.groupingBy(User::getRole));
```

### Optional

```java
// ✅ Bon usage d'Optional
public Optional<User> findById(Long id) {
    return Optional.ofNullable(userMap.get(id));
}

// Utilisation
Optional<User> user = findById(1L);
user.ifPresent(u -> System.out.println(u.getName()));

String name = user
    .map(User::getName)
    .orElse("Unknown");

User result = user.orElseThrow(
    () -> new UserNotFoundException("User not found")
);
```

---

## 🛡️ Gestion des Exceptions

### Hiérarchie d'exceptions

```java
// Exception personnalisée
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }

    public UserNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }
}

// Avec données supplémentaires
public class ValidationException extends RuntimeException {
    private final Map<String, String> errors;

    public ValidationException(Map<String, String> errors) {
        super("Validation failed");
        this.errors = errors;
    }

    public Map<String, String> getErrors() {
        return errors;
    }
}
```

### Try-catch approprié

```java
// ✅ Bon - exceptions spécifiques
try {
    processData();
} catch (FileNotFoundException e) {
    log.error("File not found", e);
} catch (IOException e) {
    log.error("IO error", e);
}

// ❌ Éviter
try {
    processData();
} catch (Exception e) {
    // Trop général
}
```

---

## 🚀 Java Moderne (Java 8+)

### Lambda Expressions

```java
// ✅ Lambda
list.forEach(item -> System.out.println(item));
list.sort((a, b) -> a.compareTo(b));

// Method reference
list.forEach(System.out::println);
list.sort(String::compareTo);
```

### Functional Interfaces

```java
@FunctionalInterface
public interface UserValidator {
    boolean validate(User user);
}

// Utilisation
UserValidator validator = user -> user.getAge() >= 18;
if (validator.validate(user)) {
    // ...
}
```

### Records (Java 14+)

```java
// ✅ Immutable data class
public record User(Long id, String name, String email) {
    // Compact constructor
    public User {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("Name cannot be blank");
        }
    }

    // Méthodes additionnelles
    public String getDisplayName() {
        return name.toUpperCase();
    }
}
```

### Switch Expressions (Java 14+)

```java
// ✅ Switch expression
String result = switch (status) {
    case PENDING -> "En attente";
    case ACTIVE -> "Actif";
    case INACTIVE -> "Inactif";
    default -> "Inconnu";
};

// Avec yield
String message = switch (status) {
    case PENDING -> {
        log.info("Pending status");
        yield "En attente";
    }
    case ACTIVE -> "Actif";
    default -> "Inconnu";
};
```

### Pattern Matching (Java 16+)

```java
// ✅ Pattern matching pour instanceof
if (obj instanceof String s) {
    System.out.println(s.toUpperCase());
}

// Pattern matching dans switch (Java 17+)
String formatted = switch (obj) {
    case Integer i -> String.format("int %d", i);
    case Long l -> String.format("long %d", l);
    case String s -> String.format("String %s", s);
    default -> obj.toString();
};
```

### Sealed Classes (Java 17+)

```java
public sealed interface Shape
    permits Circle, Rectangle, Triangle {
}

public final class Circle implements Shape {
    private final double radius;
}

public final class Rectangle implements Shape {
    private final double width;
    private final double height;
}

public non-sealed class Triangle implements Shape {
    // Peut être étendu
}
```

---

## 🎨 Design Patterns

### Singleton

```java
public class DatabaseConnection {
    private static volatile DatabaseConnection instance;

    private DatabaseConnection() {}

    public static DatabaseConnection getInstance() {
        if (instance == null) {
            synchronized (DatabaseConnection.class) {
                if (instance == null) {
                    instance = new DatabaseConnection();
                }
            }
        }
        return instance;
    }
}

// ✅ Ou enum (thread-safe)
public enum DatabaseConnection {
    INSTANCE;

    public void connect() {
        // ...
    }
}
```

### Builder

```java
public class User {
    private final String name;
    private final String email;
    private final int age;

    private User(Builder builder) {
        this.name = builder.name;
        this.email = builder.email;
        this.age = builder.age;
    }

    public static class Builder {
        private String name;
        private String email;
        private int age;

        public Builder name(String name) {
            this.name = name;
            return this;
        }

        public Builder email(String email) {
            this.email = email;
            return this;
        }

        public Builder age(int age) {
            this.age = age;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }
}

// Utilisation
User user = new User.Builder()
    .name("John")
    .email("john@example.com")
    .age(30)
    .build();
```

---

## 🧪 Tests

### JUnit 5

```java
import org.junit.jupiter.api.*;
import static org.junit.jupiter.api.Assertions.*;

class UserServiceTest {
    private UserService userService;

    @BeforeEach
    void setUp() {
        userService = new UserService();
    }

    @Test
    @DisplayName("Should create user successfully")
    void testCreateUser() {
        User user = new User("John", "john@example.com");
        User created = userService.create(user);

        assertNotNull(created);
        assertEquals("John", created.getName());
    }

    @Test
    void testInvalidEmail() {
        assertThrows(ValidationException.class, () -> {
            new User("John", "invalid-email");
        });
    }

    @ParameterizedTest
    @ValueSource(strings = {"", " ", "  "})
    void testBlankName(String name) {
        assertThrows(IllegalArgumentException.class, () -> {
            new User(name, "email@example.com");
        });
    }
}
```

### Mockito

```java
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class UserControllerTest {
    @Mock
    private UserService userService;

    @InjectMocks
    private UserController userController;

    @Test
    void testGetUser() {
        User mockUser = new User(1L, "John");
        when(userService.findById(1L)).thenReturn(Optional.of(mockUser));

        User result = userController.getUser(1L);

        assertEquals("John", result.getName());
        verify(userService).findById(1L);
    }
}
```

---

## 🔒 Sécurité

### Input Validation

```java
public class UserValidator {
    public void validate(User user) {
        Objects.requireNonNull(user, "User cannot be null");

        if (user.getName() == null || user.getName().isBlank()) {
            throw new ValidationException("Name is required");
        }

        if (!user.getEmail().matches("^[A-Za-z0-9+_.-]+@(.+)$")) {
            throw new ValidationException("Invalid email format");
        }
    }
}
```

### Éviter les fuites de ressources

```java
// ✅ Try-with-resources
try (Connection conn = dataSource.getConnection();
     PreparedStatement stmt = conn.prepareStatement(sql)) {
    ResultSet rs = stmt.executeQuery();
    // ...
}
```

---

## 📝 Documentation

### Javadoc

```java
/**
 * Service de gestion des utilisateurs.
 *
 * <p>Cette classe fournit des méthodes pour créer, modifier et supprimer
 * des utilisateurs dans le système.</p>
 *
 * @author John Doe
 * @version 1.0
 * @since 2024-01-01
 */
public class UserService {

    /**
     * Recherche un utilisateur par son identifiant.
     *
     * @param id l'identifiant de l'utilisateur
     * @return un Optional contenant l'utilisateur s'il existe
     * @throws IllegalArgumentException si l'id est null ou négatif
     */
    public Optional<User> findById(Long id) {
        if (id == null || id < 0) {
            throw new IllegalArgumentException("Invalid id");
        }
        return repository.findById(id);
    }
}
```

---

## 🛠️ Outils

### Maven

```xml
<project>
    <properties>
        <java.version>17</java.version>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>
</project>
```

### Lombok (optionnel)

```java
import lombok.*;

@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Long id;
    private String name;
    private String email;
}
```

---

## 📚 Ressources

- [Oracle Java Tutorials](https://docs.oracle.com/javase/tutorial/)
- [Effective Java (Joshua Bloch)](https://www.oreilly.com/library/view/effective-java/9780134686097/)
- [Java Code Conventions](https://www.oracle.com/java/technologies/javase/codeconventions-contents.html)
- [Baeldung](https://www.baeldung.com/)
