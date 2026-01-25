# Conventions & Bonnes Pratiques SCSS

## 📋 Conventions de Nommage

### Architecture 7-1

```
scss/
├── abstracts/
│   ├── _variables.scss
│   ├── _mixins.scss
│   ├── _functions.scss
│   └── _placeholders.scss
├── base/
│   ├── _reset.scss
│   ├── _typography.scss
│   └── _base.scss
├── components/
│   ├── _button.scss
│   ├── _card.scss
│   └── _modal.scss
├── layout/
│   ├── _header.scss
│   ├── _footer.scss
│   ├── _sidebar.scss
│   └── _grid.scss
├── pages/
│   ├── _home.scss
│   ├── _about.scss
│   └── _contact.scss
├── themes/
│   ├── _dark.scss
│   └── _light.scss
├── vendors/
│   └── _bootstrap.scss
└── main.scss
```

### main.scss

```scss
// 1. Abstracts
@import "abstracts/variables";
@import "abstracts/functions";
@import "abstracts/mixins";
@import "abstracts/placeholders";

// 2. Vendors
@import "vendors/bootstrap";

// 3. Base
@import "base/reset";
@import "base/typography";
@import "base/base";

// 4. Layout
@import "layout/header";
@import "layout/footer";
@import "layout/sidebar";
@import "layout/grid";

// 5. Components
@import "components/button";
@import "components/card";
@import "components/modal";

// 6. Pages
@import "pages/home";
@import "pages/about";

// 7. Themes
@import "themes/light";
@import "themes/dark";
```

---

## 🎨 Variables

### \_variables.scss

```scss
// ✅ Nommage systématique

// Colors
$color-primary: #3498db;
$color-primary-light: lighten($color-primary, 10%);
$color-primary-dark: darken($color-primary, 10%);

$color-secondary: #2ecc71;
$color-accent: #e74c3c;

$color-gray-100: #f8f9fa;
$color-gray-200: #e9ecef;
$color-gray-300: #dee2e6;
$color-gray-900: #212529;

$color-success: #28a745;
$color-warning: #ffc107;
$color-danger: #dc3545;
$color-info: #17a2b8;

// Typography
$font-family-base: "Roboto", sans-serif;
$font-family-heading: "Montserrat", sans-serif;
$font-family-code: "Fira Code", monospace;

$font-size-base: 1rem;
$font-size-sm: 0.875rem;
$font-size-lg: 1.25rem;
$font-size-xl: 1.5rem;

$font-weight-light: 300;
$font-weight-regular: 400;
$font-weight-medium: 500;
$font-weight-bold: 700;

$line-height-base: 1.5;
$line-height-heading: 1.2;

// Spacing
$spacing-unit: 8px;
$spacing-xs: $spacing-unit * 0.5; // 4px
$spacing-sm: $spacing-unit; // 8px
$spacing-md: $spacing-unit * 2; // 16px
$spacing-lg: $spacing-unit * 3; // 24px
$spacing-xl: $spacing-unit * 4; // 32px

// Breakpoints
$breakpoint-xs: 0;
$breakpoint-sm: 576px;
$breakpoint-md: 768px;
$breakpoint-lg: 992px;
$breakpoint-xl: 1200px;
$breakpoint-xxl: 1400px;

$breakpoints: (
  "xs": $breakpoint-xs,
  "sm": $breakpoint-sm,
  "md": $breakpoint-md,
  "lg": $breakpoint-lg,
  "xl": $breakpoint-xl,
  "xxl": $breakpoint-xxl,
);

// Border
$border-radius-sm: 0.25rem;
$border-radius-md: 0.5rem;
$border-radius-lg: 1rem;
$border-width: 1px;

// Shadows
$shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
$shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
$shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.15);

// Transitions
$transition-base: all 0.3s ease;
$transition-fast: all 0.15s ease;
$transition-slow: all 0.5s ease;

// Z-index
$z-index-dropdown: 1000;
$z-index-sticky: 1020;
$z-index-fixed: 1030;
$z-index-modal-backdrop: 1040;
$z-index-modal: 1050;
$z-index-popover: 1060;
$z-index-tooltip: 1070;
```

---

## 🔧 Mixins

### \_mixins.scss

```scss
// Responsive breakpoints
@mixin respond-to($breakpoint) {
  @if map-has-key($breakpoints, $breakpoint) {
    @media (min-width: map-get($breakpoints, $breakpoint)) {
      @content;
    }
  } @else {
    @warn "Unknown breakpoint: #{$breakpoint}.";
  }
}

// Usage
.container {
  width: 100%;

  @include respond-to("md") {
    max-width: 720px;
  }

  @include respond-to("lg") {
    max-width: 960px;
  }
}

// Flexbox
@mixin flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

@mixin flex-between {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

// Typography
@mixin text-truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

@mixin line-clamp($lines) {
  display: -webkit-box;
  -webkit-line-clamp: $lines;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

// Positioning
@mixin absolute-center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

@mixin cover {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

// Pseudo elements
@mixin pseudo($display: block, $pos: absolute, $content: "") {
  content: $content;
  display: $display;
  position: $pos;
}

// Clear fix
@mixin clearfix {
  &::after {
    content: "";
    display: table;
    clear: both;
  }
}

// Aspect ratio
@mixin aspect-ratio($width, $height) {
  position: relative;

  &::before {
    content: "";
    display: block;
    padding-top: ($height / $width) * 100%;
  }

  > * {
    @include cover;
  }
}

// Buttons
@mixin button-variant($bg, $color: #fff) {
  background-color: $bg;
  color: $color;
  border-color: $bg;

  &:hover {
    background-color: darken($bg, 10%);
    border-color: darken($bg, 10%);
  }

  &:active {
    background-color: darken($bg, 15%);
    border-color: darken($bg, 15%);
  }

  &:disabled {
    background-color: lighten($bg, 20%);
    border-color: lighten($bg, 20%);
    cursor: not-allowed;
  }
}

// Transitions
@mixin transition($properties...) {
  transition: $properties;
}

// Prefix
@mixin prefix($property, $value) {
  -webkit-#{$property}: $value;
  -moz-#{$property}: $value;
  -ms-#{$property}: $value;
  -o-#{$property}: $value;
  #{$property}: $value;
}
```

---

## 📐 Functions

### \_functions.scss

```scss
// Convertir px en rem
@function rem($pixels, $context: 16) {
  @return ($pixels / $context) * 1rem;
}

// Usage
.element {
  font-size: rem(18); // 1.125rem
  padding: rem(16) rem(24);
}

// Color functions
@function tint($color, $percentage) {
  @return mix(white, $color, $percentage);
}

@function shade($color, $percentage) {
  @return mix(black, $color, $percentage);
}

// Usage
.button {
  background: $color-primary;

  &:hover {
    background: shade($color-primary, 20%);
  }
}

// Map deep get
@function map-deep-get($map, $keys...) {
  @each $key in $keys {
    $map: map-get($map, $key);
  }
  @return $map;
}

// Strip unit
@function strip-unit($number) {
  @if type-of($number) == "number" and not unitless($number) {
    @return $number / ($number * 0 + 1);
  }
  @return $number;
}
```

---

## 🎯 BEM avec SCSS

```scss
// Block
.card {
  padding: $spacing-md;
  background: white;
  border-radius: $border-radius-md;

  // Element
  &__header {
    margin-bottom: $spacing-sm;
    font-size: $font-size-lg;
    font-weight: $font-weight-bold;
  }

  &__body {
    color: $color-gray-900;
  }

  &__footer {
    margin-top: $spacing-md;
    padding-top: $spacing-sm;
    border-top: $border-width solid $color-gray-200;
  }

  // Modifier
  &--primary {
    background: $color-primary;
    color: white;
  }

  &--large {
    padding: $spacing-lg;
  }

  // State
  &.is-active {
    box-shadow: $shadow-lg;
  }

  &.is-disabled {
    opacity: 0.5;
    pointer-events: none;
  }
}

// Usage HTML
// <div class="card card--primary card--large">
//   <h2 class="card__header">Title</h2>
//   <p class="card__body">Content</p>
//   <div class="card__footer">Footer</div>
// </div>
```

---

## 🔄 Nesting

```scss
// ✅ Bon - nesting limité (max 3 niveaux)
.navigation {
  background: $color-primary;

  &__list {
    @include flex-center;
    list-style: none;
  }

  &__item {
    margin: 0 $spacing-sm;
  }

  &__link {
    color: white;
    text-decoration: none;

    &:hover {
      text-decoration: underline;
    }
  }
}

// ❌ Éviter - nesting trop profond
.page {
  .container {
    .row {
      .col {
        .card {
          .header {
            // Trop profond !
          }
        }
      }
    }
  }
}
```

---

## 🎨 Placeholders (Extend)

### \_placeholders.scss

```scss
// Placeholders réutilisables
%button-base {
  display: inline-block;
  padding: $spacing-sm $spacing-md;
  font-family: $font-family-base;
  font-size: $font-size-base;
  line-height: $line-height-base;
  text-align: center;
  text-decoration: none;
  border: $border-width solid transparent;
  border-radius: $border-radius-sm;
  cursor: pointer;
  transition: $transition-base;

  &:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }
}

%card-base {
  background: white;
  border-radius: $border-radius-md;
  box-shadow: $shadow-sm;
  padding: $spacing-md;
}

// Utilisation
.btn {
  @extend %button-base;
}

.btn-primary {
  @extend %button-base;
  @include button-variant($color-primary);
}

.card {
  @extend %card-base;
}
```

---

## 🌈 Theming

```scss
// _variables.scss
$themes: (
  light: (
    bg-primary: #ffffff,
    bg-secondary: #f8f9fa,
    text-primary: #212529,
    text-secondary: #6c757d,
  ),
  dark: (
    bg-primary: #212529,
    bg-secondary: #343a40,
    text-primary: #ffffff,
    text-secondary: #adb5bd,
  ),
);

// _mixins.scss
@mixin themed() {
  @each $theme, $map in $themes {
    .theme-#{$theme} & {
      $theme-map: $map !global;
      @content;
      $theme-map: null !global;
    }
  }
}

@function themed-value($key) {
  @return map-get($theme-map, $key);
}

// Usage
.card {
  @include themed() {
    background: themed-value("bg-primary");
    color: themed-value("text-primary");
  }
}

// Génère :
// .theme-light .card {
//   background: #ffffff;
//   color: #212529;
// }
// .theme-dark .card {
//   background: #212529;
//   color: #ffffff;
// }
```

---

## 🚀 Optimisations

### Code modulaire

```scss
// ✅ Bon - Import uniquement ce qui est nécessaire
@import "abstracts/variables";
@import "abstracts/mixins";
@import "components/button";
@import "components/card";

// ❌ Éviter - Import de tout
@import "bootstrap";
```

### Éviter les sélecteurs trop spécifiques

```scss
// ❌ Éviter
body div.container .row .col-12 .card .header h2 {
  color: red;
}

// ✅ Bon
.card__header {
  color: red;
}
```

### Utiliser des maps pour les variantes

```scss
$button-variants: (
  "primary": $color-primary,
  "secondary": $color-secondary,
  "success": $color-success,
  "danger": $color-danger,
);

@each $name, $color in $button-variants {
  .btn-#{$name} {
    @include button-variant($color);
  }
}
```

---

## 📚 Ressources

- [Sass Documentation](https://sass-lang.com/documentation)
- [Sass Guidelines](https://sass-guidelin.es/)
- [BEM Methodology](http://getbem.com/)
- [SCSS Best Practices](https://www.sitepoint.com/sass-reference/)
