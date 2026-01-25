# Conventions & Bonnes Pratiques GSAP

## 📋 Installation

```bash
npm install gsap
```

```javascript
// Import complet
import gsap from 'gsap';

// Plugins
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import { Draggable } from 'gsap/Draggable';
import { MotionPathPlugin } from 'gsap/MotionPathPlugin';

// Register plugins
gsap.registerPlugin(ScrollTrigger, Draggable, MotionPathPlugin);
```

---

## ✅ Animations de Base

### gsap.to()
```javascript
// Anime vers les valeurs spécifiées
gsap.to('.box', {
  x: 100,
  y: 50,
  rotation: 360,
  duration: 1,
  ease: 'power2.out',
});

// Plusieurs propriétés
gsap.to('.box', {
  duration: 2,
  x: 300,
  y: 200,
  backgroundColor: '#ff0000',
  borderRadius: '50%',
  scale: 2,
  opacity: 0.5,
});
```

### gsap.from()
```javascript
// Anime depuis les valeurs spécifiées
gsap.from('.box', {
  x: -100,
  opacity: 0,
  duration: 1,
});
```

### gsap.fromTo()
```javascript
// Contrôle complet from → to
gsap.fromTo('.box',
  { x: -100, opacity: 0 },
  { x: 100, opacity: 1, duration: 1 }
);
```

### gsap.set()
```javascript
// Définit les valeurs instantanément (sans animation)
gsap.set('.box', {
  x: 100,
  y: 50,
  scale: 2,
});
```

---

## ⏱️ Timeline

### Création et utilisation
```javascript
const tl = gsap.timeline({
  defaults: {
    duration: 1,
    ease: 'power2.inOut',
  },
});

// Animations séquentielles
tl.to('.box1', { x: 100 })
  .to('.box2', { y: 100 })
  .to('.box3', { rotation: 360 });

// Position relative
tl.to('.box1', { x: 100 })
  .to('.box2', { y: 100 }, '-=0.5')  // Commence 0.5s avant la fin
  .to('.box3', { rotation: 360 }, '+=0.5');  // Commence 0.5s après

// Position absolue
tl.to('.box1', { x: 100 })
  .to('.box2', { y: 100 }, 1)  // Commence à 1s
  .to('.box3', { rotation: 360 }, 2);  // Commence à 2s

// Labels
tl.to('.box1', { x: 100 })
  .addLabel('middle')
  .to('.box2', { y: 100 })
  .to('.box3', { rotation: 360 }, 'middle');  // Commence au label
```

### Contrôle de Timeline
```javascript
const tl = gsap.timeline({ paused: true });

// Play/Pause/Reverse
tl.play();
tl.pause();
tl.reverse();
tl.restart();

// Seek
tl.seek(1.5);  // Va à 1.5s
tl.progress(0.5);  // Va à 50%

// Vitesse
tl.timeScale(2);  // 2x plus rapide
tl.timeScale(0.5);  // 2x plus lent

// Callbacks
tl.eventCallback('onComplete', () => console.log('Done!'));
tl.eventCallback('onUpdate', () => console.log('Updating...'));
```

---

## 🎨 Easing

```javascript
// Linear
ease: 'none'

// Power
ease: 'power1.in'
ease: 'power2.out'
ease: 'power3.inOut'
ease: 'power4.in'

// Back (dépasse puis revient)
ease: 'back.in'
ease: 'back.out'
ease: 'back.inOut'

// Elastic (rebond)
ease: 'elastic.in'
ease: 'elastic.out'
ease: 'elastic.inOut'

// Bounce
ease: 'bounce.in'
ease: 'bounce.out'
ease: 'bounce.inOut'

// Steps
ease: 'steps(10)'

// Custom avec CustomEase (plugin)
ease: 'M0,0 C0.5,0 0.5,1 1,1'
```

---

## 🔄 Répétition et Yoyo

```javascript
gsap.to('.box', {
  x: 100,
  duration: 1,
  repeat: 3,          // Répète 3 fois
  repeatDelay: 0.5,   // Délai entre répétitions
  yoyo: true,         // Revient en arrière
});

// Infini
gsap.to('.box', {
  rotation: 360,
  duration: 2,
  repeat: -1,         // Infini
  ease: 'none',
});
```

---

## 📜 ScrollTrigger

### Configuration de base
```javascript
gsap.registerPlugin(ScrollTrigger);

gsap.to('.box', {
  x: 500,
  scrollTrigger: {
    trigger: '.box',
    start: 'top center',    // Quand le haut de .box atteint le centre du viewport
    end: 'bottom center',   // Quand le bas de .box atteint le centre
    scrub: true,            // Lie l'animation au scroll
    markers: true,          // Debug markers
  },
});

// Positions
start: 'top top'      // Haut élément → Haut viewport
start: 'top center'   // Haut élément → Centre viewport
start: 'top bottom'   // Haut élément → Bas viewport
start: 'center center'
start: '100px 80%'    // 100px de l'élément → 80% du viewport
```

### Scrub et Pin
```javascript
// Scrub - lie l'animation au scroll
scrollTrigger: {
  trigger: '.box',
  scrub: true,        // Sync parfait
  scrub: 1,           // Smooth avec 1s de delay
}

// Pin - fixe l'élément pendant l'animation
scrollTrigger: {
  trigger: '.section',
  pin: true,
  start: 'top top',
  end: '+=500',       // 500px de scroll
}

// Toggle Actions
scrollTrigger: {
  trigger: '.box',
  toggleActions: 'play pause resume reset',
  // onEnter onLeave onEnterBack onLeaveBack
}
```

### Timeline avec ScrollTrigger
```javascript
const tl = gsap.timeline({
  scrollTrigger: {
    trigger: '.container',
    pin: true,
    start: 'top top',
    end: '+=2000',
    scrub: 1,
  },
});

tl.to('.box1', { x: 500 })
  .to('.box2', { y: 500 })
  .to('.box3', { rotation: 360 });
```

### Callbacks
```javascript
scrollTrigger: {
  trigger: '.box',
  onEnter: () => console.log('Entered'),
  onLeave: () => console.log('Left'),
  onEnterBack: () => console.log('Entered back'),
  onLeaveBack: () => console.log('Left back'),
  onUpdate: (self) => console.log('Progress:', self.progress),
}
```

---

## 🎯 Animations Courantes

### Fade In
```javascript
gsap.from('.element', {
  opacity: 0,
  y: 50,
  duration: 1,
  stagger: 0.2,  // Délai entre éléments
});
```

### Stagger
```javascript
// Animer plusieurs éléments avec délai
gsap.to('.box', {
  x: 100,
  duration: 1,
  stagger: 0.2,  // 0.2s entre chaque
});

// Stagger avancé
gsap.to('.box', {
  x: 100,
  duration: 1,
  stagger: {
    amount: 1,      // Total de 1s entre tous
    from: 'center', // Commence du centre
    grid: 'auto',   // Pour grille
    ease: 'power2.in',
  },
});
```

### Hover
```javascript
const box = document.querySelector('.box');

box.addEventListener('mouseenter', () => {
  gsap.to(box, {
    scale: 1.2,
    duration: 0.3,
  });
});

box.addEventListener('mouseleave', () => {
  gsap.to(box, {
    scale: 1,
    duration: 0.3,
  });
});
```

### Text reveal
```javascript
// Anime chaque lettre
import { SplitText } from 'gsap/SplitText';
gsap.registerPlugin(SplitText);

const split = new SplitText('.text', { type: 'chars' });

gsap.from(split.chars, {
  opacity: 0,
  y: 50,
  stagger: 0.05,
});
```

---

## 🖱️ Draggable

```javascript
import { Draggable } from 'gsap/Draggable';
gsap.registerPlugin(Draggable);

// Simple drag
Draggable.create('.box', {
  type: 'x,y',
  bounds: '.container',
  inertia: true,
});

// Avec callbacks
Draggable.create('.box', {
  type: 'x,y',
  onDragStart: function() {
    console.log('Drag started');
  },
  onDrag: function() {
    console.log('Dragging...', this.x, this.y);
  },
  onDragEnd: function() {
    console.log('Drag ended');
  },
});

// Rotation
Draggable.create('.knob', {
  type: 'rotation',
  bounds: { minRotation: 0, maxRotation: 360 },
});
```

---

## 🎬 Exemples Pratiques

### Menu mobile
```javascript
const tl = gsap.timeline({ paused: true });

tl.to('.menu', {
  x: 0,
  duration: 0.5,
  ease: 'power2.out',
})
.from('.menu-item', {
  x: -50,
  opacity: 0,
  stagger: 0.1,
}, '-=0.3');

// Toggle
document.querySelector('.burger').addEventListener('click', () => {
  tl.reversed(!tl.reversed());
});
```

### Parallax scroll
```javascript
gsap.to('.bg', {
  y: -200,
  scrollTrigger: {
    trigger: '.section',
    scrub: true,
  },
});

gsap.to('.content', {
  y: -100,
  scrollTrigger: {
    trigger: '.section',
    scrub: true,
  },
});
```

### Counter animation
```javascript
const obj = { value: 0 };

gsap.to(obj, {
  value: 100,
  duration: 2,
  onUpdate: () => {
    document.querySelector('.counter').textContent = Math.round(obj.value);
  },
});
```

### Loading bar
```javascript
const tl = gsap.timeline();

tl.to('.progress-bar', {
  width: '100%',
  duration: 2,
  ease: 'power2.out',
})
.to('.loader', {
  opacity: 0,
  duration: 0.5,
  onComplete: () => {
    document.querySelector('.loader').style.display = 'none';
  },
});
```

---

## 🚀 Performance

### Best Practices
```javascript
// ✅ Bon - utiliser transforms
gsap.to('.box', {
  x: 100,      // transform: translateX()
  y: 50,       // transform: translateY()
  rotation: 45, // transform: rotate()
  scale: 2,    // transform: scale()
});

// ❌ Éviter - propriétés lourdes
gsap.to('.box', {
  left: '100px',   // Provoque reflow
  top: '50px',     // Provoque reflow
  width: '200px',  // Provoque reflow
});

// ✅ Force3D pour GPU acceleration
gsap.to('.box', {
  x: 100,
  force3D: true,
});

// ✅ Batch updates
gsap.set(['.box1', '.box2', '.box3'], {
  x: 100,
  opacity: 0.5,
});
```

---

## 🔧 Utils

```javascript
// Interpoler valeurs
gsap.utils.interpolate(0, 100, 0.5);  // 50

// Map range
gsap.utils.mapRange(0, 100, 0, 1, 50);  // 0.5

// Clamp
gsap.utils.clamp(0, 100, 150);  // 100

// Wrap
gsap.utils.wrap(['red', 'blue', 'green'], 3);  // 'red'

// Random
gsap.utils.random(1, 10);  // Random entre 1 et 10
gsap.utils.random(['a', 'b', 'c']);  // Random dans array

// Distribute
gsap.utils.distribute({
  base: 0,
  amount: 100,
  from: 'center',
});
```

---

## 📚 Ressources

- [GSAP Documentation](https://greensock.com/docs/)
- [GSAP Cheat Sheet](https://greensock.com/cheatsheet/)
- [ScrollTrigger Demos](https://greensock.com/st-demos/)
- [GSAP Forum](https://greensock.com/forums/)
