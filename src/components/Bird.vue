<script setup>
import {
  BoxBufferGeometry,
  Color,
  Mesh,
  MeshBasicMaterial,
  MeshStandardMaterial,
  PerspectiveCamera,
  DirectionalLight,
  HemisphereLight,
  Scene,
  WebGLRenderer,
  AnimationClip,
  AnimationMixer,
  Clock
} from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'


import { onMounted } from 'vue'

let container
let scene;
let camera;
let mainLight;
let partLight;
let renderer;
let animationList = [];
const loader = new GLTFLoader();
const clock = new Clock();

onMounted(async () => {
  container = document.querySelector('#scene-container');
  initScene();
  initCamera();
  await initModel();
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

async function initModel() {
    mainLight = new DirectionalLight('white', 3);
    mainLight.position.set(10, 10, 10);
    partLight = new HemisphereLight('white', 'darkslategrey', 1);
    const [parrotData, flamingoData, storkData] = (await Promise.all([
        loader.loadAsync('/models/Parrot.glb'),
        loader.loadAsync('/models/Flamingo.glb'),
        loader.loadAsync('/models/Stork.glb'),
    ])).map(setupModel);
    parrotData.position.set(0, 0, 2.5);
    flamingoData.position.set(7.5, 0, -10);
    storkData.position.set(0, -2.5, -10);
    scene.add(parrotData, flamingoData, storkData, mainLight, partLight);
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
        animationList.forEach(item => {
            item.action.play();
        })
        window.requestAnimationFrame(draw)
}

function draw() {
    const delta = clock.getDelta();
    animationList.forEach(item => item.mixer.update(delta));
    window.requestAnimationFrame(draw);
    renderer.render(scene, camera);
}

function setupModel(data) {
    console.log(data);
    const model = data.scene.children[0];
    const animateData = data.animations[0];
    const mixer = new AnimationMixer(model);
    const action = mixer.clipAction(animateData);
    animationList.push({mixer, action});
    return model;
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
