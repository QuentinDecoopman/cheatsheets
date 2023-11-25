# Cheatsheet Node.js

- [Index](/Readme.md)
- [Doc](https://nodejs.org/en/docs)

## Modules

```js
// Importer un module
const nomModule = require("nom-module");

// Exporter un module
module.exports = maFonction;
```

## Gestionnaire de paquets (npm)

```bash
# Initialiser un projet Node.js (créer un fichier package.json)
npm init

# Installer un module
npm install nom-module

# Installer un module en tant que dépendance de développement
npm install nom-module --save-dev

# Exécuter un script défini dans package.json
npm run nom-script
```

## Événements

```js
const EventEmitter = require("events");
const monEmitter = new EventEmitter();

// Écouter un événement
monEmitter.on("nomEvenement", (parametre) => {
  console.log("Événement déclenché avec paramètre :", parametre);
});

// Émettre un événement
monEmitter.emit("nomEvenement", valeurDuParametre);
```

## Système de fichiers

```js
const fs = require("fs");

// Lire un fichier
fs.readFile("chemin/vers/fichier.txt", "utf8", (err, data) => {
  if (err) throw err;
  console.log(data);
});

// Écrire dans un fichier
fs.writeFile("chemin/vers/fichier.txt", "Contenu à écrire", (err) => {
  if (err) throw err;
  console.log("Écriture réussie !");
});
```

## HTTP

```js
const http = require("http");

// Créer un serveur HTTP
const serveur = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader("Content-Type", "text/plain");
  res.end("Bonjour, monde !");
});

// Écouter sur un port
serveur.listen(3000, "127.0.0.1", () => {
  console.log("Le serveur écoute sur le port 3000");
});
```
