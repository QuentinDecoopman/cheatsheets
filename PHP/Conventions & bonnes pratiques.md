# Conventions & Bonnes Pratiques PHP

## 📋 Conventions de Nommage (PSR-1, PSR-12)

### Variables et Fonctions

- **camelCase** pour les variables et fonctions

```php
<?php
$userName = "John";
function getUserData() {
    return [];
}
```

### Constantes

- **UPPER_SNAKE_CASE** pour les constantes

```php
<?php
const MAX_RETRY_COUNT = 3;
const API_BASE_URL = "https://api.example.com";

define('DB_HOST', 'localhost');
```

### Classes

- **PascalCase** pour les classes
- Un fichier par classe

```php
<?php
class UserProfile {
    public function __construct(string $name) {
        $this->name = $name;
    }
}
```

### Namespaces

- **PascalCase** pour les namespaces

```php
<?php
namespace App\Services\User;

class UserService {
    // ...
}
```

### Propriétés et méthodes

- **camelCase** pour les propriétés et méthodes

```php
<?php
class User {
    private string $firstName;
    protected int $userId;

    public function getFullName(): string {
        return $this->firstName;
    }
}
```

---

## 🏗️ Standards PSR

### PSR-1: Basic Coding Standard

```php
<?php
// Toujours utiliser <?php, jamais <?
namespace App\Models;

use App\Services\Database;

class User {
    const STATUS_ACTIVE = 'active';

    public function save(): bool {
        // ...
    }
}
```

### PSR-4: Autoloading

```
src/
├── Controllers/
│   └── UserController.php  → App\Controllers\UserController
├── Models/
│   └── User.php            → App\Models\User
└── Services/
    └── EmailService.php    → App\Services\EmailService
```

### PSR-12: Extended Coding Style

```php
<?php
declare(strict_types=1);

namespace App\Services;

use App\Models\User;
use App\Interfaces\ServiceInterface;

class UserService implements ServiceInterface
{
    private Database $db;

    public function __construct(Database $db)
    {
        $this->db = $db;
    }

    public function findUser(int $id): ?User
    {
        if ($id <= 0) {
            return null;
        }

        return $this->db->find($id);
    }
}
```

---

## ✅ Bonnes Pratiques Générales

### 1. Typage strict

```php
<?php
declare(strict_types=1);

function add(int $a, int $b): int {
    return $a + $b;
}

// Erreur si on passe une string
add("5", 3);  // TypeError
```

### 2. Type Hints (PHP 7.0+)

```php
<?php
// Types scalaires
function process(string $name, int $age, bool $active): void {
    // ...
}

// Types de retour
function getUser(): ?User {
    return $user ?? null;
}

// Types composés (PHP 8.0+)
function getValue(): int|float {
    return 42;
}

// Union types
function process(string|int $value): void {
    // ...
}
```

### 3. Null coalescing et opérateurs

```php
<?php
// Null coalescing operator
$name = $user['name'] ?? 'Anonymous';

// Null safe operator (PHP 8.0+)
$country = $user?->getAddress()?->getCountry();

// Spaceship operator
$result = $a <=> $b;  // -1, 0, ou 1
```

### 4. Arrow Functions (PHP 7.4+)

```php
<?php
// Fonction courte
$multiply = fn($a, $b) => $a * $b;

// Dans les callbacks
$numbers = array_map(
    fn($n) => $n * 2,
    [1, 2, 3]
);
```

### 5. Named Arguments (PHP 8.0+)

```php
<?php
function createUser(
    string $name,
    string $email,
    string $role = 'user'
): User {
    return new User($name, $email, $role);
}

// Utilisation
$user = createUser(
    name: 'John',
    email: 'john@example.com',
    role: 'admin'
);
```

---

## 🏛️ POO et Classes

### Visibilité

```php
<?php
class User {
    public string $name;         // Accessible partout
    protected string $email;     // Classe et sous-classes
    private string $password;    // Uniquement cette classe

    // Constructor property promotion (PHP 8.0+)
    public function __construct(
        public string $firstName,
        private string $lastName
    ) {}
}
```

### Propriétés readonly (PHP 8.1+)

```php
<?php
class User {
    public function __construct(
        public readonly string $id,
        public readonly string $email
    ) {}
}

$user = new User('123', 'john@example.com');
// $user->id = '456';  // Erreur
```

### Interfaces et Traits

```php
<?php
interface Serializable {
    public function serialize(): string;
    public function unserialize(string $data): void;
}

trait Timestampable {
    private DateTime $createdAt;
    private DateTime $updatedAt;

    public function updateTimestamp(): void {
        $this->updatedAt = new DateTime();
    }
}

class User implements Serializable {
    use Timestampable;

    public function serialize(): string {
        return json_encode($this);
    }

    public function unserialize(string $data): void {
        // ...
    }
}
```

### Abstract et Final

```php
<?php
abstract class Animal {
    abstract public function makeSound(): string;

    public function move(): void {
        echo "Moving...";
    }
}

final class Dog extends Animal {
    public function makeSound(): string {
        return "Woof!";
    }
}

// ❌ Erreur - Dog est final
// class Puppy extends Dog {}
```

---

## 🎯 Fonctions et Méthodes

### Docblocks PHPDoc

```php
<?php
/**
 * Calcule le prix total avec taxes
 *
 * @param float $price Prix de base
 * @param float $taxRate Taux de taxe (0.2 = 20%)
 * @return float Prix total avec taxes
 * @throws InvalidArgumentException Si le prix est négatif
 */
function calculateTotal(float $price, float $taxRate): float {
    if ($price < 0) {
        throw new InvalidArgumentException('Price cannot be negative');
    }

    return $price * (1 + $taxRate);
}
```

### Paramètres variadic

```php
<?php
function sum(int ...$numbers): int {
    return array_sum($numbers);
}

sum(1, 2, 3, 4);  // 10
```

### Return types

```php
<?php
// Types simples
function getName(): string {}
function getAge(): int {}
function isActive(): bool {}

// Nullable
function getUser(): ?User {}

// Void
function logMessage(string $msg): void {}

// Never (PHP 8.1+)
function redirect(): never {
    header('Location: /');
    exit;
}

// Self et Static
class User {
    public function clone(): self {
        return new self();
    }

    public static function create(): static {
        return new static();
    }
}
```

---

## 🛡️ Gestion des Erreurs

### Exceptions

```php
<?php
// Exception personnalisée
class ValidationException extends Exception {
    public function __construct(
        string $message,
        private array $errors = []
    ) {
        parent::__construct($message);
    }

    public function getErrors(): array {
        return $this->errors;
    }
}

// Utilisation
try {
    $user = User::create($data);
} catch (ValidationException $e) {
    foreach ($e->getErrors() as $error) {
        echo $error;
    }
} catch (Exception $e) {
    error_log($e->getMessage());
} finally {
    // Nettoyage
}
```

### Try-catch moderne

```php
<?php
// PHP 8.0+: non-capturing catch
try {
    riskyOperation();
} catch (SpecificException) {
    // Pas besoin de la variable si inutilisée
    handleError();
}
```

---

## 📦 Namespaces et Autoloading

### Organisation des namespaces

```php
<?php
namespace App\Controllers;

use App\Models\User;
use App\Services\{EmailService, LogService};
use App\Exceptions\NotFoundException;

class UserController {
    public function __construct(
        private EmailService $emailService,
        private LogService $logService
    ) {}
}
```

### Composer autoload

```json
{
  "autoload": {
    "psr-4": {
      "App\\": "src/"
    }
  }
}
```

---

## 🔄 Arrays et Collections

### Array functions modernes

```php
<?php
// array_map
$doubled = array_map(fn($n) => $n * 2, [1, 2, 3]);

// array_filter
$evens = array_filter([1, 2, 3, 4], fn($n) => $n % 2 === 0);

// array_reduce
$sum = array_reduce([1, 2, 3], fn($carry, $n) => $carry + $n, 0);

// Spread operator
$array1 = [1, 2];
$array2 = [3, 4];
$merged = [...$array1, ...$array2];

// Destructuring
[$first, $second] = [1, 2];
['name' => $name, 'age' => $age] = $user;
```

---

## 🚀 PHP 8+ Features

### Attributes (PHP 8.0+)

```php
<?php
#[Route('/api/users', methods: ['GET'])]
class UserController {
    #[Deprecated('Use getUsers() instead')]
    public function listUsers(): array {
        return [];
    }
}
```

### Enums (PHP 8.1+)

```php
<?php
enum Status: string {
    case Pending = 'pending';
    case Active = 'active';
    case Inactive = 'inactive';

    public function label(): string {
        return match($this) {
            self::Pending => 'En attente',
            self::Active => 'Actif',
            self::Inactive => 'Inactif',
        };
    }
}

// Utilisation
$status = Status::Active;
echo $status->value;  // 'active'
echo $status->label();  // 'Actif'
```

### Match Expression (PHP 8.0+)

```php
<?php
// ✅ Match (strict comparison)
$result = match($status) {
    'pending' => 'En attente',
    'active' => 'Actif',
    'inactive' => 'Inactif',
    default => 'Inconnu'
};

// Comparé à switch
switch($status) {
    case 'pending':
        $result = 'En attente';
        break;
    // ...
}
```

### Fibers (PHP 8.1+)

```php
<?php
$fiber = new Fiber(function (): void {
    $value = Fiber::suspend('fiber');
    echo "Value: $value";
});

$value = $fiber->start();
$fiber->resume('test');
```

---

## 🔒 Sécurité

### 1. Protection XSS

```php
<?php
// ✅ Toujours échapper les sorties
echo htmlspecialchars($userInput, ENT_QUOTES, 'UTF-8');

// Dans les templates
<?= htmlspecialchars($name) ?>
```

### 2. Protection SQL Injection

```php
<?php
// ✅ PDO avec prepared statements
$stmt = $pdo->prepare('SELECT * FROM users WHERE id = :id');
$stmt->execute(['id' => $userId]);

// ❌ Jamais de concaténation
// $query = "SELECT * FROM users WHERE id = " . $userId;
```

### 3. CSRF Protection

```php
<?php
// Générer un token
$_SESSION['csrf_token'] = bin2hex(random_bytes(32));

// Vérifier
if (!hash_equals($_SESSION['csrf_token'], $_POST['token'])) {
    die('Invalid CSRF token');
}
```

### 4. Passwords

```php
<?php
// ✅ Hashing
$hash = password_hash($password, PASSWORD_ARGON2ID);

// Vérification
if (password_verify($password, $hash)) {
    // OK
}

// Vérifier si rehash nécessaire
if (password_needs_rehash($hash, PASSWORD_ARGON2ID)) {
    $newHash = password_hash($password, PASSWORD_ARGON2ID);
}
```

### 5. Input Validation

```php
<?php
// Filter functions
$email = filter_var($input, FILTER_VALIDATE_EMAIL);
$url = filter_var($input, FILTER_VALIDATE_URL);
$int = filter_var($input, FILTER_VALIDATE_INT);

// Sanitize
$clean = filter_var($input, FILTER_SANITIZE_STRING);
```

---

## 🎨 Design Patterns

### Singleton

```php
<?php
class Database {
    private static ?Database $instance = null;

    private function __construct() {}

    public static function getInstance(): Database {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}
```

### Factory

```php
<?php
class UserFactory {
    public static function create(array $data): User {
        return new User(
            $data['name'],
            $data['email']
        );
    }
}
```

### Dependency Injection

```php
<?php
class UserController {
    public function __construct(
        private UserRepository $repository,
        private EmailService $emailService
    ) {}

    public function store(array $data): User {
        $user = $this->repository->create($data);
        $this->emailService->sendWelcome($user);
        return $user;
    }
}
```

---

## 🧪 Tests

### PHPUnit

```php
<?php
use PHPUnit\Framework\TestCase;

class UserTest extends TestCase {
    private User $user;

    protected function setUp(): void {
        $this->user = new User('John', 'john@example.com');
    }

    public function testGetName(): void {
        $this->assertEquals('John', $this->user->getName());
    }

    public function testInvalidEmail(): void {
        $this->expectException(InvalidArgumentException::class);
        new User('John', 'invalid-email');
    }
}
```

---

## 🛠️ Outils

### Composer

```json
{
  "require": {
    "php": "^8.1",
    "monolog/monolog": "^2.0"
  },
  "require-dev": {
    "phpunit/phpunit": "^9.5"
  }
}
```

### PHP-CS-Fixer

```php
<?php
// .php-cs-fixer.php
$finder = PhpCsFixer\Finder::create()
    ->in(__DIR__ . '/src');

return (new PhpCsFixer\Config())
    ->setRules([
        '@PSR12' => true,
        'array_syntax' => ['syntax' => 'short'],
        'strict_param' => true,
    ])
    ->setFinder($finder);
```

### PHPStan

```neon
# phpstan.neon
parameters:
    level: 8
    paths:
        - src
```

---

## 📚 Ressources

- [PHP-FIG (PSR Standards)](https://www.php-fig.org/)
- [PHP The Right Way](https://phptherightway.com/)
- [PHP Documentation](https://www.php.net/docs.php)
- [Laravel Best Practices](https://github.com/alexeymezenin/laravel-best-practices)
