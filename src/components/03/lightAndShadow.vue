<template>
  <div class="shadow_page">
    <div id="loading-text-intro">
      <p>Loading</p>
    </div>
    <div class="content" style="visibility: hidden">
      <nav class="header"></nav>
      <section class="section first">
        <div class="info"></div>
        <canvas id="canvas-container" class="webgl"></canvas>
      </section>
      <section class="section second">
        <div class="second-container"></div>
        <canvas id="canvas-container-details" class="webgl"></canvas>
      </section>
    </div>
  </div>
</template>

<script>
import * as TWEEN from '@tweenjs/tween.js'
import { Clock, Scene, LoadingManager, WebGLRenderer, SRGBColorSpace, Group, PerspectiveCamera, DirectionalLight, PointLight, MeshPhongMaterial } from 'three';
import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader.js';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
export default {
  name: 'LightAndShadow',
  mounted () {
    this.init()
  },
  methods: {
    init () {
      const section = document.getElementsByClassName('section')[0];
      let width = section.clientWidth;
      let height = section.clientHeight;
      // 初始化渲染器1
      const renderer = new WebGLRenderer({
        canvas: document.querySelector('#canvas-container'),
        antialias: true,
        alpha: true,
        // 提示用户代理怎样的配置更适用当前的WebGl环境
        powerPreference: 'high-performance',
      })
      renderer.setSize(width, height);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      // 定义渲染器是否在渲染每一帧之前自动清除其输出缓冲区
      renderer.autoClear = true;
      // 定义渲染器的输出编码
      renderer.outputEncoding = SRGBColorSpace;

      // 初始化渲染器2
      const renderer2 = new WebGLRenderer({
        canvas: document.querySelector('#canvas-container-details'),
        antialias: false,
      })
      renderer2.setSize(width, height);
      renderer2.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      renderer2.outputEncoding = SRGBColorSpace;

      // 初始化场景
      const scene = new Scene();
      // 初始化相机1
      const cameraGroup = new Group();
      scene.add(cameraGroup)
      const camera = new PerspectiveCamera(35, width / height, 1, 100);
      camera.position.set(19, 1.54, -.1);
      cameraGroup.add(camera);

      // 初始化相机2
      const camera2 = new PerspectiveCamera(35, width / height, 1, 100);
      camera2.position.set(3.2, 2.8, 3.2)
      camera.rotation.set(0, 1, 0)
      scene.add(camera2);

      // 页面缩放事件监听
      window.addEventListener('resize', () => {
        let section = document.getElementsByClassName('section')[0];
        camera.aspect = section.clientWidth / section.clientHeight;
        camera.updateProjectionMatrix();
        camera2.aspect = section.clientWidth / section.clientHeight;
        camera2.updateProjectionMatrix();
        renderer.setSize(section.clientWidth, section.clientHeight);
        renderer2.setSize(section.clientWidth, section.clientHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer2.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      })

      // 初始化加载管理器
      const loadingManager = new LoadingManager();
      loadingManager.onLoad = () => {
        document.querySelector('.content').style.visibility = 'visible';
        const yPosition = { y: 0 }
        const ftsLoader = document.querySelector('.lds-roller');
        const loadingCover = document.getElementById('loading-text-intro')
        // 隐藏加载页面动画
        new TWEEN.Tween(yPosition).to({ y: 100 }, 900).easing(TWEEN.Easing.Quadratic.InOut).start().onUpdate(() => {
          loadingCover.style.setProperty('transform', `translate(0,${yPosition.y}%)`)
        }).onComplete(() => {
          loadingCover.parentNode.removeChild(document.getElementById('loading-text-intro'))
          TWEEN.remove(this)
        })
        // 页面一相机添加入场动画
        new TWEEN.Tween(
          camera.position.set(0, 4, 2)
        ).to({ x: 0, y: 2.4, z: 5.8 }, 3500).easing(TWEEN.Easing.Quadratic.InOut).start().onComplete(() => {
          TWEEN.remove(this)
          document.querySelector('.header').classList.add('ended')
          document.querySelector('.description').add('ended')
        })
        ftsLoader.parentNode.removeChild(ftsLoader)
        window.scroll(0, 0)
      }

    }
  },
}
</script>

<style>
html,
body {
  overflow: hidden;
  background: #000000;
  font-family: abduction;
  cursor: none;
}
.shadow_page {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100%;
  scroll-behavior: smooth;
  overscroll-behavior: none;
  color: #fff;
  overflow: hidden auto;
}
.shadow_page .content .section {
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
  user-select: none;
  flex-direction: column;
  align-content: center;
  justify-content: center;
  align-items: flex-start;
  position: relative;
  z-index: 1;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  pointer-events: none;
  box-sizing: border-box;
}
.shadow_page .content .section .webgl {
  height: 100%;
  width: 100%;
}
.shadow_page .content .section.first {
  pointer-events: none;
  font-size: 2em;
  letter-spacing: 0.5em;
  text-align: center;
  width: 100%;
  display: flex;
  height: 100vh;
  align-content: center;
  justify-content: flex-end;
  align-items: center;
  flex-direction: column;
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
  user-select: none;
  position: relative;
  z-index: 1;
  background: linear-gradient(0deg, #050505 20%, rgba(5, 5, 5, 0) 50%);
  overflow: hidden;
}
.shadow_page .content .section.first .info {
  position: relative;
  z-index: 1;
}
.shadow_page .content .section.first .info .name {
  font-size: 2em;
  font-weight: 100;
  letter-spacing: 0.25em;
  font-style: italic;
  color: #03c03c;
}
.shadow_page .content .section.first .info .title {
  margin: 10px 0;
  font-weight: 100;
  letter-spacing: 0.4em;
  font-size: 1.8em;
}
.shadow_page .content .section.first .info .title::after {
  content: "";
  position: absolute;
  margin-top: 105px;
  left: calc(50% - 25px);
  width: 50px;
  height: 2px;
  background: rgba(255, 255, 255, 0.439);
}
.shadow_page .content .section.first .info .description {
  font-size: 0.45em;
  letter-spacing: 0;
  font-family: sans-serif;
  width: 80%;
  line-height: 28px;
  font-weight: lighter;
  margin: 32px auto 16px;
  color: rgba(201, 201, 201, 0.588);
  opacity: 0;
  transition: all 3.2s ease-in-out;
}
.shadow_page .content .section.first .info .description.ended {
  opacity: 1;
}
.shadow_page .content .section.first .webgl {
  pointer-events: none;
  position: absolute;
  top: 0;
  left: 0;
  outline: none;
  z-index: 0;
  height: 100vh;
  width: 100vw;
  margin: 0;
  padding: 0;
  background: #000;
  background: radial-gradient(circle at center center, #171717 0, #050505 58%);
}
.shadow_page .content .section.second {
  pointer-events: all;
  font-size: 2em;
  width: 100%;
  display: flex;
  height: 100vh;
  background: #141414;
  z-index: 1;
  margin: 0;
  padding: 0;
  overflow: hidden;
}
.shadow_page .content .section.second .second-container {
  pointer-events: all;
  width: 100%;
  display: flex;
  height: 100vh;
  margin: 0;
  padding: 0 10%;
  flex-direction: column;
  justify-content: center;
  z-index: 2;
  background: radial-gradient(
    circle at 90% center,
    rgba(5, 5, 5, 0) 30%,
    #141414 70%
  );
}
.shadow_page .content .section.second .webgl {
  pointer-events: none;
  position: absolute;
  top: 0;
  left: 0;
  outline: none;
  z-index: 0;
  height: 100%;
  width: 100%;
  margin: 0;
  padding: 0;
  pointer-events: all;
  overflow: hidden;
}
.lds-roller {
  width: 80px;
  height: 80px;
  position: absolute;
  top: 50%;
  left: 50%;
  margin-right: -50%;
  transform: translate(-50%, -50%);
  z-index: 5;
}
.lds-roller div {
  animation: lds-roller 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
  transform-origin: 40px 40px;
}
.lds-roller div:after {
  content: " ";
  display: block;
  position: absolute;
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #f9f0ec;
  margin: -4px 0 0 -4px;
}
.lds-roller div:nth-child(1) {
  animation-delay: -0.036s;
}
.lds-roller div:nth-child(1):after {
  top: 63px;
  left: 63px;
}
.lds-roller div:nth-child(2) {
  animation-delay: -0.072s;
}
.lds-roller div:nth-child(2):after {
  top: 68px;
  left: 56px;
}
.lds-roller div:nth-child(3) {
  animation-delay: -0.108s;
}
.lds-roller div:nth-child(3):after {
  top: 71px;
  left: 48px;
}
.lds-roller div:nth-child(4) {
  animation-delay: -0.144s;
}
.lds-roller div:nth-child(4):after {
  top: 72px;
  left: 40px;
}
.lds-roller div:nth-child(5) {
  animation-delay: -0.18s;
}
.lds-roller div:nth-child(5):after {
  top: 71px;
  left: 32px;
}
.lds-roller div:nth-child(6) {
  animation-delay: -0.216s;
}
.lds-roller div:nth-child(6):after {
  top: 68px;
  left: 24px;
}
.lds-roller div:nth-child(7) {
  animation-delay: -0.252s;
}
.lds-roller div:nth-child(7):after {
  top: 63px;
  left: 17px;
}
.lds-roller div:nth-child(8) {
  animation-delay: -0.288s;
}
.lds-roller div:nth-child(8):after {
  top: 56px;
  left: 12px;
}
#loading-text-intro {
  z-index: 3;
  position: absolute;
  width: 100vw;
  height: 100%;
  display: flex;
  align-content: center;
  justify-content: center;
  align-items: center;
  font-size: 12px;
  color: #f9f0ec;
  background: radial-gradient(circle at center center, #5d5d5d 0, #090909 58%);
  font-family: Arial, Helvetica, sans-serif;
}
#loading-text-intro.ended {
  transform: translateY(200%);
}
nav {
  width: 100%;
  padding: 1rem;
  position: fixed;
  z-index: 2;
}
span {
  display: inline-block;
  pointer-events: none;
  transition: transform 0.1s linear;
}
.cursor {
  pointer-events: none;
  position: fixed;
  top: 10px;
  left: 10px;
  padding: 10px;
  background: rgba(255, 255, 255, 0.3);
  backdrop-filter: blur(4px);
  border-radius: 50%;
  transform: translate(-50%, -50%);
  mix-blend-mode: difference;
  transition: transform 0.8s ease, opacity 0.6s ease;
  z-index: 2;
  border: 0.5px solid rgba(255, 255, 255, 0.1);
}
.a {
  display: inline-block;
  color: #fff;
  padding: 1rem;
  margin-right: 4rem;
  letter-spacing: 0.4em;
  font-size: 1.2em;
  transition: all 0.3s ease, color 0.3s ease;
}
nav.header .a:hover {
  cursor: pointer;
  color: #afafaf;
  transform: scale(1.1);
}
nav.header .a:hover ~ .cursor {
  transform: translate(-50%, -50%) scale(5);
  opacity: 0.1;
}
.dg.ac {
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
  user-select: none;
  z-index: 2 !important;
}
.header {
  position: absolute;
  top: -2em;
  left: 0;
  color: #fff;
  font-size: 0.8em;
  width: 100%;
  text-align: center;
  z-index: 2;
  opacity: 0;
  transition: all 1.8s ease-in-out;
  padding: 0;
  margin: 0;
}
.header.ended {
  top: 3em;
  opacity: 1;
}
.header > span {
  padding: 0 3.25em;
  letter-spacing: 0.4em;
  position: relative;
}
.header > span.active:after,
.first {
  position: absolute;
  left: 50%;
  -webkit-transform: translate3d(-50%, 0, 0);
  transform: translate3d(-50%, 0, 0);
}
.header > span.active:after {
  content: "";
  bottom: -10px;
  width: 20px;
  height: 2px;
  background: #fff;
}
.second-container > ul {
  list-style: none;
  display: inline-flex;
  padding: 0px;
  margin: 0px 0px 64px 64px;
  color: rgba(255, 255, 255, 0.11);
  z-index: 2;
}
.second-container > ul > li.active:after {
  content: "";
  top: 20px;
  width: 50px;
  height: 2px;
  background: #fff;
  position: relative;
  left: 0px;
  display: block;
}
.second-container > ul > li {
  padding-right: 32px;
  transition: all 0.8s ease-out;
  font-size: 1.2em;
}
.second-container > ul > li:hover {
  color: #f5f5f5;
  pointer-events: all;
  cursor: pointer;
}
.second-container > ul > li:hover ~ nav.header.ended.cursor {
  transform: translate(-50%, -50%) scale(5);
  opacity: 1;
}
.second-container > ul > li.active {
  color: #f5f5f5;
}
.second-container .text {
  font-size: 1.4em;
  width: 30%;
  color: rgba(255, 255, 255, 0.8);
  margin-left: 60px;
  height: 300px;
  line-height: 2;
  letter-spacing: 8px;
}
@media only screen and (max-width: 660px) {
  .a {
    padding: 10px;
    margin-right: 0rem;
    letter-spacing: 0.3em;
  }
  .footer {
    margin-bottom: 20px;
  }
  .header > span {
    padding: 0 1em;
  }
  .header {
    font-size: 0.6em;
  }
  .main-section .product-display h3 {
    width: 260px;
    font-size: 42px;
    margin-left: 30px;
    line-height: 45px;
  }
  .first > h1 {
    margin: 10px 0;
    font-weight: 100;
    letter-spacing: 0.2em;
    font-size: 13vw;
  }
  .first > p {
    width: 85%;
    line-height: 22px;
  }
  .second-container {
    padding: 0;
    justify-content: flex-end;
  }
  .second-container > ul {
    margin: 0px 0px 30px 30px;
    width: 80%;
  }
  .second-container > ul > li {
    padding-right: 20px;
    transition: all 0.8s ease-out;
    font-size: 20px;
  }
  .second-container > p {
    width: 85%;
    margin-left: 30px;
    line-height: 21px;
    margin-bottom: 40px;
  }
  .third > p {
    column-count: 1;
  }
}
@-moz-keyframes lds-roller {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
@-webkit-keyframes lds-roller {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
@-o-keyframes lds-roller {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
@keyframes lds-roller {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>