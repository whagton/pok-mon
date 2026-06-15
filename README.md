<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Pokémon Battle Simulator</title>
    <style>
        body { background: #0d0d1a; color: #fff; font-family: sans-serif; display: flex; flex-direction: column; align-items: center; min-height: 100vh; margin: 0; }
        
        /* Flash de tela */
        .flash-overlay { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: yellow; opacity: 0; z-index: 9999; pointer-events: none; }
        @keyframes piscar { 0%, 100% { opacity: 0; } 50% { opacity: 0.7; } }
        .animar-flash { animation: piscar 0.2s; }

        #start-screen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #0d0d1a; display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 10000; }
        .pokeball { width: 150px; height: 150px; background: white; border: 8px solid #000; border-radius: 50%; cursor: pointer; position: relative; }
        .pokeball::before, .pokeball::after { content: ""; position: absolute; background: #000; }
        .pokeball::before { top: 50%; left: 0; width: 100%; height: 10px; }
        .pokeball::after { top: 50%; left: 50%; width: 40px; height: 40px; background: white; border: 8px solid #000; border-radius: 50%; transform: translate(-50%, -50%); }

        #game-ui { display: none; text-align: center; width: 100%; max-width: 500px; padding: 20px; }
        .controls { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 20px; }
        .move-btn { padding: 15px; cursor: pointer; background: #252547; color: white; border: 2px solid #3b3b73; border-radius: 8px; font-weight: bold; }
        .arena { display: flex; justify-content: center; align-items: center; gap: 40px; margin-top: 20px; }
        .pokemon { width: 150px; height: 150px; }
    </style>
</head>
<body>

    <div id="start-screen">
        <div class="pokeball" onclick="iniciarJogo()"></div>
        <p>Clique/Toque para iniciar</p>
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
        <div class="arena">
            <img src="https://img.pokemondb.net/sprites/black-white/anim/back-normal/pikachu.gif" class="pokemon">
            <img src="https://img.pokemondb.net/sprites/black-white/anim/normal/mewtwo.gif" class="pokemon">
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

        function piscarTela(vezes) {
            const flash = document.getElementById('flash');
            let count = 0;
            const intervalo = setInterval(() => {
                flash.classList.add('animar-flash');
                setTimeout(() => flash.classList.remove('animar-flash'), 150);
                count++;
                if (count >= vezes) clearInterval(intervalo);
            }, 250);
        }

        function atacar(dano, isSuper) {
            if (isSuper) {
                piscarTela(6); // 6 vezes no super
                const s = document.getElementById('sfx-thunder');
                s.volume = 1.0; s.play();
            } else {
                piscarTela(2); // 2 vezes no normal
                const s = document.getElementById('sfx-shock');
                s.volume = 1.0; s.play();
            }

            eHP = Math.max(0, eHP - dano);
            document.getElementById('e-hp').innerText = eHP;

            if (eHP > 0) {
                setTimeout(() => {
                    pHP = Math.max(0, pHP - 200);
                    document.getElementById('p-hp').innerText = pHP;
                }, 800);
            } else {
                alert("Você venceu o Mewtwo!");
            }
        }
    </script>
</body>
</html>
