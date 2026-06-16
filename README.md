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
        .battle-arena { display: flex; flex-direction: column; justify-content: space-between; align-items: center; height: 100%; max-width: 600px; margin: 0 auto; padding: 30px 20px; box-sizing: border-box; }
        
        /* Containers dos Pokémons com transições e animações de Entrada */
        .mewtwo-container { display: flex; flex-direction: column; align-items: center; align-self: flex-end; text-align: center; transition: 0.3s; }
        .pikachu-container { display: flex; flex-direction: column; align-items: center; align-self: flex-start; text-align: center; transition: 0.3s; }
        
        /* Classes de animação de entrada */
        .slide-in-right { animation: slideRight 0.8s cubic-bezier(0.25, 1, 0.5, 1) forwards; }
        .slide-in-left { animation: slideLeft 0.8s cubic-bezier(0.25, 1, 0.5, 1) forwards; }
        
        @keyframes slideRight { from { transform: translateX(300px); opacity: 0; } to { transform: translateX(0); opacity: 1; } }
        @keyframes slideLeft { from { transform: translateX(-300px); opacity: 0; } to { transform: translateX(0); opacity: 1; } }

        .pokemon { width: 150px; height: 150px; object-fit: contain; transition: filter 0.1s, opacity 0.5s; }
        
        /* Nova HUD estilizada com Barra de Vida (Lifebar) */
        .hud { width: 190px; background: rgba(0, 0, 0, 0.7); padding: 8px 12px; border-radius: 10px; border: 2px solid #444; margin-bottom: 5px; text-align: left; }
        .hp-bar-container { width: 100%; height: 100%; background: #333; border-radius: 6px; border: 1px solid #000; overflow: hidden; margin-top: 5px; height: 12px; }
        .hp-bar-fill { width: 100%; height: 100%; background: #4cd137; transition: width 0.4s ease-out, background-color 0.4s; }
        .hp-text { font-weight: bold; font-size: 13px; display: flex; justify-content: space-between; }
        
        /* Barra de Especial HUD */
        .special-container { width: 100%; height: 6px; background: #222; border-radius: 3px; margin-top: 4px; overflow: hidden; border: 1px solid #111; }
        .special-fill { width: 0%; height: 100%; background: #00a8ff; transition: width 0.4s ease-out; }

        /* Animação de Pulo de Ataque do Pikachu */
        .pika-pulo { animation: pikaAtk 0.4s ease-in-out; }
        @keyframes pikaAtk { 0%, 100% { transform: translate(0, 0); } 40% { transform: translate(40px, -40px) scale(1.1); } }

        /* Animação de Piscar em Vermelho ao tomar Dano */
        .tomar-dano { animation: piscarDano 0.1s 4; }
        @keyframes piscarDano { 0%, 100% { filter: brightness(1); } 50% { filter: brightness(0.5) sepia(1) hue-rotate(-50deg) saturate(5); } }

        /* Partículas Elétricas Geradas por Código */
        .spark { position: absolute; width: 6px; height: 6px; background: #fffb00; box-shadow: 0 0 10px #ffea00; border-radius: 50%; pointer-events: none; z-index: 999; animation: sparkFade 0.5s ease-out forwards; }
        @keyframes sparkFade { from { transform: translate(0, 0) scale(1); opacity: 1; } to { transform: translate(var(--mx), var(--my)) scale(0); opacity: 0; } }

        /* Painel de Ataques no rodapé expandido */
        .controls-container { width: 100%; display: flex; justify-content: center; padding-top: 10px; position: relative; }
        .controls { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; width: 100%; max-width: 450px; }
        .move-btn { padding: 12px; cursor: pointer; background: #222; color: white; border: 2px solid #ffcc00; font-weight: bold; border-radius: 8px; transition: 0.2s; font-size: 13px; }
        .move-btn:hover:not(:disabled) { background: #ffcc00; color: black; }
        .move-btn:disabled { background: #444; border-color: #666; cursor: not-allowed; opacity: 0.5; }
        
        /* Botões especiais utilitários */
        .heal-btn { border-color: #4cd137; }
        .heal-btn:hover:not(:disabled) { background: #4cd137; color: white; }
        .ult-btn { border-color: #00a8ff; color: #00a8ff; }
        .ult-btn:hover:not(:disabled) { background: #00a8ff; color: white; }

        /* Botão de Jogar Novamente */
        #btn-restart { display: none; position: absolute; padding: 15px 30px; cursor: pointer; background: #ffcc00; border: 3px solid #000; font-weight: bold; border-radius: 10px; color: black; font-size: 16px; z-index: 10; animation: popIn 0.3s ease-out; }

        /* Log de Mensagens da Batalha */
        #battle-log { text-align: center; font-size: 15px; font-weight: bold; margin-top: 5px; min-height: 40px; color: #ffcc00; padding: 0 10px; line-height: 1.4; }

        /* Animações de Flash */
        .flash-overlay { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; opacity: 0; pointer-events: none; z-index: 9999; }
        .animar-flash-trovao { animation: flash-thunder 0.12s 4; }
        .animar-flash-choque { animation: flash-shock 0.15s 2; }
        .animar-flash-mew { animation: flash-purple 0.1s 4; }
        
        @keyframes flash-thunder { 0%, 100% { opacity: 0; } 20%, 60% { background: yellow; opacity: 0.7; } 40%, 80% { background: black; opacity: 0.7; } }
        @keyframes flash-shock { 0%, 100% { opacity: 0; } 25%, 75% { background: yellow; opacity: 0.6; } 50% { background: black; opacity: 0.4; } }
        @keyframes flash-purple { 0%, 100% { opacity: 0; } 25% { background: #8a2be2; opacity: 0.6; } 50% { background: black; opacity: 0.6; } 75% { background: #8a2be2; opacity: 0.6; } }
        
        .tremer-tela { animation: shake 0.1s 4; }
        @keyframes shake { 0%, 100% { transform: translate(0,0); } 25% { transform: translate(-10px, -10px); } 50% { transform: translate(10px, 10px); } 75% { transform: translate(-10px, 10px); } }
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
                <div class="hud">
                    <div class="hp-text"><span>MEWTWO</span> <span id="mewtwo-hp-txt">3000/3000</span></div>
                    <div class="hp-bar-container">
                        <div class="hp-bar-fill" id="mewtwo-hp-bar"></div>
                    </div>
                </div>
                <img src="https://pokemondb.net" class="pokemon" id="mewtwo-img">
            </div>
            
            <!-- Texto de Status da Luta -->
            <div id="battle-log">Mewtwo selvagem apareceu!</div>

            <!-- Pikachu (Jogador) -->
            <div class="pikachu-container" id="pikachu-box">
                <img src="https://pokemondb.net" class="pokemon" id="pikachu-img">
                <div class="hud">
                    <div class="hp-text"><span>PIKACHU</span> <span id="pikachu-hp-txt">3000/3000</span></div>
                    <div class="hp-bar-container">
                        <div class="hp-bar-fill" id="pikachu-hp-bar"></div>
                    </div>
                    <div class="special-container">
                        <div class="special-fill" id="pikachu-ult-bar"></div>
                    </div>
                </div>
            </div>

            <!-- Painel de Comandos / Reiniciar -->
            <div class="controls-container">
                <div class="controls" id="action-panel">
                    <button class="move-btn" onclick="executarAtaque('TROVÃO', 450, 'trovao')" id="btn-atk1">TROVÃO</button>
                    <button class="move-btn" onclick="executarAtaque('CHOQUE DO TROVÃO', 300, 'choque')" id="btn-atk2">CHOQUE</button>
                    <button class="move-btn heal-btn" onclick="usarItem()" id="btn-item">MOCHILA (POTION: 3)</button>
