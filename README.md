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
### 3 Lights
