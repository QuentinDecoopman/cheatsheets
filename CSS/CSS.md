# Cheatsheet CSS

- [Index](/Readme.md)
- [Doc MDN](https://developer.mozilla.org/fr/docs/Web/CSS)

## Sélecteurs

```css

/* Sélection par élément */
element {
    property: value;
}

/* Sélection par classe */
.classe {
    property: value;
}

/* Sélection par ID */
#identifiant {
    property: value;
}

/* Sélection par attribut */
[type='text'] {
    property: value;
}

/* Sélection par relation parent-enfant */
parent enfant {
    property: value;
}

/* Sélection par pseudo-classes */
:hover {
    property: value;
}

/* Sélection par pseudo-éléments */
::before {
    content: 'Texte avant l'élément';
}
```

## Propriétés de base

```css
/* Couleur */
color: #ff0000;
background-color: #ffffff;

/* Police */
font-family: "Arial", sans-serif;
font-size: 16px;
font-weight: bold;

/* Marge, bordure, et rembourrage */
margin: 10px;
border: 1px solid #000000;
padding: 5px;

/* Largeur et hauteur */
width: 100px;
height: 100px;

/* Affichage et positionnement */
display: block;
position: relative;

/* Opacité et ombre */
opacity: 0.8;
box-shadow: 2px 2px 4px #888888;
```

## Flexbox

```css
/* Conteneur Flexbox */
.conteneur {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* Éléments Flexbox */
.element {
  flex: 1;
}
```

## Grid

```css
/* Conteneur Grid */
.conteneur {
  display: grid;
  grid-template-columns: 100px 100px;
  grid-gap: 10px;
}

/* Éléments Grid */
.element {
  grid-column: span 2;
}
```

## Animation

```css
@keyframes monAnimation {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

.element {
  animation: monAnimation 2s infinite;
}
```

## Transitions

```css
.element {
  transition: property 0.5s ease-in-out;
}

.element:hover {
  property: value;
}
```

## Media Queries

```css
/* Appliquer un style pour les écrans de petite taille */
@media screen and (max-width: 600px) {
  body {
    background-color: lightblue;
  }
}
```

## Variables

```css
:root {
  --ma-couleur: #ff9900;
}

.element {
  color: var(--ma-couleur);
}
```

## Positionnement

```css
/* Position absolue */
.element {
  position: absolute;
  top: 10px;
  left: 20px;
}

/* Position fixe */
.element {
  position: fixed;
  bottom: 0;
  right: 0;
}
```
