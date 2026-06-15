<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Pokémon Battle - Start</title>
    <style>
        body, html { margin: 0; padding: 0; height: 100%; background: #0d0d1a; overflow: hidden; }

        /* Centralização total */
        #start-screen { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); display: flex; flex-direction: column; align-items: center; z-index: 10000; }
        
        .pokeball-container { position: relative; width: 200px; height: 200px; cursor: pointer; }
        .p-top, .p-bottom { width: 200px; height: 100px; background: red; border: 8px solid #000; position: absolute; transition: 0.8s; }
        .p-top { top: 0; border-radius: 100px 100px 0 0; border-bottom: 5px solid #000; z-index: 2; }
        .p-bottom { bottom: 0; background: white; border-radius: 0 0 100px 100px; border-top: 5px solid #000; z-index: 1; }
        
        .open-top { transform: translateY(-120px); }
        .open-bottom { transform: translateY(120px); }

        /* Botão no meio da bola */
        #btn-iniciar { display: none; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); padding: 15px; font-size: 1rem; cursor: pointer; background: #000; color: #fff; border: 2px solid #ffcc00; border-radius: 5px; z-index: 3; white-space: nowrap; }

        /* Jogo */
        #game-ui { display: none; width: 100%; height: 100%; flex-direction: column; justify-content: space-between; color: white; }
        .pokemon { width: 200px; }
        .controls { background: #fff; padding: 20px; color: black; display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
    </style>
</head>
<body>

    <div id="start-screen">
        <div class="pokeball-container" onclick="abrirPokebola()">
            <div class="p-top" id="top"></div>
            <div class="p-bottom" id="bottom"></div>
            <button id="btn-iniciar" onclick="iniciarJogo()">INICIAR BATALHA</button>
        </div>
    </div>

    <div id="game-ui">
        <div style="flex:1; display:flex; justify-content:flex-end; align-items:flex-start; padding: 50px;">
            <img src="https://img.pokemondb.net/sprites/black-white/anim/normal/mewtwo.gif" class="pokemon">
        </div>
        <div style="flex:1; display:flex; justify-content:flex-start; align-items:flex-end; padding: 50px;">
            <img src="https://img.pokemondb.net/sprites/black-white/anim/back-normal/pikachu.gif" class="pokemon">
        </div>
        <div class="controls">
            <button onclick="tocarSom('thunder')">USAR TROVÃO</button>
            <button onclick="tocarSom('shock')">USAR CHOQUE</button>
        </div>
    </div>

    <audio id="sfx-open" src="https://www.myinstants.com/media/sounds/pokemon-bag.mp3"></audio>
    <audio id="sfx-thunder" src="https://www.myinstants.com/media/sounds/thunder-sound-effect.mp3"></audio>
    <audio id="sfx-shock" src="https://www.myinstants.com/media/sounds/electric-shock.mp3"></audio>

    <script>
        function abrirPokebola() {
            document.getElementById('top').classList.add('open-top');
            document.getElementById('bottom').classList.add('open-bottom');
            document.getElementById('sfx-open').play();
            setTimeout(() => { document.getElementById('btn-iniciar').style.display = 'block'; }, 600);
        }

        function iniciarJogo() {
            document.getElementById('start-screen').style.display = 'none';
            document.getElementById('game-ui').style.display = 'flex';
        }

        function tocarSom(tipo) {
            const s = document.getElementById(tipo === 'thunder' ? 'sfx-thunder' : 'sfx-shock');
            s.currentTime = 0;
            s.play();
        }
    </script>
</body>
</html>
