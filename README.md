<!DOCTYPE html>
<html lang="es" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>David Alex - Minecraft Portfolio</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter & Press Start 2P for Retro Vibes -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Press+Start+2P&display=swap" rel="stylesheet">
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Three.js & OrbitControls -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>

    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0b0f19;
            color: #f3f4f6;
            overflow-x: hidden;
        }
        .pixel-font {
            font-family: 'Press Start 2P', monospace;
        }
        /* Custom Minecraft styled scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #111827;
        }
        ::-webkit-scrollbar-thumb {
            background: #4b5563;
            border-radius: 4px;
            border: 2px solid #111827;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #10b981;
        }
        /* Minecraft hotbar active slot highlight */
        .hotbar-slot {
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .hotbar-slot.active {
            border-color: #10b981 !important;
            box-shadow: 0 0 15px rgba(16, 185, 129, 0.6);
            transform: scale(1.1) translateY(-4px);
            background-color: rgba(16, 185, 129, 0.1) !important;
        }
        /* Pulse glow animation for ores */
        @keyframes pulse-glow {
            0%, 100% { opacity: 0.6; }
            50% { opacity: 1; filter: drop-shadow(0 0 8px rgba(16, 185, 129, 0.8)); }
        }
        .glowing-badge {
            animation: pulse-glow 2s infinite ease-in-out;
        }
    </style>
</head>
<body class="h-full flex flex-col justify-between select-none">

    <!-- Background Canvas for ThreeJS -->
    <div id="canvas-container" class="absolute inset-0 w-full h-full z-0"></div>

    <!-- Retro ambient dust/grid background -->
    <div class="absolute inset-0 bg-gradient-to-b from-transparent via-[#0b0f19]/30 to-[#0b0f19] pointer-events-none z-1"></div>

    <!-- TOP NAV / HERO BAR -->
    <header class="relative z-10 w-full p-4 md:p-6 flex flex-col md:flex-row justify-between items-center gap-4 bg-gradient-to-b from-black/60 to-transparent pointer-events-none">
        <div class="flex items-center gap-3 pointer-events-auto">
            <div class="w-12 h-12 rounded-lg bg-emerald-500 border-2 border-emerald-400 flex items-center justify-center shadow-lg shadow-emerald-950/40">
                <i class="fa-solid fa-cube text-2xl text-black"></i>
            </div>
            <div>
                <h1 class="pixel-font text-xs md:text-sm text-emerald-400 tracking-wider">DAVID ALEX</h1>
                <p class="text-xs text-gray-400 font-medium">Full Stack & VoIP AI Engineer</p>
            </div>
        </div>

        <div class="flex items-center gap-4 pointer-events-auto">
            <span class="text-xs font-mono bg-slate-900/90 border border-slate-700/50 px-3 py-1.5 rounded-md text-emerald-400 flex items-center gap-2">
                <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                SURVIVAL MODE: READY TO BUILD
            </span>
        </div>
    </header>

    <!-- MAIN PORTFOLIO PANELS (MODALS/CARDS ON DEMAND) -->
    <main class="relative z-10 flex-1 flex flex-col lg:flex-row items-center lg:items-stretch justify-between p-4 md:p-8 pointer-events-none gap-6 h-[calc(100vh-180px)] overflow-hidden">
        
        <!-- Left Help & Navigation Panel -->
        <div class="w-full lg:w-80 flex flex-col gap-4 pointer-events-auto shrink-0 justify-end self-end mb-4 lg:mb-0">
            <div class="bg-slate-900/90 border border-slate-800 backdrop-blur-md p-4 rounded-xl shadow-2xl relative overflow-hidden">
                <div class="absolute top-0 right-0 w-24 h-24 bg-emerald-500/5 blur-xl rounded-full"></div>
                
                <h3 class="pixel-font text-[10px] text-emerald-400 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-compass"></i> GUÍA DE MUNDO
                </h3>
                <p class="text-xs text-gray-400 leading-relaxed mb-4">
                    Utiliza el <strong>mouse / touch</strong> para rotar y explorar la isla flotante. Haz clic sobre los <strong>Ores/Minerales</strong> para minar y descubrir la información de cada sección.
                </p>

                <!-- Achievement pop -->
                <div id="achievement-unlocked" class="hidden flex items-center gap-3 bg-emerald-950/60 border border-emerald-500/30 p-2.5 rounded-lg animate-bounce">
                    <div class="text-xl">🏆</div>
                    <div>
                        <div class="pixel-font text-[8px] text-emerald-400">LOGRO DESBLOQUEADO</div>
                        <div class="text-[11px] font-semibold text-white" id="achievement-title">¡Mineral descubierto!</div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Center 3D focus helper (No text, just spacing for the beautiful island) -->
        <div class="flex-1 min-h-[150px] lg:min-h-0"></div>

        <!-- Right Side Dynamic Content Deck (Minecraft Chest Style but Super Modern) -->
        <div id="content-deck" class="w-full lg:w-[480px] shrink-0 bg-slate-950/90 border-2 border-slate-800 backdrop-blur-xl rounded-2xl shadow-2xl flex flex-col max-h-full pointer-events-auto transition-all duration-300 transform translate-x-0 overflow-hidden">
            
            <!-- Top Card Tab Controller (Looks like a clean tab inside inventory UI) -->
            <div class="border-b border-slate-800 bg-slate-900/40 px-5 py-4 flex justify-between items-center">
                <div class="flex items-center gap-2.5">
                    <span class="w-2.5 h-2.5 rounded bg-red-500 shadow-md shadow-red-500/50" id="card-dot"></span>
                    <h2 class="pixel-font text-xs text-white" id="card-title">INVENTARIO: BIO</h2>
                </div>
                <div class="text-xs text-emerald-400 font-mono tracking-wider" id="card-subtitle">
                    [SLOT 1]
                </div>
            </div>

            <!-- Content Area (Scrollable) -->
            <div class="flex-1 overflow-y-auto p-5 space-y-5" id="card-content">
                <!-- BIO DEFAULT CONTENT (Loaded initially or via Slot 1) -->
                <div class="space-y-4">
                    <div class="flex items-center gap-4">
                        <div class="relative w-16 h-16 bg-slate-800 border-2 border-emerald-500 rounded-xl overflow-hidden flex items-center justify-center">
                            <!-- Procedural Head / Minecraft Icon representation -->
                            <div class="w-12 h-12 bg-gradient-to-tr from-emerald-600 to-teal-400 rounded-md flex items-center justify-center text-slate-950 text-xl font-bold font-mono">
                                DA
                            </div>
                        </div>
                        <div>
                            <h3 class="text-lg font-bold text-white">Aller Onocc David Alex</h3>
                            <p class="text-xs text-emerald-400 font-mono">Ingeniero de Software con IA</p>
                            <p class="text-[11px] text-gray-500 mt-0.5">Lima, Perú • Full Stack VoIP & CRM Expert</p>
                        </div>
                    </div>
                    
                    <div class="bg-slate-900/40 border border-slate-800/80 p-3 rounded-xl">
                        <p class="text-xs text-gray-300 leading-relaxed">
                            Especialista en desarrollo backend y sistemas de telecomunicaciones empresariales. Construyo integraciones de alta fidelidad como conmutadores de voz Inteligentes (VoiceBots), optimizaciones de bases de datos a gran escala y CRMs a la medida optimizados para un rendimiento estricto.
                        </p>
                    </div>

                    <div class="grid grid-cols-2 gap-3 pt-2">
                        <div class="bg-slate-900/60 p-3 rounded-xl border border-slate-800/50">
                            <span class="text-[11px] text-gray-400 block mb-1">PROYECTOS</span>
                            <span class="text-xl font-bold text-white font-mono">10K+ <span class="text-xs text-emerald-400">usuarios</span></span>
                        </div>
                        <div class="bg-slate-900/60 p-3 rounded-xl border border-slate-800/50">
                            <span class="text-[11px] text-gray-400 block mb-1">EXP. DEV</span>
                            <span class="text-xl font-bold text-white font-mono">3+ <span class="text-xs text-teal-400">Años</span></span>
                        </div>
                    </div>
                    
                    <!-- Quick action buttons -->
                    <div class="flex gap-2 pt-2">
                        <a href="https://www.linkedin.com/in/david-alex-aller-onocc-1b4731300/" target="_blank" class="flex-1 py-2.5 px-4 rounded-xl bg-[#0077b5]/10 border border-[#0077b5]/40 hover:bg-[#0077b5]/20 text-[#00a0dc] text-xs font-semibold text-center transition-all flex items-center justify-center gap-2">
                            <i class="fab fa-linkedin"></i> LinkedIn
                        </a>
                        <a href="https://github.com/daller123" target="_blank" class="flex-1 py-2.5 px-4 rounded-xl bg-slate-800/80 border border-slate-700/60 hover:bg-slate-700/80 text-white text-xs font-semibold text-center transition-all flex items-center justify-center gap-2">
                            <i class="fab fa-github"></i> GitHub
                        </a>
                    </div>
                </div>
            </div>

            <!-- Bottom decorative detail (Looks like status/experience bar) -->
            <div class="bg-slate-950 p-2.5 border-t border-slate-900 flex items-center gap-2.5">
                <span class="text-[9px] font-mono text-gray-400 tracking-wider">XP LEVEL</span>
                <div class="flex-1 h-2 bg-slate-900 rounded-full overflow-hidden border border-slate-800">
                    <div class="h-full bg-emerald-500 rounded-full" style="width: 82%;"></div>
                </div>
                <span class="text-[9px] font-mono text-emerald-400 font-bold">82</span>
            </div>
        </div>
    </main>

    <!-- INTERACTIVE MINECRAFT HOTBAR -->
    <footer class="relative z-10 w-full p-4 flex flex-col items-center gap-3 bg-gradient-to-t from-black/80 to-transparent">
        <div class="text-center">
            <p id="hotbar-tip" class="text-[10px] text-gray-400 font-mono tracking-wider animate-pulse">SELECCIONA UN APARTADO DEL INVENTARIO O GIRA LA ISLA</p>
        </div>
        
        <!-- The classic Minecraft 9-slot Hotbar, designed in an ultra elegant modern glass style -->
        <div class="flex items-center justify-center bg-slate-950/80 border border-slate-800 p-1.5 rounded-2xl shadow-2xl gap-1">
            
            <!-- Slot 1: Bio (Steve Head) -->
            <button onclick="selectSlot(1)" id="slot-1" class="hotbar-slot active w-11 h-11 md:w-13 md:h-13 rounded-xl bg-slate-900 border border-slate-800 flex flex-col items-center justify-center relative cursor-pointer" title="Sobre Mí">
                <i class="fa-solid fa-user-astronaut text-sm text-emerald-400"></i>
                <span class="absolute bottom-0.5 right-1.5 text-[8px] font-mono text-gray-500">1</span>
            </button>
            
            <!-- Slot 2: Tech Stack (Redstone) -->
            <button onclick="selectSlot(2)" id="slot-2" class="hotbar-slot w-11 h-11 md:w-13 md:h-13 rounded-xl bg-slate-900 border border-slate-800 flex flex-col items-center justify-center relative cursor-pointer" title="Tecnologías">
                <i class="fa-solid fa-fire text-sm text-red-400"></i>
                <span class="absolute bottom-0.5 right-1.5 text-[8px] font-mono text-gray-500">2</span>
            </button>
            
            <!-- Slot 3: CRM Geincos (Diamond) -->
            <button onclick="selectSlot(3)" id="slot-3" class="hotbar-slot w-11 h-11 md:w-13 md:h-13 rounded-xl bg-slate-900 border border-slate-800 flex flex-col items-center justify-center relative cursor-pointer" title="CRM Geincos">
                <i class="fa-solid fa-gem text-sm text-cyan-400"></i>
                <span class="absolute bottom-0.5 right-1.5 text-[8px] font-mono text-gray-500">3</span>
            </button>
            
            <!-- Slot 4: VoiceBot (Gold) -->
            <button onclick="selectSlot(4)" id="slot-4" class="hotbar-slot w-11 h-11 md:w-13 md:h-13 rounded-xl bg-slate-900 border border-slate-800 flex flex-col items-center justify-center relative cursor-pointer" title="VoiceBot System">
                <i class="fa-solid fa-microphone-lines text-sm text-amber-400"></i>
                <span class="absolute bottom-0.5 right-1.5 text-[8px] font-mono text-gray-500">4</span>
            </button>

            <!-- Slot 5: DevOps (Iron) -->
            <button onclick="selectSlot(5)" id="slot-5" class="hotbar-slot w-11 h-11 md:w-13 md:h-13 rounded-xl bg-slate-900 border border-slate-800 flex flex-col items-center justify-center relative cursor-pointer" title="DevOps Suite">
                <i class="fa-solid fa-terminal text-sm text-slate-300"></i>
                <span class="absolute bottom-0.5 right-1.5 text-[8px] font-mono text-gray-500">5</span>
            </button>

            <!-- Slot 6: Analytics (Emerald) -->
            <button onclick="selectSlot(6)" id="slot-6" class="hotbar-slot w-11 h-11 md:w-13 md:h-13 rounded-xl bg-slate-900 border border-slate-800 flex flex-col items-center justify-center relative cursor-pointer" title="Estadísticas">
                <i class="fa-solid fa-chart-line text-sm text-emerald-400"></i>
                <span class="absolute bottom-0.5 right-1.5 text-[8px] font-mono text-gray-500">6</span>
            </button>

            <!-- Slot 7: Contact (Ender Pearl) -->
            <button onclick="selectSlot(7)" id="slot-7" class="hotbar-slot w-11 h-11 md:w-13 md:h-13 rounded-xl bg-slate-900 border border-slate-800 flex flex-col items-center justify-center relative cursor-pointer" title="Contacto">
                <i class="fa-solid fa-envelope-open-text text-sm text-indigo-400"></i>
                <span class="absolute bottom-0.5 right-1.5 text-[8px] font-mono text-gray-500">7</span>
            </button>
        </div>
    </footer>

    <script>
        // Web Audio Synthesizer for retro Minecraft sounds
        const AudioCtx = window.AudioContext || window.webkitAudioContext;
        let audioCtx = null;

        function initAudio() {
            if (!audioCtx) {
                audioCtx = new AudioCtx();
            }
        }

        // Retro synthesized click sound
        function playClickSound() {
            try {
                initAudio();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(300, audioCtx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(100, audioCtx.currentTime + 0.1);
                
                gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.1);
                
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start();
                osc.stop(audioCtx.currentTime + 0.1);
            } catch(e) {}
        }

        // Retro synthesized Minecraft "Level Up" ding sound
        function playLevelUpSound() {
            try {
                initAudio();
                const now = audioCtx.currentTime;
                
                // Double note synth
                const notes = [440, 554, 659, 880];
                notes.forEach((freq, idx) => {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.08);
                    
                    gain.gain.setValueAtTime(0.08, now + idx * 0.08);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + idx * 0.08 + 0.25);
                    
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(now + idx * 0.08);
                    osc.stop(now + idx * 0.08 + 0.25);
                });
            } catch(e) {}
        }

        // Retro sound of breaking a voxel block
        function playBlockBreakSound() {
            try {
                initAudio();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                
                osc.type = 'square';
                osc.frequency.setValueAtTime(150, audioCtx.currentTime);
                osc.frequency.linearRampToValueAtTime(50, audioCtx.currentTime + 0.15);
                
                gain.gain.setValueAtTime(0.12, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
                
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start();
                osc.stop(audioCtx.currentTime + 0.15);
            } catch(e) {}
        }
    </script>

    <script>
        // Draw voxel textures programmatically on HTML5 canvases
        function createPixelTexture(type) {
            const canvas = document.createElement('canvas');
            canvas.width = 16;
            canvas.height = 16;
            const ctx = canvas.getContext('2d');

            // Draw based on block style
            if (type === 'grass_top') {
                ctx.fillStyle = '#4c9e38'; // Dark grass base
                ctx.fillRect(0, 0, 16, 16);
                // Pixel noise
                for (let i = 0; i < 35; i++) {
                    ctx.fillStyle = i % 2 === 0 ? '#5cae48' : '#3c8e28';
                    ctx.fillRect(Math.floor(Math.random() * 16), Math.floor(Math.random() * 16), 1, 1);
                }
            } else if (type === 'grass_side') {
                ctx.fillStyle = '#866043'; // Dirt side
                ctx.fillRect(0, 0, 16, 16);
                // Grass hanging down
                ctx.fillStyle = '#4c9e38';
                ctx.fillRect(0, 0, 16, 4);
                ctx.fillRect(1, 4, 2, 2);
                ctx.fillRect(5, 4, 3, 1);
                ctx.fillRect(11, 4, 2, 3);
                ctx.fillRect(14, 4, 1, 1);
            } else if (type === 'dirt') {
                ctx.fillStyle = '#866043';
                ctx.fillRect(0, 0, 16, 16);
                for (let i = 0; i < 20; i++) {
                    ctx.fillStyle = '#573d26';
                    ctx.fillRect(Math.floor(Math.random() * 16), Math.floor(Math.random() * 16), 1, 2);
                }
            } else if (type === 'stone') {
                ctx.fillStyle = '#737373';
                ctx.fillRect(0, 0, 16, 16);
                for (let i = 0; i < 30; i++) {
                    ctx.fillStyle = i % 2 === 0 ? '#525252' : '#a3a3a3';
                    ctx.fillRect(Math.floor(Math.random() * 16), Math.floor(Math.random() * 16), 1, 1);
                }
            } else if (type === 'diamond_ore') {
                ctx.fillStyle = '#737373';
                ctx.fillRect(0, 0, 16, 16);
                // Gems
                ctx.fillStyle = '#22d3ee';
                ctx.fillRect(2, 4, 2, 2);
                ctx.fillRect(8, 2, 2, 2);
                ctx.fillRect(11, 10, 3, 2);
                ctx.fillRect(4, 12, 2, 2);
            } else if (type === 'redstone_ore') {
                ctx.fillStyle = '#737373';
                ctx.fillRect(0, 0, 16, 16);
                // Emissive Redstone
                ctx.fillStyle = '#f87171';
                ctx.fillRect(3, 3, 2, 2);
                ctx.fillRect(10, 4, 2, 2);
                ctx.fillRect(12, 11, 2, 3);
                ctx.fillRect(5, 11, 2, 2);
            } else if (type === 'gold_ore') {
                ctx.fillStyle = '#737373';
                ctx.fillRect(0, 0, 16, 16);
                // Gold
                ctx.fillStyle = '#fbbf24';
                ctx.fillRect(4, 2, 2, 2);
                ctx.fillRect(9, 6, 3, 2);
                ctx.fillRect(2, 11, 2, 2);
                ctx.fillRect(11, 12, 2, 2);
            } else if (type === 'lapis_ore') {
                ctx.fillStyle = '#737373';
                ctx.fillRect(0, 0, 16, 16);
                // Lapis
                ctx.fillStyle = '#2563eb';
                ctx.fillRect(3, 4, 3, 2);
                ctx.fillRect(10, 3, 2, 2);
                ctx.fillRect(11, 10, 2, 2);
                ctx.fillRect(4, 11, 3, 2);
            }

            const texture = new THREE.CanvasTexture(canvas);
            texture.magFilter = THREE.NearestFilter;
            texture.minFilter = THREE.NearestFilter;
            return texture;
        }
    </script>

    <script>
        let scene, camera, renderer, controls;
        let blocks = [];
        let particles = [];
        const container = document.getElementById('canvas-container');

        // Create Three.js Material sets
        const materials = {};
        function initMaterials() {
            const grassTop = createPixelTexture('grass_top');
            const grassSide = createPixelTexture('grass_side');
            const dirt = createPixelTexture('dirt');
            const stone = createPixelTexture('stone');
            const diamond = createPixelTexture('diamond_ore');
            const redstone = createPixelTexture('redstone_ore');
            const gold = createPixelTexture('gold_ore');
            const lapis = createPixelTexture('lapis_ore');

            materials.grass = [
                new THREE.MeshLambertMaterial({ map: grassSide }), // Right
                new THREE.MeshLambertMaterial({ map: grassSide }), // Left
                new THREE.MeshLambertMaterial({ map: grassTop }),  // Top
                new THREE.MeshLambertMaterial({ map: dirt }),      // Bottom
                new THREE.MeshLambertMaterial({ map: grassSide }), // Front
                new THREE.MeshLambertMaterial({ map: grassSide })  // Back
            ];

            materials.dirt = new THREE.MeshLambertMaterial({ map: dirt });
            materials.stone = new THREE.MeshLambertMaterial({ map: stone });
            materials.diamond = new THREE.MeshLambertMaterial({ map: diamond, emissive: new THREE.Color('#06b6d4'), emissiveIntensity: 0.1 });
            materials.redstone = new THREE.MeshLambertMaterial({ map: redstone, emissive: new THREE.Color('#ef4444'), emissiveIntensity: 0.15 });
            materials.gold = new THREE.MeshLambertMaterial({ map: gold, emissive: new THREE.Color('#f59e0b'), emissiveIntensity: 0.1 });
            materials.lapis = new THREE.MeshLambertMaterial({ map: lapis, emissive: new THREE.Color('#2563eb'), emissiveIntensity: 0.1 });
        }

        // Initialize Three.js WebGL application
        function initThree() {
            scene = new THREE.Scene();
            scene.fog = new THREE.FogExp2('#0b0f19', 0.045);

            camera = new THREE.PerspectiveCamera(50, container.clientWidth / container.clientHeight, 0.1, 1000);
            camera.position.set(6, 6, 12);

            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            renderer.setSize(container.clientWidth, container.clientHeight);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            renderer.shadowMap.enabled = true;
            renderer.shadowMap.type = THREE.PCFSoftShadowMap;
            container.appendChild(renderer.domElement);

            // Orbit Controls setup
            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.05;
            controls.maxPolarAngle = Math.PI / 2.1; // Don't let user go completely under ground
            controls.minDistance = 5;
            controls.maxDistance = 25;
            controls.autoRotate = true;
            controls.autoRotateSpeed = 0.5;

            // Lights
            const ambientLight = new THREE.AmbientLight('#ffffff', 0.6);
            scene.add(ambientLight);

            const dirLight = new THREE.DirectionalLight('#ffffff', 0.8);
            dirLight.position.set(10, 20, 10);
            dirLight.castShadow = true;
            dirLight.shadow.mapSize.width = 1024;
            dirLight.shadow.mapSize.height = 1024;
            scene.add(dirLight);

            // Add warm, cozy orange torch light underneath
            const pointLight = new THREE.PointLight('#f97316', 1.5, 10);
            pointLight.position.set(0, 1.5, 0);
            scene.add(pointLight);

            initMaterials();
            createVoxelIsland();

            // Resize listener
            window.addEventListener('resize', onWindowResize);
            
            // Raycasting for clicking blocks
            renderer.domElement.addEventListener('pointerdown', onPointerDown);
        }

        function createVoxelIsland() {
            const geometry = new THREE.BoxGeometry(1, 1, 1);
            
            // Build a 5x5 Island structure
            // Layer 0 (Top - Grass)
            for (let x = -2; x <= 2; x++) {
                for (let z = -2; z <= 2; z++) {
                    // Skip corners to make it slightly round
                    if (Math.abs(x) === 2 && Math.abs(z) === 2) continue;
                    
                    const mesh = new THREE.Mesh(geometry, materials.grass);
                    mesh.position.set(x, 1, z);
                    mesh.userData = { type: 'grass', name: 'Grass Block' };
                    scene.add(mesh);
                    blocks.push(mesh);
                }
            }

            // Layer -1 (Dirt layers with some ores)
            for (let x = -2; x <= 2; x++) {
                for (let z = -2; z <= 2; z++) {
                    if (Math.abs(x) === 2 && Math.abs(z) === 2) continue;
                    
                    const mesh = new THREE.Mesh(geometry, materials.dirt);
                    mesh.position.set(x, 0, z);
                    mesh.userData = { type: 'dirt' };
                    scene.add(mesh);
                    blocks.push(mesh);
                }
            }

            // Base core (Stone & Ores)
            // Placement of specific interactive Ores
            const oresData = [
                { pos: [0, -1, 0], mat: materials.diamond, name: 'Diamond Ore', slot: 3 },  // Center core - CRM
                { pos: [1, -1, 1], mat: materials.redstone, name: 'Redstone Ore', slot: 2 }, // Redstone - Technologies
                { pos: [-1, -1, 0], mat: materials.gold, name: 'Gold Ore', slot: 4 },      // Gold - VoiceBot
                { pos: [0, -1, 1], mat: materials.lapis, name: 'Lapis Ore', slot: 5 }       // Lapis - DevOps
            ];

            // Build stone foundation and inject Ores
            for (let x = -1; x <= 1; x++) {
                for (let z = -1; z <= 1; z++) {
                    // Check if there is an ore in this slot
                    const ore = oresData.find(o => o.pos[0] === x && o.pos[1] === -1 && o.pos[2] === z);
                    if (ore) {
                        const mesh = new THREE.Mesh(geometry, ore.mat);
                        mesh.position.set(x, -1, z);
                        mesh.userData = { type: 'ore', name: ore.name, slot: ore.slot };
                        scene.add(mesh);
                        blocks.push(mesh);
                    } else {
                        const mesh = new THREE.Mesh(geometry, materials.stone);
                        mesh.position.set(x, -1, z);
                        mesh.userData = { type: 'stone' };
                        scene.add(mesh);
                        blocks.push(mesh);
                    }
                }
            }

            // Add a little floating Cloud above the island
            const cloudGeometry = new THREE.BoxGeometry(2.5, 0.4, 1.5);
            const cloudMat = new THREE.MeshBasicMaterial({ color: 0xffffff, transparent: true, opacity: 0.8 });
            const cloud = new THREE.Mesh(cloudGeometry, cloudMat);
            cloud.position.set(2, 3.5, -2);
            scene.add(cloud);
            
            // Subtle tree on grass
            const trunkGeo = new THREE.BoxGeometry(0.4, 1.2, 0.4);
            const trunkMat = new THREE.MeshLambertMaterial({ color: 0x5c4033 });
            const trunk = new THREE.Mesh(trunkGeo, trunkMat);
            trunk.position.set(1.5, 2.1, -1);
            scene.add(trunk);

            const leavesGeo = new THREE.BoxGeometry(1.2, 1.2, 1.2);
            const leavesMat = new THREE.MeshLambertMaterial({ color: 0x2e5c1e });
            const leaves = new THREE.Mesh(leavesGeo, leavesMat);
            leaves.position.set(1.5, 3, -1);
            scene.add(leaves);
        }

        // Particle system for block mining / clicking explosion
        function spawnParticles(position, colorHex) {
            const particleCount = 20;
            const particleGeometry = new THREE.BoxGeometry(0.12, 0.12, 0.12);
            const particleMaterial = new THREE.MeshBasicMaterial({ color: colorHex });

            for (let i = 0; i < particleCount; i++) {
                const particle = new THREE.Mesh(particleGeometry, particleMaterial);
                particle.position.copy(position);
                
                // Random velocities
                particle.userData = {
                    velocity: new THREE.Vector3(
                        (Math.random() - 0.5) * 0.2,
                        (Math.random() - 0.1) * 0.3,
                        (Math.random() - 0.5) * 0.2
                    ),
                    gravity: 0.01,
                    lifespan: 1.0 // opacity/time indicator
                };
                
                scene.add(particle);
                particles.push(particle);
            }
        }

        // Handle Pointer/Click Events inside WebGL
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();

        function onPointerDown(event) {
            // Only respond to left clicks / direct taps
            if (event.button !== 0) return;

            // Calculate mouse position in normalized device coordinates
            const rect = renderer.domElement.getBoundingClientRect();
            mouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1;
            mouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects(blocks);

            if (intersects.length > 0) {
                const clickedObj = intersects[0].object;
                
                // Disable auto rotate so user can examine closely
                controls.autoRotate = false;
                
                // Play sound effects
                if (clickedObj.userData.type === 'ore') {
                    playBlockBreakSound();
                    
                    // Bounce animation effect on click
                    clickedObj.scale.set(0.8, 0.8, 0.8);
                    setTimeout(() => clickedObj.scale.set(1, 1, 1), 150);
                    
                    // Particle coloring
                    let col = 0xffffff;
                    if (clickedObj.userData.name.includes('Diamond')) col = 0x22d3ee;
                    if (clickedObj.userData.name.includes('Redstone')) col = 0xef4444;
                    if (clickedObj.userData.name.includes('Gold')) col = 0xf59e0b;
                    if (clickedObj.userData.name.includes('Lapis')) col = 0x2563eb;
                    
                    spawnParticles(clickedObj.position, col);

                    // Show visual reward achievement in HUD
                    const achievement = document.getElementById('achievement-unlocked');
                    const achTitle = document.getElementById('achievement-title');
                    achTitle.innerText = `¡Minado: ${clickedObj.userData.name}!`;
                    achievement.classList.remove('hidden');
                    playLevelUpSound();
                    setTimeout(() => achievement.classList.add('hidden'), 4000);

                    // Direct user to correct slot in info panel
                    if (clickedObj.userData.slot) {
                        selectSlot(clickedObj.userData.slot);
                    }
                } else {
                    playClickSound();
                    spawnParticles(intersects[0].point, 0x866043);
                }
            }
        }

        function onWindowResize() {
            camera.aspect = container.clientWidth / container.clientHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(container.clientWidth, container.clientHeight);
        }
    </script>

    <script>
        // Simplified and elegant portfolios data
        const slotData = {
            1: {
                title: "INVENTARIO: BIO",
                subtitle: "[SLOT 1 - STEVE]",
                dotColor: "bg-emerald-500",
                content: `
                    <div class="space-y-4">
                        <div class="flex items-center gap-4">
                            <div class="relative w-16 h-16 bg-slate-800 border-2 border-emerald-500 rounded-xl overflow-hidden flex items-center justify-center">
                                <div class="w-12 h-12 bg-gradient-to-tr from-emerald-600 to-teal-400 rounded-md flex items-center justify-center text-slate-950 text-xl font-bold font-mono">DA</div>
                            </div>
                            <div>
                                <h3 class="text-lg font-bold text-white">Aller Onocc David Alex</h3>
                                <p class="text-xs text-emerald-400 font-mono">Ingeniero de Software con IA</p>
                                <p class="text-[11px] text-gray-500 mt-0.5">Lima, Perú • Full Stack VoIP & CRM Expert</p>
                            </div>
                        </div>
                        <div class="bg-slate-900/40 border border-slate-800/80 p-3 rounded-xl">
                            <p class="text-xs text-gray-300 leading-relaxed">
                                Especialista en desarrollo backend y sistemas de telecomunicaciones empresariales. Construyo integraciones de alta fidelidad como conmutadores de voz Inteligentes (VoiceBots), optimizaciones de bases de datos a gran escala y CRMs a la medida optimizados para un rendimiento estricto.
                            </p>
                        </div>
                        <div class="grid grid-cols-2 gap-3 pt-2">
                            <div class="bg-slate-900/60 p-3 rounded-xl border border-slate-800/50">
                                <span class="text-[11px] text-gray-400 block mb-1">PROYECTOS</span>
                                <span class="text-xl font-bold text-white font-mono">10K+ <span class="text-xs text-emerald-400">usuarios</span></span>
                            </div>
                            <div class="bg-slate-900/60 p-3 rounded-xl border border-slate-800/50">
                                <span class="text-[11px] text-gray-400 block mb-1">EXP. DEV</span>
                                <span class="text-xl font-bold text-white font-mono">3+ <span class="text-xs text-teal-400">Años</span></span>
                            </div>
                        </div>
                        <div class="flex gap-2 pt-2">
                            <a href="https://www.linkedin.com/in/david-alex-aller-onocc-1b4731300/" target="_blank" class="flex-1 py-2.5 px-4 rounded-xl bg-[#0077b5]/10 border border-[#0077b5]/40 hover:bg-[#0077b5]/20 text-[#00a0dc] text-xs font-semibold text-center transition-all flex items-center justify-center gap-2">
                                <i class="fab fa-linkedin"></i> LinkedIn
                            </a>
                            <a href="https://github.com/daller123" target="_blank" class="flex-1 py-2.5 px-4 rounded-xl bg-slate-800/80 border border-slate-700/60 hover:bg-slate-700/80 text-white text-xs font-semibold text-center transition-all flex items-center justify-center gap-2">
                                <i class="fab fa-github"></i> GitHub
                            </a>
                        </div>
                    </div>
                `
            },
            2: {
                title: "MINERAL: TECH STACK",
                subtitle: "[SLOT 2 - REDSTONE ORE]",
                dotColor: "bg-red-500",
                content: `
                    <div class="space-y-4">
                        <div class="p-3 bg-red-950/20 border border-red-900/50 rounded-xl">
                            <p class="text-xs text-red-300 leading-relaxed">
                                Un "circuito" sólido de tecnologías backend, telecomunicaciones robustas e infraestructuras automatizadas.
                            </p>
                        </div>

                        <div class="space-y-3">
                            <div>
                                <h4 class="text-[11px] font-mono text-gray-400 mb-1.5">BACKEND ENTERPRISE</h4>
                                <div class="flex flex-wrap gap-1.5">
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-white font-mono">PHP 8.2+</span>
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-red-400 font-mono">Laravel 10</span>
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-green-400 font-mono">Node.js</span>
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-blue-400 font-mono">Python</span>
                                </div>
                            </div>

                            <div>
                                <h4 class="text-[11px] font-mono text-gray-400 mb-1.5">TELECOMUNICACIONES & VOIP</h4>
                                <div class="flex flex-wrap gap-1.5">
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-teal-400 font-mono">WebRTC</span>
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-amber-400 font-mono">Janus Gateway</span>
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-sky-400 font-mono">Asterisk PBX</span>
                                </div>
                            </div>

                            <div>
                                <h4 class="text-[11px] font-mono text-gray-400 mb-1.5">DATA & DEV_INFRA</h4>
                                <div class="flex flex-wrap gap-1.5">
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-white font-mono">MySQL 8 / Postgres</span>
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-blue-500 font-mono">Docker & Compose</span>
                                    <span class="text-xs bg-slate-900 border border-slate-800 px-2.5 py-1 rounded text-emerald-400 font-mono">Redis</span>
                                </div>
                            </div>
                        </div>
                    </div>
                `
            },
            3: {
                title: "PROYECTO: CRM GEINCOS",
                subtitle: "[SLOT 3 - DIAMOND ORE]",
                dotColor: "bg-cyan-400",
                content: `
                    <div class="space-y-4">
                        <div class="flex items-center gap-2">
                            <span class="px-2.5 py-1 rounded bg-cyan-950/50 border border-cyan-500/30 text-[10px] text-cyan-400 font-mono font-bold uppercase">PROYECTO CORE</span>
                        </div>
                        
                        <h3 class="text-md font-bold text-white">CRM Geincos - Plataforma Integral de Gestión</h3>
                        <p class="text-xs text-gray-400 leading-relaxed">
                            Diseñado como un sistema robusto a gran escala para carteras judiciales masivas, reclutamiento automatizado, telemercadeo y analíticas complejas en vivo.
                        </p>

                        <div class="bg-slate-900/50 p-3.5 rounded-xl border border-slate-800/50 space-y-2">
                            <div class="flex items-start gap-2.5">
                                <i class="fa-solid fa-circle-check text-xs text-emerald-500 mt-1"></i>
                                <p class="text-xs text-gray-300"><strong>Capacidad:</strong> Manejo fluido de más de 10,000 contactos simultáneos.</p>
                            </div>
                            <div class="flex items-start gap-2.5">
                                <i class="fa-solid fa-circle-check text-xs text-emerald-500 mt-1"></i>
                                <p class="text-xs text-gray-300"><strong>Estadísticas:</strong> Reportes avanzados de llamadas con latencias menores a 100ms.</p>
                            </div>
                            <div class="flex items-start gap-2.5">
                                <i class="fa-solid fa-circle-check text-xs text-emerald-500 mt-1"></i>
                                <p class="text-xs text-gray-300"><strong>Infraestructura:</strong> Dockerizado, balanceado con Nginx y supervisado por daemons.</p>
                            </div>
                        </div>

                        <div class="flex flex-wrap gap-1">
                            <span class="text-[10px] bg-slate-900 px-2 py-0.5 rounded text-gray-400 font-mono">Laravel 10</span>
                            <span class="text-[10px] bg-slate-900 px-2 py-0.5 rounded text-gray-400 font-mono">MySQL 8</span>
                            <span class="text-[10px] bg-slate-900 px-2 py-0.5 rounded text-gray-400 font-mono">Tailwind</span>
                            <span class="text-[10px] bg-slate-900 px-2 py-0.5 rounded text-gray-400 font-mono">Alpine.js</span>
                        </div>
                    </div>
                `
            },
            4: {
                title: "SISTEMA: VOICEBOT",
                subtitle: "[SLOT 4 - GOLD ORE]",
                dotColor: "bg-amber-400",
                content: `
                    <div class="space-y-4">
                        <div class="flex items-center gap-2">
                            <span class="px-2.5 py-1 rounded bg-amber-950/50 border border-amber-500/30 text-[10px] text-amber-400 font-mono font-bold uppercase">TELECOMUNICACIONES</span>
                        </div>

                        <h3 class="text-md font-bold text-white">VoiceBot Automatizado VoIP</h3>
                        <p class="text-xs text-gray-400 leading-relaxed">
                            Plataforma autónoma e inteligente para optimizar y auditar campañas de llamadas en tiempo real mediante análisis de voz.
                        </p>

                        <div class="bg-slate-900/50 p-3.5 rounded-xl border border-slate-800/50 space-y-2">
                            <div class="flex items-start gap-2.5">
                                <i class="fa-solid fa-circle-check text-xs text-amber-500 mt-1"></i>
                                <p class="text-xs text-gray-300"><strong>Integración:</strong> Integración directa con VoIP Gateways como Janus y Asterisk.</p>
                            </div>
                            <div class="flex items-start gap-2.5">
                                <i class="fa-solid fa-circle-check text-xs text-amber-500 mt-1"></i>
                                <p class="text-xs text-gray-300"><strong>Data Stream:</strong> Análisis de tiempos de llamadas (timbrado, ACW, audio activo).</p>
                            </div>
                        </div>

                        <div class="flex flex-wrap gap-1">
                            <span class="text-[10px] bg-slate-900 px-2 py-0.5 rounded text-gray-400 font-mono">Node.js</span>
                            <span class="text-[10px] bg-slate-900 px-2 py-0.5 rounded text-gray-400 font-mono">Janus Gateway</span>
                            <span class="text-[10px] bg-slate-900 px-2 py-0.5 rounded text-gray-400 font-mono">WebRTC</span>
                        </div>
                    </div>
                `
            },
            5: {
                title: "SUITE: DEVOPS & INFRA",
                subtitle: "[SLOT 5 - LAPIS ORE]",
                dotColor: "bg-indigo-400",
                content: `
                    <div class="space-y-4">
                        <div class="flex items-center gap-2">
                            <span class="px-2.5 py-1 rounded bg-indigo-950/50 border border-indigo-500/30 text-[10px] text-indigo-400 font-mono font-bold uppercase">LOGÍSTICA & DEPLOY</span>
                        </div>

                        <h3 class="text-md font-bold text-white">DevOps Automatizado</h3>
                        <p class="text-xs text-gray-400 leading-relaxed">
                            Arquitectura limpia de despliegue continuo orientada a optimizar el rendimiento y anular la inactividad operativa.
                        </p>

                        <div class="space-y-3">
                            <div class="p-3 bg-slate-900/50 rounded-lg border border-slate-800">
                                <h4 class="text-xs font-bold text-white mb-1">🔧 Migraciones Zero-Downtime</h4>
                                <p class="text-[11px] text-gray-400">Migración exitosa a contenedores Docker con cero cortes para los usuarios concurrentes.</p>
                            </div>
                            <div class="p-3 bg-slate-900/50 rounded-lg border border-slate-800">
                                <h4 class="text-xs font-bold text-white mb-1">📊 Auditoría de Procesos</h4>
                                <p class="text-[11px] text-gray-400">Scripts personalizados para testing de VoIP y base de datos con diagnósticos de integridad avanzados.</p>
                            </div>
                        </div>
                    </div>
                `
            },
            6: {
                title: "ESTADÍSTICAS & LOGROS",
                subtitle: "[SLOT 6 - EMERALD]",
                dotColor: "bg-emerald-400",
                content: `
                    <div class="space-y-4">
                        <h3 class="text-md font-bold text-white">Impacto Profesional</h3>

                        <div class="space-y-2">
                            <div class="flex justify-between items-center text-xs">
                                <span class="text-gray-400">Rendimiento en Consultas</span>
                                <span class="text-emerald-400 font-bold font-mono">+70%</span>
                            </div>
                            <div class="w-full bg-slate-900 h-2 rounded-full overflow-hidden">
                                <div class="bg-emerald-500 h-full" style="width: 70%"></div>
                            </div>
                        </div>

                        <div class="space-y-2">
                            <div class="flex justify-between items-center text-xs">
                                <span class="text-gray-400">Reducción de Fallos en Prod</span>
                                <span class="text-emerald-400 font-bold font-mono">-60%</span>
                            </div>
                            <div class="w-full bg-slate-900 h-2 rounded-full overflow-hidden">
                                <div class="bg-emerald-500 h-full" style="width: 60%"></div>
                            </div>
                        </div>

                        <div class="bg-slate-900/60 p-3 rounded-xl border border-slate-800 space-y-1">
                            <span class="text-[10px] text-emerald-400 font-mono uppercase block">Último logro</span>
                            <p class="text-xs text-white">Estadísticas e integración de Audio optimizada bajo latencia.</p>
                        </div>
                    </div>
                `
            },
            7: {
                title: "INTERFAZ: CONTACTO",
                subtitle: "[SLOT 7 - ENDER PEARL]",
                dotColor: "bg-indigo-400",
                content: `
                    <div class="space-y-4">
                        <h3 class="text-md font-bold text-white">¡Inicia una nueva Partida!</h3>
                        <p class="text-xs text-gray-400 leading-relaxed">
                            Si buscas un ingeniero capaz de desarrollar arquitecturas backend sólidas, optimizar infraestructuras DevOps o configurar integraciones WebRTC/VoIP eficientes, hablemos.
                        </p>

                        <div class="space-y-2 pt-2">
                            <div class="flex items-center gap-3 p-3 bg-slate-900/60 border border-slate-800 rounded-xl">
                                <i class="fa-solid fa-envelope text-indigo-400 text-sm"></i>
                                <div>
                                    <span class="text-[10px] text-gray-500 block">CORREO</span>
                                    <span class="text-xs text-white select-all">david.aller.onocc@gmail.com</span>
                                </div>
                            </div>

                            <div class="flex items-center gap-3 p-3 bg-slate-900/60 border border-slate-800 rounded-xl">
                                <i class="fab fa-linkedin text-indigo-400 text-sm"></i>
                                <div>
                                    <span class="text-[10px] text-gray-500 block">LINKEDIN</span>
                                    <a href="https://www.linkedin.com/in/david-alex-aller-onocc-1b4731300/" target="_blank" class="text-xs text-white hover:underline">david-alex-aller-onocc-1b4731300/</a>
                                </div>
                            </div>
                        </div>
                    </div>
                `
            }
        };

        // Navigation Controller
        function selectSlot(slotNum) {
            // Update Active UI States
            document.querySelectorAll('.hotbar-slot').forEach(slot => {
                slot.classList.remove('active');
            });
            const activeSlot = document.getElementById(`slot-${slotNum}`);
            if (activeSlot) {
                activeSlot.classList.add('active');
            }

            // Play clean retro audio response
            playClickSound();

            // Load Content into Deck dynamically
            const data = slotData[slotNum];
            if (data) {
                // Glow Dot indicators
                const dot = document.getElementById('card-dot');
                dot.className = `w-2.5 h-2.5 rounded shadow-md ${data.dotColor}`;
                
                document.getElementById('card-title').innerText = data.title;
                document.getElementById('card-subtitle').innerText = data.subtitle;
                document.getElementById('card-content').innerHTML = data.content;
            }

            // Adjust hotbar tip feedback
            const tip = document.getElementById('hotbar-tip');
            tip.innerText = `ACTIVASTE EL SLOT ${slotNum}: ${data ? data.title : ''}`;
        }
    </script>

    <script>
        // Init WebGL App on Load
        window.onload = function() {
            initThree();
            animate();
        };

        // Animation Loop
        function animate() {
            requestAnimationFrame(animate);

            // Rotate controls smoothly
            if (controls) {
                controls.update();
            }

            // Animate Particles with physical gravity
            for (let i = particles.length - 1; i >= 0; i--) {
                const p = particles[i];
                p.position.add(p.userData.velocity);
                p.userData.velocity.y -= p.userData.gravity; // Gravity pull down
                p.userData.lifespan -= 0.02;
                p.material.opacity = p.userData.lifespan;

                // Cleanup faded out particles
                if (p.userData.lifespan <= 0) {
                    scene.remove(p);
                    particles.splice(i, 1);
                }
            }

            if (renderer && scene && camera) {
                renderer.render(scene, camera);
            }
        }
    </script>
</body>
</html>
