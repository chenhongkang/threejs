<script setup>
import {
  DodecahedronGeometry,
  Color,
  Mesh,
  MeshStandardMaterial,
  PerspectiveCamera,
  DirectionalLight,
  AmbientLight,
  HemisphereLight,
  Scene,
  WebGLRenderer,
  Clock
} from 'three';

import { onMounted } from 'vue'

let container
let scene;
let camera;
let dodecahedron;
let mainLight;
let ambientLight;
let renderer;
let animateFlug;
const clock = new Clock();

onMounted(() => {
  container = document.querySelector('#scene-container');
  initScene();
  initCamera();
  initMesh();
  initRender();
  startAnimate();
})

function initScene() {
  scene = new Scene();
  scene.background = new Color('skyblue');
}

function initCamera() {
  const fov = 35; // AKA Field of View
  const aspect = container.clientWidth / container.clientHeight;
  const near = 0.1; // the near clipping plane
  const far = 100; // the far clipping plane

  camera = new PerspectiveCamera(fov, aspect, near, far);
  camera.position.set(0, 0, 10);
}

function initMesh() {
    // ambientLight = new AmbientLight('white', 1);
    ambientLight = new HemisphereLight(
        'white', // bright sky color
        'darkslategrey', // dim ground color
        5, // intensity
    );
    mainLight = new DirectionalLight('white', 5);
    mainLight.position.set(10, 10, 10);
    const geometry = new DodecahedronGeometry(3, 0);
    const material = new MeshStandardMaterial({
        color: "red"
    });
    dodecahedron = new Mesh(geometry, material);
    scene.add(dodecahedron, ambientLight, mainLight);
}

function initRender() {
  const renderConfig = { antialias: true };
  renderer = new WebGLRenderer(renderConfig);
  renderer.setSize(container.clientWidth, container.clientHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
  container.append(renderer.domElement);
  renderer.render(scene, camera);
}

function startAnimate() {
  animateFlug = window.requestAnimationFrame(draw)
}

function endAnimate() {
  if(animateFlug) {
    window.cancelAnimationFrame(animateFlug);
    animateFlug = null;
  }
}

let lastTime = performance.now();
function draw() {
  let currentTime =  performance.now();
  const delta = clock.getDelta();
  console.log(delta, currentTime - lastTime);
  lastTime = currentTime
  dodecahedron.rotation.x += 0.5 * delta ;
  dodecahedron.rotation.y += 0.5 * delta ;
  dodecahedron.rotation.z += 0.5 * delta ;
  window.requestAnimationFrame(draw);
  renderer.render(scene, camera);
}
</script>

<template>
  <div id="scene-container"></div>
</template>

<style scoped>
#scene-container {
  position: absolute;
  width: 100%;
  height: 100%;

  background-color: skyblue;
}
</style>
