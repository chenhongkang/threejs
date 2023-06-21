<script setup>
import {
  CapsuleGeometry,
  Color,
  Mesh,
  MeshStandardMaterial,
  PerspectiveCamera,
  DirectionalLight,
  Scene,
  WebGLRenderer
} from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

import { onMounted } from 'vue'

let container
let scene;
let camera;
let capsule;
let light;
let renderer;
let controls;

onMounted(() => {
  container = document.querySelector('#scene-container');
  initScene();
  initCamera();
  initMesh();
  initRender();
  createControls();
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
  light = new DirectionalLight('white', 8);
  light.position.set(10, 10, 10);
  const geometry = new CapsuleGeometry(1, 1, 4, 8);
  const material = new MeshStandardMaterial({
    color: "green"
  });
  capsule = new Mesh(geometry, material);
  scene.add(capsule, light);
}

function initRender() {
  const renderConfig = { antialias: true };
  renderer = new WebGLRenderer(renderConfig);
  renderer.setSize(container.clientWidth, container.clientHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
  container.append(renderer.domElement);
  renderer.render(scene, camera);
}

function createControls() {
    controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    // controls.target.copy(capsule.position);
    controls.update();
}

function startAnimate() {
    window.requestAnimationFrame(draw)
}

function draw() {
    controls.update();
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
