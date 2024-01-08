# Cheatsheet SCSS (SASS)

- [Index](/Readme.md)
- [doc](https://sass-lang.com/)

## Variables

```scss
$primary-color: #3498db;
$font-family: "Arial", sans-serif;

body {
  color: $primary-color;
  font-family: $font-family;
}
```

## Nesting

```scss
nav {
  background-color: #333;

  ul {
    list-style: none;
    padding: 0;

    li {
      display: inline-block;
      margin-right: 10px;
    }
  }
}
```

## Mixins

```scss
@mixin xs {
  @media (max-width: 576px) {
    @content;
  }
}

@mixin sm {
  @media (min-width: 577px) and (max-width: 768px) {
    @content;
  }
}

@mixin sm-lt {
  @media (max-width: 768px) {
    @content;
  }
}

@mixin md {
  @media (min-width: 769px) and (max-width: 992px) {
    @content;
  }
}

@mixin md-lt {
  @media (max-width: 992px) {
    @content;
  }
}

@mixin lg {
  @media (min-width: 993px) and (max-width: 1200px) {
    @content;
  }
}

@mixin lg-lt {
  @media (max-width: 1200px) {
    @content;
  }
}

@mixin xl {
  @media (min-width: 1201px) {
    @content;
  }
}
```

## Partials

```scss
// _variables.scss
$primary-color: #3498db;

// main.scss
@import "variables";

body {
  color: $primary-color;
}
```
