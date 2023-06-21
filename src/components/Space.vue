<script setup>
import {
    SphereGeometry,
    Color,
    Mesh,
    Group,
    MeshStandardMaterial,
    PerspectiveCamera,
    DirectionalLight,
    HemisphereLight,
    PointLight,
    Scene,
    WebGLRenderer,
    TextureLoader,
    Clock
} from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

import { onMounted } from 'vue'

let container
let scene;
let camera;
let meshMap = {};
let mainLight;
let departLight;
let renderer;
let controls;
let animateFlug;
const clock = new Clock();

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
  camera.position.set(0, 0, 100);
}

function initMesh() {
    const earthGroup = new Group();
    const sunGroup = new Group();
    mainLight = new DirectionalLight('white', 8);
    mainLight.position.set(10, 10, 10);
    departLight = new HemisphereLight(
        'white', // bright sky color
        'darkslategrey', // dim ground color
        3, // intensity
    );
    const sunGeom = new SphereGeometry(10, 64, 32);
    const earthGeom = new SphereGeometry(3, 64, 32);
    const moonGeom = new SphereGeometry(1, 64, 32);
    const sunMaterial = new MeshStandardMaterial({
        color: "red"
    });
    const earthMaterial = new MeshStandardMaterial({
        color: "blue"
    });
    const moonMaterial = new MeshStandardMaterial({
        color: "yellow"
    });
    const sunMesh = new Mesh(sunGeom, sunMaterial);
    const earthMesh = new Mesh(earthGeom, earthMaterial);
    const moonMesh = new Mesh(moonGeom, moonMaterial);
    meshMap = {
        sun: sunMesh,
        earth: earthMesh,
        moon: moonMesh,
        sunGroup,
        earthGroup
    };
    earthGroup.add(earthMesh);
    earthGroup.add(moonMesh);
    sunGroup.add(sunMesh);
    sunGroup.add(earthGroup);
    earthGroup.position.set(20, 0, 0);
    moonMesh.position.set(0, 5, 0);
    scene.add(sunGroup, mainLight, departLight);
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
    animateFlug = window.requestAnimationFrame(draw)
}

function endAnimate() {
    if(animateFlug) {
        window.cancelAnimationFrame(animateFlug);
        animateFlug = null;
    }
}

function draw() {
    compute();
    renderer.render(scene, camera);
    window.requestAnimationFrame(draw);
}

function compute() {
    const delta = clock.getDelta();
    meshMap.sunGroup.rotation.y += 0.5 * delta;
    meshMap.earthGroup.rotation.x += 4 * delta;
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
