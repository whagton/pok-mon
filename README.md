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
            justify-content: space-between; padding: 15px; background: #04040c;
            position: fixed; top: 0; left: 0; z-index: 5;
        }
        .board-hud {
            display: flex; justify-content: space-between; align-items: center;
            background: rgba(0,0,0,0.8); padding: 10px; border-radius: 10px;
            border: 1px solid rgba(153, 0, 255, 0.3); width: 100%;
        }
        .credits-container { display: flex; flex-direction: column; gap: 2px; }
        .dev-tag { font-size: 0.7rem; color: #00ffcc; font-weight: bold; }
        .hud-center { text-align: center; color: #ffffff; padding: 0 10px; flex: 1; }
        .hud-center h2 { color: #9900ff; font-size: 1.3rem; }
        #battleStatus { font-size: 0.8rem; color: #00ffcc; font-weight: 500; min-height: 45px; display: flex; align-items: center; justify-content: center; line-height: 1.4; white-space: pre-line; }
        .table-ring { border: 1px solid #ff0055; padding: 6px 12px; border-radius: 5px; color: #ff0055; font-weight: bold; font-size: 0.9rem; }

        /* ÁREA DE BATALHA COM MULTI-BOSS */
        .battle-area {
            flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: space-between; gap: 10px; margin: 10px 0;
        }
        
        .enemies-row {
            display: flex; gap: 15px; justify-content: center; width: 100%;
        }

        .status-box {
            background: rgba(255, 255, 255, 0.04); padding: 10px 20px; border-radius: 8px; 
            border: 1px solid rgba(255,255,255,0.1); text-align: center; min-width: 200px;
            position: relative; transition: all 0.2s ease;
        }
        
        .status-box.enemy-target {
            border-color: #00ffcc;
            box-shadow: 0 0 15px rgba(0, 255, 204, 0.4);
            background: rgba(0, 255, 204, 0.05);
            cursor: pointer;
        }
        
        .status-box.selectable-enemy { cursor: pointer; }
        .status-box.selectable-enemy:hover { border-color: #9900ff; background: rgba(153, 0, 255, 0.05); }

        .status-box.dead-enemy {
            opacity: 0.3; border-color: #333; background: rgba(0,0,0,0.5); pointer-events: none;
        }
        .status-box.dead-enemy .hp-text { color: #555 !important; }

        .hp-text { font-size: 1.3rem; font-weight: bold; color: #ff0055; margin-top: 3px; }
        .shield-badge { 
            font-size: 0.7rem; background: #0077ff; color: white; padding: 1px 6px; 
            border-radius: 10px; position: absolute; top: -8px; left: 50%; transform: translateX(-50%);
            display: none; box-shadow: 0 0 8px #0077ff;
        }

        /* ÁREA DAS CARTAS EXPANDIDA */
        .hand-container {
            display: flex; gap: 12px; justify-content: center; align-items: center; width: 100%; min-height: 165px; perspective: 1000px;
        }

        /* DESIGN DAS CARTAS CÓSMICAS */
        .quantum-card {
            width: 125px; height: 165px;
            background: linear-gradient(145deg, #070412 0%, #140d2e 100%);
            border: 2px solid #9900ff; border-radius: 12px;
            display: flex; flex-direction: column; justify-content: space-between;
            padding: 10px; cursor: pointer; position: relative; user-select: none;
            transition: all 0.25s cubic-bezier(0.25, 0.8, 0.25, 1);
        }

        .quantum-card:hover {
            transform: translateY(-15px) scale(1.06); box-shadow: 0 12px 25px var(--card-glow); border-color: var(--card-glow);
        }

        .card-header {
            font-size: 0.65rem; font-weight: bold; text-align: center; color: #fff;
            text-transform: uppercase; letter-spacing: 0.5px; line-height: 1.2;
            min-height: 28px; display: flex; align-items: center; justify-content: center;
        }

        .card-value { font-size: 1.3rem; font-weight: 900; text-align: center; text-shadow: 0 0 10px rgba(255,255,255,0.2); }
        .card-effect-desc { font-size: 0.58rem; color: #bbb; text-align: center; line-height: 1.2; margin: 3px 0; font-style: italic; }
        .card-footer { font-size: 0.55rem; text-align: center; color: #fff; background: rgba(255, 255, 255, 0.08); padding: 2px 0; border-radius: 50px; font-weight: 700; }

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
            <button class="menu-btn" onclick="iniciarJogoAtivo()">Batalhar (PvE Chaos Royale)</button>
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
                <h2>ARENA QUANTUM - CHAOS ROYALE</h2>
                <p id="battleStatus">Os chefes agora têm 60% de chance de se atacarem mutuamente! Aproveite o caos.</p>
            </div>
            <div class="table-ring" id="turnIndicator">Rodada: 1</div>
        </div>

        <div class="battle-area">
            
            <div class="enemies-row">
                <div class="status-box selectable-enemy" id="bossBox_0" onclick="selecionarAlvo(0)">
                    <h3>Boss Alfa</h3>
                    <div class="hp-text" id="enemyHp_0">HP: 10000</div>
                </div>
                <div class="status-box selectable-enemy" id="bossBox_1" onclick="selecionarAlvo(1)">
                    <h3>Boss Beta</h3>
                    <div class="hp-text" id="enemyHp_1">HP: 10000</div>
                </div>
                <div class="status-box selectable-enemy" id="bossBox_2" onclick="selecionarAlvo(2)">
                    <h3>Boss Ômega</h3>
                    <div class="hp-text" id="enemyHp_2">HP: 10000</div>
                </div>
            </div>

            <div class="hand-container" id="playerHand"></div>

            <div class="status-box">
                <div class="shield-badge" id="shieldIndicator">ESCUDO ATIVO</div>
                <h3>Seu Status</h3>
                <div class="hp-text" id="playerHp" style="color: #00ffcc;">HP: 10000</div>
            </div>
        </div>

        <div style="text-align: center;">
            <button class="menu-btn" style="width: auto; padding: 5px 25px;" onclick="voltarAoMenu()">Fugir da Batalha</button>
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
        let playerHp = 10000;
        let rodada = 1;
        let escudoJogador = false;
        let jogoAcabou = false;
        let alvoSelecionado = 0; 

        let bosses = [
            { nome: "Boss Alfa", hp: 10000, vivo: true, id: 0 },
            { nome: "Boss Beta", hp: 10000, vivo: true, id: 1 },
            { nome: "Boss Ômega", hp: 10000, vivo: true, id: 2 }
        ];

        const deckDeCartas = [
            { nome: "Pulso de Fóton",    valor: 800,  efeito: "ataque",   desc: "Disparo rápido padrão",       cor: "#00ffcc", tag: "FÓTON" },
            { nome: "Raio Gamma",       valor: 1400, efeito: "ataque",   desc: "Forte feixe radioativo",     cor: "#ff0055", tag: "CÓSMICO" },
            { nome: "Buraco Negro",     valor: 2400, efeito: "critico",  desc: "50% de chance de CRÍTICO",   cor: "#9900ff", tag: "SINGULARIDADE" },
            { nome: "Plasma Estelar",    valor: 700,  efeito: "ataque",   desc: "Calor constante básico",       cor: "#ffcc00", tag: "ESTRELA" },
            { nome: "Dreno Sombrio",    valor: 900,  efeito: "vampiro",  desc: "Causa dano e te cura um pouco",cor: "#ff00aa", tag: "ANTIMATÉRIA" },
            { nome: "Matriz de Éter",   valor: 1500, efeito: "cura",     desc: "Restaura sua integridade molecular",cor: "#00ff55", tag: "REGEN" },
            { nome: "Dobra Espacial",    valor: 0,    efeito: "escudo",   desc: "Bloqueia o próximo golpe recebido", cor: "#0077ff", tag: "DEFESA" },
            { nome: "Aniquilação Total",valor: 800,  efeito: "area",     desc: "Dano balanceado em todos os chefes", cor: "#ff5500", tag: "ÁREA" },
            { nome: "Choque Quântico",  valor: 1100, efeito: "ataque",   desc: "Sobrecarga elétrica estática", cor: "#00ffff", tag: "ELÉTRICO" },
            { nome: "Cometa Binário",   valor: 1800, efeito: "ataque",   desc: "Impacto duplo concentrado",    cor: "#e100ff", tag: "METEORO" },
            { nome: "Inversor Carga",   valor: 500,  efeito: "hibrido",  desc: "Cura você 500 e bate 500 no alvo", cor: "#ffa500", tag: "MUTAÇÃO" },
            { nome: "Estática Quântica",valor: 600,  efeito: "ataque",   desc: "Dano elétrico leve contínuo", cor: "#bfff00", tag: "PARTÍCULA" },
            { nome: "Escudo Espelhos",  valor: 300,  efeito: "refletor", desc: "Ganha escudo e reflete 300 de dano", cor: "#ff007f", tag: "REVERSO" },
            { nome: "Sonda Espiã",      valor: 400,  efeito: "ataque",   desc: "Ataque fraco de monitoramento", cor: "#a9a9a9", tag: "SONDA" }
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

        function selecionarAlvo(index) {
            if (jogoAcabou || !bosses[index].vivo) return;
            
            alvoSelecionado = index;
            for (let i = 0; i < 3; i++) {
                let box = document.getElementById(`bossBox_${i}`);
                if (bosses[i].vivo) {
                    box.className = (i === index) ? "status-box selectable-enemy enemy-target" : "status-box selectable-enemy";
                }
            }
        }

        function iniciarJogoAtivo() {
            playerHp = 10000;
            rodada = 1;
            escudoJogador = false;
            jogoAcabou = false;
            alvoSelecionado = 0;

            bosses.forEach(b => {
                b.hp = 10000;
                b.vivo = true;
                document.getElementById(`enemyHp_${b.id}`).innerText = "HP: " + b.hp;
                document.getElementById(`bossBox_${b.id}`).className = "status-box selectable-enemy";
            });
            
            selecionarAlvo(0);

            document.getElementById('playerHp').innerText = "HP: " + playerHp;
            document.getElementById('turnIndicator').innerText = "Rodada: " + rodada;
            document.getElementById('battleStatus').innerText = "Arena Royale Pronta! 60% de chance dos bosses brigarem entre si.";
            document.getElementById('shieldIndicator').style.display = "none";
            
            maoAtual = [];
            for(let i = 0; i < 4; i++) {
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
                if(carta.efeito === "ataque" || carta.efeito === "critico" || carta.efeito === "vampiro" || carta.efeito === "area" || carta.efeito === "hibrido") exibicaoValor = "±" + carta.valor;
                if(carta.efeito === "escudo" || carta.efeito === "refletor") exibicaoValor = "DEF";

                elementoCarta.innerHTML = `
                    <div class="card-header">${carta.nome}</div>
                    <div class="card-value" style="color: ${carta.cor};">${exibicaoValor}</div>
                    <div class="card-effect-desc">${carta.desc}</div>
                    <div class="card-footer" style="background:${carta.cor}22; color:${carta.cor}">${carta.tag}</div>
                `;

                containerMao.appendChild(elementoCarta);
            });
        }

        function processarTurnoCompleto(indiceCarta) {
            if(jogoAcabou) return;

            const cartaJogada = maoAtual[indiceCarta];
            let alvo = bosses[alvoSelecionado];
            let msgLog = "";

            // 1. TURNO DO JOGADOR
            if (cartaJogada.efeito === "ataque") {
                alvo.hp -= cartaJogada.valor;
                msgLog = `Você atacou [${alvo.nome}] com ${cartaJogada.nome} (-${cartaJogada.valor} HP).\n`;
                aplicarFlashVisual('flash-damage');
            } 
            else if (cartaJogada.efeito === "critico") {
                if (Math.random() >= 0.5) {
                    let superDano = cartaJogada.valor * 2;
                    alvo.hp -= superDano;
                    msgLog = `💥 CRÍTICO! ${cartaJogada.nome} causou ${superDano} de dano no [${alvo.nome}].\n`;
                    aplicarFlashVisual('flash-damage');
                } else {
                    msgLog = `❌ Falha! [${cartaJogada.nome}] errou o golpe no [${alvo.nome}].\n`;
                }
            }
            else if (cartaJogada.efeito === "vampiro") {
                alvo.hp -= cartaJogada.valor;
                playerHp += Math.floor(cartaJogada.valor * 0.5);
                if (playerHp > 10000) playerHp = 10000;
                msgLog = `🩸 Dreno! Causa ${cartaJogada.valor} no [${alvo.nome}] e recuperou vida.\n`;
                aplicarFlashVisual('flash-heal');
            }
            else if (cartaJogada.efeito === "cura") {
                playerHp += cartaJogada.valor;
                if(playerHp > 10000) playerHp = 10000;
                msgLog = `💚 Auto-Reparo! Você recuperou +${cartaJogada.valor} HP.\n`;
                aplicarFlashVisual('flash-heal');
            } 
            else if (cartaJogada.efeito === "escudo" || cartaJogada.efeito === "refletor") {
                escudoJogador = true;
                document.getElementById('shieldIndicator').style.display = "block";
                msgLog = `🛡️ Barreira ativada! Você bloqueou a próxima investida direta.\n`;
                aplicarFlashVisual('flash-shield');
            }
            else if (cartaJogada.efeito === "area") {
                bosses.forEach(b => { if (b.vivo) b.hp -= cartaJogada.valor; });
                msgLog = `🌌 Área! Todos os chefes ativos sofreram -${cartaJogada.valor} HP.\n`;
                aplicarFlashVisual('flash-damage');
            }
            else if (cartaJogada.efeito === "hibrido") {
                alvo.hp -= cartaJogada.valor;
                playerHp += cartaJogada.valor;
                if (playerHp > 10000) playerHp = 10000;
                msgLog = `⚡ Carga Invertida! +${cartaJogada.valor} HP para você e -${cartaJogada.valor} HP no [${alvo.nome}].\n`;
                aplicarFlashVisual('flash-heal');
            }

            atualizarVidaDosBosses();

            let vivos = bosses.filter(b => b.vivo);
            if (vivos.length === 0) {
                document.getElementById('battleStatus').innerText = msgLog + "🏆 VITÓRIA ROYALE! Você sobrou sozinho na arena cósmica!";
                finalizarPartida();
                return;
            }

            if (!alvo.vivo && vivos.length > 0) {
                selecionarAlvo(vivos[0].id);
            }

            // 2. IA REPROGRAMADA - FOCO ABSOLUTO NA GUERRA INTERNA (60% Briga entre eles / 40% Jogador)
            setTimeout(() => {
                if(jogoAcabou) return;

                let logAtaquesBoss = "";
                
                bosses.forEach(b => {
                    if (!b.vivo) return;

                    // Mecânica de Recuo/Cura Inteligente
                    if (b.hp < 2000 && Math.random() < 0.4) {
                        let autoCura = 1200;
                        b.hp += autoCura;
                        if (b.hp > 10000) b.hp = 10000;
                        logAtaquesBoss += `👾 [${b.nome}] recuou para se regenerar (+${autoCura} HP).\n`;
                    } else {
                        // Varre outros bosses vivos para briga interna
                        let outrosBossesVivos = bosses.filter(outro => outro.vivo && outro.id !== b.id);
                        
                        // Decisão da IA: rola um dado (0 a 1). Se for menor que 0.60 e houver outros chefes, eles se atacam!
                        let atacarOutroBoss = (Math.random() < 0.60) && (outrosBossesVivos.length > 0);
                        let danoSorteado = Math.floor(Math.random() * (1200 - 500 + 1)) + 500;

                        if (!atacarOutroBoss) {
                            // Ataca o Jogador (40% de chance)
                            if (escudoJogador) {
                                logAtaquesBoss += `🛡️ [${b.nome}] tentou te alvejar, mas seu Escudo ABSORVEU o golpe.\n`;
                                if(cartaJogada.efeito === "refletor") {
                                    b.hp -= 300;
                                    logAtaquesBoss += `✨ Contra-ataque! Seu escudo refletiu 300 de dano em [${b.nome}].\n`;
                                }
                                escudoJogador = false;
                                document.getElementById('shieldIndicator').style.display = "none";
                            } else {
                                playerHp -= danoSorteado;
                                logAtaquesBoss += `💥 [${b.nome}] focou em VOCÊ e causou -${danoSorteado} HP.\n`;
                                aplicarFlashVisual('flash-boss-damage');
                            }
                        } else {
                            // Briga Interna Dinâmica (60% de chance)
                            let bossAlvoAleatorio = outrosBossesVivos[Math.floor(Math.random() * outrosBossesVivos.length)];
                            bossAlvoAleatorio.hp -= danoSorteado;
                            logAtaquesBoss += `⚔️ GUERRA! [${b.nome}] meteu um golpe crítico em [${bossAlvoAleatorio.nome}] (-${danoSorteado} HP)!\n`;
                        }
                    }
                });

                if (playerHp < 0) playerHp = 0;
                document.getElementById('playerHp').innerText = "HP: " + playerHp;
                atualizarVidaDosBosses();

                document.getElementById('battleStatus').innerText = msgLog + logAtaquesBoss;

                if (playerHp === 0) {
                    document.getElementById('battleStatus').innerText = "💀 DERROTA! Você virou poeira cósmica enquanto eles se enfrentavam.";
                    finalizarPartida();
                    return;
                }

                let sobreviventesPosBriga = bosses.filter(b => b.vivo);
                if (sobreviventesPosBriga.length === 0) {
                    document.getElementById('battleStatus').innerText = "🏆 VITÓRIA POR WO! Os bosses se aniquilaram mutuamente na explosão final!";
                    finalizarPartida();
                    return;
                }

                rodada++;
                document.getElementById('turnIndicator').innerText = "Rodada: " + rodada;

                // Compra nova carta para repor a mão
                maoAtual[indiceCarta] = sortearNovaCarta();
                renderizarMaoDoJogador();

            }, 950);
        }

        function atualizarVidaDosBosses() {
            bosses.forEach(b => {
                if(b.hp < 0) b.hp = 0;
                
                let elementHp = document.getElementById(`enemyHp_${b.id}`);
                let elementBox = document.getElementById(`bossBox_${b.id}`);
                
                elementHp.innerText = "HP: " + b.hp;
                
                if(b.hp === 0 && b.vivo) {
                    b.vivo = false;
                    elementBox.className = "status-box dead-enemy";
                }
            });
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
