<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Advanced 3D Shooting Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: black;
        }

        #ui {
            position: absolute;
            top: 10px;
            left: 10px;
            color: white;
            font-size: 18px;
        }

        #crosshair {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 24px;
            color: white;
        }
    </style>
</head>

<body>

    <div id="ui">
        Score: <span id="score">0</span><br>
        Time: <span id="time">60</span>
    </div>
    <div id="crosshair">+</div>

    <script src="https://cdn.jsdelivr.net/npm/three@0.152.2/build/three.min.js"></script>

    <script>
        let scene = new THREE.Scene();
        scene.background = new THREE.Color(0x000000);

        let camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.set(0, 2, 5);

        let renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Light
        scene.add(new THREE.AmbientLight(0xffffff, 0.5));
        let light = new THREE.DirectionalLight(0xffffff, 1);
        light.position.set(5, 10, 5);
        scene.add(light);

        // Floor
        let floor = new THREE.Mesh(
            new THREE.PlaneGeometry(100, 100),
            new THREE.MeshStandardMaterial({ color: 0x333333 })
        );
        floor.rotation.x = -Math.PI / 2;
        scene.add(floor);

        // Targets
        let targets = [];
        let targetGeo = new THREE.BoxGeometry(1, 1, 1);

        for (let i = 0; i < 5; i++) {
            let t = new THREE.Mesh(
                targetGeo,
                new THREE.MeshStandardMaterial({ color: 0xff0000 })
            );
            t.position.set(
                (Math.random() * 20) - 10,
                Math.random() * 4 + 1,
                -(Math.random() * 30 + 10)
            );
            scene.add(t);
            targets.push(t);
        }

        // Raycaster
        let raycaster = new THREE.Raycaster();
        let score = 0;
        let timeLeft = 60;

        // Movement
        let keys = {};
        document.addEventListener("keydown", e => keys[e.key.toLowerCase()] = true);
        document.addEventListener("keyup", e => keys[e.key.toLowerCase()] = false);

        // Shooting
        window.addEventListener("click", () => {
            raycaster.setFromCamera({ x: 0, y: 0 }, camera);
            let hits = raycaster.intersectObjects(targets);
            if (hits.length > 0) {
                score += 10;
                document.getElementById("score").innerText = score;
                hits[0].object.position.set(
                    (Math.random() * 20) - 10,
                    Math.random() * 4 + 1,
                    -(Math.random() * 30 + 10)
                );
            }
        });

        // Timer
        let timer = setInterval(() => {
            timeLeft--;
            document.getElementById("time").innerText = timeLeft;
            if (timeLeft <= 0) {
                alert("Game Over! Your Score: " + score);
                clearInterval(timer);
                location.reload();
            }
        }, 1000);

        // Animation
        function animate() {
            requestAnimationFrame(animate);

            if (keys["w"]) camera.position.z -= 0.15;
            if (keys["s"]) camera.position.z += 0.15;
            if (keys["a"]) camera.position.x -= 0.15;
            if (keys["d"]) camera.position.x += 0.15;

            // Move targets
            targets.forEach(t => {
                t.position.x += Math.sin(Date.now() * 0.002) * 0.02;
            });

            renderer.render(scene, camera);
        }
        animate();
    </script>

</body>

</html>
