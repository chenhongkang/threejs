<script setup>
import {
    BoxBufferGeometry,
    Color,
    Mesh,
    Group,
    MeshStandardMaterial,
    MeshPhysicalMaterial,
    PerspectiveCamera,
    DirectionalLight,
    HemisphereLight,
    Scene,
    WebGLRenderer,
    // VectorKeyframeTrack,
    // NumberKeyframeTrack,
    // AnimationClip,
    // AnimationMixer,
    Clock,
    Vector2,
    Box3,
    Vector3,
    Raycaster
} from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import { TextGeometry } from 'three/addons/geometries/TextGeometry.js';
import { FontLoader } from 'three/addons/loaders/FontLoader.js';

import { onMounted } from 'vue'

class Box {
    cubeConfig = {
        gap: 0.2,
        lineCount: 6,
        cubeLength: 4
    };
    lastActiveCube;
    cubePosMap = new Map();
    cubeList = [];
    constructor(options) {
        this.raycaster = new Raycaster();
        this.hoverMouse = new Vector2(); 
        this.clickMouse = new Vector2(); 
        this.BoxGroup = new Group();
    }
    init() {
        this.getContainer();
        this.initScene();
        this.initCamera();
        this.initLight();
        this.initMesh();
        this.dataLayout();
        this.initGroup();
        this.initRender();
        this.createControls();
        this.registerMouseEvent();
    }
    getContainer() {
        this.container = document.querySelector('#scene-container');
    }
    initScene() {
        this.scene = new Scene();
        this.scene.background = new Color('white');
    }
    initCamera() {
        const fov = 35; // AKA Field of View
        const aspect = this.container.clientWidth / this.container.clientHeight;
        const near = 0.1; // the near clipping plane
        const far = 1000; // the far clipping plane

        this.camera = new PerspectiveCamera(fov, aspect, near, far);
        this.camera.position.set(0, 0, 50);
    }
    initLight() {
        this.light = new DirectionalLight('white', 4);
        this.light.position.set(10, 10, 10);
        this.partLight = new HemisphereLight('white', 'darkslategrey', 1);
        this.scene.add(this.light, this.partLight);
    }
    initMesh() {
        const { gap, lineCount, cubeLength } = this.cubeConfig;
        for(let i = 0; i < lineCount * lineCount * lineCount; i++) {
            const rowIndex = i % lineCount;
            const columIndex = Math.floor((i % (lineCount * lineCount)) / lineCount);
            const areaIndex = Math.floor(i / lineCount / lineCount);
            const x = rowIndex * (gap + cubeLength);
            const y = columIndex * (gap + cubeLength);
            const z = areaIndex * (gap + cubeLength);
            const material = new MeshPhysicalMaterial({
                color: "blue",
                transparent: true,
                opacity: 0.6
                // transmission: 0.8,
            });
            const geometry = new BoxBufferGeometry(cubeLength, cubeLength, cubeLength);
            const cube = new Mesh(geometry, material);
            cube.position.set(x, y, z);
            this.cubePosMap.set(cube, columIndex);
            this.cubePosMap.set(`${rowIndex},${columIndex},${areaIndex}`, {
                cube,
                originPos: {...cube.position}
            });
            this.cubeList.push(cube);
            this.BoxGroup.add(cube);
        }
    }
    dataLayout() {
        const x1data = [
            {
                text: "优秀"
            },
            {
                text: "良好"
            },
            {
                text: "合格"
            },
            {
                text: "不合格"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "良好"
            },
            {
                text: "合格"
            },
            {
                text: "不合格"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "良好"
            },
            {
                text: "合格"
            },
            {
                text: "不合格"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "良好"
            },
            {
                text: "合格"
            },
            {
                text: "不合格"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "良好"
            },
            {
                text: "合格"
            },
            {
                text: "不合格"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
            {
                text: "良好"
            },
            {
                text: "合格"
            },
            {
                text: "不合格"
            },
            {
                text: "优秀"
            },
            {
                text: "优秀"
            },
        ];
        const z1data = [
            {
                text: "光明街道",
                color: "blue"
            },
            {
                text: "光明区",
                color: "blue"
            },
            {
                text: "光明",
                color: "blue"
            },
            {
                text: "光明大街道",
                color: "blue"
            },
            {
                text: "大大街道",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "光明街道",
                color: "blue"
            },
            {
                text: "光明区",
                color: "blue"
            },
            {
                text: "光明",
                color: "blue"
            },
            {
                text: "光明大街道",
                color: "blue"
            },
            {
                text: "大大街道",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "光明街道",
                color: "blue"
            },
            {
                text: "光明区",
                color: "blue"
            },
            {
                text: "光明",
                color: "blue"
            },
            {
                text: "光明大街道",
                color: "blue"
            },
            {
                text: "大大街道",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "光明街道",
                color: "blue"
            },
            {
                text: "光明区",
                color: "blue"
            },
            {
                text: "光明",
                color: "blue"
            },
            {
                text: "光明大街道",
                color: "blue"
            },
            {
                text: "大大街道",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "光明街道",
                color: "blue"
            },
            {
                text: "光明区",
                color: "blue"
            },
            {
                text: "光明",
                color: "blue"
            },
            {
                text: "光明大街道",
                color: "blue"
            },
            {
                text: "大大街道",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "光明街道",
                color: "red"
            },
            {
                text: "光明区",
                color: "blue"
            },
            {
                text: "光明",
                color: "blue"
            },
            {
                text: "光明大街道",
                color: "green"
            },
            {
                text: "大大街道",
                color: "green"
            },
            {
                text: "光",
                color: "blue"
            }
        ];
        const y1data = [
        {
                text: "全区态势",
                color: "blue"
            },
            {
                text: "全态势",
                color: "blue"
            },
            {
                text: "态势",
                color: "blue"
            },
            {
                text: "全区全区态势",
                color: "blue"
            },
            {
                text: "全",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "全区态势",
                color: "blue"
            },
            {
                text: "全态势",
                color: "blue"
            },
            {
                text: "态势",
                color: "blue"
            },
            {
                text: "全区全区态势",
                color: "blue"
            },
            {
                text: "全",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "全区态势",
                color: "blue"
            },
            {
                text: "全态势",
                color: "blue"
            },
            {
                text: "态势",
                color: "blue"
            },
            {
                text: "全区全区态势",
                color: "blue"
            },
            {
                text: "全",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "全区态势",
                color: "blue"
            },
            {
                text: "全态势",
                color: "blue"
            },
            {
                text: "态势",
                color: "blue"
            },
            {
                text: "全区全区态势",
                color: "blue"
            },
            {
                text: "全",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "全区态势",
                color: "blue"
            },
            {
                text: "全态势",
                color: "blue"
            },
            {
                text: "态势",
                color: "blue"
            },
            {
                text: "全区全区态势",
                color: "blue"
            },
            {
                text: "全",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            },
            {
                text: "全区态势",
                color: "blue"
            },
            {
                text: "全态势",
                color: "blue"
            },
            {
                text: "态势",
                color: "blue"
            },
            {
                text: "全区全区态势",
                color: "blue"
            },
            {
                text: "全",
                color: "blue"
            },
            {
                text: "光",
                color: "blue"
            }
        ];
        const fontOptions = {
            size: 0.5,
            height: 0.01,
            curveSegments: 12,
            bevelEnabled: true,
            bevelThickness: 0.01,
            bevelSize: 0.01,
            bevelSegments: 1
        };
        const loader = new FontLoader();
        const font = loader.load(
            '/fonts/YouSheBiaoTiHei_Regular.json',
            font => {
                x1data.forEach((item, index) => {
                    const textGeometry = new TextGeometry(item.text, {font, ...fontOptions});
                    const material = new MeshStandardMaterial({
                        color: "skyblue"
                    });
                    const textMesh = new Mesh(textGeometry, material);
                    this.adjustMeshPosition(textMesh, index, "x1");
                    this.BoxGroup.add(textMesh);
                });
                z1data.forEach((item, index) => {
                    const textGeometry = new TextGeometry(item.text, {font, ...fontOptions});
                    const material = new MeshStandardMaterial({
                        color: "skyblue"
                    });
                    const textMesh = new Mesh(textGeometry, material);
                    this.adjustMeshPosition(textMesh, index, "z1");
                    this.BoxGroup.add(textMesh);
                });
                y1data.forEach((item, index) => {
                    const textGeometry = new TextGeometry(item.text, {font, ...fontOptions});
                    const material = new MeshStandardMaterial({
                        color: "skyblue"
                    });
                    const textMesh = new Mesh(textGeometry, material);
                    this.adjustMeshPosition(textMesh, index, "y1");
                    this.BoxGroup.add(textMesh);
                });
            }
        )
        
    }
    initGroup() {
        const { gap, lineCount, cubeLength } = this.cubeConfig;
        this.BoxGroup.position.set(
            -((cubeLength + gap) * lineCount - gap) / 2,
            -((cubeLength + gap) * lineCount - gap) / 2,
            0
        );
        this.scene.add(this.BoxGroup);
    }
    initRender() {
        const renderConfig = { antialias: true };
        this.renderer = new WebGLRenderer(renderConfig);
        this.renderer.setSize(this.container.clientWidth, this.container.clientHeight);
        this.renderer.setPixelRatio(window.devicePixelRatio);
        this.container.append(this.renderer.domElement);
        this.renderer.render(this.scene, this.camera);
    }
    createControls() {
        this.controls = new OrbitControls(this.camera, this.renderer.domElement);
        this.controls.enableDamping = true;
    }
    startAnimate() {
        if(this.animateFlug) { return; }
        this.animateFlug = window.requestAnimationFrame(this.draw.bind(this));
    }
    endAnimate() {
        window.cancelAnimationFrame(this.animateFlug);
        this.animateFlug = null;
    }
    draw() {
        const handleMousemoveAnimate = () => {
            this.raycaster.setFromCamera(this.hoverMouse, this.camera);
            const intersects = this.raycaster.intersectObjects(this.scene.children);
            if(this.lastActiveCube) {
                this.lastActiveCube.material.color.set("blue");
                this.lastActiveCube = null;
            }
            for (let i = 0; i < intersects.length; i ++) {
                if(intersects[i].object.geometry.type === "BoxGeometry") {
                    intersects[i].object.material.color.set("green");
                    this.lastActiveCube = intersects[i].object;
                    break;
                }
            }
        }
        const handleClickAnimate = () => {
            this.raycaster.setFromCamera(this.clickMouse, this.camera);
            const intersects = this.raycaster.intersectObjects(this.scene.children);
            for (let mapItem of this.cubePosMap) {
                const [key, value] = mapItem;
                if(typeof key === "string") {
                    value.cube.position.y = value.originPos.y;
                }
            }
            for (let i = 0; i < intersects.length; i ++) {
                if(intersects[i].object.geometry.type === "BoxGeometry") {
                    // intersects[i].object.material.color.set("red");
                    const currnetColumnIndex = this.cubePosMap.get(intersects[i].object);
                    for(let mapItem of this.cubePosMap) {
                        const [key, value] = mapItem;
                        if(typeof key === "string") {
                            const [rowIndex, columnIndex, areaIndex] = key.split(',');
                            if(Number(columnIndex) > currnetColumnIndex) {
                                value.cube.position.y = value.originPos.y + 10;
                            }
                        }
                    }
                    break;
                }
            }
        }
        this.controls.update();
        handleMousemoveAnimate();
        handleClickAnimate();
        
        this.renderer.render( this.scene, this.camera );
        window.requestAnimationFrame(this.draw.bind(this));
    }
    registerMouseEvent() {
        document.addEventListener('mousemove', this.onDocumentMouseMove.bind(this), false); 
        document.addEventListener('click', this.onDocumentClick.bind(this), false); 
    }
    removeMouseEvent() {
        // todo 这样清除不了
        document.removeEventListener('mousemove', this.onDocumentMouseMove.bind(this), false); 
        document.addEventListener('click', this.onDocumentClick.bind(this), false); 
    }
    onDocumentMouseMove(event) {  
        this.hoverMouse.x = (event.clientX / window.innerWidth) * 2 - 1;  
        this.hoverMouse.y = -(event.clientY / window.innerHeight) * 2 + 1;  
    }
    onDocumentClick(event) {  
        this.clickMouse.x = (event.clientX / window.innerWidth) * 2 - 1;
        this.clickMouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    }
    /**
     * 
     * @param {*} mesh 
     * @param {*} index 
     * @param {*} area : x0,x1,y0,y1,z0,z1表示立方体的6个面
     */
    adjustMeshPosition(mesh, index, area) {
        const { gap, cubeLength, lineCount } = this.cubeConfig;
        const size = this.getMeshSize(mesh);
        const row = Math.floor(index / lineCount);
        const column = index % lineCount;
        if(size.x > cubeLength) {
            return;
        } else {
            if(area === "z0") {
                mesh.position.x = row * (gap + cubeLength) + (cubeLength - size.x) / 2;
                mesh.position.y = column * (gap + cubeLength) + (cubeLength - size.y) / 2;
                mesh.position.z = 0;
            } else if(area === "z1") {
                mesh.position.x = row * (gap + cubeLength) + (cubeLength - size.x) / 2 - cubeLength / 2;
                mesh.position.y = column * (gap + cubeLength) + (cubeLength - size.y) / 2 - cubeLength / 2;
                mesh.position.z = (lineCount - 1) * (gap + cubeLength) + cubeLength / 2;
            } else if(area === "y1") {
                mesh.position.x = (lineCount - 1) * (gap + cubeLength) + cubeLength / 2;
                mesh.position.y = column * (gap + cubeLength) + (cubeLength - size.y) / 2 - cubeLength / 2;
                mesh.position.z =  row * (gap + cubeLength) + size.x / 2;
                // mesh.position.z = row * (gap + cubeLength) + (cubeLength - size.x) / 2;
                mesh.rotation.y = Math.PI / 2;
            } else if(area === "x1") {
                mesh.position.x = column * (gap + cubeLength) - (size.x / 2);
                mesh.position.y = (lineCount - 1) * (gap + cubeLength) + cubeLength / 2;
                mesh.position.z =  row * (gap + cubeLength) + (size.y / 2) ;
                column * (gap + cubeLength) + (cubeLength - size.y) / 2 - cubeLength / 2;
                mesh.rotation.x = -Math.PI / 2;
            }
        }
    }
    getMeshSize(mesh) {
        const box = new Box3();
        var size = new Vector3();
        box.setFromObject(mesh);
        return box.getSize(size);
    }
}

onMounted(() => {
    const box = new Box();
    box.init();
    box.startAnimate();
})

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
