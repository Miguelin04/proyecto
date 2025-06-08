## Corazón animado con "sallu"

Este proyecto crea una animación 3D de un corazón con partículas usando Three.js y GSAP. Incluye el texto "sallu" animado.

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Corazón Animado con "sallu"</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    body {margin:0;overflow:hidden;background:#000;font-family:sans-serif}
    canvas {position:absolute}
    svg {position:absolute;display:none}
  </style>
</head>
<body>
  <svg viewBox="0 0 600 552">
    <path d="M300,107.77C284.68,55.67,239.76,0,162.31,0,64.83,0,0,82.08,0,171.71c0,.48,0,.95,0,1.43-.52,19.5,0,217.94,299.87,379.69v0l0,0,.05,0,0,0,0,0v0C600,391.08,600.48,192.64,600,173.14c0-.48,0-.95,0-1.43C600,82.08,535.17,0,437.69,0,360.24,0,315.32,55.67,300,107.77" fill="#ee5282"/>
  </svg>
  <script src='https://cdn.jsdelivr.net/npm/three@0.136.0/build/three.min.js'></script>
  <script src='https://cdn.jsdelivr.net/npm/three@0.136.0/examples/js/controls/OrbitControls.js'></script>
  <script src='https://cdn.jsdelivr.net/npm/three@0.136.0/examples/js/loaders/FontLoader.js'></script>
  <script src='https://cdn.jsdelivr.net/npm/three@0.136.0/examples/js/geometries/TextGeometry.js'></script>
  <script src='https://unpkg.co/gsap@3/dist/gsap.min.js'></script>
  <script>
  // Limpiar consola y configurar escena
  console.clear();
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 5000);
  camera.position.z = 500;

  const renderer = new THREE.WebGLRenderer();
  renderer.setPixelRatio(window.devicePixelRatio > 1 ? 2 : 1);
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);

  // Controles
  const controlsWebGL = new THREE.OrbitControls(camera, renderer.domElement);

  // Partículas del corazón
  const tl = gsap.timeline({repeat: -1, yoyo: true});
  const path = document.querySelector("path");
  const length = path.getTotalLength();
  const vertices = [];
  
  for (let i = 0; i < length; i += 0.1) {
    const point = path.getPointAtLength(i);
    const vector = new THREE.Vector3(point.x, -point.y, 0);
    vector.x += (Math.random() - 0.5) * 30;
    vector.y += (Math.random() - 0.5) * 30;
    vector.z += (Math.random() - 0.5) * 70;
    vertices.push(vector);
    tl.from(vector, {
      x: 600/2, y: -552/2, z: 0,
      ease: "power2.inOut",
      duration: "random(2, 5)"
    }, i * 0.002);
  }
  
  const geometry = new THREE.BufferGeometry().setFromPoints(vertices);
  const material = new THREE.PointsMaterial({color: 0xee5282, blending: THREE.AdditiveBlending, size: 3});
  const particles = new THREE.Points(geometry, material);
  particles.position.x -= 600/2;
  particles.position.y += 552/2;
  scene.add(particles);

  // Texto "sallu"
  const createSalluText = () => {
    const fontLoader = new THREE.FontLoader();
    fontLoader.load('https://cdn.jsdelivr.net/npm/three@0.136.0/examples/fonts/helvetiker_bold.typeface.json', function(font) {
      const textGeometry = new THREE.TextGeometry('sallu', {
        font: font,
        size: 80,
        height: 20,
        curveSegments: 12,
        bevelEnabled: true,
        bevelThickness: 6,
        bevelSize: 3,
        bevelOffset: 0,
        bevelSegments: 5
      });
      
      textGeometry.computeBoundingBox();
      const centerOffsetX = -0.5 * (textGeometry.boundingBox.max.x - textGeometry.boundingBox.min.x);
      const centerOffsetY = -0.5 * (textGeometry.boundingBox.max.y - textGeometry.boundingBox.min.y);
      
      const textMaterial = new THREE.MeshPhongMaterial({ 
        color: 0xffffff,
        specular: 0xffffff,
        shininess: 100,
        emissive: 0xff3399,
        emissiveIntensity: 0.5
      });
      
      const pointLight = new THREE.PointLight(0xffffff, 1, 1000);
      pointLight.position.set(0, 0, 200);
      scene.add(pointLight);
      
      const textMesh = new THREE.Mesh(textGeometry, textMaterial);
      textMesh.position.x = centerOffsetX;
      textMesh.position.y = centerOffsetY - 30;
      textMesh.position.z = 50;
      scene.add(textMesh);
      
      const textTimeline = gsap.timeline({repeat: -1, repeatDelay: 2});
      
      textTimeline
        .fromTo(textMesh.scale, 
          {x: 0, y: 0, z: 0}, 
          {x: 1, y: 1, z: 1, duration: 2, ease: "elastic.out(1, 0.3)"}
        )
        .to(textMesh, {duration: 3})
        .to(textMesh.rotation, {y: Math.PI*2, duration: 2, ease: "power2.in"}, "-=0.5")
        .to(textMesh.scale, {x: 0, y: 0, z: 0, duration: 1.5, ease: "power3.in"}, "-=1.5");
    });
  };

  setTimeout(createSalluText, 2000);

  // Animación de la escena
  gsap.fromTo(scene.rotation, {y: -0.2}, {y: 0.2, repeat: -1, yoyo: true, ease: 'power2.inOut', duration: 3});

  // Renderizado
  function render() {
    requestAnimationFrame(render);
    geometry.setFromPoints(vertices);
    renderer.render(scene, camera);
  }

  // Eventos
  function onWindowResize() {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  }
  window.addEventListener("resize", onWindowResize, false);

  requestAnimationFrame(render);
  </script>
</body>
</html>
