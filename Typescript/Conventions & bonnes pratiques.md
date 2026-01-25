# Conventions & Bonnes Pratiques TypeScript

## 📋 Conventions de Nommage

### Variables et Fonctions

- **camelCase** pour les variables et fonctions

```typescript
let userName: string = "John";
const getUserData = (): User => {};
```

### Interfaces et Types

- **PascalCase** pour les interfaces et types
- Préfixer les interfaces avec `I` (optionnel, débattu)

```typescript
interface User {
  name: string;
  age: number;
}

type UserRole = "admin" | "user" | "guest";

// Alternative avec préfixe (moins recommandé)
interface IUser {
  name: string;
}
```

### Classes et Enums

- **PascalCase** pour les classes et enums

```typescript
class UserService {
  constructor(private apiUrl: string) {}
}

enum UserRole {
  Admin = "ADMIN",
  User = "USER",
  Guest = "GUEST",
}
```

### Génériques

- Noms courts et descriptifs : `T`, `K`, `V`, ou plus explicites

```typescript
function identity<T>(arg: T): T {
  return arg;
}

class Repository<TEntity, TId> {
  findById(id: TId): TEntity | null {}
}
```

---

## 🏗️ Types et Interfaces

### Préférer les interfaces aux types pour les objets

```typescript
// ✅ Bon - interface pour les objets
interface User {
  name: string;
  email: string;
}

// ✅ Type pour les unions, intersections, primitives
type ID = string | number;
type Status = "pending" | "active" | "inactive";
```

### Utiliser les types utilitaires

```typescript
// Partial, Required, Readonly, Pick, Omit
type PartialUser = Partial<User>;
type ReadonlyUser = Readonly<User>;
type UserWithoutEmail = Omit<User, "email">;
type UserNameAndEmail = Pick<User, "name" | "email">;

// Record
type UserRoles = Record<string, User[]>;
```

### Éviter `any`, préférer `unknown`

```typescript
// ❌ Mauvais
function processData(data: any) {
  return data.value;
}

// ✅ Bon
function processData(data: unknown) {
  if (typeof data === "object" && data !== null && "value" in data) {
    return (data as { value: string }).value;
  }
  return null;
}
```

---

## ✅ Bonnes Pratiques

### 1. Typage strict

```typescript
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitThis": true
  }
}
```

### 2. Type Guards

```typescript
// Type guard personnalisé
function isUser(obj: unknown): obj is User {
  return (
    typeof obj === "object" && obj !== null && "name" in obj && "email" in obj
  );
}

// Utilisation
if (isUser(data)) {
  console.log(data.name); // TypeScript sait que c'est un User
}
```

### 3. Discriminated Unions

```typescript
interface Success {
  type: "success";
  data: string;
}

interface Error {
  type: "error";
  message: string;
}

type Result = Success | Error;

function handleResult(result: Result) {
  switch (result.type) {
    case "success":
      console.log(result.data);
      break;
    case "error":
      console.error(result.message);
      break;
  }
}
```

### 4. Nullish Coalescing et Optional Chaining

```typescript
// ✅ Bon
const value = user?.profile?.name ?? "Anonymous";
const port = config.port ?? 3000;
```

### 5. Readonly pour l'immutabilité

```typescript
interface User {
  readonly id: string;
  name: string;
}

const readonlyArray: readonly number[] = [1, 2, 3];
// readonlyArray.push(4); // Erreur
```

---

## 🎯 Fonctions

### Typage des paramètres et retours

```typescript
// ✅ Toujours typer les paramètres
function add(a: number, b: number): number {
  return a + b;
}

// Paramètres optionnels
function greet(name: string, title?: string): string {
  return title ? `${title} ${name}` : name;
}

// Valeurs par défaut
function createUser(name: string, role: UserRole = UserRole.User): User {
  return { name, role };
}
```

### Overloading

```typescript
function parse(value: string): string[];
function parse(value: number): number;
function parse(value: string | number): string[] | number {
  if (typeof value === "string") {
    return value.split(",");
  }
  return value;
}
```

### Callback typing

```typescript
// ✅ Bon
type Callback = (error: Error | null, data?: string) => void;

function fetchData(url: string, callback: Callback): void {
  // implementation
}
```

---

## 📦 Modules et Imports

### Organisation des imports

```typescript
// 1. Imports de types uniquement
import type { User, Role } from "./types";

// 2. Imports normaux
import { UserService } from "./services";

// 3. Imports de types inline (TS 4.5+)
import { type User, createUser } from "./user";
```

### Exports typés

```typescript
// ✅ Export des types séparément
export type { User, Role };
export { UserService, createUser };

// ✅ Re-export
export type * from "./types";
export * from "./utils";
```

### Path mapping

```typescript
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/types/*": ["src/types/*"],
      "@/utils/*": ["src/utils/*"]
    }
  }
}

// Utilisation
import { User } from '@/types/user';
```

---

## 🏛️ Classes et POO

### Modificateurs d'accès

```typescript
class User {
  public name: string; // Accessible partout
  protected email: string; // Accessible dans la classe et sous-classes
  private password: string; // Accessible uniquement dans la classe
  readonly id: string; // Lecture seule

  constructor(name: string, email: string) {
    this.name = name;
    this.email = email;
    this.id = crypto.randomUUID();
  }
}

// Raccourci avec modificateurs dans le constructeur
class User {
  constructor(
    public name: string,
    private email: string,
    readonly id: string,
  ) {}
}
```

### Classes abstraites

```typescript
abstract class Animal {
  abstract makeSound(): void;

  move(): void {
    console.log("Moving...");
  }
}

class Dog extends Animal {
  makeSound(): void {
    console.log("Woof!");
  }
}
```

### Interfaces pour les contrats

```typescript
interface Serializable {
  serialize(): string;
}

class User implements Serializable {
  serialize(): string {
    return JSON.stringify(this);
  }
}
```

---

## 🔄 Génériques

### Contraintes de génériques

```typescript
// ✅ Contraindre les génériques
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// Contrainte d'interface
interface HasId {
  id: string;
}

function findById<T extends HasId>(items: T[], id: string): T | undefined {
  return items.find((item) => item.id === id);
}
```

### Génériques avec valeurs par défaut

```typescript
interface Response<T = any> {
  data: T;
  status: number;
}

const response: Response<User> = {
  data: { name: "John", email: "john@example.com" },
  status: 200,
};
```

---

## 🛡️ Gestion des Erreurs

### Types d'erreurs personnalisés

```typescript
class ValidationError extends Error {
  constructor(
    message: string,
    public field: string,
  ) {
    super(message);
    this.name = "ValidationError";
  }
}

// Utilisation
function validateEmail(email: string): void {
  if (!email.includes("@")) {
    throw new ValidationError("Invalid email format", "email");
  }
}
```

### Type-safe error handling

```typescript
type Result<T, E = Error> =
  | { success: true; value: T }
  | { success: false; error: E };

function divide(a: number, b: number): Result<number> {
  if (b === 0) {
    return { success: false, error: new Error("Division by zero") };
  }
  return { success: true, value: a / b };
}

// Utilisation
const result = divide(10, 2);
if (result.success) {
  console.log(result.value);
} else {
  console.error(result.error);
}
```

---

## 🎨 Patterns TypeScript

### Builder Pattern

```typescript
class UserBuilder {
  private user: Partial<User> = {};

  setName(name: string): this {
    this.user.name = name;
    return this;
  }

  setEmail(email: string): this {
    this.user.email = email;
    return this;
  }

  build(): User {
    if (!this.user.name || !this.user.email) {
      throw new Error("Missing required fields");
    }
    return this.user as User;
  }
}

// Utilisation
const user = new UserBuilder()
  .setName("John")
  .setEmail("john@example.com")
  .build();
```

### Factory Pattern

```typescript
interface Shape {
  draw(): void;
}

class Circle implements Shape {
  draw(): void {
    console.log("Circle");
  }
}

class Square implements Shape {
  draw(): void {
    console.log("Square");
  }
}

class ShapeFactory {
  static createShape(type: "circle" | "square"): Shape {
    switch (type) {
      case "circle":
        return new Circle();
      case "square":
        return new Square();
    }
  }
}
```

---

## 🔍 Utility Types Avancés

### Conditional Types

```typescript
type IsString<T> = T extends string ? true : false;
type A = IsString<string>; // true
type B = IsString<number>; // false
```

### Mapped Types

```typescript
type Optional<T> = {
  [K in keyof T]?: T[K];
};

type Nullable<T> = {
  [K in keyof T]: T[K] | null;
};
```

### Template Literal Types

```typescript
type EventName = "click" | "focus" | "blur";
type EventHandler = `on${Capitalize<EventName>}`;
// 'onClick' | 'onFocus' | 'onBlur'
```

---

## 📝 Documentation

### JSDoc avec TypeScript

````typescript
/**
 * Calcule le prix total avec taxes
 * @param price - Prix de base
 * @param taxRate - Taux de taxe (0.2 = 20%)
 * @returns Prix total avec taxes
 * @throws {ValidationError} Si le prix est négatif
 * @example
 * ```ts
 * const total = calculateTotal(100, 0.2);
 * console.log(total); // 120
 * ```
 */
function calculateTotal(price: number, taxRate: number): number {
  if (price < 0) {
    throw new ValidationError("Price cannot be negative", "price");
  }
  return price * (1 + taxRate);
}
````

---

## 🚀 Performance et Optimisation

### Lazy Types

```typescript
// Utiliser des imports de types uniquement quand possible
import type { HeavyType } from "./heavy-module";

type MyType = {
  data: HeavyType;
};
```

### Éviter les types trop complexes

```typescript
// ❌ Trop complexe, ralentit la compilation
type VeryComplexType<T> =
  T extends Array<infer U>
    ? U extends Promise<infer V>
      ? V extends object
        ? { [K in keyof V]: VeryComplexType<V[K]> }
        : V
      : U
    : T;

// ✅ Simplifier quand possible
type ExtractArrayType<T> = T extends Array<infer U> ? U : T;
```

---

## 🧪 Tests

### Typage des tests

```typescript
import { describe, it, expect } from "vitest";

describe("UserService", () => {
  it("should create a user", () => {
    const user: User = createUser("John", "john@example.com");
    expect(user.name).toBe("John");
  });
});

// Mock avec types
const mockUserService: jest.Mocked<UserService> = {
  getUser: jest.fn(),
  createUser: jest.fn(),
};
```

---

## 🛠️ Configuration

### tsconfig.json recommandé

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "lib": ["ES2022", "DOM"],
    "moduleResolution": "bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "declaration": true,
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

---

## 🔒 Sécurité des Types

### Assertion de type sûre

```typescript
// ❌ Éviter
const value = data as string;

// ✅ Préférer la validation
function isString(value: unknown): value is string {
  return typeof value === "string";
}

if (isString(data)) {
  const value = data; // TypeScript sait que c'est une string
}
```

### Non-null assertion (utiliser avec prudence)

```typescript
// ❌ À éviter si possible
const value = obj!.property;

// ✅ Préférer
const value = obj?.property ?? defaultValue;
```

---

## 📚 Ressources

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [Effective TypeScript](https://effectivetypescript.com/)
- [Type Challenges](https://github.com/type-challenges/type-challenges)
