# Conventions & Bonnes Pratiques Three.js

## 📋 Structure de Base

### Setup initial
```javascript
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

class ThreeScene {
  constructor(container) {
    this.container = container;
    this.scene = null;
    this.camera = null;
    this.renderer = null;
    this.controls = null;
    
    this.init();
    this.animate();
  }
  
  init() {
    // Scene
    this.scene = new THREE.Scene();
    this.scene.background = new THREE.Color(0x000000);
    
    // Camera
    this.camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    this.camera.position.set(0, 0, 5);
    
    // Renderer
    this.renderer = new THREE.WebGLRenderer({
      antialias: true,
      alpha: true,
    });
    this.renderer.setSize(window.innerWidth, window.innerHeight);
    this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    this.container.appendChild(this.renderer.domElement);
    
    // Controls
    this.controls = new OrbitControls(this.camera, this.renderer.domElement);
    this.controls.enableDamping = true;
    
    // Lights
    this.setupLights();
    
    // Objects
    this.createObjects();
    
    // Events
    window.addEventListener('resize', this.onResize.bind(this));
  }
  
  setupLights() {
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
    this.scene.add(ambientLight);
    
    const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
    directionalLight.position.set(5, 10, 7.5);
    this.scene.add(directionalLight);
  }
  
  createObjects() {
    // Créer des objets ici
  }
  
  onResize() {
    this.camera.aspect = window.innerWidth / window.innerHeight;
    this.camera.updateProjectionMatrix();
    this.renderer.setSize(window.innerWidth, window.innerHeight);
  }
  
  animate() {
    requestAnimationFrame(this.animate.bind(this));
    
    if (this.controls.enableDamping) {
      this.controls.update();
    }
    
    this.renderer.render(this.scene, this.camera);
  }
  
  dispose() {
    window.removeEventListener('resize', this.onResize.bind(this));
    this.renderer.dispose();
    this.controls.dispose();
  }
}

// Utilisation
const container = document.getElementById('canvas-container');
const app = new ThreeScene(container);
```

---

## 🎨 Géométries et Matériaux

### Géométries de base
```javascript
// Box
const boxGeometry = new THREE.BoxGeometry(1, 1, 1);

// Sphere
const sphereGeometry = new THREE.SphereGeometry(1, 32, 32);

// Plane
const planeGeometry = new THREE.PlaneGeometry(10, 10);

// Cylinder
const cylinderGeometry = new THREE.CylinderGeometry(1, 1, 2, 32);

// Torus
const torusGeometry = new THREE.TorusGeometry(1, 0.4, 16, 100);
```

### Matériaux
```javascript
// Basic - pas affecté par la lumière
const basicMaterial = new THREE.MeshBasicMaterial({
  color: 0xff0000,
  wireframe: false,
});

// Standard - PBR réaliste
const standardMaterial = new THREE.MeshStandardMaterial({
  color: 0xff0000,
  metalness: 0.5,
  roughness: 0.5,
});

// Phong - brillant
const phongMaterial = new THREE.MeshPhongMaterial({
  color: 0xff0000,
  shininess: 100,
});

// Lambert - mat
const lambertMaterial = new THREE.MeshLambertMaterial({
  color: 0xff0000,
});

// Avec texture
const textureLoader = new THREE.TextureLoader();
const texture = textureLoader.load('/textures/wood.jpg');
const materialWithTexture = new THREE.MeshStandardMaterial({
  map: texture,
});

// Material avec normal map
const normalMap = textureLoader.load('/textures/normal.jpg');
const material = new THREE.MeshStandardMaterial({
  map: texture,
  normalMap: normalMap,
  roughness: 0.5,
  metalness: 0.3,
});
```

---

## 💡 Lumières

```javascript
// Ambient - éclairage global
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

// Directional - comme le soleil
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
directionalLight.position.set(5, 10, 7.5);
directionalLight.castShadow = true;
scene.add(directionalLight);

// Point - comme une ampoule
const pointLight = new THREE.PointLight(0xffffff, 1, 100);
pointLight.position.set(0, 5, 0);
scene.add(pointLight);

// Spot - projecteur
const spotLight = new THREE.SpotLight(0xffffff, 1);
spotLight.position.set(0, 10, 0);
spotLight.angle = Math.PI / 6;
spotLight.castShadow = true;
scene.add(spotLight);

// Hemisphere - ciel/sol
const hemisphereLight = new THREE.HemisphereLight(0xffffbb, 0x080820, 0.5);
scene.add(hemisphereLight);

// Helper pour debug
const directionalLightHelper = new THREE.DirectionalLightHelper(directionalLight);
scene.add(directionalLightHelper);
```

---

## 🎬 Animations

### Animation avec requestAnimationFrame
```javascript
const clock = new THREE.Clock();

function animate() {
  requestAnimationFrame(animate);
  
  const elapsedTime = clock.getElapsedTime();
  
  // Rotation
  mesh.rotation.x = elapsedTime * 0.5;
  mesh.rotation.y = elapsedTime * 0.3;
  
  // Position oscillante
  mesh.position.y = Math.sin(elapsedTime) * 2;
  
  renderer.render(scene, camera);
}

animate();
```

### Animation avec GSAP
```javascript
import gsap from 'gsap';

// Animer position
gsap.to(mesh.position, {
  x: 2,
  duration: 2,
  ease: 'power2.inOut',
});

// Animer rotation
gsap.to(mesh.rotation, {
  y: Math.PI * 2,
  duration: 3,
  repeat: -1,
  ease: 'none',
});

// Timeline
const tl = gsap.timeline();
tl.to(mesh.position, { x: 2, duration: 1 })
  .to(mesh.position, { y: 2, duration: 1 })
  .to(mesh.position, { z: 2, duration: 1 });
```

---

## 🎥 Caméras

### Perspective Camera
```javascript
const camera = new THREE.PerspectiveCamera(
  75,                              // FOV
  window.innerWidth / window.innerHeight, // Aspect
  0.1,                             // Near
  1000                             // Far
);
camera.position.set(0, 0, 5);
```

### Orthographic Camera
```javascript
const aspect = window.innerWidth / window.innerHeight;
const camera = new THREE.OrthographicCamera(
  -10 * aspect, // left
  10 * aspect,  // right
  10,           // top
  -10,          // bottom
  0.1,          // near
  1000          // far
);
```

### Controls
```javascript
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
import { FlyControls } from 'three/examples/jsm/controls/FlyControls';
import { FirstPersonControls } from 'three/examples/jsm/controls/FirstPersonControls';

// Orbit Controls
const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.dampingFactor = 0.05;
controls.minDistance = 2;
controls.maxDistance = 20;
controls.maxPolarAngle = Math.PI / 2;

// Update dans animate()
function animate() {
  controls.update();
  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

---

## 🖼️ Textures

```javascript
const textureLoader = new THREE.TextureLoader();

// Charger une texture
const texture = textureLoader.load('/textures/door.jpg');

// Avec callbacks
const texture = textureLoader.load(
  '/textures/door.jpg',
  () => console.log('Loaded'),
  () => console.log('Progress'),
  () => console.log('Error')
);

// Répétition
texture.wrapS = THREE.RepeatWrapping;
texture.wrapT = THREE.RepeatWrapping;
texture.repeat.set(2, 2);

// Offset
texture.offset.set(0.5, 0.5);

// Rotation
texture.rotation = Math.PI / 4;
texture.center.set(0.5, 0.5);

// Multiple textures
const colorTexture = textureLoader.load('/textures/color.jpg');
const normalTexture = textureLoader.load('/textures/normal.jpg');
const roughnessTexture = textureLoader.load('/textures/roughness.jpg');

const material = new THREE.MeshStandardMaterial({
  map: colorTexture,
  normalMap: normalTexture,
  roughnessMap: roughnessTexture,
});
```

---

## 🌫️ Post-processing

```javascript
import { EffectComposer } from 'three/examples/jsm/postprocessing/EffectComposer';
import { RenderPass } from 'three/examples/jsm/postprocessing/RenderPass';
import { UnrealBloomPass } from 'three/examples/jsm/postprocessing/UnrealBloomPass';

// Composer
const composer = new EffectComposer(renderer);

// Render pass
const renderPass = new RenderPass(scene, camera);
composer.addPass(renderPass);

// Bloom pass
const bloomPass = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth, window.innerHeight),
  1.5,  // strength
  0.4,  // radius
  0.85  // threshold
);
composer.addPass(bloomPass);

// Render avec composer
function animate() {
  requestAnimationFrame(animate);
  composer.render();
}
```

---

## 🎯 Raycasting (Détection de clic)

```javascript
const raycaster = new THREE.Raycaster();
const mouse = new THREE.Vector2();

function onMouseClick(event) {
  // Normaliser les coordonnées
  mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
  mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
  
  // Update raycaster
  raycaster.setFromCamera(mouse, camera);
  
  // Calculer les intersections
  const intersects = raycaster.intersectObjects(scene.children);
  
  if (intersects.length > 0) {
    const object = intersects[0].object;
    object.material.color.set(0xff0000);
  }
}

window.addEventListener('click', onMouseClick);
```

---

## 🔨 Importation de Modèles 3D

### GLTF Loader
```javascript
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader';

// Setup Draco (compression)
const dracoLoader = new DRACOLoader();
dracoLoader.setDecoderPath('/draco/');

const gltfLoader = new GLTFLoader();
gltfLoader.setDRACOLoader(dracoLoader);

// Charger modèle
gltfLoader.load(
  '/models/model.gltf',
  (gltf) => {
    const model = gltf.scene;
    model.scale.set(0.5, 0.5, 0.5);
    scene.add(model);
    
    // Animations
    if (gltf.animations.length > 0) {
      const mixer = new THREE.AnimationMixer(model);
      const action = mixer.clipAction(gltf.animations[0]);
      action.play();
      
      // Update dans animate()
      function animate() {
        const delta = clock.getDelta();
        mixer.update(delta);
        renderer.render(scene, camera);
        requestAnimationFrame(animate);
      }
    }
  },
  (progress) => {
    console.log((progress.loaded / progress.total) * 100 + '% loaded');
  },
  (error) => {
    console.error('Error loading model:', error);
  }
);
```

---

## 🎮 Performance

### Optimisations
```javascript
// ✅ Limiter le pixel ratio
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

// ✅ Frustum culling (automatique)
// Les objets hors du champ de vision ne sont pas rendus

// ✅ Réutiliser les géométries et matériaux
const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshStandardMaterial({ color: 0xff0000 });

for (let i = 0; i < 100; i++) {
  const mesh = new THREE.Mesh(geometry, material);
  mesh.position.x = (Math.random() - 0.5) * 10;
  scene.add(mesh);
}

// ✅ Instanced Mesh pour nombreuses copies
const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshStandardMaterial({ color: 0xff0000 });
const instancedMesh = new THREE.InstancedMesh(geometry, material, 1000);

for (let i = 0; i < 1000; i++) {
  const matrix = new THREE.Matrix4();
  matrix.setPosition(
    (Math.random() - 0.5) * 100,
    (Math.random() - 0.5) * 100,
    (Math.random() - 0.5) * 100
  );
  instancedMesh.setMatrixAt(i, matrix);
}

scene.add(instancedMesh);

// ✅ Dispose des ressources
geometry.dispose();
material.dispose();
texture.dispose();
renderer.dispose();
```

### Stats.js pour monitoring
```javascript
import Stats from 'three/examples/jsm/libs/stats.module';

const stats = Stats();
document.body.appendChild(stats.dom);

function animate() {
  stats.begin();
  
  renderer.render(scene, camera);
  
  stats.end();
  requestAnimationFrame(animate);
}
```

---

## 🔧 Debug avec lil-gui

```javascript
import GUI from 'lil-gui';

const gui = new GUI();

// Contrôles simples
gui.add(mesh.position, 'x', -3, 3, 0.01);
gui.add(mesh.position, 'y', -3, 3, 0.01);
gui.add(mesh.position, 'z', -3, 3, 0.01);

// Couleur
gui.addColor(material, 'color');

// Boolean
gui.add(mesh, 'visible');

// Dropdown
const params = { shape: 'box' };
gui.add(params, 'shape', ['box', 'sphere', 'torus']);

// Bouton
gui.add({ reset: () => mesh.position.set(0, 0, 0) }, 'reset');

// Dossiers
const folderPosition = gui.addFolder('Position');
folderPosition.add(mesh.position, 'x', -3, 3);
folderPosition.add(mesh.position, 'y', -3, 3);
folderPosition.add(mesh.position, 'z', -3, 3);
```

---

## 📚 Ressources

- [Three.js Documentation](https://threejs.org/docs/)
- [Three.js Examples](https://threejs.org/examples/)
- [Three.js Journey (Course)](https://threejs-journey.com/)
- [Discover Three.js](https://discoverthreejs.com/)
- [GLTF Models](https://sketchfab.com/)
