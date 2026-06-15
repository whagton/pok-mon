<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Pokémon Battle Simulator - Classic</title>
    <style>
        body, html { margin: 0; padding: 0; height: 100%; background: #0d0d1a; font-family: sans-serif; overflow: hidden; }
        
        /* Centralização e Início */
        #start-screen { position: absolute; width: 100%; height: 100%; display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 10000; }
        .pokeball-img { width: 200px; cursor: pointer; transition: transform 0.6s ease; }
        .pokeball-open { transform: scale(0.8) rotate(180deg); opacity: 0; pointer-events: none; }
        #btn-iniciar { display: none; margin-top: 20px; padding: 20px 40px; font-size: 1.5rem; cursor: pointer; border: 4px solid #ffcc00; background: #0d0d1a; color: #ffcc00; font-weight: bold; border-radius: 15px; }

        /* Arena estilo Jogo Clássico */
        #game-ui { display: none; width: 100%; height: 100%; display: flex; flex-direction: column; justify-content: space-between; }
        .enemy-side { flex: 1; display: flex; justify-content: flex-end; align-items: flex-start; padding: 50px; }
        .player-side { flex: 1; display: flex; justify-content: flex-start; align-items: flex-end; padding: 50px; }
        
        .controls { background: #fff; padding: 20px; border-top: 5px solid #000; display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
        .move-btn { padding: 15px; cursor: pointer; background: #f0f0f0; border: 2px solid #333; border-radius: 5px; font-weight: bold; }
        
        .pokemon { width: 200px; height: 200px; transition: all 0.2s; }
        
        /* Efeitos */
        .flash-overlay { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: yellow; opacity: 0; z-index: 9999; pointer-events: none; }
        @keyframes piscar { 0%, 100% { opacity: 0; } 50% { opacity: 0.7; } }
        .animar-flash { animation: piscar 0.2s; }
        @keyframes shake-extreme { 0% { transform: translate(10px, 10px); } 50% { transform: translate(-10px, -5px); } 100% { transform: translate(10px, 5px); } }
        .tremer-tela-intenso { animation: shake-extreme 0.1s infinite; }
        .atk-anim { transform: translateX(50px) scale(1.1); }
        .super-atk-anim { transform: translateX(150px) scale(1.5) rotate(5deg); }
    </style>
</head>
<body>

    <div id="start-screen">
        <img src="https://upload.wikimedia.org/wikipedia/commons/5/53/Pok%C3%A9_Ball_icon.svg" class="pokeball-img" id="pokeball" onclick="abrirPokebola()">
        <button id="btn-iniciar" onclick="iniciarJogo()">INICIAR BATALHA</button>
    </div>

    <div class="flash-overlay" id="flash"></div>

    <div id="game-ui">
        <div class="enemy-side">
            <img id="mewtwo" src="https://img.pokemondb.net/sprites/black-white/anim/normal/mewtwo.gif" class="pokemon">
        </div>
        
        <div class="player-side">
            <img id="pikachu" src="https://img.pokemondb.net/sprites/black-white/anim/back-normal/pikachu.gif" class="pokemon">
        </div>

        <div class="controls">
            <div style="grid-column: span 2; text-align: center; font-weight: bold;">HP Mewtwo: <span id="e-hp">3000</span></div>
            <button class="move-btn" onclick="atacar(600, true)">TROVÃO (SUPER)</button>
            <button class="move-btn" onclick="atacar(150, false)">Choque</button>
            <button class="move-btn" onclick="atacar(250, false)">Raio</button>
            <button class="move-btn" onclick="atacar(350, false)">Investida</button>
        </div>
    </div>

    <audio id="sfx-thunder" src="https://www.myinstants.com/media/sounds/thunder-sound-effect.mp3"></audio>
    <audio id="sfx-shock" src="https://www.myinstants.com/media/sounds/electric-shock.mp3"></audio>

    <script>
        function abrirPokebola() {
            document.getElementById('pokeball').classList.add('pokeball-open');
            setTimeout(() => { document.getElementById('btn-iniciar').style.display = 'block'; }, 500);
        }

        function iniciarJogo() {
            document.getElementById('start-screen').style.display = 'none';
            document.getElementById('game-ui').style.display = 'flex';
        }

        let eHP = 3000;
        function atacar(dano, isSuper) {
            const pikachu = document.getElementById('pikachu');
            const flash = document.getElementById('flash');
            
            if (isSuper) {
                document.getElementById('sfx-thunder').play();
                pikachu.classList.add('super-atk-anim');
                let count = 0;
                const int = setInterval(() => {
                    flash.classList.add('animar-flash');
                    document.body.classList.add('tremer-tela-intenso');
                    setTimeout(() => { flash.classList.remove('animar-flash'); document.body.classList.remove('tremer-tela-intenso'); }, 150);
                    count++;
                    if (count >= 4) clearInterval(int);
                }, 250);
                setTimeout(() => pikachu.classList.remove('super-atk-anim'), 600);
            } else {
                document.getElementById('sfx-shock').play();
                pikachu.classList.add('atk-anim');
                flash.classList.add('animar-flash');
                setTimeout(() => flash.classList.remove('animar-flash'), 150);
                setTimeout(() => pikachu.classList.remove('atk-anim'), 300);
            }

            eHP = Math.max(0, eHP - dano);
            document.getElementById('e-hp').innerText = eHP;
            if (eHP <= 0) alert("Você venceu o Mewtwo!");
        }
    </script>
</body>
</html>
