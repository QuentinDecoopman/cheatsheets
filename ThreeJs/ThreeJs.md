# Three.js

## Documentation

https://threejs.org/docs/

## Installation

- Via npm (recommandé pour projets modernes) :

```sh
npm install three
```

- CDN (script tag) :

```html
<script src="https://unpkg.com/three@0.****/build/three.min.js"></script>
```

## Exemple minimal (ESM)

```js
import * as THREE from "three";

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const geometry = new THREE.BoxGeometry();
const material = new THREE.MeshStandardMaterial({ color: 0x0077ff });
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);

const light = new THREE.DirectionalLight(0xffffff, 1);
light.position.set(5, 10, 7.5);
scene.add(light);

camera.position.z = 5;

function animate() {
  requestAnimationFrame(animate);
  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;
  renderer.render(scene, camera);
}
animate();

window.addEventListener("resize", () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});
```

## Contrôles utiles

- `OrbitControls` (rotation/pan/zoom) :

```js
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true; // smoothing
```

## Loaders (format GLTF/GLB recommandé)

```js
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
const loader = new GLTFLoader();
loader.load("/models/model.glb", (gltf) => {
  scene.add(gltf.scene);
});
```

## Lumières courantes

- `AmbientLight` : lumière globale douce.
- `DirectionalLight` : comme le soleil (utilisé pour ombres).
- `PointLight` : lumière ponctuelle (ampoule).
- `HemisphereLight` : ciel/sol pour éclairage ambiant.

Exemple d'ombres :

```js
renderer.shadowMap.enabled = true;
light.castShadow = true;
cube.castShadow = true;
plane.receiveShadow = true;
```

## Matériaux utiles

- `MeshBasicMaterial` : pas d'éclairage (ne reçoit pas d'ombres).
- `MeshStandardMaterial` / `MeshPhysicalMaterial` : PBR, recommandé.
- `MeshPhongMaterial` : éclairage classique (brillant).

## Textures

```js
import { TextureLoader } from "three";
const tex = new TextureLoader().load("/textures/wood.jpg");
material.map = tex;
```

## Performance & bonnes pratiques

- Limiter le nombre de draw calls (instancing, merged geometries).
- Réduire la résolution des textures, utiliser mipmaps.
- Utiliser `requestAnimationFrame` et ne pas redessiner si pas nécessaire.
- Désactiver ombres si trop coûteux ou utiliser `PCFSoftShadowMap` / `VSM`.
- Profiling : devtools, `renderer.info` (triangles, calls).

## Outils et workflow

- `three` + bundler (Vite, Webpack, Rollup) pour projet moderne.
- `GLTF`/`GLB` : format recommandé pour les modèles. Export depuis Blender, glTF-Pipeline pour optimisations.
- `draco` compression pour réduire la taille des meshes (requiert loader spécifique).

## Commandes courantes / snippets terminal

- Installer three : `npm install three`
- Démarrer un serveur de développement (avec Vite) :

```sh
npx create-vite@latest my-three-app --template vanilla
cd my-three-app
npm install
npm run dev
```

- Build production : `npm run build`
- Ouvrir Prisma-like viewer (local) : utiliser `npx serve` pour servir les fichiers statiques :

```sh
npx serve ./dist -l 3000
```

## Liens et ressources

- Docs : https://threejs.org/docs/
- Exemples officiels : https://threejs.org/examples/
- glTF : https://www.khronos.org/gltf/
- Blender -> glTF export : https://docs.blender.org/manual/en/latest/addons/import_export/scene_gltf2.html


## Lien utiles

- https://www.arroway-textures.ch/
- https://3dtextures.me/
- https://www.poliigon.com/
