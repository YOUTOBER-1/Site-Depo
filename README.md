<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Voxel World</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
        }
        canvas {
            display: block;
        }
    </style>
</head>
<body>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    <script>
        let scene, camera, renderer, controls;
        const size = 20;
        const blockSize = 1;

        function init() {
            // Create scene
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x87ceeb); // Sky color

            // Create camera
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.set(size / 2, size, size * 2);
            camera.lookAt(size / 2, 0, size / 2);

            // Create renderer
            renderer = new THREE.WebGLRenderer();
            renderer.setSize(window.innerWidth, window.innerHeight);
            document.body.appendChild(renderer.domElement);

            // Add lights
            const light = new THREE.DirectionalLight(0xffffff, 1);
            light.position.set(1, 1, 1).normalize();
            scene.add(light);

            // Create voxel world
            createVoxelWorld();

            // Add orbit controls
            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.25;
            controls.enableZoom = true;

            // Render the scene
            render();
        }

        function createVoxelWorld() {
            const geometry = new THREE.BoxGeometry(blockSize, blockSize, blockSize);
            const materials = {
                grass: new THREE.MeshLambertMaterial({ color: 0x00ff00 }), // Green
                dirt: new THREE.MeshLambertMaterial({ color: 0x8B4513 }),  // Brown
                stone: new THREE.MeshLambertMaterial({ color: 0x808080 })  // Gray
            };

            for (let x = 0; x < size; x++) {
                for (let y = 0; y < size / 2; y++) {
                    for (let z = 0; z < size; z++) {
                        let material;
                        if (y < size / 4) {
                            material = materials.stone;
                        } else if (y < size / 3) {
                            material = materials.dirt;
                        } else {
                            material = materials.grass;
                        }
                        const voxel = new THREE.Mesh(geometry, material);
                        voxel.position.set(x * blockSize, y * blockSize, z * blockSize);
                        scene.add(voxel);
                    }
                }
            }
        }

        function render() {
            requestAnimationFrame(render);
            controls.update();
            renderer.render(scene, camera);
        }

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        init();
    </script>
</body>
</html>
