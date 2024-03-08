<template>
  <canvas class="webgl"></canvas>
</template>
<script>
import * as THREE from 'three';

export default {
  name: 'HelloWorld',
  mounted () {
    this.cube()
  },
  methods: {
    cube () {
      // 渲染尺寸
      const sizes = {
        width: window.innerWidth / 2,
        height: window.innerHeight / 2
      }
      const canvas = document.querySelector('canvas.webgl');
      // 初始化渲染器，并将canvas容器作为参数传入到WebGL
      const renderer = new THREE.WebGLRenderer({
        canvas: canvas
      });
      // 设置渲染器的尺寸
      renderer.setSize(sizes.width, sizes.height);
      // 设置设备像素比，防止高分屏模糊
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      // 初始化场景，所有的网格对象，灯光，动画都需要放在场景中
      const scene = new THREE.Scene();
      // 初始化相机，分为正交相机OrthographicCamera和透视相机PerspectiveCamera
      // const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 0.1, 100)
      // camera.position.z = 3;
      const camera = new THREE.OrthographicCamera(-2, 2, 2, -2, 0.1, 1000);
      scene.add(camera)
      // 页面缩放监听，更新渲染和相机
      window.addEventListener('resize', () => {
        sizes.width = window.innerWidth / 2;
        sizes.height = window.innerHeight / 2;
        // 更新渲染
        renderer.setSize(sizes.width, sizes.height);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        // 更新相机
        camera.aspect = sizes.width / sizes.height;
        camera.updateProjectionMatrix();
      })

      // 创建一个网状模型
      const geometry = new THREE.BoxGeometry(1, 1, 1);
      // const material = new THREE.MeshBasicMaterial({ color: 0x03c03c });
      // const mesh = new THREE.Mesh(geometry, material);
      // scene.add(mesh);
      // 创建一个边缘几何体
      const edges = new THREE.EdgesGeometry(geometry);
      // 创建一个线材质
      const lineMaterial = new THREE.LineBasicMaterial({ color: 0x03c03c });
      // 创建一个线段对象
      const lineSegments = new THREE.LineSegments(edges, lineMaterial);
      scene.add(lineSegments);

      const tick = () => {
        // 更新渲染器
        renderer.render(scene, camera);
        // 使网状模型旋转
        lineSegments && (lineSegments.rotation.x += 0.01);
        lineSegments && (lineSegments.rotation.y += 0.01);
        // 页面重绘调用自身
        window.requestAnimationFrame(tick);
      }
      tick();
    },
  },
}
</script>

<style>
.webgl {
  position: fixed;
  top: 0;
  left: 0;
  outline: none;
}
</style>
