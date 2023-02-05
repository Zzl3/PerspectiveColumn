<template>
  <div id="console">
    {{ camera_data }}
  </div>
  <br />
  <div id="screen1"></div>
  <div id="three"></div>
</template>

<script lang="ts">
import { integrityStream } from "ssri";
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
//创建一个三维场景
const scene = new THREE.Scene();

export default {
  data() {
    return {
      camera: null,
      renderer: null,
      camera_data: null,
      camera1: null,
      renderer1:null
    };
  },
  mounted() {
    this.$nextTick(this.init);
  },
  methods: {
    init() {
      //Light
      //添加光源
      const ambient = new THREE.AmbientLight(0xffffff, 0.5);
      const light1 = new THREE.PointLight(0xffffff, 0.4);
      const light2 = new THREE.PointLight(0xffffff, 0.4);

      //Scene
      scene.add(ambient);
      light1.position.set(200, 300, 400);
      scene.add(light1);
      light2.position.set(-200, -300, -400);
      scene.add(light2);

      //Camera
      //创建一个透视相机
      const width = window.innerWidth,
        height = window.innerHeight;
      this.camera = new THREE.PerspectiveCamera(45, width / height, 1, 1000);
      //设置相机位置
      this.camera.position.set(200, 200, 200);
      //设置相机方向
      this.camera.lookAt(0, 0, 0);

      // //创建辅助坐标轴
      // const axesHelper = new THREE.AxesHelper(100)
      // scene.add(axesHelper)

      // //创建一个物体（形状）
      // const geometry = new THREE.BoxGeometry(100, 100, 100)
      // //创建材质（外观）
      // const material = new THREE.MeshLambertMaterial({
      //   color: 0x00ffff,//设置材质颜色
      //   transparent: true,//开启透明度
      //   opacity: 0.5 //设置透明度
      // })
      // //创建一个网格模型对象
      // const mesh = new THREE.Mesh(geometry, material)
      // //把网格模型添加到三维场景
      // scene.add(mesh)

      //Renderer
      //创建一个WebGL渲染器
      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setSize(width, height);
      this.renderer.render(scene, this.camera);
      document.getElementById("three")?.appendChild(this.renderer.domElement);

      //创建摄像头1
      const near = 1;
      const far = 10000;
      this.camera1 = new THREE.PerspectiveCamera(45, 500 / 250, near, far);
      this.camera1.setViewOffset(500, 250, 0, 0, 500, 250);
      this.camera1.position.z = 0; // z轴看向原点，正对屏幕
      this.camera1.lookAt(0, 0, 0)
      //创建渲染器1
      this.renderer1 = new THREE.WebGLRenderer();
      this.renderer1.setSize(500, 250);
      this.renderer1.render(scene, this.camera1);
      document.getElementById("screen1")?.appendChild(this.renderer1.domElement);

      //Object
      // 加载停车场地图模型
      const loader = new GLTFLoader();
      loader.load("model/park.glb", (gltf) => {
        //将模型添加至场景
        scene.add(gltf.scene);
        //设置模型位置
        gltf.scene.position.set(0, 0, 0);
        this.renderer.render(scene, this.camera);
        this.camera_data = this.camera.position;
      });

      //靠近红色区域的车
      loader.load("model/objects/Audi_A8_2014_2.glb", (gltf) => {
        //将模型添加至场景
        scene.add(gltf.scene);
        //设置模型位置
        gltf.scene.position.set(0, -1.2, -3);
        gltf.scene.rotation.set(0, (-90 / 180) * Math.PI, 0);
        //设置模型运动
        setInterval(() => {
          gltf.scene.position.x += 0.02;
          console.log(gltf.scene.position.x);
          if (gltf.scene.position.x > 15) {
            gltf.scene.position.x = 0;
          }
          this.renderer.render(scene, this.camera);
        }, 20);

        this.renderer.render(scene, this.camera);
      });

      //远离红色区域的车
      loader.load("model/objects/Audi_A8_2014_2.glb", (gltf) => {
        //将模型添加至场景
        scene.add(gltf.scene);
        //设置模型位置
        gltf.scene.position.set(0, -1.2, 14);
        gltf.scene.rotation.set(0, (-90 / 180) * Math.PI, 0);
        //设置模型运动
        setInterval(() => {
          gltf.scene.position.x += 0.02;
          console.log(gltf.scene.position.x);
          if (gltf.scene.position.x > 15) {
            gltf.scene.position.x = 0;
          }
          this.renderer.render(scene, this.camera);
        }, 20);

        this.renderer.render(scene, this.camera);
      });

      //增加1
      loader.load("model/objects/Audi_A8_2014_2.glb", (gltf) => {
        //将模型添加至场景
        scene.add(gltf.scene);
        //设置模型位置
        gltf.scene.position.set(15, 1.4, 31); //第一个数字竖直方向；第二个数字铅垂方向;第三个数字水平方向
        gltf.scene.rotation.set(0, (90 / 180) * Math.PI, 0); //调整方向
        //设置模型运动
        setInterval(() => {
          gltf.scene.position.x -= 0.02;
          gltf.scene.position.y -= 0.0017;
          console.log(gltf.scene.position.x);
          console.log(gltf.scene.position.y);
          if (gltf.scene.position.x <= -15) {
            gltf.scene.position.x = 15;
          }
          if (gltf.scene.position.y <= -1.2) {
            gltf.scene.position.y = 1.4;
          }
          this.renderer.render(scene, this.camera);
        }, 20);
        this.renderer.render(scene, this.camera);
      });

      //增加2，左右运动,完成！
      loader.load("model/objects/Audi_A8_2014_2.glb", (gltf) => {
        //将模型添加至场景
        scene.add(gltf.scene);
        //设置模型位置
        gltf.scene.position.set(-21, -1.2, -5);
        gltf.scene.rotation.set(0, (180 / 180) * Math.PI, 0); //调整方向
        //设置模型运动
        setInterval(() => {
          gltf.scene.position.z += 0.02;
          console.log(gltf.scene.position.z);
          if (gltf.scene.position.z > 25) {
            gltf.scene.position.z = -5;
          }
          this.renderer.render(scene, this.camera);
        }, 20);

        this.renderer.render(scene, this.camera);
      });

      //增加摄像头模型,显示太小
      loader.load("model/objects/security_camera.glb", (gltf) => {
        //将模型添加至场景
        scene.add(gltf.scene);
        //设置模型位置
        gltf.scene.position.set(20, 3, -3);
        //放大
        gltf.scale.set(20, 20, 20); //没啥用像
        this.renderer.render(scene, this.camera);
      });

      //Controls
      //鼠标控制
      const controls = new OrbitControls(this.camera, this.renderer.domElement);
      //添加阻尼
      controls.enableDamping = true;
      controls.dampingFactor = 0.1;
      //监听鼠标事件
      controls.addEventListener("change", () => {
        this.renderer.render(scene, this.camera);
        this.camera_data = this.camera.position;
      });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#three {
  width: 100%;
  height: 90%;
  /* display: flex; */
}
#screen1 {
  z-index: 999999;
  position: fixed;
  right: 0;
  top: 30px;
  background-color: red;
  width: 500px;
  height: 250px;
}
#console {
  width: 100%;
  height: 10%;
  /* display: flex; */
}
</style>
