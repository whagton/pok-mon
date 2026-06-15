<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Pokémon Battle Simulator - Elite</title>
    <style>
        body { background: #0d0d1a; color: #fff; font-family: sans-serif; display: flex; flex-direction: column; align-items: center; min-height: 100vh; margin: 0; overflow: hidden; }
        
        /* Flash e Trepidação */
        .flash-overlay { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: yellow; opacity: 0; z-index: 9999; pointer-events: none; }
        
        @keyframes piscar { 0%, 100% { opacity: 0; } 50% { opacity: 0.7; } }
        .animar-flash { animation: piscar 0.2s; }

        @keyframes shake {
            0% { transform: translate(1px, 1px) rotate(0deg); }
            25% { transform: translate(-3px, 0px) rotate(1deg); }
            50% { transform: translate(3px, 2px) rotate(0deg); }
            75% { transform: translate(-1px, -1px) rotate(-1deg); }
            100% { transform: translate(1px, -2px) rotate(0deg); }
        }
        .tremer-tela { animation: shake 0.2s infinite; }

        /* Pokébola Inicial */
        #start-screen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #0d0d1a; display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 10000; }
        .pokeball { width: 150px; height: 150px; background: white; border: 8px solid #000; border-radius: 50%; cursor: pointer; position: relative; }
        .pokeball::before { content: ""; position: absolute; top: 50%; left: 0; width: 100%; height: 10px; background: #000; }
        .pokeball::after { content: ""; position: absolute; top: 50%; left: 50%; width: 40px; height: 40px; background: white; border: 8px solid #000; border-radius: 50%; transform: translate(-50%, -50%); }

        /* Jogo */
        #game-ui { display: none; text-align: center; width: 100%; max-width: 500px; padding: 20px; }
        .controls { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 20px; }
        .move-btn { padding: 15px; cursor: pointer; background: #252547; color: white; border: 2px solid #3b3b73; border-radius: 8px; font-weight: bold; }
        
        .arena { display: flex; justify-content: center; align-items: center; gap: 40px; margin-top: 50px; position: relative; }
        .pokemon { width: 150px; height: 150px; transition: transform 0.3s ease; }
        
        .atk-anim { transform: translateX(50px) scale(1.2); }
        .super-atk-anim { transform: translateX(200px) scale(2.5) rotate(10deg); z-index: 10; }
    </style>
</head>
<body>

    <div id="start-screen">
        <div class="pokeball" onclick="iniciarJogo()"></div>
        <p>Clique para iniciar</p>
    </div>

    <div class="flash-overlay" id="flash"></div>

    <div id="game-ui">
        <div class="hp-info">Pikachu: <span id="p-hp">3000</span> | Mewtwo: <span id="e-hp">3000</span></div>
        <div class="controls">
            <button class="move-btn" style="border-color: #ff0000;" onclick="atacar(600, true)">TROVÃO (SUPER)</button>
            <button class="move-btn" onclick="atacar(150, false)">Choque</button>
            <button class="move-btn" onclick="atacar(250, false)">Raio</button>
            <button class="move-btn" onclick="atacar(350, false)">Investida</button>
        </div>
        <div class="arena" id="arena">
            <img id="pikachu" src="https://img.pokemondb.net/sprites/black-white/anim/back-normal/pikachu.gif" class="pokemon">
            <img id="mewtwo" src="https://img.pokemondb.net/sprites/black-white/anim/normal/mewtwo.gif" class="pokemon">
        </div>
    </div>

    <audio id="sfx-thunder" src="https://www.myinstants.com/media/sounds/thunder-sound-effect.mp3"></audio>
    <audio id="sfx-shock" src="https://www.myinstants.com/media/sounds/electric-shock.mp3"></audio>

    <script>
        let pHP = 3000, eHP = 3000;

        function iniciarJogo() {
            document.getElementById('start-screen').style.display = 'none';
            document.getElementById('game-ui').style.display = 'block';
        }

        function executarSuperEfeitos() {
            const flash = document.getElementById('flash');
            const arena = document.getElementById('arena');
            
            // 4 piscadas e 4 tremores
            let count = 0;
            const intervalo = setInterval(() => {
                flash.classList.add('animar-flash');
                arena.classList.add('tremer-tela');
                
                setTimeout(() => {
                    flash.classList.remove('animar-flash');
                    arena.classList.remove('tremer-tela');
                }, 150);

                count++;
                if (count >= 4) clearInterval(intervalo);
            }, 250);
        }

        function atacar(dano, isSuper) {
            const pikachu = document.getElementById('pikachu');
            
            if (isSuper) {
                pikachu.classList.add('super-atk-anim');
                executarSuperEfeitos();
                const s = document.getElementById('sfx-thunder');
                s.volume = 1.0; s.play();
                setTimeout(() => pikachu.classList.remove('super-atk-anim'), 500);
            } else {
                pikachu.classList.add('atk-anim');
                // Flash simples para ataque normal
                const flash = document.getElementById('flash');
                flash.classList.add('animar-flash');
                setTimeout(() => flash.classList.remove('animar-flash'), 150);
                
                const s = document.getElementById('sfx-shock');
                s.volume = 1.0; s.play();
                setTimeout(() => pikachu.classList.remove('atk-anim'), 300);
            }

            eHP = Math.max(0, eHP - dano);
            document.getElementById('e-hp').innerText = eHP;
            if (eHP <= 0) alert("Você venceu o Mewtwo!");
        }
    </script>
</body>
</html>
