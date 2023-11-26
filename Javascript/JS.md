# Cheatsheet JavaScript

- [Index](/Readme.md)
- [DOC MDN](https://developer.mozilla.org/fr/docs/Web/JavaScript)
- [JS INFO](https://fr.javascript.info/)

## Déclarations de variable et constante

```js
// let et const
let x = 10;
const y = 20;
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

## Destructuration

```js
const personne = { nom: "John", age: 30 };
```

## Template literals ou littéraux de gabarit

```js
const message = `Hello, ${nom}!`;
```

## Spread/rest operator

```js

```

## Classes

```js
class Personne {
  constructor(nom, age) {
    this.nom = nom;
    this.age = age;
  }
}
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
