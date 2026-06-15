<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pokémon Battle Simulator - Gengar vs Mewtwo</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: #0d0d1a;
            color: #fff;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
        }

        .battle-container {
            width: 100%;
            max-width: 700px;
            height: 500px;
            background: linear-gradient(180deg, #1a1a3a 0%, #0b0b16 100%);
            border: 4px solid #333366;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.7);
            display: flex;
            flex-direction: column;
            position: relative;
        }

        /* 1. TELA DE EXIBIÇÃO DOS POKÉMONS */
        .screen-area {
            flex: 1;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            padding: 20px;
            position: relative;
        }

        /* Status Hud */
        .status-hud {
            background: rgba(0, 0, 0, 0.6);
            border: 2px solid #444488;
            border-radius: 10px;
            padding: 10px;
            width: 240px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3);
        }
        .status-hud.enemy { align-self: flex-end; }
        .status-hud.player { align-self: flex-start; }
        
        .poke-info { display: flex; justify-content: space-between; font-weight: bold; font-size: 0.9rem; margin-bottom: 5px; }
        .level { color: #ffcc00; }

        /* Barra de HP */
        .hp-bar-bg { width: 100%; height: 12px; background: #333; border-radius: 6px; overflow: hidden; border: 1px solid #555; }
        .hp-bar-fill { width: 100%; height: 100%; background: #00ff66; transition: width 0.4s ease; }
        .hp-text { font-size: 0.75rem; text-align: right; margin-top: 3px; color: #aaa; }

        /* Substitutos visuais para os Sprites */
        .sprite-placeholder {
            font-size: 4rem;
            position: absolute;
            animation: float 3s ease-in-out infinite;
        }
        .enemy-sprite { top: 30px; left: 80px; }
        .player-sprite { bottom: 130px; right: 80px; animation-delay: 1.5s; }

        @keyframes float {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        /* Escudo ativo */
        .shield-effect { border-color: #00bfff !important; box-shadow: 0 0 15px #00bfff !important; }

        /* 2. LOG DE TEXTO (NARRADOR) */
        .battle-log {
            height: 70px;
            background: #111;
            border-top: 3px solid #333366;
            padding: 12px 20px;
            font-size: 0.95rem;
            color: #00ffcc;
            display: flex;
            align-items: center;
            line-height: 1.4;
        }

        /* 3. PAINEL DE COMANDOS (BOTÕES) */
        .control-panel {
            height: 120px;
            background: #222;
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
            padding: 12px;
            border-top: 2px solid #444;
        }

        .move-btn {
            background: #2d2d54;
            border: 2px solid #444488;
            color: white;
            border-radius: 8px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }
        .move-btn:hover:not(:disabled) { background: #3b3b73; border-color: #00ffcc; transform: scale(1.02); }
        .move-btn:disabled { background: #1a1a26; color: #555; border-color: #333; cursor: not-allowed; }
        .move-btn span { font-size: 0.7rem; color: #aaa; font-weight: normal; margin-top: 2px; }

        /* Classes de cores de golpes */
        .type-ghost { border-left: 6px solid #663399; }
        .type-dark { border-left: 6px solid #4a3c31; }
        .type-status { border-left: 6px solid #5d6d7e; }
    </style>
</head>
<body>

    <div class="battle-container">
        
        <div class="screen-area">
            <div class="status-hud enemy" id="enemyHud">
                <div class="poke-info">
                    <span>Mewtwo</span>
                    <span class="level">Lv.100</span>
                </div>
                <div class="hp-bar-bg">
                    <div class="hp-bar-fill" id="enemyHpBar"></div>
                </div>
                <div class="hp-text" id="enemyHpText">1000 / 1000</div>
            </div>

            <div class="sprite-placeholder enemy-sprite">🔮</div>
            <div class="sprite-placeholder player-sprite">😈</div>

            <div class="status-hud player" id="playerHud">
                <div class="poke-info">
                    <span>Gengar</span>
                    <span class="level">Lv.100</span>
                </div>
                <div class="hp-bar-bg">
                    <div class="hp-bar-fill" id="playerHpBar"></div>
                </div>
                <div class="hp-text" id="playerHpText">1000 / 1000</div>
            </div>
        </div>

        <div class="battle-log" id="logBox">
            O Mewtwo selvagem quer batalhar! O que o Gengar vai fazer?
        </div>

        <div class="control-panel" id="actionPanel">
            <button class="move-btn type-ghost" onclick="jogarTurno('shadow_ball')">
                Shadow Ball
                <span>Dano: 150 | PP: ∞</span>
            </button>
            <button class="move-btn type-dark" onclick="jogarTurno('dark_pulse')">
                Dark Pulse
                <span>Crítico (35%) | PP: ∞</span>
            </button>
            <button class="move-btn type-status" onclick="jogarTurno('hypnosis')">
                Hypnosis (Dreno)
                <span>Dano: 80 + Cura | PP: ∞</span>
            </button>
            <button class="move-btn type-status" onclick="jogarTurno('substitute')">
                Substitute (Escudo)
                <span>Bloqueia 1 Golpe | PP: ∞</span>
            </button>
        </div>
    </div>

    <script>
        // Status Iniciais
        let player = { nome: "Gengar", hp: 1000, maxHp: 1000, escudo: false };
        let enemy = { nome: "Mewtwo", hp: 1000, maxHp: 1000, escudo: false };
        let batalhaAtiva = true;

        function jogarTurno(ataqueEscolhido) {
            if (!batalhaAtiva) return;

            // Bloqueia botões temporariamente para a animação do turno funcionar
            travarBotoes(true);

            // 1. TURNO DO JOGADOR
            let danoJogador = 0;
            let logMsg = "";

            if (ataqueEscolhido === 'shadow_ball') {
                danoJogador = 150;
                logMsg = `Gengar usou Shadow Ball! Causou ${danoJogador} de dano.`;
            } 
            else if (ataqueEscolhido === 'dark_pulse') {
                if (Math.random() < 0.35) {
                    danoJogador = 260;
                    logMsg = `💥 Golpe Crítico! Gengar usou Dark Pulse e causou extraordinários ${danoJogador} de dano!`;
                } else {
                    danoJogador = 120;
                    logMsg = `Gengar usou Dark Pulse! Causou ${danoJogador} de dano.`;
                }
            } 
            else if (ataqueEscolhido === 'hypnosis') {
                danoJogador = 80;
                let cura = 120;
                player.hp = Math.min(player.maxHp, player.hp + cura);
                logMsg = `👁️ Gengar usou Hypnosis! Causou ${danoJogador} de dano e drenou ${cura} de HP!`;
            } 
            else if (ataqueEscolhido === 'substitute') {
                player.escudo = true;
                document.getElementById('playerHud').classList.add('shield-effect');
                logMsg = `🛡️ Gengar criou um Substitute! Ele está protegido contra o próximo golpe direto.`;
            }

            // Aplica dano no Mewtwo
            if (danoJogador > 0) {
                enemy.hp = Math.max(0, enemy.hp - danoJogador);
            }

            atualizarBarras();
            document.getElementById('logBox').innerText = logMsg;

            // Checa vitória antes do Mewtwo revidar
            if (enemy.hp === 0) {
                setTimeout(() => {
                    document.getElementById('logBox').innerText = `🏆 Mewtwo desmaiou! Gengar venceu a batalha Pokémon!`;
                    batalhaAtiva = false;
                }, 1500);
                return;
            }

            // 2. TURNO DO MEWTWO (IA)
            setTimeout(() => {
                if (!batalhaAtiva) return;

                // Pool de movimentos do Mewtwo
                const golpesMewtwo = [
                    { nome: "Psystrike", dano: 160 },
                    { nome: "Aura Sphere", dano: 130 },
                    { nome: "Psychic", dano: 140 }
                ];

                let golpeSorteado = golpesMewtwo[Math.floor(Math.random() * golpesMewtwo.length)];
                let msgMewtwo = "";

                if (player.escudo) {
                    msgMewtwo = `Mewtwo tentou usar ${golpeSorteado.nome}, mas o Substitute do Gengar absorveu o impacto completamente!`;
                    player.escudo = false;
                    document.getElementById('playerHud').classList.remove('shield-effect');
                } else {
                    player.hp = Math.max(0, player.hp - golpeSorteado.dano);
                    msgMewtwo = `Mewtwo revidou com ${golpeSorteado.nome}! Causou ${golpeSorteado.dano} de dano em Gengar.`;
                }

                atualizarBarras();
                document.getElementById('logBox').innerText = msgMewtwo;

                // Checa derrota
                if (player.hp === 0) {
                    setTimeout(() => {
                        document.getElementById('logBox').innerText = `💀 Gengar desmaiou... Você perdeu a batalha.`;
                        batalhaAtiva = false;
                    }, 1500);
                } else {
                    // Libera os botões para o próximo turno do jogador
                    setTimeout(() => {
                        if(batalhaAtiva) {
                            document.getElementById('logBox').innerText = "O que o Gengar vai fazer?";
                            travarBotoes(false);
                        }
                    }, 1500);
                }

            }, 1800);
        }

        function atualizarBarras() {
            // Atualiza HP do Jogador
            let pctPlayer = (player.hp / player.maxHp) * 100;
            const barPlayer = document.getElementById('playerHpBar');
            barPlayer.style.width = pctPlayer + "%";
            document.getElementById('playerHpText').innerText = `${player.hp} / ${player.maxHp}`;
            
            // Troca cor da barra com base no HP restante
            barPlayer.style.backgroundColor = pctPlayer < 20 ? "#ff3333" : pctPlayer < 50 ? "#ffcc00" : "#00ff66";

            // Atualiza HP do Inimigo
            let pctEnemy = (enemy.hp / enemy.maxHp) * 100;
            const barEnemy = document.getElementById('enemyHpBar');
            barEnemy.style.width = pctEnemy + "%";
            document.getElementById('enemyHpText').innerText = `${enemy.hp} / ${enemy.maxHp}`;
            
            barEnemy.style.backgroundColor = pctEnemy < 20 ? "#ff3333" : pctEnemy < 50 ? "#ffcc00" : "#00ff66";
        }

        function travarBotoes(status) {
            const botoes = document.querySelectorAll('.move-btn');
            botoes.forEach(btn => btn.disabled = status);
        }
    </script>
</body>
</html>
