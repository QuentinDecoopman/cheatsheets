# Cheatsheet Express

- [Index](/Readme.md)

## Installation d'Express

```bash
npm install express
```

## Configuration de base

```js
const express = require("express");
const app = express();
const port = 3000;
```

## Routes

```js
// Route avec méthode GET
app.get("/", (req, res) => {
  res.send("Bonjour, monde!");
});
```

```js
// Route avec paramètre
app.get("/utilisateur/:id", (req, res) => {
  const userId = req.params.id;
  res.send(`Profil de l'utilisateur ${userId}`);
});
```

## Middleware

```js
// Middleware de log
app.use((req, res, next) => {
  console.log(`[${new Date()}] ${req.method} ${req.url}`);
  next();
});
```

```js
// Middleware de gestion d'erreur
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send("Erreur interne du serveur!");
});
```

## Paramètres de requête

```js
// Récupération de paramètres de requête
app.get("/utilisateur", (req, res) => {
  const nom = req.query.nom;
  res.send(`Requête GET pour l'utilisateur ${nom}`);
});
```

## Formulaire POST

```js
const bodyParser = require("body-parser");
// Utilisation de bodyParser pour analyser les données POST
app.use(bodyParser.urlencoded({ extended: true }));

app.post("/utilisateur", (req, res) => {
  const nom = req.body.nom;
  res.send(`Formulaire POST pour l'utilisateur ${nom}`);
});
```

## Gestion des fichiers statiques

```js
// Répertoire contenant les fichiers statiques (images, styles, scripts, etc.)
app.use(express.static("public"));
```

## Moteurs de modèles

```js
const ejs = require("ejs");

// Configuration du moteur de modèles EJS
app.set("view engine", "ejs");

// Utilisation dans une route
app.get("/accueil", (req, res) => {
  res.render("accueil", { titre: "Page d'accueil" });
});
```

## Express Router

```js
const router = express.Router();

router.get("/page1", (req, res) => {
  res.send("Page 1");
});

router.get("/page2", (req, res) => {
  res.send("Page 2");
});

// Utilisation du routeur dans l'application principale
app.use("/pages", router);
```

## Démarrage du serveur

```js
app.listen(port, () => {
  console.log(`Le serveur écoute sur le port ${port}`);
});
```
