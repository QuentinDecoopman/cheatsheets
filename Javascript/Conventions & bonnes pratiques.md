# Conventions & Bonnes Pratiques JavaScript

## 📋 Conventions de Nommage

### Variables et Fonctions

- **camelCase** pour les variables et fonctions

```javascript
let userName = "John";
const getUserData = () => {};
```

### Constantes

- **UPPER_SNAKE_CASE** pour les constantes globales

```javascript
const MAX_RETRY_COUNT = 3;
const API_BASE_URL = "https://api.example.com";
```

### Classes et Constructeurs

- **PascalCase** pour les classes

```javascript
class UserProfile {
  constructor(name) {
    this.name = name;
  }
}
```

### Fichiers

- **kebab-case** ou **camelCase** selon la convention du projet

```
user-service.js
userService.js
```

### Booléens

- Préfixer avec `is`, `has`, `should`, `can`

```javascript
const isActive = true;
const hasPermission = false;
const shouldUpdate = true;
const canEdit = false;
```

---

## 🏗️ Structure et Organisation

### Ordre des déclarations

```javascript
// 1. Imports
import React from "react";
import { useState } from "react";

// 2. Constantes
const API_URL = "https://api.example.com";

// 3. Fonctions utilitaires
const formatDate = (date) => {};

// 4. Composant/Classe principale
class MyComponent {}

// 5. Export
export default MyComponent;
```

### Organisation des fonctions

- Déclarer les fonctions avant leur utilisation (ou utiliser le hoisting consciemment)
- Grouper les fonctions par fonctionnalité

---

## ✅ Bonnes Pratiques Générales

### 1. Utiliser des noms explicites

```javascript
// ❌ Mauvais
const d = new Date();
const arr = [1, 2, 3];

// ✅ Bon
const currentDate = new Date();
const userIds = [1, 2, 3];
```

### 2. Éviter les nombres magiques

```javascript
// ❌ Mauvais
setTimeout(callback, 86400000);

// ✅ Bon
const MILLISECONDS_IN_DAY = 24 * 60 * 60 * 1000;
setTimeout(callback, MILLISECONDS_IN_DAY);
```

### 3. Préférer const et let à var

```javascript
// ❌ Éviter
var count = 0;

// ✅ Bon
const MAX_COUNT = 100;
let count = 0;
```

### 4. Utiliser === au lieu de ==

```javascript
// ❌ Mauvais
if (value == "5") {
}

// ✅ Bon
if (value === 5) {
}
```

### 5. Destructuration

```javascript
// ✅ Bon
const { name, age } = user;
const [first, second] = array;
```

### 6. Template Literals

```javascript
// ❌ Mauvais
const message = "Hello " + name + "!";

// ✅ Bon
const message = `Hello ${name}!`;
```

### 7. Arrow Functions

```javascript
// ✅ Bon pour les callbacks courts
const numbers = [1, 2, 3].map((n) => n * 2);

// ✅ Utiliser function pour les méthodes d'objets
const user = {
  name: "John",
  greet() {
    console.log(`Hello ${this.name}`);
  },
};
```

---

## 🔄 Asynchrone

### Préférer async/await à .then()

```javascript
// ❌ Acceptable mais moins lisible
function fetchData() {
  return fetch(url)
    .then((response) => response.json())
    .then((data) => processData(data))
    .catch((error) => handleError(error));
}

// ✅ Meilleur
async function fetchData() {
  try {
    const response = await fetch(url);
    const data = await response.json();
    return processData(data);
  } catch (error) {
    handleError(error);
  }
}
```

---

## 🛡️ Gestion des Erreurs

### Toujours gérer les erreurs

```javascript
// ✅ Bon
try {
  const data = JSON.parse(jsonString);
  processData(data);
} catch (error) {
  console.error("Erreur de parsing:", error);
  // Gérer l'erreur de manière appropriée
}
```

### Validation des paramètres

```javascript
function divide(a, b) {
  if (typeof a !== "number" || typeof b !== "number") {
    throw new TypeError("Les arguments doivent être des nombres");
  }
  if (b === 0) {
    throw new Error("Division par zéro impossible");
  }
  return a / b;
}
```

---

## 🎯 Fonctions

### Principe de responsabilité unique

```javascript
// ❌ Mauvais - fait trop de choses
function processUserAndSendEmail(user) {
  validateUser(user);
  saveToDatabase(user);
  sendWelcomeEmail(user.email);
  updateAnalytics(user);
}

// ✅ Bon - fonctions séparées
function processUser(user) {
  validateUser(user);
  saveToDatabase(user);
}

function notifyUser(user) {
  sendWelcomeEmail(user.email);
}
```

### Limiter les paramètres (max 3-4)

```javascript
// ❌ Trop de paramètres
function createUser(name, email, age, address, phone, role) {}

// ✅ Utiliser un objet
function createUser({ name, email, age, address, phone, role }) {}
```

### Retourner tôt (Early Return)

```javascript
// ✅ Bon
function processData(data) {
  if (!data) return null;
  if (!data.isValid) return null;

  return data.value * 2;
}
```

---

## 📦 Modules et Imports

### Imports organisés

```javascript
// 1. Bibliothèques externes
import React from "react";
import axios from "axios";

// 2. Imports internes
import { utils } from "@/utils";
import { config } from "@/config";

// 3. Imports relatifs
import Button from "./Button";
import "./styles.css";
```

### Exports nommés vs default

```javascript
// ✅ Export nommé (préférable pour plusieurs exports)
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// ✅ Export par défaut (pour l'export principal)
export default function Calculator() {}
```

---

## 🔍 Commentaires

### Commenter le "pourquoi", pas le "quoi"

```javascript
// ❌ Mauvais - évident
// Incrémente i de 1
i++;

// ✅ Bon - explique la raison
// Skip le premier élément car c'est l'en-tête
for (let i = 1; i < data.length; i++) {}
```

### JSDoc pour les fonctions publiques

```javascript
/**
 * Calcule le prix total avec taxes
 * @param {number} price - Prix de base
 * @param {number} taxRate - Taux de taxe (ex: 0.2 pour 20%)
 * @returns {number} Prix total avec taxes
 */
function calculateTotal(price, taxRate) {
  return price * (1 + taxRate);
}
```

---

## 🚀 Performance

### 1. Éviter les lookups répétitifs

```javascript
// ❌ Mauvais
for (let i = 0; i < array.length; i++) {
  console.log(array[i]);
}

// ✅ Bon
const length = array.length;
for (let i = 0; i < length; i++) {
  console.log(array[i]);
}
```

### 2. Debounce et Throttle

```javascript
// Pour les événements fréquents (scroll, resize, input)
const debouncedSearch = debounce(searchFunction, 300);
input.addEventListener("input", debouncedSearch);
```

### 3. Utiliser les méthodes appropriées

```javascript
// ✅ find() plutôt que filter()[0]
const user = users.find((u) => u.id === 5);

// ✅ some() plutôt que filter().length > 0
const hasAdmin = users.some((u) => u.role === "admin");
```

---

## 🔒 Sécurité

### 1. Éviter eval()

```javascript
// ❌ Dangereux
eval(userInput);

// ✅ Utiliser des alternatives sûres
JSON.parse(jsonString);
```

### 2. Valider les inputs utilisateur

```javascript
function sanitizeInput(input) {
  return input.trim().replace(/[<>]/g, "");
}
```

### 3. Utiliser strict mode

```javascript
"use strict";
```

---

## 🧪 Tests et Qualité

### Noms de tests descriptifs

```javascript
// ✅ Bon
describe("UserService", () => {
  it("should return null when user is not found", () => {});
  it("should throw error when email is invalid", () => {});
});
```

### Tester les cas limites

- Valeurs nulles/undefined
- Tableaux vides
- Chaînes vides
- Nombres négatifs ou zéro

---

## 📝 Formatage et Style

### Indentation

- Utiliser 2 ou 4 espaces (cohérent dans le projet)
- Configurer avec Prettier ou ESLint

### Point-virgules

- Être cohérent (toujours ou jamais)
- Recommandé : toujours utiliser

### Guillemets

- Simple quotes `'` ou doubles `"` (cohérent)
- Template literals `` ` `` pour les chaînes avec variables

### Longueur de ligne

- Maximum 80-120 caractères

---

## 🛠️ Outils Recommandés

### Linting et Formatting

- **ESLint** : Détection d'erreurs et application de règles
- **Prettier** : Formatage automatique du code

### Configuration ESLint de base

```json
{
  "extends": ["eslint:recommended"],
  "rules": {
    "no-console": "warn",
    "no-unused-vars": "error",
    "prefer-const": "error",
    "eqeqeq": "error"
  }
}
```

---

## 🎨 Patterns Courants

### Module Pattern

```javascript
const UserModule = (() => {
  const privateVar = "private";

  function privateMethod() {}

  return {
    publicMethod() {},
  };
})();
```

### Factory Pattern

```javascript
function createUser(name, role) {
  return {
    name,
    role,
    greet() {
      console.log(`Hello, I'm ${this.name}`);
    },
  };
}
```

### Observer Pattern

```javascript
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }

  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach((callback) => callback(data));
    }
  }
}
```

---

## 📚 Ressources

- [MDN Web Docs](https://developer.mozilla.org/fr/docs/Web/JavaScript)
- [JavaScript.info](https://javascript.info/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [Clean Code JavaScript](https://github.com/ryanmcdermott/clean-code-javascript)
