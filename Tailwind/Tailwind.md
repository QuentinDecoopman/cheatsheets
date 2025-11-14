# Tailwind

- [Index](/Readme.md)
- [Doc](https://v2.tailwindcss.com/docs)

Framework CSS utility-first et open source.
N'utilise pas de composants pré-définis comme Bootstrap.

## Installation

```bash
npm install -D tailwindcss

npx tailwindcss init
```

## Configuration

### Dans un fichier `tailwind.config.js` ajouter :

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{html,js}"],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

### Ajouter Tailwind dans le fichier CSS principal :

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### Lancez le process de build & hot-reload avec:

```bash
npx tailwindcss -i ./src/input.css -o ./src/output.css --watch
```

## Exemple d'un fichier html de base:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <link href="./output.css" rel="stylesheet">
</head>
<body>
  <h1 class="text-3xl font-bold underline">
    Hello world!
  </h1>
</html>
```
## Exemple de customisation (tailwind.config.js)

```js
module.exports = {
  theme: {
    extend: {
      colors: {
        'primary': '#FF6347',
        'secondary': '#6B7280',
      },
      fontFamily: {
        'body': ['Roboto', 'sans-serif'],
      },
      spacing: {
        '72': '18rem',
      },
    },
  },
  variants: {},
  plugins: [],
}
```

## Exemple de Mode Sombre
```js
<div class="dark:bg-gray-800 bg-gray-200 text-gray-700 dark:text-gray-200">
  <!-- Contenu -->
</div>
```
## Plugins officiel
```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  // ...
  plugins: [
    require('@tailwindcss/typography'),
    require('@tailwindcss/forms'),
    require('@tailwindcss/aspect-ratio'),
    require('@tailwindcss/container-queries'),
  ]
}
