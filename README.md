# learning-3d-graphics-on-the-web-with-three-js
https://www.linkedin.com/learning/learning-3d-graphics-on-the-web-with-three-js/


3D graphics are a core part of virtual reality, augmented reality, which is a core of Metaverse
### Learning objectives
- Building a simple scene
- Creating three.js 3D objects
- Adding light and shadows
- Animating with Tween.js
- Working with materials and textures
- Editing and importing geometries
- Creating a particle system
- Post-processing with effects and shaders

### Live on Codesandbox
https://codesandbox.io/s/laughing-faraday-gietv0?file=/chapter-04/04_04/end/index.html

---

## Transcript
### 2 Three.js Scene Object
<details>
<summary> details </summary>

- Three.js objects
  - Most objects in Three.js are instances of the object Three base class. 
  - This means that they share some common properties with each other. Now, if you are to type scene inside the console, we can see the properties and metas that are associated with the scene object.
  - but it is not reflective rendering until we use requestAnimationFrame()
- requestAnimationFrame() function
  - What we did here (update) is that we set up a function that gets recursively called by the request animation frame function, so that things will get continuously rendered, about 60 times a second until we close the browser.
    ```js
    function update(renderer, scene, camera) {
      renderer.render(
        scene,
        camera
      );
      requestAnimationFrame(function() {
        update(renderer, scene, camera);
      })
    }
    ```
  - As you are continuously rendering the scene, any change we make will immediately take effect, creating real-time interaction and animation possibilities.
- Other Object3D properties
  - children and parent properties. The scene object happens to be the parent for all the objects, as everything else gets added to the scene object using its "add" method. we could add objects inside other objects as well. This helps us to establish a parent child relationship between different objects. So here, instead of adding the box to the scene, I could be adding the box to the plane. Doing so would make the box children of the plane object. 
    ```js
    // scene.add(box);
    plane.add(box);
    ```
  - The name property allows us to assign a name to objects, which makes it easy to find them in the scene using the "get object by name" method.
    ```js
    somePlane.name = 'plane-1';
    var plane = scene.getObjectByName('plane-1');
    ```
  - "traverse" method. Traverse allows us to execute a given callback function on the current object, and all of his descendants. It's useful when you are working with lots of objects, and if you wanted to call a function on all the children of a certain object. 
    ```js
    scene.traverse((child) => child.scale.x = 0.01)
    ```
- Adding fog to the scene
  - On the scene objects on this fog property, I will be calling this fog objects FogExp2 objects that takes two parameters. The first parameter is the color of the fog. Which I'm going to be using white color. The second parameter is the density of the fog, Which I'm going to be using 0.2 for this purpose.
  ```js
  scene.fog = new THREE.FogExp2(0xffffff, 0.2);
  ```
</details>

### 3 Lights
- <details>
  <summary> dat.GUI & orbit controls </summary>
    <img width='400' src="https://user-images.githubusercontent.com/24782000/163728626-31c3315c-319d-423b-add2-1de6013df721.png" />
  </details>
- <details>
  <summary> Shadows </summary>
  
    [Shadows](chapter-03/03_05/end/main.js)
    Rendering shadow is unfortunately not too straightforward.
    - First we need to tell the renderer to start rendering shadows. `renderer.shadowMap.enable = true` 
    - And then we need to tell the light to cast shadows.  `light.castShadow = true;`
    - Then we should also tell the objects to cast or receive shadows. In our case we will make the box cast shadows `mesh.castShadow = true`, and the plane 
  </details>
- <details>
  <summary> group </summary>
  
    [group as container](chapter-03/03_06/end/main.js)
    ```js
      // work as a container
      var group = new THREE.Group();
      group.add(obj)
    ```
    <img width="566" alt="image" src="https://user-images.githubusercontent.com/24782000/163734109-ad237c65-07f8-400b-bdc2-c1b01f9a8d27.png">
  </details>
- <details>
  <summary> SpotLight </summary>
  
    [define](chapter-03/03_07/end/main.js)
    ```js
      function getSpotLight(intensity) {
        var light = new THREE.SpotLight(0xffffff, intensity);
        light.castShadow = true;

        light.shadow.bias = 0.001; // Which parameter do you need to adjust to remove some of the common shadow map artifacts?
        light.shadow.mapSize.width = 2048; // resolution, default 1024*1024
        light.shadow.mapSize.height = 2048;

        return light;
      }
    ```
    <img width="566" alt="image" src="https://user-images.githubusercontent.com/24782000/163734109-ad237c65-07f8-400b-bdc2-c1b01f9a8d27.png">
  </details>
- <details>
  <summary> DirectionalLight </summary>
  
    [means parallel light](chapter-03/03_08/end/main.js)
    ```js
      function getDirectionalLight(intensity) {
        var light = new THREE.DirectionalLight(0xffffff, intensity);
        light.castShadow = true;

        light.shadow.camera.left = -10;
        light.shadow.camera.bottom = -10;
        light.shadow.camera.right = 10;
        light.shadow.camera.top = 10;

        return light;
      }
  
      // to check the parallel, add CameraHelper
      // The camera helper is just the helper geometry that shows us the field of view of the camera.
      var helper = new THREE.CameraHelper(directionalLight.shadow.camera);
    ```
    <img width="542" alt="image" src="https://user-images.githubusercontent.com/24782000/163735216-1509c118-7aba-4d1d-9697-4be793e8afdb.png">
  </details>
- <details>
  <summary> AmbientLight </summary>
  
    [means parallel light](chapter-03/03_09/end/main.js)
    ```js
      function getAmbientLight(intensity) {
        var light = new THREE.AmbientLight('rgb(10, 30, 50)', intensity);

        return light;
      }
    ```
    As you can see, ambient light illuminates all the objects in the scene equally. It doesn't have any direction, and it doesn't cast any shadows. Because of these reasons, it doesn't come close to how lighting behaves in real life. So you should use it sparingly if you are looking to create realistic scenes. 
    The pic shows ambient light work alone with directional light:
    <img width="545" alt="image" src="https://user-images.githubusercontent.com/24782000/163735412-6179c579-1214-49fd-b1bf-102ec425bb54.png">
  </details>
- <details>
  <summary>RectAreaLight</summary>
  
  a more realistic light [see docs](https://threejs.org/docs/#api/en/lights/RectAreaLight)
  </details>
  
### 4 Animations
- <details>
  <summary>Orthographic Camera</summary>
  
  ```js
  var camera = new THREE.OrthographicCamera(
		-15,
		15,
		15,
		-15,
		1,
		1000
	);
  ```
  ![image](https://user-images.githubusercontent.com/24782000/164358426-73530924-eefc-4677-b304-a85fb1d95226.png)
  </details>
- <details>
  <summary>Animation Rig</summary>
  
  to build a simple camera animation
  ```js
  var camera = new THREE.PerspectiveCamera(
		45,
		window.innerWidth/window.innerHeight,
		1,
		1000
	);

	var cameraZPosition = new THREE.Group();
	var cameraXRotation = new THREE.Group();
	var cameraYRotation = new THREE.Group();

	cameraZPosition.add(camera);
	cameraXRotation.add(cameraZPosition);
	cameraYRotation.add(cameraXRotation);
	scene.add(cameraYRotation);

	gui.add(cameraZPosition.position, 'z', 0, 100);
	gui.add(cameraYRotation.rotation, 'y', -Math.PI, Math.PI);
	gui.add(cameraXRotation.rotation, 'x', -Math.PI, Math.PI);
  ```
  
  <img width="626" alt="image" src="https://user-images.githubusercontent.com/24782000/164359801-6cd443c6-e040-4c5d-8062-eb629f90e38b.png">
  </details>

### Materials and Textures
- <details>
  <summary>MeshLamberMaterial and MeshPhongMaterial</summary>

  Here are the key takeaways from the "MeshLambertMaterial and MeshPhongMaterial" video:

	Using MeshLambertMaterial:
	
	MeshLambertMaterial is suitable for simulating non-shiny surfaces like rubber, clay, or stone.
	```javascript
	const material = new THREE.MeshLambertMaterial({ color: 0xffffff });
	const sphere = new THREE.Mesh(new THREE.SphereGeometry(5, 32, 32), material);
	scene.add(sphere);
	```
 
	Switching to MeshPhongMaterial:
	
	MeshPhongMaterial allows control over highlights and is better for simulating glossy surfaces.
	```javascript
	const material = new THREE.MeshPhongMaterial({ color: 0xffffff, shininess: 30 });
	const sphere = new THREE.Mesh(new THREE.SphereGeometry(5, 32, 32), material);
	scene.add(sphere);
	```
 
	Adjusting Shininess:
	
	The shininess attribute in MeshPhongMaterial controls the sharpness of light reflections.
	```javascript
	const material = new THREE.MeshPhongMaterial({ color: 0xffffff, shininess: 100 });
	const sphere = new THREE.Mesh(new THREE.SphereGeometry(5, 32, 32), material);
	scene.add(sphere);
	```
 
	Using dat.GUI to Control Shininess:
	
	dat.GUI can be used to create a user interface for adjusting material properties.
	```javascript
	const gui = new dat.GUI();
	const material = new THREE.MeshPhongMaterial({ color: 0xffffff, shininess: 30 });
	const sphere = new THREE.Mesh(new THREE.SphereGeometry(5, 32, 32), material);
	scene.add(sphere);
	```
	
	gui.add(material, 'shininess', 0, 1000);
		
	These examples illustrate how to use MeshLambertMaterial and MeshPhongMaterial in three.js and adjust their properties for different visual effects.
</details>

- <details>
  <summary>MeshStandardMaterial</summary>
	Here are the key takeaways from the "MeshStandardMaterial" video:

	Realistic Rendering:
	
	MeshStandardMaterial uses physically based rendering for more realistic results.
	```javascript
	const material = new THREE.MeshStandardMaterial({ color: 0xffffff });
	const sphere = new THREE.Mesh(new THREE.SphereGeometry(5, 32, 32), material);
	scene.add(sphere);
	```
 
	Roughness Parameter:
	
	Controls the sharpness of reflections. Lower values result in sharper reflections, higher values result in blurrier reflections.
	```javascript
	material.roughness = 0.5; // Value between 0 and 1
	```
 
	Metalness Parameter:
	
	Affects the lighting and reflection distribution, controlling how metallic an object looks.
	```javascript
	material.metalness = 0.8; // Value between 0 and 1
	```
	
	These points illustrate how MeshStandardMaterial can be used to create more realistic materials in three.js.
</details>
