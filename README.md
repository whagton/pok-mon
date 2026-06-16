<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Pokémon Battle Simulator</title>
    <style>
        body, html { margin: 0; padding: 0; height: 100%; background: #0d0d1a; color: white; font-family: sans-serif; overflow: hidden; }

        /* Tela Inicial com Pokébola e Botão Centralizados */
        #start-screen { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); display: flex; flex-direction: column; align-items: center; justify-content: center; z-index: 10000; width: 300px; height: 300px; }
        .pokeball-container { position: relative; width: 150px; height: 150px; cursor: pointer; display: flex; align-items: center; justify-content: center; }
        .p-top, .p-bottom { width: 150px; height: 75px; background: red; border: 6px solid #000; position: absolute; transition: 0.8s; box-sizing: border-box; }
        .p-top { top: 0; border-radius: 75px 75px 0 0; z-index: 2; }
        .p-bottom { bottom: 0; background: white; border-radius: 0 0 75px 75px; z-index: 1; border-top: none; }
        
        /* Animação da Pokébola abrindo */
        .open-top { transform: translateY(-60px); }
        .open-bottom { transform: translateY(60px); }
        
        /* Botão Iniciar aparece exatamente no meio */
        #btn-iniciar { display: none; position: absolute; z-index: 3; padding: 12px 24px; cursor: pointer; background: #ffcc00; border: 3px solid #000; font-weight: bold; border-radius: 10px; color: black; font-size: 14px; white-space: nowrap; animation: popIn 0.3s ease-out; }
        @keyframes popIn { from { transform: scale(0); opacity: 0; } to { transform: scale(1); opacity: 1; } }

        /* Arena Centralizada */
        #game-ui { display: none; width: 100%; height: 100%; position: relative; }
        .battle-arena { display: flex; flex-direction: column; justify-content: space-between; align-items: center; height: 100%; max-width: 600px; margin: 0 auto; padding: 40px 20px; box-sizing: border-box; }
        
        /* Containers dos Pokémons */
        .mewtwo-container { display: flex; flex-direction: column; align-items: center; align-self: flex-end; text-align: center; }
        .pikachu-container { display: flex; flex-direction: column; align-items: center; align-self: flex-start; text-align: center; }
        .pokemon { width: 150px; height: 150px; object-fit: contain; }
        .hp-bar { font-weight: bold; margin-bottom: 5px; background: rgba(0,0,0,0.5); padding: 5px 15px; border-radius: 20px; border: 1px solid #444; }
        
        /* Painel de Ataques no rodapé */
        .controls-container { width: 100%; display: flex; justify-content: center; padding-top: 20px; }
        .controls { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; width: 100%; max-width: 400px; }
        .move-btn { padding: 15px; cursor: pointer; background: #222; color: white; border: 2px solid #ffcc00; font-weight: bold; border-radius: 8px; transition: 0.2s; }
        .move-btn:hover { background: #ffcc00; color: black; }

        /* Flash Amarelo e Preto */
        .flash-overlay { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; opacity: 0; pointer-events: none; z-index: 9999; }
        @keyframes flash-fast { 0%, 100% { opacity: 0; } 25% { background: yellow; opacity: 0.6; } 50% { background: black; opacity: 0.6; } 75% { background: yellow; opacity: 0.6; } }
        .animar-flash { animation: flash-fast 0.1s 4; }
        
        /* Trepidação da Arena */
        @keyframes shake { 0%, 100% { transform: translate(0,0); } 25% { transform: translate(-10px, -10px); } 50% { transform: translate(10px, 10px); } 75% { transform: translate(-10px, 10px); } }
        .tremer-tela { animation: shake 0.1s 4; }
    </style>
</head>
<body>

    <!-- Tela Inicial -->
    <div id="start-screen">
        <div class="pokeball-container" onclick="abrirPokebola()">
            <div class="p-top" id="top"></div>
            <div class="p-bottom" id="bottom"></div>
        </div>
        <button id="btn-iniciar" onclick="iniciarJogo()">INICIAR BATALHA</button>
    </div>

    <!-- Overlay de Flash do Trovão -->
    <div class="flash-overlay" id="flash"></div>

    <!-- Interface do Jogo -->
    <div id="game-ui">
        <div class="battle-arena" id="arena">
            <!-- Mewtwo (Inimigo - Topo Direito) -->
            <div class="mewtwo-container">
                <div class="hp-bar" id="mewtwo-hp-text">HP: 3000</div>
                <img src="https://pokemondb.net" class="pokemon">
            </div>
            
            <!-- Pikachu (Jogador - Baixo Esquerdo) -->
            <div class="pikachu-container">
                <img src="https://pokemondb.net" class="pokemon">
                <div class="hp-bar">HP: 3000</div>
            </div>

            <!-- Botões de Ataque -->
            <div class="controls-container">
                <div class="controls">
                    <button class="move-btn" onclick="executarAtaque('TROVÃO')">TROVÃO</button>
                    <button class="move-btn" onclick="executarAtaque('CHOQUE')">CHOQUE</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Som do Trovão -->
    <audio id="sfx" src="https://myinstants.com"></audio>

    <script>
        function abrirPokebola() {
            document.getElementById('top').classList.add('open-top');
            document.getElementById('bottom').classList.add('open-bottom');
            // Exibe o botão exatamente no centro após a animação terminar
            setTimeout(() => { 
                document.getElementById('btn-iniciar').style.display = 'block'; 
            }, 600);
        }

        function iniciarJogo() {
            document.getElementById('start-screen').style.display = 'none';
            document.getElementById('game-ui').style.display = 'block';
        }

        function executarAtaque(nomeAtaque) {
            const flash = document.getElementById('flash');
            const arena = document.getElementById('arena');
            const sfx = document.getElementById('sfx');
            
            // Força o áudio a recomeçar do zero caso clique rápido
            sfx.currentTime = 0;
            sfx.play();
            
            // Adiciona as classes de animação na arena e no flash
            flash.classList.add('animar-flash');
            arena.classList.add('tremer-tela');
            
            // Remove as classes após 500ms para poder atacar de novo
            setTimeout(() => {
                flash.classList.remove('animar-flash');
                arena.classList.remove('tremer-tela');
            }, 500);
        }
    </script>
</body>
</html>
