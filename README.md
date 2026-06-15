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

        /* Flash de efeitos visuais */
        body.flash-damage { background: #4a0000 !important; }
        body.flash-boss-damage { background: #220033 !important; }
        body.flash-heal { background: #003311 !important; }
        body.flash-shield { background: #003344 !important; }

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
        .hud-center { text-align: center; color: #ffffff; padding: 0 10px; flex: 1; }
        .hud-center h2 { color: #9900ff; font-size: 1.4rem; }
        #battleStatus { font-size: 0.95rem; color: #00ffcc; font-weight: 500; }
        .table-ring { border: 1px solid #ff0055; padding: 8px 15px; border-radius: 5px; color: #ff0055; font-weight: bold; }

        /* ÁREA DE BATALHA */
        .battle-area {
            flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 15px; margin: 10px 0;
        }
        .status-box {
            background: rgba(255, 255, 255, 0.04); padding: 10px 25px; border-radius: 8px; 
            border: 1px solid rgba(255,255,255,0.1); text-align: center; min-width: 280px;
            position: relative;
        }
        .hp-text { font-size: 1.5rem; font-weight: bold; color: #ff0055; margin-top: 3px; }
        .shield-badge { 
            font-size: 0.75rem; background: #0077ff; color: white; padding: 2px 8px; 
            border-radius: 10px; position: absolute; top: -10px; left: 50%; transform: translateX(-50%);
            display: none; box-shadow: 0 0 10px #0077ff;
        }

        /* ÁREA DAS CARTAS (MÃO) */
        .hand-container {
            display: flex;
            gap: 15px;
            justify-content: center;
            align-items: center;
            width: 100%;
            min-height: 190px;
            perspective: 1000px;
        }

        /* DESIGN ESTILIZADO DAS CARTAS COŚMICAS */
        .quantum-card {
            width: 135px;
            height: 185px;
            background: linear-gradient(145deg, #070412 0%, #140d2e 100%);
            border: 2px solid #9900ff;
            border-radius: 12px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            padding: 12px;
            cursor: pointer;
            transition: all 0.25s cubic-bezier(0.25, 0.8, 0.25, 1);
            position: relative;
            user-select: none;
        }

        .quantum-card:hover {
            transform: translateY(-20px) scale(1.06);
            box-shadow: 0 12px 30px var(--card-glow);
            border-color: var(--card-glow);
        }

        .card-header {
            font-size: 0.75rem;
            font-weight: bold;
            text-align: center;
            color: #fff;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            line-height: 1.2;
            min-height: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .card-value {
            font-size: 1.6rem;
            font-weight: 900;
            text-align: center;
            text-shadow: 0 0 10px rgba(255,255,255,0.2);
        }

        .card-effect-desc {
            font-size: 0.65rem;
            color: #bbb;
            text-align: center;
            line-height: 1.2;
            margin: 5px 0;
            font-style: italic;
        }

        .card-footer {
            font-size: 0.6rem;
            text-align: center;
            color: #fff;
            background: rgba(255, 255, 255, 0.08);
            padding: 3px 0;
            border-radius: 50px;
            font-weight: 700;
            letter-spacing: 0.5px;
        }

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
                <p id="battleStatus">Sua vez! Use uma carta contra o Boss.</p>
            </div>
            <div class="table-ring" id="turnIndicator">Rodada: 1</div>
        </div>

        <div class="battle-area">
            <div class="status-box">
                <h3>Inimigo: Boss Quântico</h3>
                <div class="hp-text" id="enemyHp">HP: 1000</div>
            </div>

            <div class="hand-container" id="playerHand"></div>

            <div class="status-box">
                <div class="shield-badge" id="shieldIndicator">ESCUDO ATIVO</div>
                <h3>Seu Status</h3>
                <div class="hp-text" id="playerHp" style="color: #00ffcc;">HP: 1000</div>
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
        // Configurações e Status da Batalha Elevada para 1000 HP
        let enemyHp = 1000;
        let playerHp = 1000;
        let rodada = 1;
        let escudoJogador = false;
        let jogoAcabou = false;

        // EXPANSÃO DO BARALHO: 8 cartas com mecânicas e efeitos variados
        const deckDeCartas = [
            { nome: "Pulso de Fóton",  valor: 80,  efeito: "ataque",   desc: "Disparo rápido padrão",       cor: "#00ffcc", tag: "FÓTON" },
            { nome: "Raio Gamma",     valor: 140, efeito: "ataque",   desc: "Forte feixe radioativo",     cor: "#ff0055", tag: "CÓSMICO" },
            { nome: "Buraco Negro",   valor: 250, efeito: "critico",  desc: "50% de chance de erro ou CRÍTICO",cor: "#9900ff", tag: "SINGULARIDADE" },
            { nome: "Plasma Estelar",  valor: 60,  efeito: "ataque",   desc: "Calor constante básico",       cor: "#ffcc00", tag: "ESTRELA" },
            { nome: "Dreno Sombrio",  valor: 90,  efeito: "vampiro",  desc: "Causa dano e cura você",      cor: "#ff00aa", tag: "ANTIMATÉRIA" },
            { nome: "Matriz de Éter", valor: 150, efeito: "cura",     desc: "Restaura integridade molecular", cor: "#00ff55", tag: "REGEN" },
            { nome: "Dobra Espacial",  valor: 0,   efeito: "escudo",   desc: "Bloqueia o próximo golpe do Boss", cor: "#0077ff", tag: "DEFESA" },
            { nome: "Supernova",      valor: 320, efeito: "ataque",   desc: "Dano extremo massivo",        cor: "#ff5500", tag: "FINAL" }
        ];

        let maoAtual = [];

        function avancarSplash() {
            document.getElementById('splashScreen').style.display = 'none';
            document.getElementById('betaScreen').style.display = 'flex';
        }

        function avancarBeta() {
            document.getElementById('betaScreen').style.display = 'none';
            document.getElementById('mainMenu').style.display = 'flex';
        }

        function iniciarJogoAtivo() {
            enemyHp = 1000;
            playerHp = 1000;
            rodada = 1;
            escudoJogador = false;
            jogoAcabou = false;
            
            document.getElementById('enemyHp').innerText = "HP: " + enemyHp;
            document.getElementById('playerHp').innerText = "HP: " + playerHp;
            document.getElementById('turnIndicator').innerText = "Rodada: " + rodada;
            document.getElementById('battleStatus').innerText = "O Boss Quântico desafia você! Escolha sua carta.";
            document.getElementById('shieldIndicator').style.display = "none";
            
            // Sorteia as 3 cartas iniciais
            maoAtual = [];
            for(let i = 0; i < 3; i++) {
                maoAtual.push(sortearNovaCarta());
            }

            renderizarMaoDoJogador();
            
            document.getElementById('mainMenu').style.display = 'none';
            document.getElementById('gameBoard').style.display = 'flex';
        }

        function sortearNovaCarta() {
            const indiceAleatorio = Math.floor(Math.random() * deckDeCartas.length);
            return deckDeCartas[indiceAleatorio];
        }

        function renderizarMaoDoJogador() {
            const containerMao = document.getElementById('playerHand');
            containerMao.innerHTML = '';

            if (jogoAcabou) return;

            maoAtual.forEach((carta, indice) => {
                const elementoCarta = document.createElement('div');
                elementoCarta.className = 'quantum-card';
                elementoCarta.style.setProperty('--card-glow', carta.cor + "55");
                elementoCarta.style.borderColor = carta.cor;

                elementoCarta.onclick = () => processarTurnoCompleto(indice);

                let exibicaoValor = carta.valor > 0 ? carta.valor : "";
                if(carta.efeito === "cura") exibicaoValor = "+" + carta.valor;
                if(carta.efeito === "ataque" || carta.efeito === "critico" || carta.efeito === "vampiro") exibicaoValor = "-" + carta.valor;
                if(carta.efeito === "escudo") exibicaoValor = "DEF";

                elementoCarta.innerHTML = `
                    <div class="card-header">${carta.nome}</div>
                    <div class="card-value" style="color: ${carta.cor};">${exibicaoValor}</div>
                    <div class="card-effect-desc">${carta.desc}</div>
                    <div class="card-footer" style="background:${carta.cor}22; color:${carta.cor}">${carta.tag}</div>
                `;

                containerMao.appendChild(elementoCarta);
            });
        }

        // CONTROLADOR DE TURNO: Sua Ação -> Verificação -> Ataque do Boss
        function processarTurnoCompleto(indiceCarta) {
            if(jogoAcabou) return;

            const cartaJogada = maoAtual[indiceCarta];
            let msgLog = "";

            // 1. PROCESSA A CARTA DO JOGADOR
            if (cartaJogada.efeito === "ataque") {
                enemyHp -= cartaJogada.valor;
                msgLog = `Você usou ${cartaJogada.nome} e causou ${cartaJogada.valor} de dano! `;
                aplicarFlashVisual('flash-damage');
            } 
            else if (cartaJogada.efeito === "critico") {
                // Mecânica Crítica: 50% de chance de duplicar ou zerar
                if (Math.random() >= 0.5) {
                    let superDano = cartaJogada.valor * 2;
                    enemyHp -= superDano;
                    msgLog = `💥 CRÍTICO COŚMICO! ${cartaJogada.nome} causou ${superDano} de dano! `;
                    aplicarFlashVisual('flash-damage');
                } else {
                    msgLog = `❌ O colapso falhou! ${cartaJogada.nome} não causou dano nesta dimensão. `;
                }
            }
            else if (cartaJogada.efeito === "vampiro") {
                enemyHp -= cartaJogada.valor;
                playerHp += Math.floor(cartaJogada.valor * 0.6); // Cura 60% do dano dado
                if (playerHp > 1000) playerHp = 1000;
                msgLog = `🩸 Vampirismo! Causou ${cartaJogada.valor} de dano e converteu em vida! `;
                aplicarFlashVisual('flash-heal');
            }
            else if (cartaJogada.efeito === "cura") {
                playerHp += cartaJogada.valor;
                if(playerHp > 1000) playerHp = 1000;
                msgLog = `💚 Recuperação Quântica! Você se curou em +${cartaJogada.valor} HP. `;
                aplicarFlashVisual('flash-heal');
            } 
            else if (cartaJogada.efeito === "escudo") {
                escudoJogador = true;
                document.getElementById('shieldIndicator').style.display = "block";
                msgLog = `🛡️ Dobra Espacial ativada! Você está protegido do próximo golpe. `;
                aplicarFlashVisual('flash-shield');
            }

            // Trava HP em 0
            if(enemyHp < 0) enemyHp = 0;
            document.getElementById('enemyHp').innerText = "HP: " + enemyHp;

            // Verifica se o jogador ganhou antes do boss revidar
            if (enemyHp === 0) {
                document.getElementById('battleStatus').innerText = msgLog + "🏆 VITÓRIA! Você pulverizou o Boss Quântico!";
                finalizarPartida();
                return;
            }

            // 2. TURNO DO BOSS (CONTRA-ATAQUE AUTOMÁTICO)
            setTimeout(() => {
                if(jogoAcabou) return;

                // Sorteia um dano aleatório do Boss entre 60 e 160
                let danoDoBoss = Math.floor(Math.random() * (160 - 60 + 1)) + 60;
                
                // Frases de ataque do Boss
                const frasesBoss = ["Desparou um Laser Quântico", "Invocou uma Tempestade Estelar", "裂 Alterou a Gravidade", "Ejetou Matéria Escura"];
                let ataqueEscolhido = frasesBoss[Math.floor(Math.random() * frasesBoss.length)];

                if (escudoJogador) {
                    msgLog += `| 🛡️ O Boss usou [${ataqueEscolhido}], mas seu escudo ABSORVEU o golpe!`;
                    escudoJogador = false; // Gasta o escudo
                    document.getElementById('shieldIndicator').style.display = "none";
                } else {
                    playerHp -= danoDoBoss;
                    if (playerHp < 0) playerHp = 0;
                    msgLog += `| 👾 O Boss usou [${ataqueEscolhido}] e te causou ${danoDoBoss} de dano!`;
                    aplicarFlashVisual('flash-boss-damage');
                }

                document.getElementById('playerHp').innerText = "HP: " + playerHp;
                document.getElementById('battleStatus').innerText = msgLog;

                // Verifica se o jogador perdeu
                if (playerHp === 0) {
                    document.getElementById('battleStatus').innerText = "💀 FIM DE JOGO! Sua energia colapsou na arena do Whagton.";
                    finalizarPartida();
                    return;
                }

                // Passa a rodada
                rodada++;
                document.getElementById('turnIndicator').innerText = "Rodada: " + rodada;

                // Substitui a carta jogada por uma nova do deck
                maoAtual[indiceCarta] = sortearNovaCarta();
                renderizarMaoDoJogador();

            }, 700); // Pequeno atraso para dar tempo de ler o que a sua carta fez
        }

        function aplicarFlashVisual(classeCSS) {
            document.body.classList.add(classeCSS);
            setTimeout(() => document.body.classList.remove(classeCSS), 180);
        }

        function finalizarPartida() {
            jogoAcabou = true;
            document.getElementById('playerHand').innerHTML = '';
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
