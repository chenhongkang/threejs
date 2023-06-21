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
    VectorKeyframeTrack,
    NumberKeyframeTrack,
    AnimationClip,
    AnimationMixer,
    Clock,
    Vector2,
    Raycaster
} from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

import { onMounted } from 'vue'

let container
let scene;
let camera;
let cubeList = [];
let light;
let renderer;
let controls;
let animateFlug;
const clock = new Clock();
const raycaster = new Raycaster();
const mouse = new Vector2(); 
document.addEventListener('mousemove', onDocumentMouseMove, false);  

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
    camera.position.set(0, 0, 50);
}

function initMesh() {
    light = new DirectionalLight('white', 8);
    light.position.set(10, 10, 10);
    for(let i = 0; i < 30; i++) {
        const material = new MeshStandardMaterial({
            color: "white"
        });
        const geometry = new BoxBufferGeometry(2, 2, 2);
        // const geometry = new BoxBufferGeometry(Math.random() + 1, Math.random() + 1, Math.random() + 1);
        const cube = new Mesh(geometry, material);
        cube.position.set(10 * Math.random(), 10 * Math.random(), 0);

        const scaleKF = new VectorKeyframeTrack(
            ".scale.z",
            [0, 1],
            [1, 2]
        );
        // const opacityKF = new NumberKeyframeTrack(
        //     ".material.opacity",
        //     [0, 1, 2, 3, 4, 5, 6],
        //     [0, 1, 0, 1, 0, 1, 0]
        // );
        const animationClip = new AnimationClip("extend", -1, [
            scaleKF,
            // opacityKF
        ]);
        const mixer = new AnimationMixer(cube);
        const action = mixer.clipAction(animationClip);
        scene.add(cube);
        cubeList.push({cube, action, mixer});
    }

    scene.add(light)
    
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

function draw() {
    controls.update();
    window.requestAnimationFrame(draw);
    raycaster.setFromCamera(mouse, camera);

	// calculate objects intersecting the picking ray
	const intersects = raycaster.intersectObjects(scene.children);
    for (let i = 0; i < cubeList.length; i ++) {
		cubeList[i].cube.material.color.set("white");
		cubeList[i].cube.position.z = 0;
	}
	for (let i = 0; i < intersects.length; i ++) {
		intersects[i].object.material.color.set("green");
		intersects[i].object.position.z = 2;
	}
    // intersects.map(
    //     item => cubeList
    //         .map(cubeData => cubeData.cube )
    //         .indexOf(item.object)
    // ).forEach(
    //     index => {
    //         cubeList[index].action.play();
    //         cubeList[index].mixer.update();
    //     }
    // )
	renderer.render( scene, camera );
}
    
function onDocumentMouseMove(event) {  
    mouse.x = (event.clientX / window.innerWidth) * 2 - 1;  
    mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;  
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
