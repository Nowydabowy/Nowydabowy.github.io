<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flop City 3D</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #87CEEB; /* Sky blue */
            font-family: 'Inter', sans-serif;
            user-select: none;
        }
        canvas {
            display: block;
            width: 100vw;
            height: 100vh;
        }
        #ui {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            padding: 20px;
            box-sizing: border-box;
        }
        #crosshair {
            position: absolute;
            top: 50%;
            left: 50%;
            width: 6px;
            height: 6px;
            background-color: rgba(255, 255, 255, 0.8);
            border-radius: 50%;
            transform: translate(-50%, -50%);
        }
        .hud-panel {
            background: rgba(0, 0, 0, 0.5);
            color: white;
            padding: 15px 20px;
            border-radius: 12px;
            backdrop-filter: blur(4px);
            max-width: 300px;
        }
        h1 {
            margin: 0 0 10px 0;
            font-size: 24px;
            text-transform: uppercase;
            letter-spacing: 2px;
            color: #FFD700;
        }
        ul {
            margin: 0;
            padding-left: 20px;
            line-height: 1.6;
        }
        #start-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            color: white;
            pointer-events: auto;
            z-index: 10;
            cursor: pointer;
        }
        .start-btn {
            background: #FFD700;
            color: black;
            border: none;
            padding: 15px 40px;
            font-size: 24px;
            font-weight: bold;
            border-radius: 30px;
            cursor: pointer;
            transition: transform 0.2s, background 0.2s;
            margin-top: 20px;
        }
        .start-btn:hover {
            transform: scale(1.05);
            background: #ffea00;
        }
        #coin-display {
            position: absolute;
            top: 20px;
            right: 20px;
            font-size: 24px;
            font-weight: bold;
            color: #FFD700;
            text-shadow: 2px 2px 0 #000;
            display: none;
        }
        #shop-panel {
            position: absolute;
            bottom: 20px;
            right: 20px;
            background: rgba(0,0,0,0.7);
            color: white;
            padding: 15px;
            border-radius: 12px;
            display: none;
        }
        .shop-item { margin-bottom: 10px; font-weight: bold; }

        #scope-overlay {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            pointer-events: none;
            display: none;
            background: radial-gradient(circle at center, transparent 35%, rgba(0, 0, 0, 0.95) 45%, black 100%);
            z-index: 5;
        }
        .scope-line-h {
            position: absolute; top: 50%; left: 0; width: 100%; height: 2px; background: rgba(0,0,0,0.8);
        }
        .scope-line-v {
            position: absolute; top: 0; left: 50%; height: 100%; width: 2px; background: rgba(0,0,0,0.8);
        }
    </style>
    <!-- Load Three.js and Cannon.js -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/cannon.js/0.6.2/cannon.min.js"></script>
</head>
<body>

    <div id="ui">
        <div id="scope-overlay">
            <div class="scope-line-h"></div>
            <div class="scope-line-v"></div>
        </div>
        <div class="hud-panel" style="align-self: flex-start;">
            <h1>Flop City</h1>
            <ul>
                <li><b>W, S:</b> Move Fwd/Back</li>
                <li><b>A, D:</b> Turn Left/Right</li>
                <li><b>Space:</b> Jump</li>
                <li><b>Run into walls:</b> Climb</li>
                <li><b>Shift:</b> Web Swing</li>
                <li><b>Tab:</b> Scope (WASD to look)</li>
                <li><b>F:</b> Flop / Get up</li>
            </ul>
        </div>
        <div class="hud-panel" style="align-self: flex-end; text-align: right;">
            <div id="status-text">Status: Standing</div>
        </div>
        <div id="crosshair"></div>
    </div>

    <div id="coin-display">Coins: 0</div>
    <div id="shop-panel">
        <h3 style="margin-top:0; color:#00bfff;">Guns (Up/Down)</h3>
        <div id="shop-gun-display" class="shop-item"></div>
        
        <h3 style="margin-top:15px; margin-bottom:5px; color:#ff3366;">Outfits (Left/Right)</h3>
        <div id="shop-outfit-display" class="shop-item"></div>
        
        <div style="margin-top: 10px; font-size: 12px; color: #aaa;">Press 'B' to Buy/Equip selected</div>
    </div>

    <div id="start-screen">
        <h1 style="font-size: 48px; margin-bottom: 10px;">FLOP CITY 3D</h1>
        <p style="font-size: 18px; max-width: 500px; text-align: center; margin-bottom: 30px;">
            Run around, climb anything, jump off skyscrapers, and flop to the ground safely. Like a skate game, but you are the skateboard.
        </p>
        <div style="display: flex; gap: 20px;">
            <button class="start-btn" id="start-free-btn">FREE PLAY</button>
            <button class="start-btn" id="start-water-btn" style="background: #00bfff; color: white;">WATER GUN MODE</button>
        </div>
    </div>

    <script>
        // Game State Variables
        let scene, camera, renderer;
        let world;
        let playerBody, playerMesh;
        let armL, armR, legL, legR;
        let time, lastTime;
        
        // Input
        const keys = { w: false, a: false, s: false, d: false, space: false };
        let isFlopping = false;
        let flopStartTime = 0;
        let isGrounded = false;
        let canJump = true;
        let airTime = 0;
        
        // Camera setup
        let pitchObject = new THREE.Object3D();
        let yawObject = new THREE.Object3D();
        let cameraRadius = 8;
        
        let collidableMeshes = [];
        let activeWeb = null;
        let targetFOV = 75;
        let isScoping = false;
        
        // Game Modes & Economy
        let gameMode = 'free'; // 'free' or 'water'
        let coins = 0;
        let npcs = [];
        let waterParticles = [];
        const weapons = {
            squirt: { name: 'Squirty Gun', cost: 0, unlocked: true, fireRate: 400, range: 40, power: 5, color: 0x00bfff, spray: 0.1 },
            machine: { name: 'Machine Gun', cost: 20, unlocked: false, fireRate: 80, range: 60, power: 3, color: 0x0088ff, spray: 0.2 },
            sniper: { name: 'Sniper Gun', cost: 50, unlocked: false, fireRate: 1500, range: 300, power: 30, color: 0x00ffff, spray: 0.0 }
        };
        let currentWeapon = 'squirt';
        let gunKeys = Object.keys(weapons);
        let selectedGunIndex = 0;
        let lastFireTime = 0;
        let isShootingWater = false;
        let gunMesh = null;
        
        const outfits = {
            default: { name: 'Pink (Default)', cost: 0, unlocked: true, color: 0xff3366 },
            red: { name: 'Red', cost: 10, unlocked: false, color: 0xff0000 },
            orange: { name: 'Orange', cost: 15, unlocked: false, color: 0xff8800 },
            yellow: { name: 'Yellow', cost: 20, unlocked: false, color: 0xffff00 },
            green: { name: 'Green', cost: 25, unlocked: false, color: 0x00ff00 },
            blue: { name: 'Blue', cost: 30, unlocked: false, color: 0x0000ff },
            purple: { name: 'Purple', cost: 40, unlocked: false, color: 0x8800ff },
            white: { name: 'White', cost: 50, unlocked: false, color: 0xffffff },
            ninja: { name: 'Ninja', cost: 75, unlocked: false, color: 0x333333 },
            gold: { name: 'Gold', cost: 100, unlocked: false, color: 0xffd700 }
        };
        let currentOutfit = 'default';
        let outfitKeys = Object.keys(outfits);
        let selectedOutfitIndex = 0;
        let playerMaterial = null;
        
        // Materials
        let physicsMaterial;

        // Colors
        const colors = [0xff9a9e, 0xfecfef, 0xa1c4fd, 0xc2e9fb, 0xd4fc79, 0x96e6a1, 0xfdfbfb, 0xebedee];

        // Procedural Building Texture Generator
        function getBuildingTexture(hexColor) {
            const canvas = document.createElement('canvas');
            canvas.width = 512;
            canvas.height = 512;
            const ctx = canvas.getContext('2d');
            
            // Base wall color
            ctx.fillStyle = '#' + hexColor.toString(16).padStart(6, '0');
            ctx.fillRect(0, 0, 512, 512);
            
            // Draw Windows
            for(let x = 16; x < 512; x += 64) {
                for(let y = 16; y < 512; y += 64) {
                    // Randomize lights on/off
                    ctx.fillStyle = Math.random() > 0.7 ? '#ffffaa' : '#223344';
                    ctx.fillRect(x, y, 32, 48);
                }
            }
            const texture = new THREE.CanvasTexture(canvas);
            texture.wrapS = THREE.RepeatWrapping;
            texture.wrapT = THREE.RepeatWrapping;
            return texture;
        }

        window.onload = () => {
            document.getElementById('start-free-btn').addEventListener('click', () => {
                gameMode = 'free';
                init();
            });
            document.getElementById('start-water-btn').addEventListener('click', () => {
                gameMode = 'water';
                document.getElementById('coin-display').style.display = 'block';
                document.getElementById('shop-panel').style.display = 'block';
                // Update HUD instructions
                document.querySelector('.hud-panel ul').innerHTML += '<li><b>Left Click:</b> Shoot Water</li><li><b>Up/Down:</b> Browse Guns</li><li><b>Left/Right:</b> Browse Skins</li><li><b>B:</b> Buy/Equip</li><li><b>C:</b> Toggle HUD</li>';
                updateShopUI();
                init();
            });
        };

        function updateShopUI() {
            if (gameMode !== 'water') return;
            
            const gKey = gunKeys[selectedGunIndex];
            const g = weapons[gKey];
            let gStatus = g.unlocked ? (currentWeapon === gKey ? "(Equipped)" : "(Owned)") : `(${g.cost}c)`;
            const gunDisplay = document.getElementById('shop-gun-display');
            gunDisplay.innerHTML = `> ${g.name} ${gStatus}`;
            gunDisplay.style.color = g.unlocked ? (currentWeapon === gKey ? "#00ff00" : "#aaaaaa") : "#ffffff";

            const oKey = outfitKeys[selectedOutfitIndex];
            const o = outfits[oKey];
            let oStatus = o.unlocked ? (currentOutfit === oKey ? "(Equipped)" : "(Owned)") : `(${o.cost}c)`;
            const outfitDisplay = document.getElementById('shop-outfit-display');
            outfitDisplay.innerHTML = `> ${o.name} ${oStatus}`;
            outfitDisplay.style.color = o.unlocked ? (currentOutfit === oKey ? "#00ff00" : "#aaaaaa") : "#ffffff";
        }

        function init() {
            // Hide start screen
            document.getElementById('start-screen').style.display = 'none';

            // 1. THREE.JS SETUP
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x87CEEB);
            scene.fog = new THREE.Fog(0x87CEEB, 20, 150);

            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            
            renderer = new THREE.WebGLRenderer({ antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.shadowMap.enabled = true;
            renderer.shadowMap.type = THREE.PCFSoftShadowMap;
            document.body.appendChild(renderer.domElement);

            // Lighting
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
            scene.add(ambientLight);

            const dirLight = new THREE.DirectionalLight(0xffffff, 0.8);
            dirLight.position.set(50, 100, 50);
            dirLight.castShadow = true;
            dirLight.shadow.camera.top = 100;
            dirLight.shadow.camera.bottom = -100;
            dirLight.shadow.camera.left = -100;
            dirLight.shadow.camera.right = 100;
            dirLight.shadow.camera.near = 0.1;
            dirLight.shadow.camera.far = 200;
            dirLight.shadow.mapSize.width = 2048;
            dirLight.shadow.mapSize.height = 2048;
            scene.add(dirLight);

            // 2. CANNON.JS SETUP (Physics)
            world = new CANNON.World();
            world.gravity.set(0, -20, 0); // slightly higher gravity for snappy jumps
            world.broadphase = new CANNON.SAPBroadphase(world);
            world.solver.iterations = 10;

            // Physics Material
            physicsMaterial = new CANNON.Material("standard");
            const physicsContactMaterial = new CANNON.ContactMaterial(
                physicsMaterial,
                physicsMaterial,
                { friction: 0.1, restitution: 0.2 }
            );
            world.addContactMaterial(physicsContactMaterial);

            // 3. BUILD WORLD
            createCity();
            createPlayer();

            // 4. SETUP CONTROLS & EVENTS
            setupControls();

            // 5. START LOOP
            lastTime = performance.now();
            animate();
        }

        function createCity() {
            // Ground
            const groundShape = new CANNON.Plane();
            const groundBody = new CANNON.Body({ mass: 0, material: physicsMaterial });
            groundBody.addShape(groundShape);
            groundBody.quaternion.setFromAxisAngle(new CANNON.Vec3(1, 0, 0), -Math.PI / 2);
            world.addBody(groundBody);

            const groundGeo = new THREE.PlaneGeometry(300, 300);
            const groundMat = new THREE.MeshStandardMaterial({ color: 0x3d4a3e, roughness: 0.8 });
            const groundMesh = new THREE.Mesh(groundGeo, groundMat);
            groundMesh.rotation.x = -Math.PI / 2;
            groundMesh.receiveShadow = true;
            scene.add(groundMesh);

            // Buildings
            for (let i = 0; i < 80; i++) {
                const width = Math.random() * 10 + 5;
                const depth = Math.random() * 10 + 5;
                const height = Math.random() * 30 + 5;

                const x = (Math.random() - 0.5) * 150;
                const z = (Math.random() - 0.5) * 150;

                // Keep center clear for spawn
                if (Math.abs(x) < 15 && Math.abs(z) < 15) continue;

                const color = colors[Math.floor(Math.random() * colors.length)];

                // Physics
                const boxShape = new CANNON.Box(new CANNON.Vec3(width / 2, height / 2, depth / 2));
                const boxBody = new CANNON.Body({ mass: 0, material: physicsMaterial });
                boxBody.addShape(boxShape);
                boxBody.position.set(x, height / 2, z);
                world.addBody(boxBody);

                // Visuals
                const boxGeo = new THREE.BoxGeometry(width, height, depth);
                const tex = getBuildingTexture(color);
                tex.repeat.set(Math.ceil(width / 15), Math.ceil(height / 15)); // Scale texture to building size
                
                const boxMat = new THREE.MeshStandardMaterial({ map: tex, roughness: 0.6 });
                const boxMesh = new THREE.Mesh(boxGeo, boxMat);
                boxMesh.position.copy(boxBody.position);
                boxMesh.castShadow = true;
                boxMesh.receiveShadow = true;
                scene.add(boxMesh);
                collidableMeshes.push(boxMesh);
                
                // Spawn NPCs on regular buildings
                if (Math.random() > 0.4) {
                    createNPC(x, height + 2, z);
                }
            }
            
            // Skyscrapers (The super tall ones!)
            for (let i = 0; i < 25; i++) {
                const width = Math.random() * 15 + 10;
                const depth = Math.random() * 15 + 10;
                const height = Math.random() * 100 + 40; // Heights from 40 up to 140!

                const x = (Math.random() - 0.5) * 180;
                const z = (Math.random() - 0.5) * 180;

                // Keep center clear for spawn
                if (Math.abs(x) < 20 && Math.abs(z) < 20) continue;

                const color = colors[Math.floor(Math.random() * colors.length)];

                // Physics
                const boxShape = new CANNON.Box(new CANNON.Vec3(width / 2, height / 2, depth / 2));
                const boxBody = new CANNON.Body({ mass: 0, material: physicsMaterial });
                boxBody.addShape(boxShape);
                boxBody.position.set(x, height / 2, z);
                world.addBody(boxBody);

                // Visuals
                const boxGeo = new THREE.BoxGeometry(width, height, depth);
                const tex = getBuildingTexture(color);
                tex.repeat.set(Math.ceil(width / 15), Math.ceil(height / 15)); // Scale texture to skyscraper size
                
                const boxMat = new THREE.MeshStandardMaterial({ map: tex, roughness: 0.5 });
                const boxMesh = new THREE.Mesh(boxGeo, boxMat);
                boxMesh.position.copy(boxBody.position);
                boxMesh.castShadow = true;
                boxMesh.receiveShadow = true;
                scene.add(boxMesh);
                collidableMeshes.push(boxMesh);
                
                // Spawn NPCs on skyscrapers in all modes
                if (Math.random() > 0.2) {
                    createNPC(x, height + 2, z);
                }
            }

            // Ramps
            for(let i=0; i<10; i++) {
                const rWidth = 8;
                const rDepth = 15;
                const rHeight = 1;
                
                const x = (Math.random() - 0.5) * 100;
                const z = (Math.random() - 0.5) * 100;
                const angle = Math.random() * Math.PI * 2;
                const slope = Math.random() * 0.5 + 0.2; // tilt
                
                const rampShape = new CANNON.Box(new CANNON.Vec3(rWidth/2, rHeight/2, rDepth/2));
                const rampBody = new CANNON.Body({ mass: 0, material: physicsMaterial });
                rampBody.addShape(rampShape);
                rampBody.position.set(x, rHeight, z);
                rampBody.quaternion.setFromEuler(slope, angle, 0);
                world.addBody(rampBody);
                
                const rampGeo = new THREE.BoxGeometry(rWidth, rHeight, rDepth);
                const rampMat = new THREE.MeshStandardMaterial({ color: 0xffaa00, roughness: 0.5 });
                const rampMesh = new THREE.Mesh(rampGeo, rampMat);
                rampMesh.position.copy(rampBody.position);
                rampMesh.quaternion.copy(rampBody.quaternion);
                rampMesh.castShadow = true;
                rampMesh.receiveShadow = true;
                scene.add(rampMesh);
            }
        }

        function createPlayer() {
            const radius = 1.35; // Increased radius so the sphere fully encapsulates the mesh
            
            // Physics Body (Sphere for smooth movement and rolling)
            const sphereShape = new CANNON.Sphere(radius);
            playerBody = new CANNON.Body({
                mass: 50,
                material: physicsMaterial,
                fixedRotation: false, // We'll manually control rotation when standing
                linearDamping: 0.4,
                angularDamping: 0.4
            });
            playerBody.addShape(sphereShape);
            playerBody.position.set(0, 5, 0);

            // Crash detection against buildings
            playerBody.addEventListener("collide", function(e) {
                if (isFlopping) return;
                
                // e.contact.ni is the normal vector of the collision. 
                // If its Y (up/down) value is close to 0, it means we hit a vertical surface (a wall)
                if (Math.abs(e.contact.ni.y) < 0.5) {
                    const speed = playerBody.velocity.length();
                    
                    // Flop if we smash into a wall extremely fast, 
                    // or if we hit a wall while NOT actively trying to climb (holding W)
                    if (speed > 25 || (speed > 8 && !keys.w)) {
                        toggleFlop();
                    }
                }
            });

            world.addBody(playerBody);

            // Visual Character Mesh (Composite)
            playerMesh = new THREE.Group();
            
            playerMaterial = new THREE.MeshStandardMaterial({ color: outfits[currentOutfit].color, roughness: 0.4 });
            const mat = playerMaterial; // Alias to apply to all body parts
            
            // Body (Shifted down to center within the larger physics sphere)
            const bodyGeo = new THREE.BoxGeometry(0.8, 1.2, 0.5);
            const bodyMesh = new THREE.Mesh(bodyGeo, mat);
            bodyMesh.position.y = 0.05; 
            bodyMesh.castShadow = true;
            playerMesh.add(bodyMesh);

            // Head
            const headGeo = new THREE.BoxGeometry(0.6, 0.6, 0.6);
            const headMesh = new THREE.Mesh(headGeo, mat);
            headMesh.position.y = 1.05;
            headMesh.castShadow = true;
            playerMesh.add(headMesh);
            
            // Eyes
            const eyeGeo = new THREE.BoxGeometry(0.1, 0.1, 0.1);
            const eyeMat = new THREE.MeshBasicMaterial({color: 0x000000});
            const eye1 = new THREE.Mesh(eyeGeo, eyeMat);
            eye1.position.set(-0.15, 1.15, 0.3);
            playerMesh.add(eye1);
            const eye2 = new THREE.Mesh(eyeGeo, eyeMat);
            eye2.position.set(0.15, 1.15, 0.3);
            playerMesh.add(eye2);

            // Arms
            const armGeo = new THREE.BoxGeometry(0.3, 1, 0.3);
            armGeo.translate(0, -0.4, 0); // Translate so the pivot is at the shoulder
            armL = new THREE.Mesh(armGeo, mat);
            armL.position.set(-0.6, 0.55, 0);
            armL.castShadow = true;
            playerMesh.add(armL);
            
            armR = new THREE.Mesh(armGeo, mat);
            armR.position.set(0.6, 0.55, 0);
            armR.castShadow = true;
            playerMesh.add(armR);

            // Legs
            const legGeo = new THREE.BoxGeometry(0.3, 1, 0.3);
            legGeo.translate(0, -0.4, 0); // Translate so the pivot is at the hips
            legL = new THREE.Mesh(legGeo, mat);
            legL.position.set(-0.25, -0.45, 0);
            legL.castShadow = true;
            playerMesh.add(legL);

            legR = new THREE.Mesh(legGeo, mat);
            legR.position.set(0.25, -0.45, 0);
            legR.castShadow = true;
            playerMesh.add(legR);

            // Add Gun Mesh if in water mode
            if (gameMode === 'water') {
                const gunGeo = new THREE.BoxGeometry(0.2, 0.3, 0.8);
                const gunMat = new THREE.MeshStandardMaterial({ color: weapons.squirt.color });
                gunMesh = new THREE.Mesh(gunGeo, gunMat);
                gunMesh.position.set(0.7, 0.5, 0.3); // attached to right arm area
                playerMesh.add(gunMesh);
            }

            scene.add(playerMesh);

            // Camera setup attached to player logic
            scene.add(yawObject);
            yawObject.add(pitchObject);
            pitchObject.add(camera);
            camera.position.set(0, 2, cameraRadius); // Offset back
        }

        function setupControls() {
            // Pointer Lock for mouse controls
            document.addEventListener('mousedown', (e) => {
                if (document.pointerLockElement !== document.body) {
                    document.body.requestPointerLock();
                } else {
                    if (gameMode === 'water') {
                        if (e.button === 0) startShootingWater();
                    }
                }
            });

            document.addEventListener('mouseup', (e) => {
                if (gameMode === 'water' && e.button === 0) stopShootingWater();
            });
            
            // Prevent context menu to allow right-click webs
            document.addEventListener('contextmenu', e => e.preventDefault());

            document.addEventListener('mousemove', (event) => {
                // Mouse no longer controls looking around
            });

            document.addEventListener('keydown', (e) => {
                if (e.code === 'Tab') e.preventDefault(); // Prevent browser tabbing
                
                switch(e.code) {
                    case 'KeyW': keys.w = true; break;
                    case 'KeyA': keys.a = true; break;
                    case 'KeyS': keys.s = true; break;
                    case 'KeyD': keys.d = true; break;
                    case 'ShiftLeft':
                    case 'ShiftRight':
                        if (!e.repeat) shootWeb();
                        break;
                    case 'Tab':
                        if (!e.repeat) {
                            isScoping = true;
                            targetFOV = 20;
                            document.getElementById('scope-overlay').style.display = 'block';
                            document.getElementById('crosshair').style.display = 'none';
                        }
                        break;
                    case 'Space': 
                        if(canJump && !isFlopping) {
                            playerBody.velocity.y = 12;
                            canJump = false;
                        }
                        break;
                    case 'KeyF':
                        toggleFlop();
                        break;
                    case 'ArrowUp':
                        if (gameMode === 'water') {
                            selectedGunIndex = (selectedGunIndex - 1 + gunKeys.length) % gunKeys.length;
                            updateShopUI();
                        }
                        break;
                    case 'ArrowDown':
                        if (gameMode === 'water') {
                            selectedGunIndex = (selectedGunIndex + 1) % gunKeys.length;
                            updateShopUI();
                        }
                        break;
                    case 'ArrowLeft':
                        if (gameMode === 'water') {
                            selectedOutfitIndex = (selectedOutfitIndex - 1 + outfitKeys.length) % outfitKeys.length;
                            updateShopUI();
                        }
                        break;
                    case 'ArrowRight':
                        if (gameMode === 'water') {
                            selectedOutfitIndex = (selectedOutfitIndex + 1) % outfitKeys.length;
                            updateShopUI();
                        }
                        break;
                    case 'KeyB':
                        if (gameMode === 'water') {
                            buyOrEquipWeapon(gunKeys[selectedGunIndex]);
                            buyOrEquipOutfit(outfitKeys[selectedOutfitIndex]);
                        }
                        break;
                    case 'KeyC':
                        const hudPanels = document.querySelectorAll('.hud-panel');
                        hudPanels.forEach(p => {
                            p.style.display = p.style.display === 'none' ? 'block' : 'none';
                        });
                        break;
                }
            });

            document.addEventListener('keyup', (e) => {
                switch(e.code) {
                    case 'KeyW': keys.w = false; break;
                    case 'KeyA': keys.a = false; break;
                    case 'KeyS': keys.s = false; break;
                    case 'KeyD': keys.d = false; break;
                    case 'ShiftLeft':
                    case 'ShiftRight':
                        releaseWeb();
                        break;
                    case 'Tab':
                        isScoping = false;
                        targetFOV = 75;
                        document.getElementById('scope-overlay').style.display = 'none';
                        document.getElementById('crosshair').style.display = 'block';
                        break;
                }
            });
            
            window.addEventListener('resize', onWindowResize, false);
        }

        function toggleFlop() {
            isFlopping = !isFlopping;
            const status = document.getElementById('status-text');
            
            if (isFlopping) {
                status.innerText = "Status: FLOPPING!";
                status.style.color = "#ff3366";
                flopStartTime = performance.now() * 0.001; // Record when we started flopping
                // Add some random spin when flopping
                playerBody.angularVelocity.set(
                    (Math.random()-0.5)*10, 
                    (Math.random()-0.5)*10, 
                    (Math.random()-0.5)*10
                );
            } else {
                status.innerText = "Status: Standing";
                status.style.color = "white";
                // reset mesh rotation to match upright
                playerMesh.quaternion.set(0,0,0,1);
            }
        }

        function createNPC(x, y, z) {
            const radius = 1.35;
            const shape = new CANNON.Sphere(radius);
            const body = new CANNON.Body({ mass: 50, material: physicsMaterial, linearDamping: 0.4, angularDamping: 0.4 });
            body.addShape(shape);
            body.position.set(x, y, z);
            world.addBody(body);
            
            const mesh = new THREE.Group();
            const mat = new THREE.MeshStandardMaterial({ color: 0xcccccc, roughness: 0.8 }); // gray npcs
            
            const bodyMesh = new THREE.Mesh(new THREE.BoxGeometry(0.8, 1.2, 0.5), mat);
            bodyMesh.position.y = 0.05; bodyMesh.castShadow = true; mesh.add(bodyMesh);
            
            const headMesh = new THREE.Mesh(new THREE.BoxGeometry(0.6, 0.6, 0.6), mat);
            headMesh.position.y = 1.05; headMesh.castShadow = true; mesh.add(headMesh);
            
            const armGeo = new THREE.BoxGeometry(0.3, 1, 0.3); armGeo.translate(0, -0.4, 0);
            const armL = new THREE.Mesh(armGeo, mat); armL.position.set(-0.6, 0.55, 0); armL.castShadow = true; mesh.add(armL);
            const armR = new THREE.Mesh(armGeo, mat); armR.position.set(0.6, 0.55, 0); armR.castShadow = true; mesh.add(armR);
            
            const legGeo = new THREE.BoxGeometry(0.3, 1, 0.3); legGeo.translate(0, -0.4, 0);
            const legL = new THREE.Mesh(legGeo, mat); legL.position.set(-0.25, -0.45, 0); legL.castShadow = true; mesh.add(legL);
            const legR = new THREE.Mesh(legGeo, mat); legR.position.set(0.25, -0.45, 0); legR.castShadow = true; mesh.add(legR);
            
            scene.add(mesh);
            npcs.push({ body, mesh, armL, armR, legL, legR, isFlopping: false, floppedAndPaid: false });
        }
        
        function updateCoinDisplay() {
            document.getElementById('coin-display').innerText = "Coins: " + coins;
        }
        
        function buyOrEquipWeapon(type) {
            const w = weapons[type];
            if (w.unlocked) {
                equipWeapon(type);
            } else if (coins >= w.cost) {
                coins -= w.cost;
                w.unlocked = true;
                updateCoinDisplay();
                equipWeapon(type);
            }
            updateShopUI();
        }
        
        function equipWeapon(type) {
            if (!weapons[type].unlocked) return;
            currentWeapon = type;
            if (gunMesh) {
                gunMesh.material.color.setHex(weapons[type].color);
            }
        }
        
        function buyOrEquipOutfit(type) {
            const o = outfits[type];
            if (o.unlocked) {
                equipOutfit(type);
            } else if (coins >= o.cost) {
                coins -= o.cost;
                o.unlocked = true;
                updateCoinDisplay();
                equipOutfit(type);
            }
            updateShopUI();
        }
        
        function equipOutfit(type) {
            if (!outfits[type].unlocked) return;
            currentOutfit = type;
            if (playerMaterial) {
                playerMaterial.color.setHex(outfits[type].color);
            }
        }
        
        function startShootingWater() { isShootingWater = true; }
        function stopShootingWater() { isShootingWater = false; }
        
        function fireWaterGun() {
            const now = performance.now();
            const w = weapons[currentWeapon];
            if (now - lastFireTime < w.fireRate) return;
            lastFireTime = now;
            
            const raycaster = new THREE.Raycaster();
            raycaster.setFromCamera(new THREE.Vector2(0, 0), camera);
            
            let npcMeshes = npcs.map(n => n.mesh);
            const intersects = raycaster.intersectObjects(npcMeshes, true);
            
            const dir = raycaster.ray.direction.clone();
            const spreadX = (Math.random() - 0.5) * w.spray;
            const spreadY = (Math.random() - 0.5) * w.spray;
            const spreadZ = (Math.random() - 0.5) * w.spray;
            dir.add(new THREE.Vector3(spreadX, spreadY, spreadZ)).normalize();
            
            const pGeo = new THREE.SphereGeometry(0.2);
            const pMat = new THREE.MeshBasicMaterial({ color: w.color });
            const pMesh = new THREE.Mesh(pGeo, pMat);
            
            // Start from player gun location
            pMesh.position.copy(playerMesh.position);
            pMesh.position.y += 0.5;
            pMesh.position.x += 0.5;
            scene.add(pMesh);
            
            waterParticles.push({
                mesh: pMesh,
                velocity: dir.multiplyScalar(60), // bullet speed
                life: w.range / 60
            });
            
            if (intersects.length > 0) {
                const hitObj = intersects[0].object;
                const hitPoint = intersects[0].point;
                const npc = npcs.find(n => n.mesh === hitObj || n.mesh.children.includes(hitObj));
                if (npc) {
                    const forceDir = raycaster.ray.direction.clone().multiplyScalar(w.power * 500);
                    // Push them up and back with extreme knockback
                    npc.body.applyImpulse(new CANNON.Vec3(forceDir.x, w.power * 200, forceDir.z), new CANNON.Vec3(hitPoint.x, hitPoint.y, hitPoint.z));
                    if (!npc.isFlopping) {
                        npc.body.angularVelocity.set((Math.random()-0.5)*50, (Math.random()-0.5)*50, (Math.random()-0.5)*50);
                    }
                }
            }
        }

        function shootWeb() {
            const raycaster = new THREE.Raycaster();
            raycaster.setFromCamera(new THREE.Vector2(0, 0), camera);
            
            // Look for buildings
            const intersects = raycaster.intersectObjects(collidableMeshes);
            if (intersects.length > 0) {
                const hit = intersects[0].point;
                
                // Create physical anchor point for the web
                const anchorBody = new CANNON.Body({ mass: 0 });
                anchorBody.position.set(hit.x, hit.y, hit.z);
                world.addBody(anchorBody);
                
                // Create a distance constraint (spring-like tether)
                const distance = playerBody.position.distanceTo(anchorBody.position);
                const constraint = new CANNON.DistanceConstraint(playerBody, anchorBody, distance);
                world.addConstraint(constraint);
                
                // Draw a visual line
                const lineGeo = new THREE.BufferGeometry().setFromPoints([playerMesh.position, hit]);
                const lineMat = new THREE.LineBasicMaterial({ color: 0xffffff, linewidth: 3 });
                const line = new THREE.Line(lineGeo, lineMat);
                scene.add(line);
                
                activeWeb = { constraint, anchorBody, line, hit };
                
                // Automatically recover from flopping when shooting a web
                if (isFlopping) toggleFlop();
            }
        }

        function releaseWeb() {
            if (activeWeb) {
                world.removeConstraint(activeWeb.constraint);
                world.removeBody(activeWeb.anchorBody);
                scene.remove(activeWeb.line);
                activeWeb.line.geometry.dispose();
                activeWeb.line.material.dispose();
                activeWeb = null;
            }
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        function updatePhysics(dt) {
            world.step(1 / 60, dt, 3);
            let isClimbing = false;
            
            // Handle Zoom/Scope FOV Transition
            camera.fov += (targetFOV - camera.fov) * 15 * dt;
            camera.updateProjectionMatrix();
            
            // Handle Water Gun Shooting
            if (isShootingWater && gameMode === 'water') {
                fireWaterGun();
            }
            
            // Update Water Particles
            for (let i = waterParticles.length - 1; i >= 0; i--) {
                let p = waterParticles[i];
                p.mesh.position.add(p.velocity.clone().multiplyScalar(dt));
                p.life -= dt;
                if (p.life <= 0) {
                    scene.remove(p.mesh);
                    waterParticles.splice(i, 1);
                }
            }
            
            // Update NPCs
            npcs.forEach(npc => {
                if (!npc.isFlopping && npc.body.position.y < -10) {
                    npc.isFlopping = true;
                }
                
                // If they fall fast, they flop
                if (!npc.isFlopping && Math.abs(npc.body.velocity.y) > 8) {
                    npc.isFlopping = true;
                }
                
                // Award coins when they hit the ground hard / fall off building
                if (npc.isFlopping && !npc.floppedAndPaid && npc.body.position.y < 10) {
                    npc.floppedAndPaid = true;
                    coins += 10;
                    updateCoinDisplay();
                }
                
                npc.mesh.position.copy(npc.body.position);
                if (npc.isFlopping) {
                    npc.mesh.quaternion.copy(npc.body.quaternion);
                    const currTime = performance.now() * 0.001;
                    npc.armL.rotation.x = Math.sin(currTime * 20) * Math.PI;
                    npc.armR.rotation.x = Math.cos(currTime * 23) * Math.PI;
                    npc.legL.rotation.x = Math.sin(currTime * 17) * Math.PI;
                    npc.legR.rotation.x = Math.cos(currTime * 19) * Math.PI;
                } else {
                    // Standing still
                    npc.body.angularVelocity.set(0,0,0);
                    npc.mesh.quaternion.set(0,0,0,1);
                    npc.armL.rotation.x = 0; npc.armR.rotation.x = 0;
                    npc.legL.rotation.x = 0; npc.legR.rotation.x = 0;
                }
            });

            // Ground check via downward raycast
            const startCheck = new CANNON.Vec3(playerBody.position.x, playerBody.position.y, playerBody.position.z);
            // Radius is 1.35, so we check just slightly below the bottom of the physics sphere
            const endCheck = new CANNON.Vec3(playerBody.position.x, playerBody.position.y - 1.45, playerBody.position.z); 
            
            let onGround = false;
            world.raycastAll(startCheck, endCheck, { skipBackfaces: false }, function(result) {
                if (result.hasHit && result.body !== playerBody) {
                    // Make sure the surface we are standing on is pointing upwards (not a wall)
                    if (result.hitNormalWorld.y > 0.5) {
                        onGround = true;
                    }
                }
            });

            if (onGround) {
                isGrounded = true;
                canJump = true;
                airTime = 0;
            } else {
                isGrounded = false;
                airTime += dt;
            }

            // Web Swing updates
            if (activeWeb) {
                // Reel in the web slightly to pull the player forward and up
                activeWeb.constraint.distance = Math.max(5, activeWeb.constraint.distance - dt * 25);
                
                // Update visual web line to follow player
                const positions = activeWeb.line.geometry.attributes.position.array;
                positions[0] = playerMesh.position.x;
                positions[1] = playerMesh.position.y + 0.8; // Attach near shoulders
                positions[2] = playerMesh.position.z;
                activeWeb.line.geometry.attributes.position.needsUpdate = true;
                
                airTime = 0; // Prevent panicked flips while safely swinging
            }

            // Auto-flop if falling too fast
            if(!isFlopping && playerBody.velocity.y < -15 && isGrounded) {
                 toggleFlop();
            }

            // --- MOVEMENT LOGIC ---
            if (!isFlopping) {
                const moveSpeed = 15;
                const turnSpeed = 3.0; // Rotation speed
                
                // Get camera direction early to check for walls
                const direction = new THREE.Vector3();
                camera.getWorldDirection(direction);
                direction.y = 0; // Flat plane
                if (direction.lengthSq() > 0) direction.normalize();

                // Check if we are touching a wall (which allows climbing)
                const isTouchingWall = checkClimbing(direction);

                if (isScoping) {
                    // Scoping controls: WASD to look around
                    if (keys.w) pitchObject.rotation.x += turnSpeed * dt;
                    if (keys.s) pitchObject.rotation.x -= turnSpeed * dt;
                    if (keys.a) yawObject.rotation.y += turnSpeed * dt;
                    if (keys.d) yawObject.rotation.y -= turnSpeed * dt;
                    
                    // Clamp pitch
                    pitchObject.rotation.x = Math.max(-Math.PI / 2, Math.min(Math.PI / 2, pitchObject.rotation.x));
                    
                    // Stop moving when scoping on the ground
                    if (isGrounded) {
                        playerBody.velocity.x *= 0.5;
                        playerBody.velocity.z *= 0.5;
                    }
                } else {
                    // Normal controls: Turn with A/D, Move with W/S
                    if (keys.a) yawObject.rotation.y += turnSpeed * dt;
                    if (keys.d) yawObject.rotation.y -= turnSpeed * dt;

                    let moveVector = new THREE.Vector3(0,0,0);

                    if (keys.w) moveVector.add(direction);
                    if (keys.s) moveVector.sub(direction);

                    if (moveVector.lengthSq() > 0) {
                        moveVector.normalize();
                        
                        // Directly adjust velocity for responsive "skater" controls
                        playerBody.velocity.x = moveVector.x * moveSpeed;
                        playerBody.velocity.z = moveVector.z * moveSpeed;

                        // Climbing Check (if pressing W and hitting a wall)
                        if (keys.w && isTouchingWall) {
                            isClimbing = true;
                            playerBody.velocity.y = 8; // Constant upward climb speed
                        }
                        
                        if (isClimbing) {
                            airTime = 0; // Reset air time if we grab onto a wall
                        }

                        // Rotate visual mesh towards movement direction
                        const targetAngle = Math.atan2(-moveVector.x, -moveVector.z);
                        playerMesh.rotation.y = targetAngle;
                    } else if (isGrounded) {
                        // Apply heavy braking when no keys are pressed so you don't slide
                        playerBody.velocity.x *= 0.5;
                        playerBody.velocity.z *= 0.5;
                    }
                }
                
                // Uncontrollable air flips when falling
                if (!isGrounded && !isClimbing && airTime > 1.0) {
                    playerMesh.rotation.x -= 3 * dt; // Front flips (Slower)
                    playerMesh.rotation.y += 2 * dt;  // Spin around 360 (Slower)
                    playerMesh.rotation.z += 1.5 * dt;  // Side flips (Slower)
                } else {
                    // Force upright posture when on the ground, climbing, or recently jumped
                    playerMesh.rotation.x = 0;
                    playerMesh.rotation.z = 0;
                }
                
                // Stabilize the sphere so it doesn't roll crazy when trying to walk
                playerBody.angularVelocity.set(0, 0, 0);
            }

            // Sync visual mesh with physics body
            playerMesh.position.copy(playerBody.position);
            
            // If flopping, the mesh rotates with the physics sphere!
            if (isFlopping) {
                playerMesh.quaternion.copy(playerBody.quaternion);
            } else {
                // If standing, lock mesh to upright, feet naturally rest at bottom of sphere
            }
            
            // Update Camera target
            yawObject.position.copy(playerBody.position);

            // Animate limbs
            const currTime = performance.now() * 0.001;
            if (isFlopping) {
                if (currTime - flopStartTime < 5) {
                    // Flail wildly when flopping for the first 5 seconds
                    armL.rotation.x = Math.sin(currTime * 20) * Math.PI;
                    armR.rotation.x = Math.cos(currTime * 23) * Math.PI;
                    legL.rotation.x = Math.sin(currTime * 17) * Math.PI;
                    legR.rotation.x = Math.cos(currTime * 19) * Math.PI;
                    armL.rotation.z = Math.sin(currTime * 15);
                    armR.rotation.z = -Math.cos(currTime * 14);
                } else {
                    // Exhausted/Relaxed state after 5 seconds
                    armL.rotation.x = 0;
                    armR.rotation.x = 0;
                    legL.rotation.x = 0;
                    legR.rotation.x = 0;
                    armL.rotation.z = 0.1;
                    armR.rotation.z = -0.1;
                }
            } else if (activeWeb) {
                // Swinging pose (reaching up with one arm)
                armL.rotation.x = -Math.PI / 2;
                armR.rotation.x = -Math.PI + 0.3; // Reaching up
                legL.rotation.x = 0.5;
                legR.rotation.x = -0.2;
                armL.rotation.z = 0.2;
                armR.rotation.z = -0.2;
            } else if (!isClimbing) {
                // Climbing animation (arms up, moving like scaling a wall)
                const climbAnim = currTime * 15;
                armL.rotation.x = Math.PI - 0.5 + Math.sin(climbAnim) * 0.5;
                armR.rotation.x = Math.PI - 0.5 - Math.sin(climbAnim) * 0.5;
                legL.rotation.x = -0.5 - Math.sin(climbAnim) * 0.5;
                legR.rotation.x = -0.5 + Math.sin(climbAnim) * 0.5;
                armL.rotation.z = 0.1;
                armR.rotation.z = -0.1;
            } else if (!isGrounded) {
            if (airTime > 1.0) {
                // Flail around in panic while doing air flips!
                const fallAnim = currTime * 15; // Slowed down from 30
                armL.rotation.x = Math.sin(fallAnim) * Math.PI;
                armR.rotation.x = -Math.cos(fallAnim) * Math.PI;
                    legL.rotation.x = -Math.sin(fallAnim) * Math.PI;
                    legR.rotation.x = Math.cos(fallAnim) * Math.PI;
                    armL.rotation.z = 0.5;
                    armR.rotation.z = -0.5;
                } else {
                    // Normal jump/falling pose for the first 1.0 seconds
                    armL.rotation.x = -Math.PI / 4;
                    armR.rotation.x = -Math.PI / 4;
                    legL.rotation.x = 0.2;
                    legR.rotation.x = -0.2;
                    armL.rotation.z = 0.2;
                    armR.rotation.z = -0.2;
                }
            } else {
                const currentSpeed = Math.sqrt(playerBody.velocity.x**2 + playerBody.velocity.z**2);
                if (currentSpeed > 0.5) {
                    // Running animation cycle
                    const runAnim = currTime * 15;
                    armL.rotation.x = Math.sin(runAnim) * 1.2;
                    armR.rotation.x = -Math.sin(runAnim) * 1.2;
                    legL.rotation.x = -Math.sin(runAnim) * 1.2;
                    legR.rotation.x = Math.sin(runAnim) * 1.2;
                    armL.rotation.z = 0.1;
                    armR.rotation.z = -0.1;
                } else {
                    // Idle state
                    armL.rotation.x = 0;
                    armR.rotation.x = 0;
                    legL.rotation.x = 0;
                    legR.rotation.x = 0;
                    armL.rotation.z = 0.1;
                    armR.rotation.z = -0.1;
                }
            }
        }

        function checkClimbing(direction) {
            // Start raycast from the exact center of the player and cast outwards
            const start = new CANNON.Vec3(
                playerBody.position.x,
                playerBody.position.y,
                playerBody.position.z
            );
            const end = new CANNON.Vec3(
                start.x + direction.x * 2.0, // Cast forward enough to hit adjacent walls
                start.y,
                start.z + direction.z * 2.0
            );

            let hitWall = false;
            
            // Check all intersections on the line to safely ignore the player's own physics sphere
            world.raycastAll(start, end, { skipBackfaces: false }, function(result) {
                if (result.hasHit && result.body !== playerBody) {
                    // Check if it is a vertical wall by looking at the normal's Y axis
                    if (Math.abs(result.hitNormalWorld.y) < 0.5) {
                        hitWall = true;
                    }
                }
            });

            // Note: Upward boost velocity is now applied in the movement logic loop above!
            return hitWall;
        }

        function animate() {
            requestAnimationFrame(animate);

            const now = performance.now();
            const dt = (now - lastTime) / 1000;
            lastTime = now;

            updatePhysics(dt);
            
            renderer.render(scene, camera);
        }
    </script>
</body>
</html># Nowydabowy.github.io
