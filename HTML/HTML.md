# Cheatsheet HTML

- [Index](/Readme.md)

## Structure de base

```html
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Titre de la page</title>
  </head>
  <body>
    <!-- Contenu de la page -->
  </body>
</html>
```

## Éléments de texte

```html
<!-- Titres -->
<h1>Titre 1</h1>
<h2>Titre 2</h2>
<h3>Titre 3</h3>
<h4>Titre 4</h4>
<h5>Titre 5</h5>
<h6>Titre 6</h6>

<!-- Paragraphe -->
<p>Ceci est un paragraphe.</p>

<!-- Texte en gras et en italique -->
<strong>Texte en gras</strong>
<em>Texte en italique</em>

<!-- Citation -->
<blockquote>
  <p>Ceci est une citation.</p>
</blockquote>

<!-- Lien -->
<a href="https://www.example.com">Texte du lien</a>
```

## Listes

```html
<!-- Liste non ordonnée -->
<ul>
  <li>Élément 1</li>
  <li>Élément 2</li>
</ul>

<!-- Liste ordonnée -->
<ol>
  <li>Premier élément</li>
  <li>Deuxième élément</li>
</ol>

<!-- Liste de définition -->
<dl>
  <dt>Terme</dt>
  <dd>Définition du terme.</dd>
</dl>
```

## Images

```html
<img src="chemin/vers/image.jpg" alt="Texte alternatif" />
```

## Tableaux

```html
<table>
  <thead>
    <tr>
      <th>En-tête 1</th>
      <th>En-tête 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Cellule 1</td>
      <td>Cellule 2</td>
    </tr>
    <tr>
      <td>Cellule 3</td>
      <td>Cellule 4</td>
    </tr>
  </tbody>
</table>
```

## Formulaires

```html
<form action="/traitement" method="post">
  <label for="nom">Nom :</label>
  <input type="text" id="nom" name="nom" required />

  <label for="email">Email :</label>
  <input type="email" id="email" name="email" required />

  <input type="submit" value="Envoyer" />
</form>
```

## Médias

```html
<!-- Audio -->
<audio controls>
  <source src="chemin/vers/audio.mp3" type="audio/mp3" />
  Votre navigateur ne prend pas en charge l'élément audio.
</audio>

<!-- Vidéo -->
<video controls width="640" height="360">
  <source src="chemin/vers/video.mp4" type="video/mp4" />
  Votre navigateur ne prend pas en charge l'élément vidéo.
</video>
```

## Scripts et Styles

```html
<!-- Script externe -->
<script src="chemin/vers/script.js"></script>

<!-- Style interne -->
<style>
  body {
    font-family: "Arial", sans-serif;
  }
</style>
```

## Balises sémantiques

```html
<!-- Article -->
<article>
  <h1>Titre de l'article</h1>
  <p>Contenu de l'article.</p>
</article>

<!-- Section -->
<section>
  <h2>Titre de la section</h2>
  <p>Contenu de la section.</p>
</section>

<!-- Aside -->
<aside>
  <h3>Titre de l'aside</h3>
  <p>Contenu de l'aside.</p>
</aside>
```
