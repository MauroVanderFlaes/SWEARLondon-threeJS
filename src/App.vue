<template>
  <div class="colorContainer">
    <p>Inside: {{ insideSelectedColor }}</p>
    <input type="color" v-model="insideSelectedColor" @input="changeModelColor('inside')" />

    <p>Outside 1: {{ outside_1SelectedColor }}</p>
    <input type="color" v-model="outside_1SelectedColor" @input="changeModelColor('outside_1')" />

    <p>Outside 2: {{ outside_2SelectedColor }}</p>
    <input type="color" v-model="outside_2SelectedColor" @input="changeModelColor('outside_2')" />

    <p>Outside 3: {{ outside_3SelectedColor }}</p>
    <input type="color" v-model="outside_3SelectedColor" @input="changeModelColor('outside_3')" />

    <p>Laces Color: {{ lacesSelectedColor }}</p>
    <input type="color" v-model="lacesSelectedColor" @input="changeModelColor('laces')" />

    <p>Sole Bottom Color: {{ sole_bottomSelectedColor }}</p>
    <input type="color" v-model="sole_bottomSelectedColor" @input="changeModelColor('sole_bottom')" />

    <p>Sole Top Color: {{ sole_topSelectedColor }}</p>
    <input type="color" v-model="sole_topSelectedColor" @input="changeModelColor('sole_top')" />

    <div ref="sceneContainer"></div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

import * as THREE from "three";

//import GLFT loader
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";

// Import DRACOLoader
import { DRACOLoader } from "three/examples/jsm/loaders/DRACOLoader.js";

//import orbit controls 
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

const insideSelectedColor = ref('#ffffff');
const outside_1SelectedColor = ref('#ffffff');
const outside_2SelectedColor = ref('#ffffff');
const outside_3SelectedColor = ref('#ffffff');
const lacesSelectedColor = ref('#ffffff');
const sole_bottomSelectedColor = ref('#ffffff');
const sole_topSelectedColor = ref('#ffffff');

const draco = new DRACOLoader();
draco.setDecoderConfig({ type: 'js' });
draco.setDecoderPath('https://www.gstatic.com/draco/versioned/decoders/1.5.6/');

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

//add orbit controls
const controls = new OrbitControls(camera, renderer.domElement);

//add GLTF model
const gltfLoader = new GLTFLoader();
gltfLoader.setDRACOLoader(draco); // Attach DRACOLoader to GLTFLoader

onMounted(() => {
  // Initialize the renderer and add it to the DOM
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.querySelector("#app").appendChild(renderer.domElement);

  // Set up the GLTF model
  gltfLoader.load("/models/shoe.glb", (gltf) => {
    gltf.scene.position.set(0, 0, 0);
    gltf.scene.rotation.y = 1;
    gltf.scene.scale.set(6, 6, 6);

    let shoe = gltf.scene.children[0];

    // Traverse the children of the loaded model
    shoe.traverse((child) => {
      if (child.isMesh) {
        console.log(child.name);
        if (child.name == "inside") {
          child.material.color.set(insideSelectedColor.value);
        }
        if (child.name == "outside_1") {
          child.material.color.set(outside_1SelectedColor.value);
        }
        if (child.name == "outside_2") {
          child.material.color.set(outside_2SelectedColor.value);
        }
        if (child.name == "outside_3") {
          child.material.color.set(outside_3SelectedColor.value);
        }
        if (child.name == "laces") {
          child.material.color.set(lacesSelectedColor.value);
        }
        if (child.name == "sole_bottom") {
          child.material.color.set(sole_bottomSelectedColor.value);
        }
        if (child.name == "sole_top") {
          child.material.color.set(sole_topSelectedColor.value);
        }
        // Set up casting shadows for meshes
        child.castShadow = true;
      }
    });

    // Add the loaded model to the scene
    scene.add(gltf.scene);
  });

  camera.position.z = 2;

  //add ambient
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
  scene.add(ambientLight);

  //add clock
  const clock = new THREE.Clock();

  function animate() {
    const elapsedTime = clock.getElapsedTime();
    requestAnimationFrame(animate);

    renderer.render(scene, camera);
  }

  animate();
});

const changeModelColor = (target) => {
  scene.traverse((child) => {
    if (child.isMesh && child.name === target) {
      child.material.color.setStyle(getColorRef(target).value);
    }
  });
};

// Helper function to get the color ref based on the target name
const getColorRef = (target) => {
  switch (target) {
    case 'inside':
      return insideSelectedColor;
    case 'outside_1':
      return outside_1SelectedColor;
    case 'outside_2':
      return outside_2SelectedColor;
    case 'outside_3':
      return outside_3SelectedColor;
    case 'laces':
      return lacesSelectedColor;
    case 'sole_bottom':
      return sole_bottomSelectedColor;
    case 'sole_top':
      return sole_topSelectedColor;
    default:
      return ref('#ffffff');
  }
};
</script>

<style scoped>
/* Add your styles here */
.colorContainer {
  display: flex;
}
</style>
