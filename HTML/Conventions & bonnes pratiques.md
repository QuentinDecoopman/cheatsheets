# Conventions & Bonnes Pratiques HTML

## 📋 Conventions de Nommage

### Éléments et Attributs

- **Lowercase** pour tous les éléments et attributs

```html
<!-- ✅ Bon -->
<div class="container">
  <p id="intro">Text</p>
</div>

<!-- ❌ Éviter -->
<div class="container">
  <p id="intro">Text</p>
</div>
```

### Classes et IDs

- **kebab-case** pour les classes
- **IDs** uniques et descriptifs

```html
<!-- ✅ Bon -->
<div class="user-profile">
  <h1 id="main-title">Title</h1>
  <p class="user-description">Description</p>
</div>

<!-- ❌ Éviter -->
<div class="userProfile">
  <h1 id="title1">Title</h1>
</div>
```

---

## 🏗️ Structure Sémantique

### HTML5 Sémantique

```html
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Page Title</title>
  </head>
  <body>
    <!-- ✅ Structure sémantique -->
    <header>
      <nav>
        <ul>
          <li><a href="/">Accueil</a></li>
          <li><a href="/about">À propos</a></li>
        </ul>
      </nav>
    </header>

    <main>
      <article>
        <header>
          <h1>Titre de l'article</h1>
          <time datetime="2024-01-01">1er janvier 2024</time>
        </header>

        <section>
          <h2>Section 1</h2>
          <p>Contenu...</p>
        </section>

        <footer>
          <p>Auteur: John Doe</p>
        </footer>
      </article>

      <aside>
        <h2>Articles liés</h2>
        <ul>
          <li><a href="#">Article 1</a></li>
        </ul>
      </aside>
    </main>

    <footer>
      <p>&copy; 2024 Mon Site</p>
    </footer>
  </body>
</html>
```

### Balises Sémantiques

```html
<!-- Navigation -->
<nav>
  <ul>
    <li><a href="/">Accueil</a></li>
  </ul>
</nav>

<!-- Article -->
<article>
  <h1>Titre</h1>
  <p>Contenu autonome...</p>
</article>

<!-- Section -->
<section>
  <h2>Section thématique</h2>
  <p>Contenu groupé...</p>
</section>

<!-- Aside -->
<aside>
  <p>Contenu complémentaire</p>
</aside>

<!-- Figure et Caption -->
<figure>
  <img src="image.jpg" alt="Description" />
  <figcaption>Légende de l'image</figcaption>
</figure>

<!-- Détails -->
<details>
  <summary>Cliquez pour plus d'infos</summary>
  <p>Contenu caché par défaut</p>
</details>

<!-- Mark -->
<p>Texte avec <mark>mise en évidence</mark></p>

<!-- Time -->
<time datetime="2024-01-01">1er janvier 2024</time>
```

---

## ✅ Bonnes Pratiques

### 1. DOCTYPE et Lang

```html
<!-- ✅ Toujours déclarer DOCTYPE -->
<!DOCTYPE html>
<html lang="fr"></html>
```

### 2. Meta Tags Essentiels

```html
<head>
  <!-- Charset -->
  <meta charset="UTF-8" />

  <!-- Viewport pour responsive -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <!-- Description SEO -->
  <meta
    name="description"
    content="Description de la page (150-160 caractères)"
  />

  <!-- Keywords (moins important aujourd'hui) -->
  <meta name="keywords" content="mot-clé1, mot-clé2" />

  <!-- Auteur -->
  <meta name="author" content="John Doe" />

  <!-- Open Graph (réseaux sociaux) -->
  <meta property="og:title" content="Titre de la page" />
  <meta property="og:description" content="Description" />
  <meta property="og:image" content="https://example.com/image.jpg" />
  <meta property="og:url" content="https://example.com" />

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content="Titre" />

  <!-- Favicon -->
  <link rel="icon" type="image/x-icon" href="/favicon.ico" />
  <link rel="apple-touch-icon" href="/apple-touch-icon.png" />

  <title>Titre de la page | Nom du site</title>
</head>
```

### 3. Images Accessibles

```html
<!-- ✅ Toujours inclure alt -->
<img src="photo.jpg" alt="Description précise de l'image" />

<!-- Image décorative -->
<img src="decoration.jpg" alt="" role="presentation" />

<!-- Responsive images -->
<img
  src="image-small.jpg"
  srcset="image-small.jpg 400w, image-medium.jpg 800w, image-large.jpg 1200w"
  sizes="(max-width: 400px) 400px,
         (max-width: 800px) 800px,
         1200px"
  alt="Description"
/>

<!-- Picture pour formats multiples -->
<picture>
  <source srcset="image.webp" type="image/webp" />
  <source srcset="image.jpg" type="image/jpeg" />
  <img src="image.jpg" alt="Description" />
</picture>

<!-- Loading lazy -->
<img src="image.jpg" alt="Description" loading="lazy" />
```

### 4. Liens

```html
<!-- ✅ Liens descriptifs -->
<a href="/contact">Contactez-nous</a>

<!-- ❌ Éviter -->
<a href="/contact">Cliquez ici</a>

<!-- Lien externe -->
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
  Site externe
</a>

<!-- Lien email -->
<a href="mailto:contact@example.com">contact@example.com</a>

<!-- Lien téléphone -->
<a href="tel:+33123456789">01 23 45 67 89</a>

<!-- Ancre -->
<a href="#section-2">Aller à la section 2</a>
<section id="section-2">...</section>
```

### 5. Formulaires Accessibles

```html
<form action="/submit" method="POST">
  <!-- ✅ Label associé -->
  <div class="form-group">
    <label for="username">Nom d'utilisateur :</label>
    <input
      type="text"
      id="username"
      name="username"
      required
      aria-describedby="username-help"
    />
    <small id="username-help"> Au moins 3 caractères </small>
  </div>

  <!-- Email -->
  <div class="form-group">
    <label for="email">Email :</label>
    <input
      type="email"
      id="email"
      name="email"
      placeholder="email@example.com"
      required
    />
  </div>

  <!-- Password -->
  <div class="form-group">
    <label for="password">Mot de passe :</label>
    <input
      type="password"
      id="password"
      name="password"
      minlength="8"
      required
    />
  </div>

  <!-- Select -->
  <div class="form-group">
    <label for="country">Pays :</label>
    <select id="country" name="country" required>
      <option value="">Sélectionner...</option>
      <option value="fr">France</option>
      <option value="be">Belgique</option>
    </select>
  </div>

  <!-- Radio -->
  <fieldset>
    <legend>Genre :</legend>
    <label>
      <input type="radio" name="gender" value="m" required />
      Homme
    </label>
    <label>
      <input type="radio" name="gender" value="f" />
      Femme
    </label>
  </fieldset>

  <!-- Checkbox -->
  <div class="form-group">
    <label>
      <input type="checkbox" name="terms" required />
      J'accepte les conditions
    </label>
  </div>

  <!-- Textarea -->
  <div class="form-group">
    <label for="message">Message :</label>
    <textarea id="message" name="message" rows="5" maxlength="500"></textarea>
  </div>

  <!-- Submit -->
  <button type="submit">Envoyer</button>
  <button type="reset">Réinitialiser</button>
</form>
```

### 6. Tableaux Accessibles

```html
<!-- ✅ Tableau bien structuré -->
<table>
  <caption>
    Liste des utilisateurs
  </caption>
  <thead>
    <tr>
      <th scope="col">Nom</th>
      <th scope="col">Email</th>
      <th scope="col">Rôle</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>John Doe</td>
      <td>john@example.com</td>
      <td>Admin</td>
    </tr>
    <tr>
      <td>Jane Smith</td>
      <td>jane@example.com</td>
      <td>User</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td colspan="3">Total: 2 utilisateurs</td>
    </tr>
  </tfoot>
</table>
```

---

## 🎯 Accessibilité (ARIA)

### Rôles ARIA

```html
<!-- Navigation -->
<nav role="navigation">
  <ul>
    ...
  </ul>
</nav>

<!-- Banner -->
<header role="banner">
  <h1>Site Title</h1>
</header>

<!-- Main content -->
<main role="main">
  <article>...</article>
</main>

<!-- Search -->
<div role="search">
  <input type="search" />
</div>

<!-- Alert -->
<div role="alert" aria-live="assertive">Message d'erreur</div>
```

### Attributs ARIA

```html
<!-- Label alternatif -->
<button aria-label="Fermer la modale">×</button>

<!-- Description -->
<input type="text" aria-describedby="help-text" />
<span id="help-text">Texte d'aide</span>

<!-- États -->
<button aria-pressed="true">Actif</button>
<div aria-expanded="false">Contenu</div>
<div aria-hidden="true">Caché pour lecteurs d'écran</div>

<!-- Live regions -->
<div aria-live="polite">Notification</div>

<!-- Required -->
<input type="text" aria-required="true" />

<!-- Invalid -->
<input type="email" aria-invalid="true" aria-errormessage="email-error" />
<span id="email-error">Email invalide</span>
```

---

## 🚀 Performance

### 1. Chargement des ressources

```html
<head>
  <!-- ✅ CSS dans head -->
  <link rel="stylesheet" href="styles.css" />

  <!-- Preload pour ressources critiques -->
  <link
    rel="preload"
    href="font.woff2"
    as="font"
    type="font/woff2"
    crossorigin
  />

  <!-- Prefetch pour ressources futures -->
  <link rel="prefetch" href="next-page.html" />

  <!-- DNS Prefetch -->
  <link rel="dns-prefetch" href="https://api.example.com" />

  <!-- Preconnect -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
</head>

<body>
  <!-- Contenu... -->

  <!-- ✅ JS avant la fermeture de body -->
  <script src="script.js" defer></script>

  <!-- Async pour scripts indépendants -->
  <script src="analytics.js" async></script>
</body>
```

### 2. Lazy Loading

```html
<!-- Images -->
<img src="image.jpg" alt="Description" loading="lazy" />

<!-- Iframe -->
<iframe src="video.html" loading="lazy"></iframe>
```

### 3. Minification

```html
<!-- ✅ En production, minifier HTML -->
<!-- Utiliser des outils comme html-minifier -->
```

---

## 🔒 Sécurité

### 1. Éviter les failles XSS

```html
<!-- ❌ Jamais d'entrées utilisateur non échappées -->
<!-- Échapper côté serveur -->

<!-- ✅ Content Security Policy -->
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self'; script-src 'self' 'unsafe-inline'"
/>
```

### 2. Liens externes sûrs

```html
<!-- ✅ Avec target="_blank", toujours ajouter rel -->
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
  Lien externe
</a>
```

---

## 📱 Responsive

### Viewport

```html
<!-- ✅ Obligatoire pour responsive -->
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

### Media Queries dans HTML

```html
<!-- CSS conditionnel -->
<link rel="stylesheet" href="mobile.css" media="(max-width: 768px)" />
<link rel="stylesheet" href="desktop.css" media="(min-width: 769px)" />

<!-- Print -->
<link rel="stylesheet" href="print.css" media="print" />
```

---

## 📝 Validation et Formatage

### Indentation et formatage

```html
<!-- ✅ Bon formatage -->
<div class="container">
  <header>
    <h1>Title</h1>
    <nav>
      <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/about">About</a></li>
      </ul>
    </nav>
  </header>
</div>

<!-- Fermer les balises auto-fermantes (optionnel en HTML5) -->
<img src="image.jpg" alt="Description" />
<!-- ou -->
<img src="image.jpg" alt="Description" />
```

### Commentaires

```html
<!-- ✅ Commentaires utiles -->
<!-- Header Section -->
<header>...</header>

<!-- Main Content -->
<main>...</main>

<!-- Commentaire de fermeture pour grandes sections -->
<div class="complex-section">...</div>
<!-- .complex-section -->
```

---

## 🎨 Templates HTML5

### Template de base

```html
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content="Description de la page" />
    <title>Titre de la page</title>

    <!-- Styles -->
    <link rel="stylesheet" href="styles.css" />

    <!-- Favicon -->
    <link rel="icon" type="image/x-icon" href="/favicon.ico" />
  </head>
  <body>
    <!-- Header -->
    <header>
      <nav>
        <ul>
          <li><a href="/">Accueil</a></li>
          <li><a href="/about">À propos</a></li>
          <li><a href="/contact">Contact</a></li>
        </ul>
      </nav>
    </header>

    <!-- Main Content -->
    <main>
      <h1>Titre Principal</h1>
      <p>Contenu...</p>
    </main>

    <!-- Footer -->
    <footer>
      <p>&copy; 2024 Mon Site. Tous droits réservés.</p>
    </footer>

    <!-- Scripts -->
    <script src="script.js" defer></script>
  </body>
</html>
```

---

## 📚 Ressources

- [MDN HTML Reference](https://developer.mozilla.org/fr/docs/Web/HTML)
- [W3C HTML Validator](https://validator.w3.org/)
- [HTML Living Standard](https://html.spec.whatwg.org/)
- [Web Content Accessibility Guidelines (WCAG)](https://www.w3.org/WAI/WCAG21/quickref/)
- [Can I Use](https://caniuse.com/)
