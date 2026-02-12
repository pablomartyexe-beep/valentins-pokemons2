<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>¡Un encuentro mágico!</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=VT323&display=swap" rel="stylesheet">
    <style>
        .font-pixel { font-family: 'Press Start 2P', cursive; }
        .font-retro { font-family: 'VT323', monospace; }

        body, html { height: 100%; margin: 0; overflow: hidden; }

        /* Fondo de Bosque Romántico */
        .forest-bg {
            position: fixed;
            inset: 0;
            background-image: linear-gradient(to bottom, rgba(255, 192, 203, 0.4), rgba(0, 0, 0, 0.5)), 
                              url('https://images.alphacoders.com/132/1326442.png');
            background-size: cover;
            background-position: center;
            z-index: -1;
        }

        @keyframes float {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-20px); }
        }
        .animate-float { animation: float 3s ease-in-out infinite; }

        .heart-particle {
            position: absolute;
            pointer-events: none;
            animation: heart-fly 1s ease-out forwards;
            z-index: 50;
        }
        @keyframes heart-fly {
            0% { transform: translateY(0) scale(1); opacity: 1; }
            100% { transform: translateY(-100px) scale(1.5); opacity: 0; }
        }

        .rpg-box {
            background: rgba(255, 255, 255, 0.95);
            border: 4px solid #374151;
            box-shadow: 8px 8px 0px rgba(0,0,0,0.3);
        }

        .evolution-glow { animation: evolve-glow 2s ease-in-out; }
        @keyframes evolve-glow {
            0% { filter: brightness(1); transform: scale(1); }
            50% { filter: brightness(15) contrast(2); transform: scale(1.4); }
            100% { filter: brightness(1); transform: scale(1); }
        }
    </style>
</head>
<body class="flex items-center justify-center text-gray-800">

    <div class="forest-bg"></div>

    <audio id="bg-music" loop src="https://vgmsite.com/soundtracks/pokemon-game-boy-sound-collection/vunebvtr/107-wild%20pokemon%20battle.mp3"></audio>
    <audio id="eevee-cry" src="https://play.pokemonshowdown.com/audio/cries/eevee.mp3"></audio>
    <audio id="vaporeon-cry" src="https://play.pokemonshowdown.com/audio/cries/vaporeon.mp3"></audio>

    <div id="start-screen" class="fixed inset-0 bg-gray-900 z-[100] flex flex-col items-center justify-center text-white p-6">
        <h1 class="font-pixel text-2xl text-yellow-400 mb-6 text-center">¡UN ENCUENTRO SALVAJE!</h1>
        <button id="real-start-btn" class="bg-yellow-500 hover:bg-yellow-400 text-gray-900 font-pixel py-6 px-10 rounded-xl border-b-8 border-yellow-700 active:border-b-0 active:mt-2 transition-all">
            ¡INICIAR GAME BOY!
        </button>
    </div>

    <div id="main-card" class="relative z-10 w-full max-w-md p-4 opacity-0 transition-opacity duration-1000 pointer-events-none">
        <div class="bg-white border-4 border-gray-800 p-2 mb-6 rounded-lg shadow-lg flex justify-between items-center">
            <div class="flex flex-col flex-1">
                <span id="pkm-name" class="font-pixel text-[10px] mb-1 uppercase">Eevee de Mi Corazón</span>
                <div class="w-full h-4 border-2 border-gray-600 rounded-full bg-gray-200">
                    <div id="hp-bar" class="h-full bg-green-500 w-full transition-all duration-1000"></div>
                </div>
            </div>
            <div class="font-pixel text-[10px] ml-4">LVL 100</div>
        </div>

        <div class="flex justify-center mb-6 relative" id="pkm-container">
            <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/133.png" 
                 id="pokemon-img" class="w-52 h-52 object-contain animate-float cursor-pointer">
        </div>

        <div class="rpg-box p-6 mb-6">
            <p class="font-retro text-2xl min-h-[3rem]" id="typing-text"></p>
            <p class="font-retro text-xl mt-2 text-pink-600 font-bold">¿QUIERES SER MI SAN VALENTÍN?</p>
        </div>

        <div class="flex gap-4 justify-center items-center relative h-16">
            <button id="btn-yes" onclick="evolveSequence()" class="bg-blue-600 hover:bg-blue-500 text-white font-pixel text-[10px] py-4 px-8 rounded border-b-4 border-blue-800 shadow-lg transition-all">
                ATRAPAR (Sí)
            </button>
            <button id="btn-no" class="bg-red-500 text-white font-pixel text-[10px] py-4 px-8 rounded border-b-4 border-red-800 shadow-lg absolute" style="right: 20px;">
                HUIR (No)
            </button>
        </div>
    </div>

    <div id="success-screen" class="fixed inset-0 bg-blue-50 z-50 flex flex-col items-center justify-center opacity-0 pointer-events-none transition-opacity duration-1000">
        <div class="text-center p-8 max-w-lg">
            <div id="final-pkm-container" class="mb-6 flex justify-center">
                <img id="vaporeon-final" src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/134.png" 
                     class="w-56 h-56 object-contain animate-float cursor-pointer">
            </div>
            <h2 class="font-pixel text-xl text-blue-600 mb-4">¡CAPTURA EXITOSA!</h2>
            <div class="bg-white p-6 rounded-lg border-4 border-blue-400 shadow-xl">
                <p class="font-retro text-3xl text-gray-800 mb-4">¡Has hecho evolucionar mi corazón!</p>
                <p class="font-pixel text-[10px] text-blue-400">📅 14 de Febrero</p>
            </div>
            <p class="font-retro text-xl mt-4 text-gray-500 animate-pulse">¡Haz clic en Vaporeon!</p>
        </div>
    </div>

    <script>
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        
        const elements = {
            music: document.getElementById('bg-music'),
            eeveeCry: document.getElementById('eevee-cry'),
            vaporeonCry: document.getElementById('vaporeon-cry'),
            img: document.getElementById('pokemon-img'),
            finalVaporeon: document.getElementById('vaporeon-final'),
            startBtn: document.getElementById('real-start-btn'),
            startScreen: document.getElementById('start-screen'),
            mainCard: document.getElementById('main-card'),
            text: document.getElementById('typing-text'),
            btnNo: document.getElementById('btn-no'),
            btnYes: document.getElementById('btn-yes'),
            success: document.getElementById('success-screen'),
            name: document.getElementById('pkm-name')
        };

        // Sonido de "Bip" para el texto y botones
        function playBip(freq = 440) {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'square';
            osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
            gain.gain.setValueAtTime(0.05, audioCtx.currentTime);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start();
            osc.stop(audioCtx.currentTime + 0.08);
        }

        // Iniciar aventura
        elements.startBtn.addEventListener('click', () => {
            audioCtx.resume();
            elements.music.volume = 0.2;
            elements.music.play();
            elements.startScreen.style.display = 'none';
            elements.mainCard.classList.remove('opacity-0', 'pointer-events-none');
            typeWriter("¡Te elijo a ti para ser mi Entrenadora Pokémon favorita!", 0);
        });

        function typeWriter(txt, i) {
            if (i < txt.length) {
                elements.text.innerHTML += txt.charAt(i);
                playBip(600); // Sonido mientras sale el texto
                setTimeout(() => typeWriter(txt, i + 1), 50);
            }
        }

        // Función para lanzar partículas (corazones o gotas)
        function spawnParticles(x, y, symbol) {
            for(let i = 0; i < 6; i++) {
                const p = document.createElement('div');
                p.innerHTML = symbol;
                p.className = 'heart-particle text-2xl';
                p.style.left = (x - 20 + (Math.random() * 40)) + 'px';
                p.style.top = (y - 20) + 'px';
                document.body.appendChild(p);
                setTimeout(() => p.remove(), 1000);
            }
        }

        // Interacción Eevee
        elements.img.addEventListener('click', (e) => {
            elements.eeveeCry.currentTime = 0;
            elements.eeveeCry.play();
            spawnParticles(e.clientX, e.clientY, '❤️');
        });

        // Interacción Vaporeon (Pantalla Final)
        elements.finalVaporeon.addEventListener('click', (e) => {
            elements.vaporeonCry.currentTime = 0;
            elements.vaporeonCry.play();
            spawnParticles(e.clientX, e.clientY, '💧');
        });

        // Botón No
        elements.btnNo.addEventListener('mouseover', () => {
            playBip(200); // Sonido de error al huir
            const x = Math.random() * (window.innerWidth - 120);
            const y = Math.random() * (window.innerHeight - 60);
            elements.btnNo.style.position = 'fixed';
            elements.btnNo.style.left = x + 'px';
            elements.btnNo.style.top = y + 'px';
        });

        // Evolución
        function evolveSequence() {
            playBip(800); // Sonido de selección
            elements.music.pause();
            elements.img.classList.add('evolution-glow');
            
            setTimeout(() => {
                elements.img.src = "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/134.png";
                elements.name.innerText = "Vaporeon de Mi Corazón";
                elements.vaporeonCry.play();
                document.getElementById('hp-bar').style.backgroundColor = "#3b82f6";
            }, 1000);

            setTimeout(() => {
                elements.mainCard.style.opacity = '0';
                setTimeout(() => {
                    elements.success.classList.remove('opacity-0', 'pointer-events-none');
                    elements.success.style.opacity = '1';
                }, 500);
            }, 3000);
        }
    </script>
</body>
</html>
