<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>¡Te elijo a ti!</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=VT323&display=swap" rel="stylesheet">
    <style>
        .font-pixel { font-family: 'Press Start 2P', cursive; }
        .font-retro { font-family: 'VT323', monospace; }

        @keyframes float {
            0% { transform: translateY(0px); }
            50% { transform: translateY(-10px); }
            100% { transform: translateY(0px); }
        }
        .animate-float { animation: float 3s ease-in-out infinite; }

        @keyframes pulse-heart {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }
        .heart-beat { animation: pulse-heart 1s infinite; }

        body {
            background-color: #fce7f3;
            background-image: 
                linear-gradient(90deg, rgba(200,200,200,.1) 1px, transparent 1px),
                linear-gradient(rgba(200,200,200,.1) 1px, transparent 1px);
            background-size: 20px 20px;
        }

        .rpg-box {
            background: white;
            border: 4px solid #374151;
            box-shadow: 8px 8px 0px rgba(0,0,0,0.2);
            position: relative;
        }

        .pokeball-bg {
            position: absolute;
            width: 300px;
            height: 300px;
            background: linear-gradient(to bottom, #EF4444 50%, white 50%);
            border-radius: 50%;
            border: 12px solid #1F2937;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            opacity: 0.1;
            z-index: 0;
        }
    </style>
</head>
<body class="h-screen w-full flex items-center justify-center overflow-hidden relative text-gray-800">

    <!-- Pantalla de Inicio -->
    <div id="start-screen" class="fixed inset-0 bg-gray-900 z-[100] flex flex-col items-center justify-center text-white cursor-pointer transition-opacity duration-500">
        <h1 class="font-pixel text-2xl md:text-4xl mb-8 animate-pulse text-center text-yellow-400">¡UN ENCUENTRO SALVAJE!</h1>
        <button id="real-start-btn" class="bg-yellow-500 hover:bg-yellow-400 text-gray-900 font-pixel py-6 px-10 rounded-xl border-b-8 border-yellow-700 active:border-b-0 active:mt-2 transition-all">
            ¡EMPEZAR AVENTURA!
        </button>
        <p class="font-pixel text-[10px] mt-6 text-gray-400 animate-bounce">ASEGÚRATE DE TENER SONIDO 🔊</p>
    </div>

    <div class="pokeball-bg"></div>

    <!-- Contenedor Principal -->
    <div id="main-card" class="relative z-10 w-full max-w-md p-4 opacity-0 transition-opacity duration-1000 pointer-events-none">
        <div class="bg-white border-4 border-gray-800 p-2 mb-4 rounded-lg shadow-lg flex justify-between items-center">
            <div class="flex flex-col">
                <span class="font-pixel text-[10px] mb-1 uppercase">Eevee de Mi Corazón</span>
                <div class="w-48 h-4 border-2 border-gray-600 rounded-full overflow-hidden bg-gray-200">
                    <div class="h-full bg-green-500 w-full transition-all duration-1000" id="hp-bar"></div>
                </div>
            </div>
            <div class="font-pixel text-[10px]">LVL 100</div>
        </div>

        <div class="flex justify-center mb-6 relative">
            <div class="absolute -top-4 -right-4 text-4xl animate-bounce">❤️</div>
            <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/133.png" 
                 alt="Eevee" 
                 class="w-48 h-48 object-contain animate-float drop-shadow-xl cursor-pointer"
                 id="pokemon-img">
        </div>

        <div class="rpg-box p-6 mb-6">
            <h1 class="font-pixel text-[10px] leading-relaxed mb-2 text-pink-600">¡Un Eevee salvaje apareció!</h1>
            <p class="font-retro text-2xl min-h-[3rem]" id="typing-text"></p>
            <p class="font-retro text-xl mt-2 text-gray-500">¿QUIERES SER MI SAN VALENTÍN?</p>
        </div>

        <div class="flex gap-4 justify-center items-center relative h-16">
            <button id="btn-yes" onclick="acceptValentine()" 
                    class="bg-blue-600 hover:bg-blue-500 text-white font-pixel text-[10px] py-4 px-6 rounded border-b-4 border-blue-800 active:border-b-0 active:mt-1 shadow-lg transition-all">
                ATRAPAR (Sí)
            </button>
            <button id="btn-no" 
                    class="bg-red-500 text-white font-pixel text-[10px] py-4 px-6 rounded border-b-4 border-red-800 shadow-lg transition-all absolute"
                    style="right: 20px;">
                HUIR (No)
            </button>
        </div>
    </div>

    <!-- Pantalla de Éxito -->
    <div id="success-screen" class="fixed inset-0 bg-pink-100 z-50 flex flex-col items-center justify-center opacity-0 pointer-events-none transition-opacity duration-1000">
        <div class="text-center p-8 max-w-lg">
            <div class="text-6xl mb-6 heart-beat">💖</div>
            <h2 class="font-pixel text-xl text-red-600 mb-4">¡CAPTURA EXITOSA!</h2>
            <p class="font-retro text-3xl text-gray-800 mb-8">¡Has atrapado mi corazón!</p>
            
            <div class="bg-white p-6 rounded-lg border-4 border-pink-400 shadow-xl transform rotate-2">
                <p class="font-pixel text-[10px] text-gray-400 mb-2">DETALLES DE LA CITA:</p>
                <p class="font-retro text-2xl border-b-2 border-dashed border-gray-300 pb-2 mb-2">📅 14 de Febrero</p>
                <p class="font-retro text-2xl">📍 [TU LUGAR AQUÍ]</p>
            </div>
        </div>
    </div>

    <!-- Audios -->
    <audio id="eevee-cry" src="https://play.pokemonshowdown.com/audio/cries/eevee.mp3"></audio>
    <!-- Melodía clásica de encuentro salvaje (Rojo/Azul/Amarillo) -->
    <audio id="bg-music" loop src="https://vgmsite.com/soundtracks/pokemon-game-boy-sound-collection/vunebvtr/107-wild%20pokemon%20battle.mp3"></audio>

    <script>
        const bgMusic = document.getElementById('bg-music');
        const cry = document.getElementById('eevee-cry');
        const startBtn = document.getElementById('real-start-btn');
        const startScreen = document.getElementById('start-screen');
        const mainCard = document.getElementById('main-card');
        const textElement = document.getElementById('typing-text');
        
        let audioCtx;

        startBtn.addEventListener('click', () => {
            // Inicializar Contexto de Audio para sonidos generados
            audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            if (audioCtx.state === 'suspended') audioCtx.resume();

            // Reproducir música
            bgMusic.volume = 0.2;
            const playPromise = bgMusic.play();
            
            if (playPromise !== undefined) {
                playPromise.then(() => {
                    console.log("Música de batalla iniciada");
                }).catch(error => {
                    console.error("Error al cargar música:", error);
                });
            }
            
            proceedToGame();
        });

        function proceedToGame() {
            startScreen.classList.add('opacity-0');
            setTimeout(() => {
                startScreen.style.display = 'none';
                mainCard.classList.remove('opacity-0', 'pointer-events-none');
                typeWriter();
            }, 500);
        }

        let i = 0;
        const textToType = "¡Te elijo a ti para ser mi Entrenadora Pokémon favorita!";
        function typeWriter() {
            if (i < textToType.length) {
                textElement.innerHTML += textToType.charAt(i);
                i++;
                setTimeout(typeWriter, 50);
            }
        }

        // Efecto de sonido 8-bits al mover el botón "No"
        function playBloop() {
            if (!audioCtx) return;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'square';
            osc.frequency.setValueAtTime(440, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(110, audioCtx.currentTime + 0.1);
            gain.gain.setValueAtTime(0.05, audioCtx.currentTime);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start();
            osc.stop(audioCtx.currentTime + 0.1);
        }

        // Lógica del botón NO (Huir)
        const btnNo = document.getElementById('btn-no');
        const avoid = () => {
            playBloop();
            const x = Math.random() * (window.innerWidth - btnNo.offsetWidth - 40);
            const y = Math.random() * (window.innerHeight - btnNo.offsetHeight - 40);
            btnNo.style.position = 'fixed';
            btnNo.style.left = Math.max(20, x) + 'px';
            btnNo.style.top = Math.max(20, y) + 'px';
        };
        btnNo.addEventListener('mouseover', avoid);
        btnNo.addEventListener('touchstart', (e) => { e.preventDefault(); avoid(); });

        function acceptValentine() {
            bgMusic.pause(); // Detener música de batalla
            cry.play().catch(() => {}); // Sonido de Eevee
            
            document.getElementById('hp-bar').classList.replace('bg-green-500', 'bg-pink-500');
            document.getElementById('success-screen').classList.remove('opacity-0', 'pointer-events-none');
            
            // Melodía de victoria generada (Garantizada)
            const notes = [523, 659, 783, 1046]; 
            notes.forEach((f, index) => {
                const o = audioCtx.createOscillator();
                const g = audioCtx.createGain();
                o.type = 'square';
                o.frequency.value = f;
                g.gain.setValueAtTime(0.05, audioCtx.currentTime + index * 0.1);
                o.connect(g);
                g.connect(audioCtx.destination);
                o.start(audioCtx.currentTime + index * 0.1);
                o.stop(audioCtx.currentTime + index * 0.1 + 0.2);
            });
        }
        
        document.getElementById('pokemon-img').onclick = () => cry.play().catch(() => {});
    </script>
</body>
</html>
