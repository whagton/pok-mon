<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Pokémon Battle Simulator</title>
    <style>
        body, html { margin: 0; padding: 0; height: 100%; background: #0d0d1a; overflow: hidden; color: white; font-family: sans-serif; }

        /* Pokébola Centralizada */
        #start-screen { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); display: flex; flex-direction: column; align-items: center; z-index: 10000; }
        .pokeball-container { position: relative; width: 150px; height: 150px; cursor: pointer; }
        .p-top, .p-bottom { width: 150px; height: 75px; background: red; border: 6px solid #000; position: absolute; transition: 0.8s; }
        .p-top { top: 0; border-radius: 75px 75px 0 0; border-bottom: 3px solid #000; z-index: 2; }
        .p-bottom { bottom: 0; background: white; border-radius: 0 0 75px 75px; border-top: 3px solid #000; z-index: 1; }
        .open-top { transform: translateY(-80px); }
        .open-bottom { transform: translateY(80px); }
        #btn-iniciar { display: none; margin-top: 20px; padding: 10px 20px; cursor: pointer; background: #ffcc00; border: none; font-weight: bold; }

        /* Arena */
        #game-ui { display: none; width: 100%; height: 100%; position: relative; }
        .mewtwo-container { position: absolute; top: 10%; right: 10%; }
        .pikachu-container { position: absolute; bottom: 20%; left: 10%; }
        .pokemon { width: 120px; height: 120px; }

        /* Ataques acima do Pikachu */
        .controls { position: absolute; bottom: 25%; left: 10%; width: 250px; display: grid; grid-template-columns: 1fr 1fr; gap: 5px; }
        .move-btn { padding: 8px; cursor: pointer; background: #333; color: white; border: 1px solid #555; font-size: 0.8rem; }
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

    <div id="game-ui">
        <div class="mewtwo-container">
            <img src="https://img.pokemondb.net/sprites/black-white/anim/normal/mewtwo.gif" class="pokemon">
        </div>
        
        <div class="pikachu-container">
            <div class="controls">
                <button class="move-btn" onclick="tocarSom('thunder')">TROVÃO</button>
                <button class="move-btn" onclick="tocarSom('shock')">CHOQUE</button>
            </div>
            <img id="pikachu" src="https://img.pokemondb.net/sprites/black-white/anim/back-normal/pikachu.gif" class="pokemon">
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
            document.getElementById('game-ui').style.display = 'block';
        }

        function tocarSom(tipo) {
            const s = document.getElementById(tipo === 'thunder' ? 'sfx-thunder' : 'sfx-shock');
            s.currentTime = 0;
            s.volume = 1.0;
            s.play();
        }
    </script>
</body>
</html>
