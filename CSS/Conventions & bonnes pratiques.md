# Conventions & Bonnes Pratiques CSS

## 📋 Conventions de Nommage

### BEM (Block Element Modifier)

```css
/* Block */
.card {
}

/* Element */
.card__title {
}
.card__content {
}
.card__button {
}

/* Modifier */
.card--featured {
}
.card__button--primary {
}
.card__button--disabled {
}

/* Exemple complet */
.user-card {
}
.user-card__header {
}
.user-card__avatar {
}
.user-card__name {
}
.user-card__name--highlighted {
}
```

### Kebab-case pour les classes

```css
/* ✅ Bon */
.user-profile {
}
.main-navigation {
}
.search-input {
}

/* ❌ Éviter */
.userProfile {
}
.MainNavigation {
}
.search_input {
}
```

### Préfixes pour les utilitaires

```css
/* Layout */
.l-container {
}
.l-grid {
}
.l-flex {
}

/* Components */
.c-button {
}
.c-card {
}

/* Utilities */
.u-text-center {
}
.u-margin-top {
}
.u-hidden {
}

/* States */
.is-active {
}
.is-disabled {
}
.is-loading {
}

/* JavaScript hooks */
.js-toggle {
}
.js-dropdown {
}
```

---

## 🏗️ Structure et Organisation

### Organisation des fichiers

```
styles/
├── abstracts/
│   ├── _variables.css
│   ├── _mixins.css
│   └── _functions.css
├── base/
│   ├── _reset.css
│   ├── _typography.css
│   └── _global.css
├── components/
│   ├── _button.css
│   ├── _card.css
│   └── _form.css
├── layout/
│   ├── _header.css
│   ├── _footer.css
│   └── _grid.css
├── pages/
│   ├── _home.css
│   └── _about.css
├── themes/
│   └── _dark.css
└── main.css
```

### Ordre des propriétés

```css
.element {
  /* 1. Positionnement */
  position: absolute;
  top: 0;
  right: 0;
  z-index: 10;

  /* 2. Box Model */
  display: flex;
  width: 100%;
  height: 50px;
  margin: 10px;
  padding: 20px;

  /* 3. Typographie */
  font-family: Arial, sans-serif;
  font-size: 16px;
  line-height: 1.5;
  color: #333;
  text-align: center;

  /* 4. Visuel */
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);

  /* 5. Animations */
  transition: all 0.3s ease;
  transform: translateX(0);

  /* 6. Divers */
  cursor: pointer;
  opacity: 1;
}
```

---

## ✅ Bonnes Pratiques

### 1. Mobile First

```css
/* ✅ Bon - Mobile first */
.container {
  width: 100%;
  padding: 10px;
}

@media (min-width: 768px) {
  .container {
    width: 750px;
    padding: 20px;
  }
}

@media (min-width: 1024px) {
  .container {
    width: 970px;
  }
}
```

### 2. Variables CSS (Custom Properties)

```css
:root {
  /* Colors */
  --color-primary: #007bff;
  --color-secondary: #6c757d;
  --color-success: #28a745;
  --color-danger: #dc3545;

  /* Spacing */
  --spacing-xs: 0.25rem;
  --spacing-sm: 0.5rem;
  --spacing-md: 1rem;
  --spacing-lg: 2rem;
  --spacing-xl: 4rem;

  /* Typography */
  --font-family-base: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  --font-size-base: 16px;
  --line-height-base: 1.5;

  /* Breakpoints */
  --breakpoint-sm: 576px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 992px;
  --breakpoint-xl: 1200px;
}

/* Utilisation */
.button {
  background-color: var(--color-primary);
  padding: var(--spacing-md) var(--spacing-lg);
  font-family: var(--font-family-base);
}
```

### 3. Éviter les sélecteurs trop spécifiques

```css
/* ❌ Trop spécifique */
body div.container ul li a.link {
}

/* ✅ Bon - classe directe */
.nav-link {
}

/* ❌ IDs pour le style */
#header {
  background: blue;
}

/* ✅ Classes */
.header {
  background: blue;
}
```

### 4. Utiliser rem et em

```css
/* ✅ Bon - unités relatives */
.text {
  font-size: 1rem; /* Relatif à la racine */
  padding: 0.5em; /* Relatif au font-size de l'élément */
  margin-bottom: 2rem;
}

/* Pour les media queries */
@media (min-width: 48em) {
  /* 768px si base = 16px */
  .container {
    max-width: 60rem;
  }
}
```

### 5. Box-sizing border-box

```css
/* ✅ Toujours utiliser */
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

---

## 🎨 Layout Moderne

### Flexbox

```css
/* Container flex */
.flex-container {
  display: flex;
  flex-direction: row; /* row | column */
  justify-content: space-between; /* start | center | end | space-between | space-around */
  align-items: center; /* start | center | end | stretch */
  flex-wrap: wrap; /* nowrap | wrap */
  gap: 1rem;
}

/* Items flex */
.flex-item {
  flex: 1; /* flex-grow | flex-shrink | flex-basis */
  flex: 0 0 auto; /* Ne grandit pas, ne rétrécit pas */
  flex-basis: 50%;
}

/* Patterns courants */
.space-between {
  display: flex;
  justify-content: space-between;
}

.center {
  display: flex;
  justify-content: center;
  align-items: center;
}

.vertical-center {
  display: flex;
  align-items: center;
}
```

### Grid

```css
/* Grid container */
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: auto;
  gap: 1rem;
  grid-auto-flow: dense;
}

/* Responsive grid */
.responsive-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
}

/* Grid areas */
.layout {
  display: grid;
  grid-template-areas:
    "header header header"
    "sidebar main main"
    "footer footer footer";
  grid-template-columns: 200px 1fr 1fr;
  gap: 1rem;
}

.header {
  grid-area: header;
}
.sidebar {
  grid-area: sidebar;
}
.main {
  grid-area: main;
}
.footer {
  grid-area: footer;
}
```

---

## 🎯 Sélecteurs et Pseudo-classes

### Pseudo-classes utiles

```css
/* États */
a:hover {
  color: blue;
}
a:active {
  color: red;
}
a:focus {
  outline: 2px solid blue;
}
a:visited {
  color: purple;
}

/* Formulaires */
input:focus {
  border-color: blue;
}
input:disabled {
  opacity: 0.5;
}
input:checked + label {
  font-weight: bold;
}
input:valid {
  border-color: green;
}
input:invalid {
  border-color: red;
}

/* Structurels */
li:first-child {
  margin-top: 0;
}
li:last-child {
  margin-bottom: 0;
}
li:nth-child(odd) {
  background: #f5f5f5;
}
li:nth-child(2n) {
  background: #eee;
}
li:nth-child(3n + 1) {
  /* 1, 4, 7, 10... */
}

/* Vides */
p:empty {
  display: none;
}
div:not(.active) {
  opacity: 0.5;
}
```

### Pseudo-éléments

```css
/* ::before et ::after */
.icon::before {
  content: "→";
  margin-right: 0.5rem;
}

.clearfix::after {
  content: "";
  display: table;
  clear: both;
}

/* Autres */
p::first-letter {
  font-size: 2em;
  font-weight: bold;
}

p::first-line {
  font-variant: small-caps;
}

::selection {
  background-color: yellow;
  color: black;
}

::placeholder {
  color: #999;
  opacity: 1;
}
```

---

## 🚀 Performance

### 1. Éviter les propriétés coûteuses

```css
/* ❌ Éviter si possible (reflow/repaint) */
.element {
  width: 100px;
  height: 100px;
  margin-left: 10px;
}

/* ✅ Préférer transform (GPU) */
.element {
  transform: translateX(10px);
}
```

### 2. Will-change pour animations

```css
/* ✅ Optimiser les animations */
.animated {
  will-change: transform, opacity;
  transition:
    transform 0.3s,
    opacity 0.3s;
}

/* ❌ Ne pas abuser */
* {
  will-change: transform; /* Mauvais */
}
```

### 3. Contain pour l'isolation

```css
.widget {
  contain: layout style paint;
}

.independent-section {
  contain: content;
}
```

### 4. Minification et optimisation

```css
/* ✅ En production, minifier */
/* Utiliser des outils comme cssnano, clean-css */
```

---

## 🎨 Animations et Transitions

### Transitions

```css
/* Transition de base */
.button {
  background-color: blue;
  transition: background-color 0.3s ease;
}

.button:hover {
  background-color: darkblue;
}

/* Multiple transitions */
.element {
  transition:
    transform 0.3s ease-in-out,
    opacity 0.2s linear,
    background-color 0.3s ease;
}

/* Timing functions */
.ease {
  transition-timing-function: ease;
}
.linear {
  transition-timing-function: linear;
}
.ease-in {
  transition-timing-function: ease-in;
}
.ease-out {
  transition-timing-function: ease-out;
}
.cubic {
  transition-timing-function: cubic-bezier(0.68, -0.55, 0.265, 1.55);
}
```

### Animations

```css
/* Définir l'animation */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Appliquer l'animation */
.fade-in {
  animation: fadeIn 0.5s ease-out;
}

/* Animation complète */
.bounce {
  animation-name: bounce;
  animation-duration: 1s;
  animation-timing-function: ease-in-out;
  animation-delay: 0.5s;
  animation-iteration-count: infinite;
  animation-direction: alternate;
  animation-fill-mode: both;
}

/* Raccourci */
.bounce {
  animation: bounce 1s ease-in-out 0.5s infinite alternate both;
}

/* Keyframes avec étapes */
@keyframes colorChange {
  0% {
    background-color: red;
  }
  25% {
    background-color: yellow;
  }
  50% {
    background-color: blue;
  }
  75% {
    background-color: green;
  }
  100% {
    background-color: red;
  }
}
```

---

## 🔒 Accessibilité

### Focus visible

```css
/* ✅ Toujours visible pour le clavier */
button:focus,
a:focus {
  outline: 2px solid blue;
  outline-offset: 2px;
}

/* Focus-visible (moderne) */
button:focus-visible {
  outline: 2px solid blue;
}

button:focus:not(:focus-visible) {
  outline: none;
}
```

### Contraste et lisibilité

```css
/* ✅ Bon contraste */
.text {
  color: #333;
  background-color: #fff;
}

/* ✅ Taille de texte minimum */
body {
  font-size: 16px;
}

/* ✅ Line-height pour lisibilité */
p {
  line-height: 1.6;
}
```

### Screen reader only

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```

---

## 🌙 Dark Mode

### Prefers-color-scheme

```css
:root {
  --bg-color: #fff;
  --text-color: #333;
}

@media (prefers-color-scheme: dark) {
  :root {
    --bg-color: #1a1a1a;
    --text-color: #f0f0f0;
  }
}

body {
  background-color: var(--bg-color);
  color: var(--text-color);
}

/* Avec classe */
[data-theme="dark"] {
  --bg-color: #1a1a1a;
  --text-color: #f0f0f0;
}
```

---

## 📱 Responsive Design

### Media Queries

```css
/* Mobile first */
.container {
  padding: 1rem;
}

/* Tablet */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    padding: 3rem;
    max-width: 1200px;
    margin: 0 auto;
  }
}

/* Print */
@media print {
  .no-print {
    display: none;
  }
}

/* Orientation */
@media (orientation: landscape) {
  /* Styles paysage */
}

/* Reduced motion */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Container Queries (moderne)

```css
@container (min-width: 400px) {
  .card {
    display: grid;
    grid-template-columns: 1fr 2fr;
  }
}
```

---

## 🛠️ Outils et Méthodologies

### Reset CSS

```css
/* Modern CSS Reset */
*,
*::before,
*::after {
  box-sizing: border-box;
}

* {
  margin: 0;
  padding: 0;
}

html {
  font-size: 16px;
}

body {
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

img,
picture,
video,
canvas,
svg {
  display: block;
  max-width: 100%;
}

input,
button,
textarea,
select {
  font: inherit;
}

p,
h1,
h2,
h3,
h4,
h5,
h6 {
  overflow-wrap: break-word;
}
```

### Utility Classes

```css
/* Spacing */
.mt-1 {
  margin-top: 0.25rem;
}
.mt-2 {
  margin-top: 0.5rem;
}
.mt-3 {
  margin-top: 1rem;
}

.p-1 {
  padding: 0.25rem;
}
.p-2 {
  padding: 0.5rem;
}

/* Display */
.d-none {
  display: none;
}
.d-block {
  display: block;
}
.d-flex {
  display: flex;
}
.d-grid {
  display: grid;
}

/* Text */
.text-center {
  text-align: center;
}
.text-left {
  text-align: left;
}
.text-right {
  text-align: right;
}

/* Colors */
.text-primary {
  color: var(--color-primary);
}
.bg-primary {
  background-color: var(--color-primary);
}
```

---

## 📚 Ressources

- [MDN CSS Reference](https://developer.mozilla.org/fr/docs/Web/CSS)
- [CSS-Tricks](https://css-tricks.com/)
- [Can I Use](https://caniuse.com/)
- [Modern CSS Solutions](https://moderncss.dev/)
- [CSS Guidelines](https://cssguidelin.es/)
