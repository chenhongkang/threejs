<script setup>
import {
  BoxBufferGeometry,
  Color,
  Mesh,
  MeshBasicMaterial,
  MeshStandardMaterial,
  PerspectiveCamera,
  DirectionalLight,
  PointLight,
  Scene,
  WebGLRenderer,
  TextureLoader,
  Clock
} from 'three';

import { onMounted } from 'vue'

let container
let scene;
let camera;
let cube;
let light;
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
  light = new DirectionalLight('white', 2);
  light.position.set(10, 10, 10);
  const geometry = new BoxBufferGeometry(2, 2, 2);
  const textureLoader = new TextureLoader();
  const texture = textureLoader.load(
  '../../public/uv2.png',
  );
  const material = new MeshStandardMaterial({
    map: texture
  });
  cube = new Mesh(geometry, material);
  debugger
  cube.material.opacity = 0.1;
  cube.material.transparent = true;
  scene.add(cube, light);
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
  cube.rotation.x += 0.5 * delta ;
  cube.rotation.y += 0.5 * delta ;
  cube.rotation.z += 0.5 * delta ;
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
