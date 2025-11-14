# Cheatsheet JavaScript

- [Index](/Readme.md)
- [Doc MDN](https://developer.mozilla.org/fr/docs/Web/JavaScript)
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

## Maps

Accepte tous les types

```js
const map = new Map();

map.set("1", "value1"); // clé chaîne
map.set(1, "value2"); // clé numérique
map.set(true, "value3"); // clé booléen

console.log(map.get("1")); // 'value1'
console.log(map.get(1)); // 'value2'
console.log(map.size); // 3
```

```js
// Création
const maMap = new Map();

maMap.set(4, "quatre");
maMap.set(2, "deux");

console.log(maMap.get(2)); // "deux"
console.log(maMap.has(1)); // false

// Itération sur une Map
for (const [cle, valeur] of maMap) {
  console.log(`${cle}: ${valeur}`);
}
```

## Sets

Collection de valeurs uniques ne pouvant apparaître 1 seule fois.
Utile pour supprimer doublons dans une collection de data.

```js
const ensemble = new Set();

const jean = { nom: "Jean" };
const paul = { nom: "Paul" };
const marie = { nom: "Marie" };

ensemble.add(jean);
ensemble.add(paul);
ensemble.add(marie);
ensemble.add(jean); // tentative d'ajout en double

console.log(ensemble.size); // 3

for (let utilisateur of ensemble) {
  console.log(utilisateur.nom); // Jean, Paul, Marie
}

const marques = new Set(["tesla", "renault", "ds"]);
for (let valeur of marques) console.log(valeur);

// forEach
marques.forEach((valeur) => {
  console.log(valeur);
});
```

## Console

```js
console.log("Bonjour !");
console.assert(assertion, message); // permet d'afficher un message et la trace d'appel si l'expression passée retourne false.
console.count(label); // permet de compter le nombre de fois que la ligne a été exécutée. Si vous passez une variable, ici label, elle affichera le nombre de fois elle a été appelée en redémarrant à 0 à chaque fois que la valeur de label sera modifiée.
console.error(message); // permet d'afficher un message d'erreur en rouge dans la console du navigateur.
console.table(tableau); // permet d'afficher les tableaux sous forme de tableau avec deux colonnes : une pour les index et un pour les valeurs associées.
console.time() & console.timeEnd();
// Ces méthodes permettent de démarrer un timer (console.time()) puis de le terminer (timeEnd()) et d'afficher le temps d'exécution entre les deux.
console.trace(); //permet d'afficher la trace d'appel. Nous y reviendrons, mais en deux mots c'est l'ordre d'appel et d'exécution des fonctions.
console.warn(message); // permet d'afficher un message d'avertissement dans la console du navigateur.
console.info(message); // permet d'afficher un message d'information dans la console du navigateur.
```

## Opérateur de coalescence des nuls

Permet de définir une valeur par défaut.

```js
let uneVariable;
console.log(uneVariable ?? "Non défini"); // "Non défini" (uneVariable est undefined)

const utilisateur = "Jean";
console.log(utilisateur ?? "Inconnu"); // "Jean" (utilisateur n'est pas null/undefined)
```

## Opérateur de chaînage optionnel

Permet d'accéder à une prop dans un objet même si valeur intermédiaire n'existe pas.

```js
// Exemple
let street;

if (user && user.details && user.details.address) {
  street = user.details.address.street;
}

console.log(street); // "Rue de Rivoli" si toutes les propriétés existent, sinon `undefined`

// avec le chaînage optionnel
let street = user?.details?.address?.street;

console.log(street); // "Rue de Rivoli" si toutes les propriétés existent, sinon `undefined`
```

A utiliser avec parcimonie, possible création de bug fantômes.

## Vérification des types

```js
typeof 42; // "number"
typeof null; // "object" (quirk!)
Array.isArray([]); // true

if (val == null) {
  // true for null or undefined
}

val !== null && typeof val === "object";