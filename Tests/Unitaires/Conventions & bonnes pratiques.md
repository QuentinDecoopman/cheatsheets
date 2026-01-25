# Conventions & Bonnes Pratiques Jest

## 📋 Conventions de Nommage

### Fichiers de test

- **Same name + .test ou .spec**

```
src/
├── components/
│   ├── Button.tsx
│   └── Button.test.tsx
├── utils/
│   ├── formatDate.ts
│   └── formatDate.spec.ts
```

### Nommage des tests

```typescript
// ✅ Bon - descriptif
describe("Button component", () => {
  it("should render with default props", () => {});
  it("should call onClick handler when clicked", () => {});
  it("should be disabled when disabled prop is true", () => {});
});

// ❌ Éviter
describe("Button", () => {
  it("test 1", () => {});
  it("works", () => {});
});
```

---

## 🏗️ Structure des Tests

### Organisation AAA (Arrange, Act, Assert)

```typescript
describe("calculateTotal", () => {
  it("should calculate total with tax", () => {
    // Arrange - Préparer les données
    const price = 100;
    const taxRate = 0.2;

    // Act - Exécuter la fonction
    const result = calculateTotal(price, taxRate);

    // Assert - Vérifier le résultat
    expect(result).toBe(120);
  });
});
```

### Hooks de cycle de vie

```typescript
describe("UserService", () => {
  let userService: UserService;

  // Exécuté avant tous les tests
  beforeAll(() => {
    // Setup une fois pour tous les tests
  });

  // Exécuté avant chaque test
  beforeEach(() => {
    userService = new UserService();
  });

  // Exécuté après chaque test
  afterEach(() => {
    // Nettoyage
    jest.clearAllMocks();
  });

  // Exécuté après tous les tests
  afterAll(() => {
    // Nettoyage final
  });

  it("should create a user", () => {
    // Test
  });
});
```

---

## ✅ Assertions (Matchers)

### Matchers de base

```typescript
// Égalité
expect(value).toBe(4); // ===
expect(value).toEqual({ name: "John" }); // Deep equality
expect(value).toStrictEqual({ name: "John" }); // Strict equality

// Truthiness
expect(value).toBeTruthy();
expect(value).toBeFalsy();
expect(value).toBeDefined();
expect(value).toBeUndefined();
expect(value).toBeNull();

// Nombres
expect(value).toBeGreaterThan(3);
expect(value).toBeGreaterThanOrEqual(3.5);
expect(value).toBeLessThan(5);
expect(value).toBeLessThanOrEqual(4.5);
expect(value).toBeCloseTo(0.3); // Nombres flottants

// Strings
expect(str).toMatch(/pattern/);
expect(str).toContain("substring");

// Arrays et itérables
expect(array).toContain(item);
expect(array).toHaveLength(3);
expect(array).toEqual(expect.arrayContaining([1, 2]));

// Objects
expect(obj).toHaveProperty("name");
expect(obj).toHaveProperty("age", 25);
expect(obj).toMatchObject({ name: "John" });

// Exceptions
expect(() => fn()).toThrow();
expect(() => fn()).toThrow(Error);
expect(() => fn()).toThrow("error message");

// Async
await expect(promise).resolves.toBe(value);
await expect(promise).rejects.toThrow();
```

### Matchers personnalisés

```typescript
expect.extend({
  toBeWithinRange(received, floor, ceiling) {
    const pass = received >= floor && received <= ceiling;
    return {
      pass,
      message: () =>
        `expected ${received} to be within range ${floor} - ${ceiling}`,
    };
  },
});

// Utilisation
expect(100).toBeWithinRange(90, 110);
```

---

## 🎭 Mocks et Spies

### Mock Functions

```typescript
// Mock simple
const mockFn = jest.fn();
mockFn("arg1", "arg2");

expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledTimes(1);
expect(mockFn).toHaveBeenCalledWith("arg1", "arg2");
expect(mockFn).toHaveBeenLastCalledWith("arg1", "arg2");

// Mock avec implémentation
const mockFn = jest.fn((x) => x * 2);
expect(mockFn(5)).toBe(10);

// Mock avec valeur de retour
const mockFn = jest.fn();
mockFn.mockReturnValue(42);
mockFn.mockReturnValueOnce(1).mockReturnValueOnce(2);

// Mock avec Promise
mockFn.mockResolvedValue(42);
mockFn.mockRejectedValue(new Error("error"));

// Vérifier les appels
expect(mockFn.mock.calls).toHaveLength(2);
expect(mockFn.mock.calls[0][0]).toBe("arg1");
expect(mockFn.mock.results[0].value).toBe(42);

// Reset
mockFn.mockClear(); // Efface les appels
mockFn.mockReset(); // Clear + remove implementation
mockFn.mockRestore(); // Reset + restore original
```

### Mock Modules

```typescript
// Mock d'un module complet
jest.mock("@/services/api");

import { getUser } from "@/services/api";

// getUser est maintenant une mock function
(getUser as jest.Mock).mockResolvedValue({ id: 1, name: "John" });

// Mock partiel
jest.mock("@/services/api", () => ({
  ...jest.requireActual("@/services/api"),
  getUser: jest.fn(),
}));

// Mock avec factory
jest.mock("@/services/api", () => ({
  getUser: jest.fn(() => Promise.resolve({ id: 1 })),
}));
```

### Spies

```typescript
// Espionner une méthode
const user = {
  getName: () => "John",
};

const spy = jest.spyOn(user, "getName");
user.getName();

expect(spy).toHaveBeenCalled();
spy.mockRestore(); // Restaurer l'implémentation originale

// Spy avec implémentation
jest.spyOn(console, "log").mockImplementation(() => {});
```

---

## 🔄 Tests Asynchrones

### Callbacks

```typescript
test("callback", (done) => {
  function callback(data) {
    expect(data).toBe("peanut butter");
    done();
  }

  fetchData(callback);
});
```

### Promises

```typescript
// Return promise
test("promise", () => {
  return fetchData().then((data) => {
    expect(data).toBe("peanut butter");
  });
});

// Resolves / Rejects
test("resolves", () => {
  return expect(fetchData()).resolves.toBe("peanut butter");
});

test("rejects", () => {
  return expect(fetchData()).rejects.toThrow("error");
});
```

### Async/Await

```typescript
test("async/await", async () => {
  const data = await fetchData();
  expect(data).toBe("peanut butter");
});

test("async/await with error", async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (error) {
    expect(error).toMatch("error");
  }
});

// Avec resolves
test("async resolves", async () => {
  await expect(fetchData()).resolves.toBe("peanut butter");
});
```

---

## 🧪 Testing React Components

### Avec React Testing Library

```typescript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Button from './Button';

describe('Button', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', async () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);

    await userEvent.click(screen.getByText('Click me'));

    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>);
    expect(screen.getByText('Click me')).toBeDisabled();
  });

  it('renders with correct variant', () => {
    const { container } = render(<Button variant="primary">Click</Button>);
    expect(container.firstChild).toHaveClass('button--primary');
  });
});

// Test avec async
describe('UserProfile', () => {
  it('loads and displays user data', async () => {
    render(<UserProfile userId="1" />);

    expect(screen.getByText(/loading/i)).toBeInTheDocument();

    await waitFor(() => {
      expect(screen.getByText('John Doe')).toBeInTheDocument();
    });
  });
});

// Test avec form
describe('LoginForm', () => {
  it('submits form with correct data', async () => {
    const handleSubmit = jest.fn();
    render(<LoginForm onSubmit={handleSubmit} />);

    await userEvent.type(screen.getByLabelText(/email/i), 'john@example.com');
    await userEvent.type(screen.getByLabelText(/password/i), 'password123');
    await userEvent.click(screen.getByRole('button', { name: /submit/i }));

    await waitFor(() => {
      expect(handleSubmit).toHaveBeenCalledWith({
        email: 'john@example.com',
        password: 'password123',
      });
    });
  });
});
```

### Snapshot Testing

```typescript
import renderer from 'react-test-renderer';

it('matches snapshot', () => {
  const tree = renderer.create(<Button>Click me</Button>).toJSON();
  expect(tree).toMatchSnapshot();
});

// Update snapshots: jest --updateSnapshot ou jest -u
```

---

## 🔧 Configuration

### jest.config.js

```javascript
module.exports = {
  // Environnement
  testEnvironment: "jsdom", // ou 'node'

  // Racine des tests
  roots: ["<rootDir>/src"],

  // Patterns de fichiers de test
  testMatch: [
    "**/__tests__/**/*.{js,jsx,ts,tsx}",
    "**/*.{spec,test}.{js,jsx,ts,tsx}",
  ],

  // Setup
  setupFilesAfterEnv: ["<rootDir>/jest.setup.js"],

  // Transformation
  transform: {
    "^.+\\.(ts|tsx)$": "ts-jest",
    "^.+\\.(js|jsx)$": "babel-jest",
  },

  // Module mapping
  moduleNameMapper: {
    "^@/(.*)$": "<rootDir>/src/$1",
    "\\.(css|less|scss|sass)$": "identity-obj-proxy",
    "\\.(jpg|jpeg|png|gif|svg)$": "<rootDir>/__mocks__/fileMock.js",
  },

  // Coverage
  collectCoverageFrom: [
    "src/**/*.{js,jsx,ts,tsx}",
    "!src/**/*.d.ts",
    "!src/**/*.stories.{js,jsx,ts,tsx}",
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },

  // Ignore
  testPathIgnorePatterns: ["/node_modules/", "/dist/"],
  transformIgnorePatterns: ["/node_modules/"],
};
```

### jest.setup.js

```javascript
// Testing Library
import "@testing-library/jest-dom";

// Mock global objects
global.fetch = jest.fn();

// Mock window.matchMedia
Object.defineProperty(window, "matchMedia", {
  writable: true,
  value: jest.fn().mockImplementation((query) => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(),
    removeListener: jest.fn(),
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});
```

---

## 🚀 Best Practices

### 1. Tests isolés et indépendants

```typescript
// ✅ Bon
describe("UserService", () => {
  let userService: UserService;

  beforeEach(() => {
    userService = new UserService();
  });

  it("creates a user", () => {
    const user = userService.create({ name: "John" });
    expect(user.name).toBe("John");
  });

  it("deletes a user", () => {
    // Ne dépend pas du test précédent
    const user = userService.create({ name: "Jane" });
    userService.delete(user.id);
    expect(userService.find(user.id)).toBeNull();
  });
});
```

### 2. Tester le comportement, pas l'implémentation

```typescript
// ❌ Teste l'implémentation
it("calls internal method", () => {
  const spy = jest.spyOn(service, "_internalMethod");
  service.publicMethod();
  expect(spy).toHaveBeenCalled();
});

// ✅ Teste le comportement
it("returns correct result", () => {
  const result = service.publicMethod();
  expect(result).toBe(expectedValue);
});
```

### 3. Descriptions claires

```typescript
// ✅ Bon
describe("UserService.createUser", () => {
  it("should create user with valid data", () => {});
  it("should throw error when email is invalid", () => {});
  it("should hash password before saving", () => {});
});
```

### 4. Un seul concept par test

```typescript
// ❌ Teste plusieurs choses
it("creates and updates user", () => {
  const user = service.create({ name: "John" });
  expect(user.name).toBe("John");

  service.update(user.id, { name: "Jane" });
  expect(user.name).toBe("Jane");
});

// ✅ Tests séparés
it("creates user with correct data", () => {
  const user = service.create({ name: "John" });
  expect(user.name).toBe("John");
});

it("updates user name", () => {
  const user = service.create({ name: "John" });
  service.update(user.id, { name: "Jane" });
  expect(user.name).toBe("Jane");
});
```

---

## 📊 Coverage

```bash
# Exécuter les tests avec coverage
jest --coverage

# Coverage pour fichiers spécifiques
jest --coverage --collectCoverageFrom="src/components/**/*.{js,jsx}"

# Voir le rapport HTML
open coverage/lcov-report/index.html
```

---

## 📚 Ressources

- [Jest Documentation](https://jestjs.io/docs/getting-started)
- [Testing Library](https://testing-library.com/)
- [Jest Cheat Sheet](https://github.com/sapegin/jest-cheat-sheet)
