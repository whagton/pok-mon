<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Pokémon Battle - Start</title>
    <style>
        body, html { margin: 0; padding: 0; height: 100%; background: #0d0d1a; display: flex; justify-content: center; align-items: center; overflow: hidden; color: white; }

        /* Pokébola Animada */
        #start-screen { position: absolute; display: flex; flex-direction: column; align-items: center; gap: 20px; z-index: 10000; }
        .pokeball-container { position: relative; width: 200px; height: 200px; cursor: pointer; transition: 0.5s; }
        .p-top, .p-bottom { width: 200px; height: 100px; background: red; border: 8px solid #000; position: absolute; transition: 0.8s; }
        .p-top { top: 0; border-radius: 100px 100px 0 0; border-bottom: 5px solid #000; }
        .p-bottom { bottom: 0; background: white; border-radius: 0 0 100px 100px; border-top: 5px solid #000; }
        
        /* Abertura */
        .open-top { transform: translateY(-100px); }
        .open-bottom { transform: translateY(100px); }

        #btn-iniciar { display: none; padding: 20px 40px; font-size: 1.5rem; cursor: pointer; background: #ffcc00; border: none; border-radius: 10px; font-weight: bold; }

        /* Jogo */
        #game-ui { display: none; width: 100%; height: 100%; flex-direction: column; justify-content: space-between; }
        .enemy-side { flex: 1; display: flex; justify-content: flex-end; align-items: flex-start; padding: 50px; }
        .player-side { flex: 1; display: flex; justify-content: flex-start; align-items: flex-end; padding: 50px; }
        .pokemon { width: 200px; height: 200px; transition: 0.2s; }
        .controls { background: #fff; padding: 20px; display: grid; grid-template-columns: 1fr 1fr; gap: 10px; color: black; }
        
        .flash-overlay { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: yellow; opacity: 0; pointer-events: none; }
        @keyframes piscar { 0%, 100% { opacity: 0; } 50% { opacity: 0.7; } }
        .animar-flash { animation: piscar 0.2s; }
    </style>
</head>
<body>

    <div id="start-screen">
        <div class="pokeball-container" onclick="abrirPokebola()">
            <div class="p-top" id="top"></div>
            <div class="p-bottom" id="bottom"></div>
        </div>
        <button id="btn-iniciar" onclick="iniciarJogo()">INICIAR BATALHA</button>
    </div>

    <div class="flash-overlay" id="flash"></div>

    <div id="game-ui">
        <div class="enemy-side"><img src="https://img.pokemondb.net/sprites/black-white/anim/normal/mewtwo.gif" class="pokemon"></div>
        <div class="player-side"><img id="pikachu" src="https://img.pokemondb.net/sprites/black-white/anim/back-normal/pikachu.gif" class="pokemon"></div>
        <div class="controls">
            <div style="grid-column: span 2; text-align: center;">HP Mewtwo: <span id="e-hp">3000</span></div>
            <button onclick="atacar(600, true)">TROVÃO (SUPER)</button>
            <button onclick="atacar(150, false)">Choque</button>
        </div>
    </div>

    <script>
        function abrirPokebola() {
            document.getElementById('top').classList.add('open-top');
            document.getElementById('bottom').classList.add('open-bottom');
            setTimeout(() => { document.getElementById('btn-iniciar').style.display = 'block'; }, 500);
        }

        function iniciarJogo() {
            document.getElementById('start-screen').style.display = 'none';
            document.getElementById('game-ui').style.display = 'flex';
        }

        function atacar(dano, isSuper) {
            const flash = document.getElementById('flash');
            flash.classList.add('animar-flash');
            setTimeout(() => flash.classList.remove('animar-flash'), 200);
            
            // Lógica de dano simplificada
            let hp = parseInt(document.getElementById('e-hp').innerText);
            document.getElementById('e-hp').innerText = Math.max(0, hp - dano);
        }
    </script>
</body>
</html>
