# 3D META LOGO 🌌

![banner](./assets/images/banner.png)

## 背景

![zack](./assets/images/zack.gif)

### 什么是元宇宙

「元宇宙」的内涵是吸纳了信息革命（5G/6G)、互联网革命（web3.0）、人工智能革命，以及 VR、AR、MR，特别是游戏引擎在内的虚拟现实技术革命的成果，向人类展现出构建与传统物理世界平行的全息数字世界的可能性；引发了信息科学、量子科学，数学和生命科学的互动，改变科学范式；推动了传统的哲学、社会学甚至人文科学体系的突破；囊括了所有的数字技术，包括区块链技术成就；丰富了数字经济转型模式，融合 DeFi、IPFS、NFT 等数字金融成果。

———数字资产研究院学术与技术委员会主任朱嘉明教授

源起

「Metaverse」一词由前缀「meta」（意为「超越」「元」）和词根「verse」（源于 universe「宇宙」）组成，直译而来便是「元宇宙」。这一概念最早出自于尼尔·斯蒂芬森 1992 年出版的科幻小说《雪崩》(Snow Crash)，指在一个脱离于物理世界，却始终在线的平行数字世界中，人们能够在其中以虚拟人物角色 (avatar) 自由生活。



什么是元宇宙



元宇宙这个词源于 1992 年尼尔·斯蒂芬森的《雪崩》，这本书描述了一个平行于现实世界的虚拟世界，Metaverse，所有现实生活中的人都有一个网络分身 Avatar。维基百科对元宇宙的描述是：通过虚拟增强的物理现实，呈现收敛性和物理持久性特征的，基于未来互联网，具有链接感知和共享特征的 3D 虚拟空间。



正如电影《头号玩家》的场景，在未来的某一天，人们可以随时随地切换身份，自由穿梭于物理世界和数字世界，在虚拟空间和时间节点所构成的「元宇宙」中学习、工作、交友、购物、旅游等。元宇宙，这个建立在区块链之上的虚拟世界，去中心化平台让玩家享有所有权和自治权。通过沉浸式的体验，让虚拟进一步接近现实。



## 效果

![preview](./assets/images/preview.gif)

> 在线预览

## 实现

### 版本1

```js
function init() {

  var stats = initStats();
  var renderer = initRenderer();
  var camera = initCamera();
  var scene = new THREE.Scene();
  initDefaultLighting(scene);
  var groundPlane = addLargeGroundPlane(scene)
  groundPlane.position.y = -30;

  var step = 0;
  var spGroup;

  var controls = new function () {
    this.appliedMaterial = applyMeshNormalMaterial
    this.castShadow = true;
    this.groundPlaneVisible = true;
    this.numberOfPoints = 20;
    this.deafultpoints = [
      [0, 0.4, -0.4],
      [0.4, 0, 0],
      [0.4, 0.8, 0.4],
      [0, 0.4, 0.4],
      [-0.4, 0, 0],
      [-0.4, 0.8, -0.4],
      [0, 0.4, -0.4]
    ]
    this.segments = 64;
    this.radius = 1;
    this.radiusSegments = 8;
    // 是否闭合
    this.closed = true;
    this.points = [];
    // we need the first child, since it's a multimaterial

    this.newPoints = function () {
      var points = [];
      for (var i = 0; i < controls.deafultpoints.length; i++) {
        var _x = controls.deafultpoints[i][0] * 22;
        var _y = controls.deafultpoints[i][1] * 22;
        var _z = controls.deafultpoints[i][2] * 22;
        points.push(new THREE.Vector3(_x, _y, _z));
      }
      controls.points = points;
      controls.redraw();
    };

    this.redraw = function () {
      redrawGeometryAndUpdateUI(gui, scene, controls, function() {
        return generatePoints(controls.points, controls.segments, controls.radius, controls.radiusSegments,
          controls.closed);
      });
    };

  };

  var gui = new dat.GUI();
  gui.add(controls, 'newPoints');
  gui.add(controls, 'numberOfPoints', 2, 200).step(1).onChange(controls.newPoints);
  gui.add(controls, 'segments', 0, 200).step(1).onChange(controls.redraw);
  gui.add(controls, 'radius', 0, 10).onChange(controls.redraw);
  gui.add(controls, 'radiusSegments', 0, 200).step(1).onChange(controls.redraw);
  gui.add(controls, 'closed').onChange(controls.redraw);
  gui.add(controls, 'appliedMaterial', {
    meshNormal: applyMeshNormalMaterial,
    meshStandard: applyMeshStandardMaterial
  }).onChange(controls.redraw)

  gui.add(controls, 'castShadow').onChange(function(e) {controls.mesh.castShadow = e})
  gui.add(controls, 'groundPlaneVisible').onChange(function(e) {groundPlane.material.visible = e})

  controls.newPoints();

  render();

  function generatePoints(points, segments, radius, radiusSegments, closed) {
    if (spGroup) scene.remove(spGroup)
    spGroup = new THREE.Object3D();
    var material = new THREE.MeshBasicMaterial({
      color: 0xff0000,
      transparent: false
    });
    points.forEach(function (point) {

      var spGeom = new THREE.SphereGeometry(0.1);
      var spMesh = new THREE.Mesh(spGeom, material);
      spMesh.position.copy(point);
      spGroup.add(spMesh);
    });
    // add the points as a group to the scene
    scene.add(spGroup);
    return new THREE.TubeGeometry(new THREE.CatmullRomCurve3(points), segments, radius, radiusSegments, closed);
  }

  function render() {
    stats.update();
    controls.mesh.rotation.y = step
    controls.mesh.rotation.x = step += 0.01
    controls.mesh.rotation.z = step

    if (spGroup) {
      spGroup.rotation.y = step
      spGroup.rotation.x = step
      spGroup.rotation.z = step
    }

    requestAnimationFrame(render);
    renderer.render(scene, camera);
  }
}
```

### 版本2

#### 建模

![bilibili](./assets/images/bilibili.png)

> https://www.bilibili.com/video/BV1Bf4y1T7ce

#### 代码实现

```html
<html>
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <title>3D META LOGO</title>
  <meta name=viewport content="width=device-width,initial-scale=1,maximum-scale=1,minimum-scale=1,user-scalable=no,viewport-fit=cover">
  <meta name="keywords" content="meta,facebook,Mark Elliot Zuckerberg">
  <meta name="description" content="meta,facebook,Mark Elliot Zuckerberg">
  <meta name="apple-mobile-web-app-capable" content="yes"  />
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <link rel="shortcut icon" href="./assets/images/logo.png">
  <link rel="apple-touch-icon" href="./assets/images/logo.png"/>
  <link rel="apple-touch-icon-precomposed" href="./assets/images/logo.png">
  <link rel="Bookmark" href="./assets/images/logo.png" />
  <style>
    :root {
      --themeColor: #0072ff;
      --secondColor: #00c6ff;
    }
    * {
      margin: 0;
      padding: 0;
    }
    html {
      background: var(--themeColor);
      background: -webkit-linear-gradient(to left, var(--themeColor) , var(--secondColor));
      background: linear-gradient(to left, var(--themeColor), var(--secondColor));
    }
    body {
      background: url('./assets/images/bg.png') no-repeat bottom right;
      background-size: contain;
      font-family: Arial, Helvetica, sans-serif;
    }
    .loading {
      position: fixed;
      height: 100%;
      width: 100%;
      left: 0;
      top: 0;
      background: #FFFFFF;
    }
    .loading .content {
      height: 330px;
      width: 540px;
      position: absolute;
      top: 50%;
      left: 50%;
      margin-top: -165px;
      margin-left: -270px;
    }
    .loading .content .banner {
      display: inline-block;
      width: 540px;
      height: 303px;
      background: url('./assets/images/meta.gif') no-repeat center;
      background-size: 100% 100%;
    }
    .loading .content {
      text-align: center;
      font-size: 20px;
      color: #666666;
    }
    .loading .content span {
      color: var(--themeColor);
      padding-left: 8px;
    }
    .logo {
      position: absolute;
      top: 56px;
      left: 56px;
      width: 321px;
      height: 64.875px;
      background: url('./assets/images/icon.png') no-repeat center;
      background-size: 100% 100%;
      filter: drop-shadow(0px 1px 5px rgba(255, 255, 255, .2));
    }
  </style>
</head>

<body>
  <i class="logo"></i>
  <div class="loading" id="loading">
    <div class="content">
      <i class="banner"></i>
      <p class="text">加载进度<span id="progress">0%</span></p>
    </div>
  <div>
  <script src="./assets/libs/three.js"></script>
  <script src="./assets/libs/loaders/FBXLoader.js"></script>
  <script src="./assets/libs/inflate.min.js"></script>
  <script src="./assets/libs/OrbitControls.js"></script>
  <script src="./assets/libs/stats.js"></script>
  <script>
    var container, stats, controls, compose;
    var camera, scene, renderer, light, clickableObjects = [];
    var clock = new THREE.Clock();
    var mixer, mixerArr = [], manMixer;
    init();
    animate();
    function init() {
      container = document.createElement('div');
      document.body.appendChild(container);
      // 场景
      scene = new THREE.Scene();
      scene.transparent = true;
      scene.opacity = 0.1;
      scene.fog = new THREE.Fog(0xa0a0a0, 200, 1000);
      // 透视相机：视场、长宽比、近面、远面
      camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
      camera.position.set(0, 4, 16);
      camera.lookAt(new THREE.Vector3(0, 0, 0));
      // 半球光源：创建室外效果更加自然的光源
      light = new THREE.HemisphereLight(0xefefef);
      light.position.set(0, 20, 0);
      scene.add(light);
      // 平行光
      light = new THREE.DirectionalLight(0x2d2d2d);
      light.position.set(0, 20, 10);
      light.castShadow = true;
      scene.add(light);
      // 环境光
      var ambientLight = new THREE.AmbientLight(0xffffff, .5);
      scene.add(ambientLight);
      // 网格
      var grid = new THREE.GridHelper(100, 100, 0xffffff, 0xffffff);
      grid.position.set(0, -10, 0);
      grid.material.opacity = 0.3;
      grid.material.transparent = true;
      scene.add(grid);
      // 加载模型
      var loader = new THREE.FBXLoader();
      var texLoader = new THREE.TextureLoader();
      loader.load('assets/models/meta.fbx', function (mesh) {
        mesh.traverse(function (child) {
          if (child.isMesh) {
            child.castShadow = true;
            child.receiveShadow = true;
            // 隐藏多余物体
            if (child.name === '球体' || child.name === '球体001') {
              child.visible = false
            }
            // 设置材质
            if (child.name === '贝塞尔圆') {
              clickableObjects.push(child)
              child.material = new THREE.MeshPhysicalMaterial({
                map: texLoader.load("./assets/images/metal.png"),
                metalness: .2,
                roughness: 0.1,
                exposure: 0.4
              });
            }
          }
        });
        mesh.rotation.y = Math.PI / 2;
        mesh.position.set(0, 1, 0);
        mesh.scale.set(0.05, 0.05, 0.05);
        scene.add(mesh);
        mesh.animations.map(item => {
          let animationType = item.name.split('|')[0];
          mesh.traverse(child => {
            if (child.name === animationType && (child.name === '贝塞尔圆')) {
              console.log(child.name)
              let mixer = new THREE.AnimationMixer(child);
              mixerArr.push(mixer);
              let animationClip = item;
              animationClip.duration = 8;
              let clipAction = mixer.clipAction(animationClip).play();
              animationClip = clipAction.getClip();
            }
          })
        })
      });

      // 加载人物
      loader.load('assets/models/man.fbx', function (mesh) {
        mesh.traverse(function (child) {
          if (child.isMesh) {
            child.castShadow = true;
            child.receiveShadow = true;
          }
        });
        mesh.rotation.y = Math.PI / 2;
        mesh.position.set(-14, -8.4, -3);
        mesh.scale.set(0.085, 0.085, 0.085);
        scene.add(mesh);
        manMixer = new THREE.AnimationMixer(mesh);
        let animationClip = mesh.animations[0];
        let clipAction = manMixer.clipAction(animationClip).play();
        animationClip = clipAction.getClip();
      }, res => {
        let progress = (res.loaded / res.total * 100).toFixed(0);
        document.getElementById('progress').innerText = progress + '%';
        if (Number(progress) === 100) {
          document.getElementById('loading').style.display = 'none';
        }
      }, err => {
        console.log(err)
      });

      renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
      renderer.setPixelRatio(window.devicePixelRatio);
      renderer.outputEncoding = THREE.sRGBEncoding;
      renderer.setSize(window.innerWidth, window.innerHeight);
      // 背景色设置为透明
      renderer.setClearAlpha(0);
      // 开启阴影
      renderer.shadowMap.enabled = true;
      container.appendChild(renderer.domElement);
      // 添加镜头控制器
      controls = new THREE.OrbitControls(camera, renderer.domElement);
      controls.target.set(0, 0, 0);
      controls.update();
      window.addEventListener('resize', onWindowResize, false);
      // stats：初始化性能插件
      stats = new Stats();
      container.appendChild(stats.dom);
    }
    // 屏幕缩放
    function onWindowResize() {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    }
    // 页面更新
    function animate() {
      renderer.render(scene, camera);
      let time = clock.getDelta();
      mixerArr.map(mixer => {
        mixer && mixer.update(time);
      })
      manMixer && manMixer.update(time)
      stats.update();
      requestAnimationFrame(animate);
    }
    // 增加点击事件
    //声明raycaster和mouse变量
    var raycaster = new THREE.Raycaster();
    var mouse = new THREE.Vector2();
    function onMouseClick(event) {
      // 通过鼠标点击的位置计算出raycaster所需要的点的位置，以屏幕中心为原点，值的范围为-1到1.
      mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      mouse.y = - (event.clientY / window.innerHeight) * 2 + 1;
      // 通过鼠标点的位置和当前相机的矩阵计算出raycaster
      raycaster.setFromCamera(mouse, camera);
      // 获取raycaster直线和所有模型相交的数组集合
      let intersects = raycaster.intersectObjects(clickableObjects);
      if (intersects.length > 0) {
        console.log(intersects[0].object)
        let selectedObj = intersects[0].object;
        selectedObj.material = new THREE.MeshStandardMaterial({
          color: `#${Math.random().toString(16).slice(-6)}`,
          metalness: Math.random(),
          roughness: Math.random()
        })
      }
    }
    window.addEventListener('click', onMouseClick, false);
  </script>
</body>
</html>
```

#### 加载模型

#### 动画

#### loading

#### 点击更换材质

#### 点击添加高亮边框

## 总结

## 参考资料

* [1]. [什么是元宇宙？](https://zhuanlan.zhihu.com/p/392257538)
* [2]. [Adobe免费模型下载](https://www.mixamo.com)
