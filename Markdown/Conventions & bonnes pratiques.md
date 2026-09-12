# Conventions & Bonnes Pratiques Markdown

## 📋 Syntaxe de Base

### Titres

```markdown
# Titre 1 (H1)

## Titre 2 (H2)

### Titre 3 (H3)

#### Titre 4 (H4)

##### Titre 5 (H5)

###### Titre 6 (H6)

# ✅ Bon - un seul H1 par document

# Mon Document

## Section 1

### Sous-section 1.1

## Section 2

# ❌ Éviter - plusieurs H1

# Titre 1

# Autre Titre 1
```

### Emphases

```markdown
_italique_ ou _italique_
**gras** ou **gras**
**_gras et italique_** ou **_gras et italique_**
~~barré~~

# ✅ Bon - cohérent

**Important:** utilisez _toujours_ la même syntaxe

# ❌ Éviter - mélanger

**Important:** utilisez _différentes_ syntaxes
```

### Paragraphes et Sauts de Ligne

```markdown
# ✅ Bon - ligne vide entre paragraphes

Ceci est un paragraphe.

Ceci est un autre paragraphe.

# Saut de ligne (2 espaces en fin de ligne)

Première ligne  
Deuxième ligne

# Ou balise HTML

Première ligne<br>
Deuxième ligne

# ❌ Éviter

Pas de ligne vide entre
deux paragraphes.
```

---

## 📝 Listes

### Listes non ordonnées

```markdown
# ✅ Bon - symboles cohérents

- Item 1
- Item 2
  - Sous-item 2.1
  - Sous-item 2.2
- Item 3

# Alternative avec \*

- Item 1
- Item 2

# Alternative avec +

- Item 1
- Item 2

# ❌ Éviter - mélanger les symboles

- Item 1

* Item 2

- Item 3
```

### Listes ordonnées

```markdown
# ✅ Bon

1. Premier item
2. Deuxième item
3. Troisième item
   1. Sous-item 3.1
   2. Sous-item 3.2

# Alternative (numérotation automatique)

1. Premier item
1. Deuxième item
1. Troisième item

# Listes imbriquées (3 espaces d'indentation)

1. Item 1
   - Sous-item
   - Autre sous-item
2. Item 2
```

### Listes de tâches

```markdown
- [x] Tâche complétée
- [ ] Tâche à faire
- [ ] Autre tâche
  - [x] Sous-tâche complétée
  - [ ] Sous-tâche à faire
```

---

## 🔗 Liens

### Liens inline

```markdown
[Texte du lien](https://example.com)
[Lien avec titre](https://example.com "Titre au survol")

# Lien vers section

[Aller à la section](#nom-de-la-section)

# Email

[contact@example.com](mailto:contact@example.com)
```

### Liens de référence

```markdown
Ceci est un [lien de référence][ref1] et un [autre lien][ref2].

[ref1]: https://example.com
[ref2]: https://example.org "Avec un titre"

# Ou référence implicite

[Google][]

[Google]: https://google.com
```

### URLs automatiques

```markdown
<https://example.com>
<contact@example.com>
```

---

## 🖼️ Images

### Syntaxe de base

```markdown
![Texte alternatif](path/to/image.jpg)
![Image avec titre](image.jpg "Titre de l'image")

# Image avec lien

[![Image cliquable](image.jpg)](https://example.com)

# Image de référence

![Alt text][logo]

[logo]: images/logo.png "Logo"
```

### Dimensionnement (HTML)

```markdown
<img src="image.jpg" alt="Description" width="300">
<img src="image.jpg" alt="Description" width="50%">
```

---

## 💻 Code

### Code inline

```markdown
Utilisez la fonction `console.log()` pour afficher.
Le fichier `config.js` contient les paramètres.
```

### Blocs de code

````markdown
```javascript
function hello() {
  console.log("Hello World!");
}
```

```python
def hello():
    print("Hello World!")
```

```bash
npm install
npm start
```

# Sans coloration syntaxique

```
Code brut
```
````

### Code avec numéros de ligne (GitHub)

````markdown
```javascript showLineNumbers
function add(a, b) {
  return a + b;
}
```
````

### Mettre en évidence des lignes

````markdown
```javascript {2,4}
function example() {
  const x = 1; // Ligne mise en évidence
  const y = 2;
  return x + y; // Ligne mise en évidence
}
```
````

---

## 📊 Tableaux

### Syntaxe de base

```markdown
| Colonne 1 | Colonne 2 | Colonne 3 |
| --------- | --------- | --------- |
| Cellule 1 | Cellule 2 | Cellule 3 |
| Cellule 4 | Cellule 5 | Cellule 6 |

# Alignement

| Gauche | Centre | Droite |
| :----- | :----: | -----: |
| Texte  | Texte  |  Texte |
| 1      |   2    |      3 |

# ✅ Bon - aligné pour lisibilité

| Nom   | Âge | Ville |
| ----- | --- | ----- |
| Alice | 30  | Paris |
| Bob   | 25  | Lyon  |

# ❌ Acceptable mais moins lisible

| Nom   | Âge | Ville |
| ----- | --- | ----- |
| Alice | 30  | Paris |
```

---

## 📌 Citations

### Blockquotes

```markdown
> Ceci est une citation.

> Citation sur
> plusieurs lignes.

> Citation imbriquée
>
> > Niveau 2
> >
> > > Niveau 3

> **Note:** Les citations peuvent contenir du _formatage_.
>
> - Et des listes
> - Également
```

---

## ➖ Lignes Horizontales

```markdown
---

***

___

# ✅ Recommandation
---
```

---

## ✅ Conventions et Bonnes Pratiques

### 1. Structure du document

```markdown
# ✅ Bon - structure claire

# Titre Principal

Brève introduction du document.

## Table des Matières

- [Section 1](#section-1)
- [Section 2](#section-2)

## Section 1

Contenu...

## Section 2

Contenu...

## Références

- [Lien 1](https://example.com)
```

### 2. Nommage des fichiers

```markdown
# ✅ Bon

README.md
CONTRIBUTING.md
docs/api-reference.md
guides/getting-started.md

# ❌ Éviter

readme.MD
Read Me.md
docs/API Reference.md
```

### 3. Liens relatifs

```markdown
# ✅ Bon - chemins relatifs

[Documentation](./docs/README.md)
[API](../api/reference.md)

# ❌ Éviter - chemins absolus

[Documentation](https://example.com/docs)
```

### 4. Ancres de section

```markdown
# ✅ Bon - ancres automatiques (kebab-case)

## Ma Super Section

[Lien](#ma-super-section)

## Section avec `code`

[Lien](#section-avec-code)

## Section avec émoji 🚀

[Lien](#section-avec-émoji-)
```

### 5. Langue et cohérence

```markdown
# ✅ Bon - cohérent

Utilisez toujours la même langue dans un document.
Restez cohérent avec les conventions choisies.

# ❌ Éviter

Mixing languages dans le same document.
```

### 6. Espacement

````markdown
# ✅ Bon - ligne vide avant/après les éléments de bloc

Paragraphe de texte.

```javascript
const code = true;
```
````

Autre paragraphe.

# ❌ Éviter

Paragraphe de texte.

```javascript
const code = true;
```

Suite du texte.

````

---

## 🎨 Extensions Markdown

### GitHub Flavored Markdown (GFM)

#### Listes de tâches
```markdown
- [x] Tâche complétée
- [ ] Tâche en cours
- [ ] Tâche à faire
````

#### Tableaux

```markdown
| Colonne 1 | Colonne 2 |
| --------- | --------- |
| Donnée    | Donnée    |
```

#### Emojis

```markdown
:smile: :rocket: :heart:
😀 🚀 ❤️
```

#### Mentions

```markdown
@username
#123 (référence à une issue)
```

#### Code avec diff

````markdown
```diff
- ligne supprimée
+ ligne ajoutée
  ligne normale
```
````

### MDX (Markdown + JSX)

```markdown
import { Chart } from './Chart'

# Mon Document

<Chart data={myData} />

export const metadata = {
title: 'Mon Document'
}
```

---

## 📐 Templates Utiles

### README.md

````markdown
# Nom du Projet

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Build Status](https://travis-ci.org/user/repo.svg)](https://travis-ci.org/user/repo)

Brève description du projet.

## 📋 Prérequis

- Node.js >= 14
- npm >= 6

## 🚀 Installation

```bash
npm install
npm start
```
````

## 📖 Documentation

Consultez la [documentation complète](./docs/README.md).

## 🤝 Contribution

Les contributions sont les bienvenues ! Voir [CONTRIBUTING.md](CONTRIBUTING.md).

## 📄 Licence

Ce projet est sous licence MIT. Voir [LICENSE](LICENSE).

````

### CONTRIBUTING.md
```markdown
# Guide de Contribution

## 🐛 Signaler un Bug

Utilisez les [issues GitHub](https://github.com/user/repo/issues).

## 💡 Proposer une Fonctionnalité

1. Vérifier qu'elle n'existe pas déjà
2. Créer une issue avec le label `enhancement`
3. Décrire clairement le besoin

## 🔧 Soumettre une Pull Request

1. Fork le projet
2. Créer une branche (`git checkout -b feature/AmazingFeature`)
3. Commit (`git commit -m 'Add AmazingFeature'`)
4. Push (`git push origin feature/AmazingFeature`)
5. Ouvrir une Pull Request

## 📝 Conventions de Code

- Suivre le style du projet
- Ajouter des tests
- Mettre à jour la documentation
````

### CHANGELOG.md

```markdown
# Changelog

## [1.2.0] - 2024-01-20

### Added

- Nouvelle fonctionnalité X
- Support pour Y

### Changed

- Amélioration de la performance de Z

### Fixed

- Correction du bug #123

### Deprecated

- Ancienne API sera supprimée en v2

## [1.1.0] - 2023-12-15

### Added

- Fonctionnalité initiale
```

---

## 🛠️ Outils

### Linters

```bash
# markdownlint
npm install -g markdownlint-cli
markdownlint "**/*.md"

# Configuration .markdownlint.json
{
  "default": true,
  "MD013": false,  # Longueur de ligne
  "MD033": false   # HTML inline
}
```

### Formatters

```bash
# Prettier
npm install -D prettier
npx prettier --write "**/*.md"

# Configuration .prettierrc
{
  "proseWrap": "always",
  "printWidth": 80
}
```

### Éditeurs recommandés

```markdown
- VS Code avec extensions:
  - Markdown All in One
  - Markdown Preview Enhanced
  - markdownlint
- Typora
- Obsidian
- Notion
```

---

## 📚 Ressources

- [CommonMark Spec](https://commonmark.org/)
- [GitHub Flavored Markdown](https://github.github.com/gfm/)
- [Markdown Guide](https://www.markdownguide.org/)
- [Dillinger (Online Editor)](https://dillinger.io/)
