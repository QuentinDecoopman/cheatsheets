# Cheatsheet Gsap

GreenSocks animation platform

- [Index](/Readme.md)
- [Docs](https://gsap.com/docs/v3/)

## Installation
```
npm install gsap
```

##  Importation (ES Modules)
      import gsap from 'gsap';

## Animations de base
### Tweening d'un élément
```javascript
gsap.to(".classe", { duration: 1, x: 100, opacity: 0.5 });
gsap.from(".classe", { duration: 1, x: -100, opacity: 0 });
gsap.fromTo(".classe", { opacity: 0 }, { duration: 1, opacity: 1 });
```

### Propriétés communes
   - duration: Durée de l'animation en secondes.
   - x, y: Translation sur les axes X et Y.
   - rotation: Rotation en degrés.
   - scale: Échelle de l'élément.
   - opacity: Opacité de l'élément.

## Sélecteurs
### ID
    gsap.to("#monID", { duration: 1, y: 100 });

### Classe
    gsap.to(".maClasse", { duration: 1, x: 100 });

## Délais et répétitions
   ### Délais
```
gsap.to(".classe", { duration: 1, x: 100, delay: 0.5 });
```

### Répétitions
````javascript
gsap.to(".classe", { duration: 1, x: 100, repeat: 2 });

gsap.to(".classe", { duration: 1, x: 100, repeat: -1 }); // Répétition infinie
````


## Easing (courbes de transition)
   ### Exemples d'easing
````javascript
gsap.to(".classe", { duration: 1, x: 100, ease: "power1.inOut" });
gsap.to(".classe", { duration: 1, x: 100, ease: "bounce.out" });
````

## Types d'easing courants

   - power1, power2, power3, power4
   - elastic
   - bounce
   - back
   - circ


## Timelines
   ### Création d'une timeline
````javascript
let tl = gsap.timeline();
tl.to(".classe", { duration: 1, x: 100 })
.to(".classe", { duration: 1, y: 100 })
.to(".classe", { duration: 1, opacity: 0 });
````


### Options de timeline
    let tl = gsap.timeline({ repeat: -1, yoyo: true });

## Gestion des callbacks
   ### Callbacks courants

   - onStart: Appelé au début de l'animation.
   - onUpdate: Appelé à chaque mise à jour de l'animation.
   - onComplete: Appelé à la fin de l'animation.
   - onRepeat: Appelé à chaque répétition de l'animation.

````javascript
gsap.to(".classe", {
duration: 1,
x: 100,
onComplete: function() {
console.log("Animation terminée");
}
});
````

## Plugins
   ### ScrollTrigger (par exemple)
````javascript
gsap.registerPlugin(ScrollTrigger);

gsap.to(".classe", {
scrollTrigger: ".classe",
x: 500,
duration: 3
});
````

## Utilisation avancée
### Stagger (décalage entre les animations)

    gsap.to(".classe", { duration: 1, x: 100, stagger: 0.2 });

### Utiliser des fonctions pour des valeurs
````javascript
gsap.to(".classe", {
  duration: 1,
  x: function(index, target) {
    return index * 100;
  }
});

````

