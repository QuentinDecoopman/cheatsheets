# Cheatsheet GSAP

GreenSock Animation Platform (GSAP) — bibliothèque d'animations JavaScript.

- [Index](/Readme.md)
- [Docs](https://greensock.com/docs/v3/)

## Installation

```bash
npm install gsap
```

## Importation (ES Modules)

```javascript
import gsap from "gsap";
```

## Animations de base

### Tweening d'un élément

```javascript
gsap.to(".classe", { duration: 1, x: 100, opacity: 0.5 });
gsap.from(".classe", { duration: 1, x: -100, opacity: 0 });
gsap.fromTo(".classe", { opacity: 0 }, { duration: 1, opacity: 1 });
```

### Propriétés communes

- `duration`: durée en secondes
- `x`, `y`: translation en pixels (axe X / Y)
- `rotation`: rotation en degrés
- `scale`: échelle (1 = taille d'origine)
- `opacity`: opacité (0 à 1)

## Sélecteurs

### ID

```javascript
gsap.to("#monID", { duration: 1, y: 100 });
```

### Classe

```javascript
gsap.to(".maClasse", { duration: 1, x: 100 });
```

## Délais et répétitions

### Délais

```javascript
gsap.to(".classe", { duration: 1, x: 100, delay: 0.5 });
```

### Répétitions

```javascript
gsap.to(".classe", { duration: 1, x: 100, repeat: 2 });
// Répétition infinie
gsap.to(".classe", { duration: 1, x: 100, repeat: -1 });
```

## Easing (courbes de transition)

### Exemples d'easing

```javascript
gsap.to(".classe", { duration: 1, x: 100, ease: "power1.inOut" });
gsap.to(".classe", { duration: 1, x: 100, ease: "bounce.out" });
```

### Types d'easing courants

- `power1`, `power2`, `power3`, `power4`
- `elastic`
- `bounce`
- `back`
- `circ`

## Timelines

### Création d'une timeline

```javascript
let tl = gsap.timeline();
tl.to(".classe", { duration: 1, x: 100 })
  .to(".classe", { duration: 1, y: 100 })
  .to(".classe", { duration: 1, opacity: 0 });
```

### Options de timeline

```javascript
let tl = gsap.timeline({ repeat: -1, yoyo: true });
```

## Gestion des callbacks

- `onStart`: appelé au début
- `onUpdate`: appelé à chaque frame
- `onComplete`: appelé à la fin
- `onRepeat`: appelé à chaque répétition

```javascript
gsap.to(".classe", {
  duration: 1,
  x: 100,
  onComplete() {
    console.log("Animation terminée");
  },
});
```

## Plugins

### ScrollTrigger (exemple)

```javascript
gsap.registerPlugin(ScrollTrigger);

gsap.to(".classe", {
  scrollTrigger: ".classe",
  x: 500,
  duration: 3,
});
```

## Utilisation avancée

### Stagger (décalage entre animations)

```javascript
gsap.to(".classe", { duration: 1, x: 100, stagger: 0.2 });
```

### Valeurs basées sur des fonctions

```javascript
gsap.to(".classe", {
  duration: 1,
  x: (index, target) => index * 100,
});