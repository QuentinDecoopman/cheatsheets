# Conventional Commits

## 📋 Qu'est-ce que Conventional Commits ?

Conventional Commits est une convention de nommage pour les messages de commit qui fournit un ensemble de règles pour créer un historique de commits explicite et facile à comprendre. Cette convention facilite l'écriture d'outils automatisés comme la génération automatique de CHANGELOG et le versioning sémantique.

---

## 🏗️ Structure

### Format de base

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Exemple simple

```
feat: ajouter la fonctionnalité de recherche utilisateur
```

### Exemple complet

```
feat(auth): ajouter l'authentification OAuth2

Implémentation de l'authentification via Google et GitHub.
- Configuration des stratégies OAuth2
- Middleware de vérification des tokens
- Routes d'authentification

BREAKING CHANGE: L'ancien système d'authentification JWT est déprécié
Closes #123
```

---

## 📝 Types de Commits

### Types principaux

| Type       | Description                             | Bump version  |
| ---------- | --------------------------------------- | ------------- |
| `feat`     | Nouvelle fonctionnalité                 | MINOR (0.1.0) |
| `fix`      | Correction de bug                       | PATCH (0.0.1) |
| `docs`     | Documentation uniquement                | -             |
| `style`    | Formatage, virgules manquantes, etc.    | -             |
| `refactor` | Refactoring du code                     | -             |
| `perf`     | Amélioration des performances           | PATCH         |
| `test`     | Ajout ou modification de tests          | -             |
| `build`    | Modifications du système de build       | -             |
| `ci`       | Modifications de la CI/CD               | -             |
| `chore`    | Tâches diverses (MAJ dépendances, etc.) | -             |
| `revert`   | Annulation d'un commit précédent        | -             |

### Exemples par type

```bash
# feat - Nouvelle fonctionnalité
feat: ajouter la pagination sur la liste des produits
feat(api): ajouter endpoint pour la création d'utilisateur
feat(ui): implémenter le dark mode

# fix - Correction de bug
fix: corriger l'erreur 404 sur la page de profil
fix(auth): résoudre le problème de déconnexion automatique
fix(db): corriger la requête SQL qui causait des doublons

# docs - Documentation
docs: mettre à jour le README avec les nouvelles instructions
docs(api): ajouter la documentation Swagger
docs: corriger les fautes de frappe dans CONTRIBUTING.md

# style - Style de code
style: formater le code avec Prettier
style: corriger l'indentation dans auth.service.ts
style: appliquer les règles ESLint

# refactor - Refactoring
refactor: extraire la logique de validation dans un service séparé
refactor(user): simplifier la fonction getUserById
refactor: migrer de class components vers hooks

# perf - Performance
perf: optimiser la requête de recherche avec un index
perf(images): implémenter le lazy loading
perf: réduire la taille du bundle de 30%

# test - Tests
test: ajouter tests unitaires pour UserService
test(auth): ajouter tests d'intégration pour le login
test: augmenter la couverture de tests à 80%

# build - Build
build: mettre à jour webpack vers la version 5
build(deps): upgrade React vers 18.2.0
build: configurer le tree-shaking

# ci - CI/CD
ci: ajouter workflow GitHub Actions pour les tests
ci: configurer le déploiement automatique sur Vercel
ci(docker): optimiser l'image Docker

# chore - Tâches diverses
chore: mettre à jour les dépendances
chore(deps): bump axios from 0.27.0 to 1.0.0
chore: nettoyer les fichiers inutilisés

# revert - Annulation
revert: annuler "feat: ajouter la pagination"
```

---

## 🎯 Scope (Portée)

Le scope est optionnel et indique la partie du code affectée.

### Exemples de scopes

```bash
# Par module/feature
feat(auth): ajouter le login OAuth
fix(payment): corriger le calcul des taxes
docs(api): mettre à jour la documentation

# Par composant
feat(header): ajouter le menu mobile
fix(footer): corriger les liens cassés
style(button): harmoniser les couleurs

# Par couche applicative
feat(api): créer endpoint /users
fix(db): corriger la migration 001
refactor(service): simplifier UserService

# Par fichier/dossier
fix(user.controller): gérer l'erreur 404
test(auth.spec): ajouter tests manquants
```

---

## 💥 Breaking Changes

Pour indiquer un changement qui casse la compatibilité :

### Avec !

```bash
feat!: refonte complète de l'API
feat(api)!: changer le format de réponse JSON

# Bump MAJOR version (1.0.0)
```

### Avec BREAKING CHANGE dans le footer

```bash
feat(auth): migrer vers OAuth2

BREAKING CHANGE: L'authentification JWT n'est plus supportée.
Les utilisateurs doivent migrer vers OAuth2.
```

### Exemples complets

```bash
# Exemple 1
refactor!: renommer toutes les variables camelCase en snake_case

BREAKING CHANGE: Tous les noms de variables ont été modifiés.
Voir le guide de migration dans MIGRATION.md

# Exemple 2
feat(api)!: modifier le format de réponse des endpoints

Avant:
{
  "data": {...},
  "status": "success"
}

Après:
{
  "success": true,
  "result": {...}
}

BREAKING CHANGE: Format de réponse API modifié
```

---

## 📌 Références et Footers

### Référencer des issues

```bash
# Fermer une issue
fix: corriger le bug de connexion

Closes #123

# Fermer plusieurs issues
fix: résoudre les problèmes de performance

Closes #123, #456, #789

# Référencer sans fermer
feat: améliorer la recherche

Refs #123
See also #456
```

### Multiples footers

```bash
feat(api): ajouter l'endpoint de recherche avancée

Implémente la recherche par nom, email et date.

Reviewed-by: John Doe
Refs #123
Closes #456
BREAKING CHANGE: L'ancien endpoint /search est déprécié
```

---

## ✅ Bonnes Pratiques

### 1. Description claire et concise

```bash
# ✅ Bon
feat: ajouter la validation des emails
fix: corriger l'affichage du prix avec décimales
docs: mettre à jour le guide d'installation

# ❌ Éviter
feat: stuff
fix: fix bug
docs: update
```

### 2. Utiliser l'impératif

```bash
# ✅ Bon
feat: ajouter la fonctionnalité X
fix: corriger le problème Y
docs: mettre à jour le README

# ❌ Éviter
feat: ajouté la fonctionnalité X
fix: corrigé le problème Y
docs: mise à jour du README
```

### 3. Description en minuscules

```bash
# ✅ Bon
feat: ajouter le dark mode

# ❌ Éviter
feat: Ajouter le dark mode
feat: Ajouter Le Dark Mode
```

### 4. Pas de point final

```bash
# ✅ Bon
feat: ajouter la pagination

# ❌ Éviter
feat: ajouter la pagination.
```

### 5. Corps du message

```bash
# ✅ Bon - explique le pourquoi et le comment
fix: corriger la fuite mémoire dans le cache

Le cache gardait indéfiniment les anciennes entrées,
causant une augmentation progressive de l'utilisation mémoire.

Solution: Implémenter un TTL de 1 heure et un nettoyage automatique.

# ❌ Éviter - trop vague
fix: corriger un bug
```

### 6. Un commit = une modification logique

```bash
# ✅ Bon
feat: ajouter le bouton de partage
style: formater le code selon Prettier

# ❌ Éviter
feat: ajouter bouton de partage, formater le code, mettre à jour README
```

---

## 🛠️ Outils

### Commitizen

```bash
# Installation
npm install -D commitizen cz-conventional-changelog

# Configuration dans package.json
{
  "scripts": {
    "commit": "cz"
  },
  "config": {
    "commitizen": {
      "path": "cz-conventional-changelog"
    }
  }
}

# Utilisation
npm run commit
# Suit un questionnaire interactif
```

### Commitlint

```bash
# Installation
npm install -D @commitlint/{cli,config-conventional}

# Configuration .commitlintrc.json
{
  "extends": ["@commitlint/config-conventional"],
  "rules": {
    "type-enum": [
      2,
      "always",
      ["feat", "fix", "docs", "style", "refactor", "perf", "test", "build", "ci", "chore", "revert"]
    ],
    "subject-case": [2, "always", "lower-case"],
    "subject-full-stop": [2, "never", "."]
  }
}

# Husky pour valider avant commit
npm install -D husky

# .husky/commit-msg
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx --no -- commitlint --edit "$1"
```

### Standard Version (Versioning automatique)

```bash
# Installation
npm install -D standard-version

# package.json
{
  "scripts": {
    "release": "standard-version"
  }
}

# Utilisation
npm run release              # Auto-détecte la version
npm run release -- --release-as minor
npm run release -- --release-as major
npm run release -- --prerelease alpha

# Génère automatiquement:
# - CHANGELOG.md
# - Tag git
# - Bump de version dans package.json
```

### Semantic Release

```bash
# Installation
npm install -D semantic-release

# .releaserc.json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/npm",
    "@semantic-release/github",
    "@semantic-release/git"
  ]
}

# CI/CD (GitHub Actions)
# .github/workflows/release.yml
name: Release
on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npx semantic-release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## 📊 Génération de Changelog

### Avec Conventional Changelog

```bash
# Installation
npm install -D conventional-changelog-cli

# Générer CHANGELOG.md
npx conventional-changelog -p angular -i CHANGELOG.md -s

# Première génération (tout l'historique)
npx conventional-changelog -p angular -i CHANGELOG.md -s -r 0

# package.json
{
  "scripts": {
    "changelog": "conventional-changelog -p angular -i CHANGELOG.md -s"
  }
}
```

### Format du CHANGELOG généré

```markdown
# Changelog

## [1.2.0] - 2024-01-20

### Features

- **auth:** ajouter l'authentification OAuth2 ([a1b2c3d](link))
- **ui:** implémenter le dark mode ([e4f5g6h](link))

### Bug Fixes

- **api:** corriger l'erreur 500 sur /users ([i7j8k9l](link))
- corriger la fuite mémoire dans le cache ([m0n1o2p](link))

### Performance Improvements

- **images:** implémenter le lazy loading ([q3r4s5t](link))

### BREAKING CHANGES

- **api:** Format de réponse API modifié
```

---

## 🎨 Templates de Commit

### Nouvelle fonctionnalité

```bash
feat(scope): ajouter [fonctionnalité]

Description détaillée de ce qui a été ajouté et pourquoi.

Closes #123
```

### Correction de bug

```bash
fix(scope): corriger [problème]

Description du bug et de la solution appliquée.

Fixes #456
```

### Refactoring

```bash
refactor(scope): améliorer [composant]

Raisons du refactoring et bénéfices attendus.
```

### Documentation

```bash
docs(scope): mettre à jour [documentation]

Détails des modifications de documentation.
```

---

## 🔗 Workflow Complet

### 1. Développement

```bash
# Créer une branche
git checkout -b feat/user-authentication

# Développer et commiter
git add .
npm run commit  # Avec Commitizen

# Exemple de commits
feat(auth): ajouter le composant LoginForm
test(auth): ajouter tests pour LoginForm
docs(auth): documenter le processus d'authentification
```

### 2. Pull Request

```markdown
## Description

Implémentation de l'authentification utilisateur

## Commits

- feat(auth): ajouter le composant LoginForm
- feat(auth): ajouter le service d'authentification
- test(auth): ajouter tests unitaires
- docs(auth): mettre à jour la documentation

## Breaking Changes

Aucun

## Issues

Closes #123
```

### 3. Release

```bash
# Merge dans main
git checkout main
git merge feat/user-authentication

# Générer version et changelog
npm run release

# Push
git push --follow-tags origin main

# Semantic Release automatique via CI/CD
```

---

## 📚 Ressources

- [Conventional Commits](https://www.conventionalcommits.org/)
- [Angular Commit Guidelines](https://github.com/angular/angular/blob/main/CONTRIBUTING.md#commit)
- [Commitizen](https://github.com/commitizen/cz-cli)
- [Commitlint](https://commitlint.js.org/)
- [Standard Version](https://github.com/conventional-changelog/standard-version)
- [Semantic Release](https://semantic-release.gitbook.io/)
