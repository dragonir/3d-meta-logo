# 3D META 🌌


## meta 0

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
