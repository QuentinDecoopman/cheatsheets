# Conventions & Bonnes Pratiques Cypress

## 📋 Conventions de Nommage

### Fichiers de test

```
cypress/
├── e2e/
│   ├── auth/
│   │   ├── login.cy.ts
│   │   └── register.cy.ts
│   ├── dashboard/
│   │   └── dashboard.cy.ts
│   └── checkout.cy.ts
├── fixtures/
│   ├── users.json
│   └── products.json
├── support/
│   ├── commands.ts
│   └── e2e.ts
└── cypress.config.ts
```

### Nommage des tests

```typescript
// ✅ Bon - descriptif et orienté utilisateur
describe("User Authentication", () => {
  it("should allow user to login with valid credentials", () => {});
  it("should display error message with invalid credentials", () => {});
  it("should redirect to dashboard after successful login", () => {});
});

// ❌ Éviter
describe("Login", () => {
  it("works", () => {});
  it("test 1", () => {});
});
```

---

## 🏗️ Structure des Tests

### Organisation des tests

```typescript
describe("Feature: Shopping Cart", () => {
  beforeEach(() => {
    // Setup commun
    cy.visit("/shop");
    cy.login("user@example.com", "password");
  });

  context("When adding items", () => {
    it("should add item to cart", () => {
      cy.get("[data-cy=product-1]").click();
      cy.get("[data-cy=add-to-cart]").click();
      cy.get("[data-cy=cart-count]").should("contain", "1");
    });

    it("should update quantity", () => {
      cy.get("[data-cy=product-1]").click();
      cy.get("[data-cy=add-to-cart]").click();
      cy.get("[data-cy=add-to-cart]").click();
      cy.get("[data-cy=cart-count]").should("contain", "2");
    });
  });

  context("When removing items", () => {
    beforeEach(() => {
      // Setup spécifique
      cy.addToCart("product-1");
    });

    it("should remove item from cart", () => {
      cy.get("[data-cy=remove-from-cart]").click();
      cy.get("[data-cy=cart-count]").should("contain", "0");
    });
  });
});
```

---

## ✅ Sélecteurs

### Ordre de priorité

```typescript
// 1. ✅ data-cy attributes (recommandé)
cy.get("[data-cy=submit-button]").click();

// 2. data-test, data-testid
cy.get("[data-test=submit-button]").click();

// 3. ID (si stable)
cy.get("#submit-button").click();

// 4. Classes (moins stable)
cy.get(".btn-primary").click();

// ❌ Éviter - fragile
cy.get("button").eq(2).click();
cy.contains("Submit").click(); // Texte peut changer
```

### Bonnes pratiques de sélection

```typescript
// ✅ Sélecteurs data-cy
<button data-cy="submit-button">Submit</button>
<input data-cy="email-input" />
<div data-cy="error-message">Error</div>

// Dans les tests
cy.get('[data-cy=email-input]').type('user@example.com');
cy.get('[data-cy=submit-button]').click();
cy.get('[data-cy=error-message]').should('be.visible');

// Sélecteurs composés
cy.get('[data-cy=user-list]')
  .find('[data-cy=user-item]')
  .first()
  .click();
```

---

## 🎯 Commandes Cypress

### Navigation

```typescript
cy.visit("/");
cy.visit("/products", {
  timeout: 10000,
  onBeforeLoad: (win) => {
    // Mock avant chargement
  },
});

cy.go("back");
cy.go("forward");
cy.reload();
```

### Interaction

```typescript
// Clics
cy.get("[data-cy=button]").click();
cy.get("[data-cy=button]").click({ force: true }); // Force si élément caché
cy.get("[data-cy=button]").dblclick();
cy.get("[data-cy=button]").rightclick();

// Input
cy.get("[data-cy=input]").type("text");
cy.get("[data-cy=input]").type("text{enter}");
cy.get("[data-cy=input]").clear();
cy.get("[data-cy=input]").type("text", { delay: 100 });

// Select
cy.get("[data-cy=select]").select("option1");
cy.get("[data-cy=select]").select(["option1", "option2"]); // Multiple

// Checkbox/Radio
cy.get("[data-cy=checkbox]").check();
cy.get("[data-cy=checkbox]").uncheck();
cy.get("[data-cy=radio]").check("value");

// Focus/Blur
cy.get("[data-cy=input]").focus();
cy.get("[data-cy=input]").blur();

// Hover (simulé)
cy.get("[data-cy=element]").trigger("mouseover");
```

### Assertions

```typescript
// Existence
cy.get("[data-cy=element]").should("exist");
cy.get("[data-cy=element]").should("not.exist");

// Visibilité
cy.get("[data-cy=element]").should("be.visible");
cy.get("[data-cy=element]").should("be.hidden");

// Texte
cy.get("[data-cy=element]").should("contain", "text");
cy.get("[data-cy=element]").should("have.text", "exact text");
cy.get("[data-cy=element]").should("include.text", "partial");

// Valeur
cy.get("[data-cy=input]").should("have.value", "value");
cy.get("[data-cy=input]").should("be.empty");

// Attributs
cy.get("[data-cy=link]").should("have.attr", "href", "/path");
cy.get("[data-cy=input]").should("have.class", "active");
cy.get("[data-cy=button]").should("be.disabled");
cy.get("[data-cy=button]").should("not.be.disabled");

// État
cy.get("[data-cy=checkbox]").should("be.checked");
cy.get("[data-cy=option]").should("be.selected");

// Count
cy.get("[data-cy=item]").should("have.length", 5);
cy.get("[data-cy=item]").should("have.length.greaterThan", 3);

// CSS
cy.get("[data-cy=element]").should("have.css", "color", "rgb(255, 0, 0)");

// Multiple assertions
cy.get("[data-cy=element]")
  .should("exist")
  .and("be.visible")
  .and("contain", "text");
```

### Attentes

```typescript
// Wait explicite
cy.wait(1000); // ❌ Éviter si possible

// Wait pour requête
cy.intercept("GET", "/api/users").as("getUsers");
cy.visit("/users");
cy.wait("@getUsers");

// Wait pour élément
cy.get("[data-cy=element]", { timeout: 10000 }).should("be.visible");

// Wait pour condition
cy.get("[data-cy=count]").should("have.text", "10");
```

---

## 🌐 Requêtes API

### Intercept (cy.intercept)

```typescript
// Mock réponse
cy.intercept("GET", "/api/users", {
  statusCode: 200,
  body: [
    { id: 1, name: "John" },
    { id: 2, name: "Jane" },
  ],
}).as("getUsers");

// Modifier requête
cy.intercept("POST", "/api/users", (req) => {
  req.body.createdAt = new Date().toISOString();
  req.continue();
});

// Modifier réponse
cy.intercept("GET", "/api/users", (req) => {
  req.reply((res) => {
    res.body.data = res.body.data.slice(0, 5);
  });
});

// Delay
cy.intercept("GET", "/api/users", (req) => {
  req.reply({
    delay: 1000,
    statusCode: 200,
    body: [],
  });
});

// Erreur
cy.intercept("POST", "/api/users", {
  statusCode: 400,
  body: { error: "Invalid data" },
});

// Wait et assertions
cy.intercept("GET", "/api/users").as("getUsers");
cy.visit("/users");
cy.wait("@getUsers").then((interception) => {
  expect(interception.response.statusCode).to.equal(200);
  expect(interception.response.body).to.have.length(2);
});
```

### Fixtures

```typescript
// cypress/fixtures/users.json
[
  { id: 1, name: "John" },
  { id: 2, name: "Jane" },
];

// Utilisation
cy.fixture("users").then((users) => {
  cy.intercept("GET", "/api/users", users);
});

// Ou directement
cy.intercept("GET", "/api/users", { fixture: "users.json" });
```

---

## 🔧 Commandes Personnalisées

### cypress/support/commands.ts

```typescript
// Déclaration TypeScript
declare global {
  namespace Cypress {
    interface Chainable {
      login(email: string, password: string): Chainable<void>;
      logout(): Chainable<void>;
      addToCart(productId: string): Chainable<void>;
      getByDataCy(selector: string): Chainable<JQuery<HTMLElement>>;
    }
  }
}

// Implémentation
Cypress.Commands.add("login", (email: string, password: string) => {
  cy.session([email, password], () => {
    cy.visit("/login");
    cy.get("[data-cy=email]").type(email);
    cy.get("[data-cy=password]").type(password);
    cy.get("[data-cy=submit]").click();
    cy.url().should("include", "/dashboard");
  });
});

Cypress.Commands.add("logout", () => {
  cy.get("[data-cy=user-menu]").click();
  cy.get("[data-cy=logout]").click();
});

Cypress.Commands.add("addToCart", (productId: string) => {
  cy.request("POST", "/api/cart", { productId });
});

Cypress.Commands.add("getByDataCy", (selector: string) => {
  return cy.get(`[data-cy=${selector}]`);
});

// Utilisation
cy.login("user@example.com", "password");
cy.getByDataCy("product-1").click();
```

---

## 🎭 Sessions et Authentification

### Sessions (Cypress 12+)

```typescript
// Optimise les tests en cachant l'état de connexion
beforeEach(() => {
  cy.session("user-session", () => {
    cy.visit("/login");
    cy.get("[data-cy=email]").type("user@example.com");
    cy.get("[data-cy=password]").type("password");
    cy.get("[data-cy=submit]").click();
    cy.url().should("include", "/dashboard");
  });

  cy.visit("/dashboard");
});

// Avec validation
cy.session(
  "user-session",
  () => {
    // Login
  },
  {
    validate() {
      cy.getCookie("session").should("exist");
    },
  },
);
```

### Token/Cookie

```typescript
// Set cookie
beforeEach(() => {
  cy.setCookie("session", "token-value");
  cy.visit("/dashboard");
});

// Local storage
beforeEach(() => {
  cy.visit("/");
  cy.window().then((win) => {
    win.localStorage.setItem("token", "token-value");
  });
  cy.visit("/dashboard");
});

// Via API
beforeEach(() => {
  cy.request("POST", "/api/login", {
    email: "user@example.com",
    password: "password",
  }).then((response) => {
    cy.setCookie("session", response.body.token);
  });
});
```

---

## 📝 Configuration

### cypress.config.ts

```typescript
import { defineConfig } from "cypress";

export default defineConfig({
  e2e: {
    baseUrl: "http://localhost:3000",

    // Timeout
    defaultCommandTimeout: 10000,
    pageLoadTimeout: 30000,
    requestTimeout: 10000,

    // Viewport
    viewportWidth: 1280,
    viewportHeight: 720,

    // Video et screenshots
    video: true,
    screenshotOnRunFailure: true,
    videosFolder: "cypress/videos",
    screenshotsFolder: "cypress/screenshots",

    // Retry
    retries: {
      runMode: 2,
      openMode: 0,
    },

    // Setup
    setupNodeEvents(on, config) {
      // Plugins
    },

    // Spec pattern
    specPattern: "cypress/e2e/**/*.cy.{js,jsx,ts,tsx}",

    // Support file
    supportFile: "cypress/support/e2e.ts",

    // Fixtures
    fixturesFolder: "cypress/fixtures",

    // Env
    env: {
      apiUrl: "http://localhost:4000",
    },
  },
});
```

### Environment Variables

```typescript
// cypress.config.ts
env: {
  apiUrl: 'http://localhost:4000',
  adminEmail: 'admin@example.com',
}

// Utilisation
cy.visit(Cypress.env('apiUrl'));

// Via CLI
npx cypress run --env apiUrl=https://api.example.com

// cypress.env.json (ne pas commiter si secrets)
{
  "apiUrl": "http://localhost:4000",
  "apiKey": "secret"
}
```

---

## 🚀 Best Practices

### 1. Tests indépendants

```typescript
// ✅ Bon - chaque test est isolé
describe("Products", () => {
  beforeEach(() => {
    cy.visit("/products");
  });

  it("should display products", () => {
    cy.get("[data-cy=product]").should("have.length.greaterThan", 0);
  });

  it("should filter products", () => {
    cy.get("[data-cy=filter]").select("electronics");
    cy.get("[data-cy=product]").should("contain", "Laptop");
  });
});

// ❌ Éviter - dépendance entre tests
it("adds product", () => {
  // ...
});

it("removes product", () => {
  // Dépend du test précédent
});
```

### 2. Utiliser data-cy

```typescript
// ✅ Bon
<button data-cy="submit-button">Submit</button>
cy.get('[data-cy=submit-button]').click();

// ❌ Éviter - fragile
cy.get('.btn.btn-primary').eq(2).click();
```

### 3. Éviter cy.wait avec durée fixe

```typescript
// ❌ Éviter
cy.get("[data-cy=button]").click();
cy.wait(3000);
cy.get("[data-cy=result]").should("be.visible");

// ✅ Bon - wait pour condition
cy.get("[data-cy=button]").click();
cy.get("[data-cy=result]").should("be.visible");

// ✅ Bon - wait pour requête
cy.intercept("GET", "/api/data").as("getData");
cy.get("[data-cy=button]").click();
cy.wait("@getData");
cy.get("[data-cy=result]").should("be.visible");
```

### 4. Ne pas tester l'implémentation

```typescript
// ❌ Éviter - teste l'implémentation
it("calls API with correct parameters", () => {
  cy.intercept("GET", "/api/users*").as("getUsers");
  cy.visit("/users");
  cy.wait("@getUsers").its("request.url").should("include", "page=1");
});

// ✅ Bon - teste le résultat
it("displays users", () => {
  cy.visit("/users");
  cy.get("[data-cy=user]").should("have.length.greaterThan", 0);
});
```

### 5. Scénarios utilisateur réels

```typescript
// ✅ Bon - parcours utilisateur complet
describe("Checkout Flow", () => {
  it("should complete purchase", () => {
    // 1. Login
    cy.login("user@example.com", "password");

    // 2. Browse products
    cy.visit("/products");
    cy.get("[data-cy=product-1]").click();

    // 3. Add to cart
    cy.get("[data-cy=add-to-cart]").click();
    cy.get("[data-cy=cart-count]").should("contain", "1");

    // 4. Go to checkout
    cy.get("[data-cy=cart]").click();
    cy.get("[data-cy=checkout]").click();

    // 5. Fill form
    cy.get("[data-cy=card-number]").type("4242424242424242");
    cy.get("[data-cy=expiry]").type("12/25");
    cy.get("[data-cy=cvc]").type("123");

    // 6. Submit
    cy.get("[data-cy=submit]").click();

    // 7. Verify success
    cy.url().should("include", "/order-confirmation");
    cy.get("[data-cy=success-message]").should("be.visible");
  });
});
```

---

## 📊 Debugging

### Screenshots et vidéos

```typescript
// Screenshot manuel
cy.screenshot("my-screenshot");

// Screenshot d'élément
cy.get("[data-cy=element]").screenshot();

// Pause pour debug
cy.pause();

// Debug
cy.get("[data-cy=element]").debug();

// Log
cy.log("Custom message");
```

### Commandes utiles

```bash
# Mode interactif
npx cypress open

# Headless
npx cypress run

# Spec spécifique
npx cypress run --spec "cypress/e2e/login.cy.ts"

# Browser spécifique
npx cypress run --browser chrome

# Sans vidéo
npx cypress run --video false
```

---

## 📚 Ressources

- [Cypress Documentation](https://docs.cypress.io/)
- [Cypress Best Practices](https://docs.cypress.io/guides/references/best-practices)
- [Cypress Real World App](https://github.com/cypress-io/cypress-realworld-app)
- [Testing Library Cypress](https://testing-library.com/docs/cypress-testing-library/intro/)
