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
        .mewtwo-container { display: flex; flex-direction: column; align-items: center; align-self: flex-end; text-align: center; transition: 0.2s; }
        .pikachu-container { display: flex; flex-direction: column; align-items: center; align-self: flex-start; text-align: center; transition: 0.2s; }
        .pokemon { width: 150px; height: 150px; object-fit: contain; }
        .hp-bar { font-weight: bold; margin-bottom: 5px; background: rgba(0,0,0,0.5); padding: 5px 15px; border-radius: 20px; border: 1px solid #444; }
        
        /* Painel de Ataques no rodapé */
        .controls-container { width: 100%; display: flex; justify-content: center; padding-top: 20px; }
        .controls { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; width: 100%; max-width: 400px; }
        .move-btn { padding: 15px; cursor: pointer; background: #222; color: white; border: 2px solid #ffcc00; font-weight: bold; border-radius: 8px; transition: 0.2s; }
        .move-btn:hover:not(:disabled) { background: #ffcc00; color: black; }
        .move-btn:disabled { background: #444; border-color: #666; cursor: not-allowed; opacity: 0.5; }

        /* Log de Mensagens da Batalha */
        #battle-log { text-align: center; font-size: 16px; font-weight: bold; margin-top: 10px; min-height: 24px; color: #ffcc00; }

        /* Flash Amarelo (Pikachu) e Roxo (Mewtwo) */
        .flash-overlay { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; opacity: 0; pointer-events: none; z-index: 9999; }
        @keyframes flash-yellow { 0%, 100% { opacity: 0; } 25% { background: yellow; opacity: 0.6; } 50% { background: black; opacity: 0.6; } 75% { background: yellow; opacity: 0.6; } }
        @keyframes flash-purple { 0%, 100% { opacity: 0; } 25% { background: #8a2be2; opacity: 0.6; } 50% { background: black; opacity: 0.6; } 75% { background: #8a2be2; opacity: 0.6; } }
        .animar-flash-pika { animation: flash-yellow 0.1s 4; }
        .animar-flash-mew { animation: flash-purple 0.1s 4; }
        
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

    <!-- Overlay de Flash dos Ataques -->
    <div class="flash-overlay" id="flash"></div>

    <!-- Interface do Jogo -->
    <div id="game-ui">
        <div class="battle-arena" id="arena">
            <!-- Mewtwo (Inimigo) -->
            <div class="mewtwo-container" id="mewtwo-box">
                <div class="hp-bar" id="mewtwo-hp">HP: 3000</div>
                <img src="https://pokemondb.net" class="pokemon">
            </div>
            
            <!-- Texto de Status da Luta -->
            <div id="battle-log">Mewtwo selvagem apareceu!</div>

            <!-- Pikachu (Jogador) -->
            <div class="pikachu-container" id="pikachu-box">
                <img src="https://pokemondb.net" class="pokemon">
                <div class="hp-bar" id="pikachu-hp">HP: 3000</div>
            </div>

            <!-- Botões de Ataque -->
            <div class="controls-container">
                <div class="controls">
                    <button class="move-btn" onclick="executarAtaque('TROVÃO', 450)" id="btn-atk1">TROVÃO</button>
                    <button class="move-btn" onclick="executarAtaque('CHOQUE DO TROVÃO', 300)" id="btn-atk2">CHOQUE</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Efeitos Sonoros -->
    <audio id="sfx-pika" src="https://myinstants.com"></audio>
    <audio id="sfx-mewtwo" src="https://pokemonshowdown.com"></audio>

    <script>
        // Variaveis de vida dos Pokémons
        let mewtwoHP = 3000;
        let pikachuHP = 3000;
        let turnoBloqueado = false;

        function abrirPokebola() {
            document.getElementById('top').classList.add('open-top');
            document.getElementById('bottom').classList.add('open-bottom');
            setTimeout(() => { 
                document.getElementById('btn-iniciar').style.display = 'block'; 
            }, 600);
        }

        function iniciarJogo() {
            document.getElementById('start-screen').style.display = 'none';
            document.getElementById('game-ui').style.display = 'block';
        }

        // Desativa os botões para o jogador não clicar durante as animações
        function alternarBotoes(status) {
            document.getElementById('btn-atk1').disabled = status;
            document.getElementById('btn-atk2').disabled = status;
        }

        function executarAtaque(nomeAtaque, dano) {
            if (turnoBloqueado) return;
            turnoBloqueado = true;
            alternarBotoes(true);

            const flash = document.getElementById('flash');
            const arena = document.getElementById('arena');
            const sfxPika = document.getElementById('sfx-pika');
            const log = document.getElementById('battle-log');

            // --- TURNO DO PIKACHU ---
            log.innerText = `Pikachu usou ${nomeAtaque}!`;
            sfxPika.currentTime = 0;
            sfxPika.play();
            
            flash.classList.add('animar-flash-pika');
            arena.classList.add('tremer-tela');
            
            // Aplica o dano no Mewtwo
            mewtwoHP = Math.max(0, mewtwoHP - dano);
            document.getElementById('mewtwo-hp').innerText = `HP: ${mewtwoHP}`;

            setTimeout(() => {
                flash.classList.remove('animar-flash-pika');
                arena.classList.remove('tremer-tela');

                if (mewtwoHP <= 0) {
                    log.innerText = "Mewtwo desmaiou! Você venceu!";
                    document.getElementById('mewtwo-box').style.opacity = '0';
                    return; // Fim de jogo, Mewtwo não contra-ataca
                }

                // --- CONTRA-ATAQUE DO MEWTWO (Após 1.2 segundos) ---
                setTimeout(contraAtaqueMewtwo, 1200);

            }, 500);
        }

        function contraAtaqueMewtwo() {
            const flash = document.getElementById('flash');
            const arena = document.getElementById('arena');
            const sfxMew = document.getElementById('sfx-mewtwo');
            const log = document.getElementById('battle-log');

            log.innerText = "Mewtwo contra-atacou com GOLPE PSÍQUICO!";
            sfxMew.currentTime = 0;
            sfxMew.play();

            flash.classList.add('animar-flash-mew');
            arena.classList.add('tremer-tela');

            // Mewtwo causa dano aleatório entre 350 e 500
            let danoMewtwo = Math.floor(Math.random() * 150) + 350;
            pikachuHP = Math.max(0, pikachuHP - danoMewtwo);
            document.getElementById('pikachu-hp').innerText = `HP: ${pikachuHP}`;

            setTimeout(() => {
                flash.classList.remove('animar-flash-mew');
                arena.classList.remove('tremer-tela');

                if (pikachuHP <= 0) {
                    log.innerText = "Pikachu desmaiou! Você perdeu!";
                    document.getElementById('pikachu-box').style.opacity = '0';
                } else {
                    log.innerText = "O que o Pikachu vai fazer?";
                    turnoBloqueado = false;
