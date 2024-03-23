# Cheatsheet JavaScript

- [Index](/Readme.md)
- [DOC MDN](https://developer.mozilla.org/fr/docs/Web/JavaScript)
- [JS INFO](https://fr.javascript.info/)

## Classes

```js
class Personne {
  constructor(nom, age) {
    this.nom = nom;
    this.age = age;
  }
}
```

## Déclarations de variable et constante

```js
// let et const
let x = 10;
const y = 20;
```

## Destructuration

```js
const personne = { nom: "John", age: 30 };
```

## Fonctions

```js
function additionner(a, b) {
  return a + b;
}
```

## Fonction fléchée

```js
const additionner = (a, b) => a + b;
```

## Modules

```js
// Module export
// fichier: math.js
export const addition = (a, b) => a + b;

// Module import
// fichier: app.js
import { addition } from "./math";
console.log(addition(5, 3)); // 8
```

## Opérateurs

```js
// Opérateurs arithmétiques
let sum = 5 + 3;
let difference = 10 - 5;
let product = 2 * 4;
let quotient = 8 / 2;

// Opérateur de comparaison
let isEqual = x === 10;
let isNotEqual = y !== 5;

// Opérateur Logique
let andOperator = a && b;
let orOperator = x || y;
let notOperator = !isTrue;
```

## Promesses (Promise)

```js
function faireQuelqueChose() {
  return new Promise(function (resolve, reject) {
    // Logique asynchrone
    if (condition) {
      resolve("Succès");
    } else {
      reject("Échec");
    }
  });
}

const faireQuelqueChose = () =>
  new Promise((resolve, reject) => {
    // Logique asynchrone
    condition ? resolve("Succès") : reject("Échec");
  });
```

## Template literals ou littéraux de gabarit

```js
const message = `Hello, ${nom}!`;
```

## Types de données

```js
let name = "John"; // String

let age = 25; // Number

let isStudent = true; // Boolean

let fruits = ["apple", "orange", "banana"]; // Array

let person = { firstName: "John", lastName: "Doe" }; // Object
```
