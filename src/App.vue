<template>
  <div ref="threeContainer" class="three-container"></div>
</template>

<script>
import * as THREE from 'three';
import * as BufferGeometryUtils from 'three/addons/utils/BufferGeometryUtils.js';
import { MapControls } from 'three/addons/controls/MapControls.js';
//import { FloatType, MeshStandardMaterial, PMREMGenerator, Scene, PerspectiveCamera, WebGLRenderer, Color, ACESFilmicToneMapping, SRGBEncoding} from 'three';
//import { RGBELoader } from 'https://cdn.skypack.dev/three-stdlib@2.8.5/loaders/RGBELoader';
import {ACESFilmicToneMapping} from 'three';



export default {
  name: 'App',
  mounted() {
    this.initThree();
    this.animate();
    window.addEventListener('resize', this.onWindowResize);
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.onWindowResize);
  },
  methods: {
    initThree() {
    // Create scene
    this.scene = new THREE.Scene();

    // Create camera
    this.camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    this.camera.position.z = 5;

    // Create renderer
    this.renderer = new THREE.WebGLRenderer({ antialias: true });
    this.renderer.setSize(window.innerWidth, window.innerHeight);
    this.renderer.setPixelRatio(window.devicePixelRatio);
    this.$refs.threeContainer.appendChild(this.renderer.domElement);

    this.renderer.toneMapping = ACESFilmicToneMapping;

    // Add a cube
    const geometry = new THREE.BoxGeometry();
    const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
    this.cube = new THREE.Mesh(geometry, material);
    this.scene.add(this.cube);

    // Add a plane
    const planeGeometry = new THREE.PlaneGeometry(4, 4);
    const planeMaterial = new THREE.MeshBasicMaterial({
      color: 0x745DD9,
      side: THREE.DoubleSide,
      opacity: 0.25,
      transparent: true
    });
    const plane = new THREE.Mesh(planeGeometry, planeMaterial);
    plane.rotation.x = Math.PI / 2;
    this.scene.add(plane);

    // Controls
    this.controls = new MapControls(this.camera, this.renderer.domElement);
    this.controls.enableDamping = true;
    this.controls.enableRotate = true;
    this.controls.enablePan = true;
    this.controls.enableZoom = true;

    // Start animation loop
    this.animate = this.animate.bind(this);
    this.animate();
    },
    hexGeometry(height, position){
        let geo = new THREE.CylinderGeometry(1, 1, height, 6, 1, false);
        geo.translate(position.x, height * 0.5, position.y);
        return geo;
    },
    makeHex(height, position) {
    let geo = this.hexGeometry(height, position);
    this.hexagonalGeometries = BufferGeometryUtils.mergeGeometries([this.hexagonalGeometries, geo]);
    },
    animate() {
      requestAnimationFrame(this.animate);
      this.controls.update();
      this.cube.rotation.x += 0.01;
      this.cube.rotation.y += 0.01;
      this.renderer.render(this.scene, this.camera);
    },
    onWindowResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();
      this.renderer.setSize(window.innerWidth, window.innerHeight);
    }
  }
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  overflow: hidden;
}

.three-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}
</style>
