<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Card Battle Arena - Quantum Evolution</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        
        body {
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background: #020208;
            min-height: 100vh; 
            color: #e0e0e0; 
            overflow: hidden; 
            position: relative;
            transition: background 0.2s ease;
        }

        /* 1. TELA SPLASH */
        .splash-screen {
            position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
            background: radial-gradient(circle at center, #0a0518 0%, #020208 100%);
            z-index: 200; display: flex; flex-direction: column; align-items: center; justify-content: center;
        }
        .splash-title {
            font-size: 3.5rem; font-weight: 900; color: #00ffcc; letter-spacing: 6px; margin-bottom: 40px;
            text-shadow: 0 0 20px rgba(0, 255, 204, 0.5); text-align: center;
        }
        .start-game-btn {
            font-size: 1.3rem; font-weight: 700; padding: 16px 50px; 
            background: linear-gradient(135deg, #9900ff, #ff0055);
            border: none; color: white; border-radius: 50px; cursor: pointer; letter-spacing: 3px;
            box-shadow: 0 0 30px rgba(153, 0, 255, 0.6); transition: all 0.2s ease;
        }
        .start-game-btn:hover { transform: scale(1.05); box-shadow: 0 0 40px #ff0055; }

        /* 2. TELA BETA */
        .beta-welcome-screen {
            position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
            background: #050510; color: #ffffff; z-index: 100;
            display: none; flex-direction: column; align-items: center; justify-content: center; padding: 30px; text-align: center;
        }
        .beta-box { max-width: 600px; }
        .beta-box h2 { color: #00ffcc; margin-bottom: 25px; font-size: 2rem; }
        .beta-box p { font-size: 1.1rem; line-height: 1.8; margin-bottom: 35px; }
        .beta-btn {
            padding: 12px 35px; font-size: 1rem; background: transparent; border: 2px solid #00ffcc; color: #00ffcc;
            border-radius: 50px; cursor: pointer; transition: all 0.3s ease;
        }
        .beta-btn:hover { background: #00ffcc; color: #000; box-shadow: 0 0 25px #00ffcc; }

        /* 3. MENU PRINCIPAL */
        .screen {
            position: fixed; top:0; left:0; width:100vw; height:100vh;
            display: none; flex-direction: column; align-items: center; justify-content: center; z-index: 10; background: #03030f;
        }
        .game-title { font-size: 3rem; text-align: center; color: #9900ff; margin-bottom: 20px; letter-spacing: 4px; }
        .profile-badge {
            font-size: 0.9rem; color: #00ffcc; background: rgba(0,255,204,0.05); padding: 8px 20px; border-radius: 20px;
            border: 1px solid rgba(0,255,204,0.2); margin-bottom: 30px;
        }
        .menu-buttons { display: flex; flex-direction: column; gap: 15px; width: 300px; }
        .menu-btn {
            padding: 12px; font-size: 1rem; background: rgba(153,0,255,0.1); border: 2px solid #9900ff; color: #fff;
            border-radius: 8px; cursor: pointer; transition: 0.3s; text-align: center;
        }
        .menu-btn:hover { background: #9900ff; box-shadow: 0 0 15px #9900ff; }

        /* Flash de dano */
        body.flash-damage { background: #3a0000 !important; }

        /* 4. TABULEIRO DO JOGO COMPLETO */
        .board-screen {
            display: none; width: 100vw; height: 100vh; flex-direction: column;
            justify-content: space-between; padding: 20px; background: #04040c;
            position: fixed; top: 0; left: 0; z-index: 5;
        }
        .board-hud {
            display: flex; justify-content: space-between; align-items: center;
            background: rgba(0,0,0,0.8); padding: 15px; border-radius: 10px;
            border: 1px solid rgba(153, 0, 255, 0.3); width: 100%;
        }
        .credits-container { display: flex; flex-direction: column; gap: 2px; }
        .dev-tag { font-size: 0.7rem; color: #00ffcc; font-weight: bold; }
        .hud-center { text-align: center; color: #ffffff; }
        .hud-center h2 { color: #9900ff; font-size: 1.5rem; }
        .table-ring { border: 1px solid #ff0055; padding: 8px 15px; border-radius: 5px; color: #ff0055; font-weight: bold; }

        /* ÁREA DE BATALHA */
        .battle-area {
            flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 20px; margin: 20px 0;
        }
        .status-box {
            background: rgba(255, 255, 255, 0.05); padding: 15px 30px; border-radius: 8px; 
            border: 1px solid rgba(255,255,255,0.1); text-align: center; min-width: 250px;
        }
        .hp-text { font-size: 1.3rem; font-weight: bold; color: #ff0055; margin-top: 5px; }
        .action-btn {
            background: linear-gradient(135deg, #00ffcc, #9900ff); border: none; color: black;
            padding: 12px 30px; font-size: 1.1rem; font-weight: bold; border-radius: 5px; cursor: pointer;
            box-shadow: 0 0 15px rgba(0, 255, 204, 0.4); transition: 0.2s;
        }
        .action-btn:hover { transform: scale(1.05); box-shadow: 0 0 25px #00ffcc; }

        /* MODAL */
        .modal {
            position: fixed; top:0; left:0; width:100vw; height:100vh;
            background: rgba(0,0,0,0.8); z-index: 20; display: none; align-items: center; justify-content: center;
        }
        .modal-content {
            background: #090914; border: 2px solid #ff0055; padding: 30px; border-radius: 12px; max-width: 500px; text-align: center; width: 90%;
        }
        .modal-btn { padding: 10px 25px; background: #ff0055; border: none; color: #fff; border-radius: 5px; cursor: pointer; font-weight: bold; margin-top: 15px;}
    </style>
</head>
<body>

    <div class="splash-screen" id="splashScreen">
        <div class="splash-container">
            <h1 class="splash-title">CARD BATTLE ARENA</h1>
            <button class="start-game-btn" onclick="avancarSplash()">Iniciar Conexão</button>
        </div>
    </div>

    <div class="beta-welcome-screen" id="betaScreen">
        <div class="beta-box">
            <h2>Fase de Testes Beta</h2>
            <p>Quantum Evolution está em sua fase inicial de maturação quântica. Seus feedbacks guiarão o balanço da arena cósmica.</p>
            <button class="beta-btn" onclick="avancarBeta()">Entrar na Arena</button>
        </div>
    </div>

    <div class="screen" id="mainMenu">
        <h1 class="game-title">QUANTUM EVOLUTION</h1>
        <div class="profile-badge">Jogador: Convidado_Quantum</div>
        <div class="menu-buttons">
            <button class="menu-btn" onclick="iniciarJogoAtivo()">Batalhar (PvE)</button>
            <button class="menu-btn" onclick="abrirModal()">Customizar Deck</button>
            <button class="menu-btn" onclick="abrirModal()">Laboratório de Evolução</button>
        </div>
    </div>

    <div class="board-screen" id="gameBoard">
        <div class="board-hud">
            <div class="credits-container">
                <span class="dev-tag">DEVELOPER MODE</span>
                <span class="dev-tag">BY: WHAGTON</span>
            </div>
            <div class="hud-center">
                <h2>ARENA QUANTUM</h2>
                <p id="battleStatus">O combate começou!</p>
            </div>
            <div class="table-ring">Rodada: 1</div>
        </div>

        <div class="battle-area">
            <div class="status-box">
                <h3>Inimigo: Boss Quântico</h3>
                <div class="hp-text" id="enemyHp">HP: 100</div>
            </div>

            <button class="action-btn" onclick="atacarInimigo()">Atacar com Carta Cósmica!</button>

            <div class="status-box">
                <h3>Seu Status</h3>
                <div class="hp-text" style="color: #00ffcc;">HP: 100</div>
            </div>
        </div>

        <div style="text-align: center;">
            <button class="menu-btn" style="width: auto; padding: 10px 30px;" onclick="voltarAoMenu()">Fugir da Batalha</button>
        </div>
    </div>

    <div class="modal" id="alertModal">
        <div class="modal-content">
            <h3>Setor Bloqueado</h3>
            <p>Esta funcionalidade está sendo processada na Singularidade Tecnológica do Whagton e estará disponível na próxima atualização.</p>
            <button class="modal-btn" onclick="fecharModal()">Entendido</button>
        </div>
    </div>

    <script>
        // Variável do jogo
        let enemyHp = 100;

        function avancarSplash() {
            document.getElementById('splashScreen').style.display = 'none';
            document.getElementById('betaScreen').style.display = 'flex';
        }

        function avancarBeta() {
            document.getElementById('betaScreen').style.display = 'none';
            document.getElementById('mainMenu').style.display = 'flex';
        }

        function iniciarJogoAtivo() {
            // Reinicia o HP do inimigo ao entrar
            enemyHp = 100;
            document.getElementById('enemyHp').innerText = "HP: " + enemyHp;
            document.getElementById('battleStatus').innerText = "O combate começou!";
            
            document.getElementById('mainMenu').style.display = 'none';
            document.getElementById('gameBoard').style.display = 'flex';
        }

        function atacarInimigo() {
            if(enemyHp > 0) {
                enemyHp -= 20; // Tira 20 de vida
                if(enemyHp < 0) enemyHp = 0;
                
                document.getElementById('enemyHp').innerText = "HP: " + enemyHp;
                document.getElementById('battleStatus').innerText = "Você causou 20 de dano!";
                
                // Efeito visual de piscar a tela vermelha (Dano)
                document.body.classList.add('flash-damage');
                setTimeout(() => document.body.classList.remove('flash-damage'), 150);

                if(enemyHp === 0) {
                    document.getElementById('battleStatus').innerText = "Vitória! Você destruiu o Boss!";
                }
            }
        }

        function voltarAoMenu() {
            document.getElementById('gameBoard').style.display = 'none';
            document.getElementById('mainMenu').style.display = 'flex';
        }

        function abrirModal() {
            document.getElementById('alertModal').style.display = 'flex';
        }

        function fecharModal() {
            document.getElementById('alertModal').style.display = 'none';
        }
    </script>
</body>
</html>
