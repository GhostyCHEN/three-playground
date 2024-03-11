<template>
  <canvas class="webgl"></canvas>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
export default {
  name: 'Planet',
  mounted () {
    // 定义渲染尺寸
    const sizes = {
      width: window.innerWidth / 2,
      height: window.innerHeight / 2
    }
    // 初始化渲染器
    const canvas = document.querySelector('canvas.webgl');
    const renderer = new THREE.WebGLRenderer({
      canvas: canvas
    });
    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    // 初始化场景
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x1a1a1a);
    // 场景雾化
    scene.fog = new THREE.Fog(0x1a1a1a, 1, 1000);

    // 初始化相机
    const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 0.1, 1000);
    camera.position.set(20, 100, 450);
    scene.add(camera);

    // 页面缩放事件监听
    window.addEventListener('resize', () => {
      sizes.width = window.innerWidth / 2;
      sizes.height = window.innerHeight / 2;
      renderer.setSize(sizes.width, sizes.height);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      camera.aspect = sizes.width / sizes.height;
      camera.updateProjectionMatrix();
    })

    // 初始化镜头轨道控制器
    const controls = new OrbitControls(camera, renderer.domElement);
    // 允许移动惯性
    controls.enableDamping = true;

    // 初始化光源
    const light = new THREE.AmbientLight(0xdeedff, 1.5);
    scene.add(light);

    // 初始化星球
    const SphereMaterial = new THREE.MeshLambertMaterial({
      color: 0xff0000, // 材质的颜色
      wireframe: true, // 是否以线框模式渲染几何体
    });
    const SphereGeometry = new THREE.SphereGeometry(80, 32, 32);
    const planet = new THREE.Mesh(SphereGeometry, SphereMaterial);
    scene.add(planet);

    // 创建星轨
    const TorusGeometry = new THREE.TorusGeometry(150, 8, 2, 120);
    const TorusMaterial = new THREE.MeshBasicMaterial({
      color: 0x40a9ff,
      wireframe: true,
    });
    const ring = new THREE.Mesh(TorusGeometry, TorusMaterial);
    ring.rotation.x = Math.PI / 2;
    ring.rotation.y = -0.15 * (Math.PI / 2)
    scene.add(ring)

    // 创建卫星
    const IcoGeometry = new THREE.IcosahedronGeometry(16, 0);
    const IcoMaterial = new THREE.MeshToonMaterial({
      color: 0xfffc00,
      wireframe: true,
    });
    const satellite = new THREE.Mesh(IcoGeometry, IcoMaterial);
    scene.add(satellite);

    // 创建星星
    // const stars = new THREE.Group();
    // for (let i = 0; i < 500; i++) {
    //   const geometry = new THREE.IcosahedronGeometry(Math.random() * 2, 0);
    //   const material = new THREE.MeshToonMaterial({ color: 0xeeeeee });
    //   const mesh = new THREE.Mesh(geometry, material);
    //   mesh.position.x = (Math.random() - 0.5) * 700;
    //   mesh.position.y = (Math.random() - 0.5) * 700;
    //   mesh.position.z = (Math.random() - 0.5) * 700;
    //   mesh.rotation.x = Math.random() * 2 * Math.PI;
    //   mesh.rotation.y = Math.random() * 2 * Math.PI;
    //   mesh.rotation.z = Math.random() * 2 * Math.PI;
    //   stars.add(mesh);
    // }
    // 创建粒子系统星星
    const geometry = new THREE.BufferGeometry();
    const vertices = [];
    // 创建 1000 个粒子
    for (let i = 0; i < 10000; i++) {
      const x = THREE.MathUtils.randFloatSpread(1000);
      const y = THREE.MathUtils.randFloatSpread(1000);
      const z = THREE.MathUtils.randFloatSpread(1000);
      vertices.push(x, y, z);
    }
    geometry.setAttribute('position', new THREE.Float32BufferAttribute(vertices, 3));
    const material = new THREE.PointsMaterial({ color: 0xeeeeee });
    const stars = new THREE.Points(geometry, material);
    scene.add(stars);


    let rot = 0
    const axis = new THREE.Vector3(0, 0, 1);
    const tick = () => {
      // 更新渲染器
      renderer.render(scene, camera);
      // 给网格模型增加转动动画
      rot += Math.random() * 0.8
      const radian = (rot * Math.PI) / 180

      // 星球位置动画
      planet && (planet.rotation.y += 0.005)
      // 星轨轨道环绕动画
      ring && ring.rotateOnAxis(axis, Math.PI / 400)
      // 卫星位置动画
      satellite.position.x = 250 * Math.sin(radian)
      satellite.position.y = 100 * Math.cos(radian)
      satellite.position.z = -100 * Math.cos(radian)
      satellite.rotation.x += 0.005
      satellite.rotation.y += 0.005
      satellite.rotation.z -= 0.005
      // 星星动画
      stars.rotation.y += 0.0009;
      stars.rotation.z += 0.0003;
      controls.update();
      // 页面重绘调用自身
      window.requestAnimationFrame(tick);
    }
    tick()
  },
}
</script>