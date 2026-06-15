<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pokémon Battle Simulator - Pikachu vs Mewtwo</title>
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
            position: relative;
            transition: background 0.15s ease;
        }

        /* TELA INICIAL (SPLASH) */
        .splash-container {
            position: fixed;
            top: 0; left: 0; width: 100vw; height: 100vh;
            background: radial-gradient(circle at center, #151530 0%, #050510 100%);
            z-index: 1000;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            transition: opacity 0.5s ease, transform 0.5s ease;
        }

        .splash-title {
            font-size: 2.2rem;
            color: #f1c40f;
            letter-spacing: 4px;
            margin-bottom: 40px;
            text-shadow: 0 0 15px rgba(241, 196, 15, 0.5);
            text-align: center;
            font-weight: 800;
        }

        .pokeball-wrapper {
            position: relative;
            width: 200px; height: 200px;
            cursor: pointer;
            display: flex; align-items: center; justify-content: center;
        }

        .pokeball-part {
            position: absolute; width: 100%; height: 50%; left: 0;
            border: 6px solid #111;
            transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }
        
        .pokeball-top {
            top: 0; background: #ff3355; border-radius: 100px 100px 0 0;
            border-bottom: 3px solid #111; transform-origin: bottom;
        }

        .pokeball-bottom {
            bottom: 0; background: #f0f0f0; border-radius: 0 0 100px 100px;
            border-top: 3px solid #111; transform-origin: top;
        }

        .pokeball-center-btn {
            position: absolute; width: 50px; height: 50px; background: #fff;
            border: 6px solid #111; border-radius: 50%; z-index: 10;
            transition: all 0.4s ease;
        }

        .pokeball-wrapper:hover .pokeball-top, .pokeball-wrapper.open .pokeball-top { transform: translateY(-45px); }
        .pokeball-wrapper:hover .pokeball-bottom, .pokeball-wrapper.open .pokeball-bottom { transform: translateY(45px); }
        .pokeball-wrapper:hover .pokeball-center-btn, .pokeball-wrapper.open .pokeball-center-btn { transform: scale(0.6); opacity: 0; }

        .start-btn {
            position: absolute; padding: 12px 24px;
            background: linear-gradient(135deg, #f1c40f 0%, #f39c12 100%);
            border: none; color: #050510; font-size: 1.1rem; font-weight: bold;
            border-radius: 25px; letter-spacing: 1px; cursor: pointer;
            box-shadow: 0 0 20px rgba(241, 196, 15, 0.6);
            opacity: 0; transform: scale(0.8); transition: all 0.3s ease;
            z-index: 5; pointer-events: none; white-space: nowrap;
        }

        .pokeball-wrapper:hover .start-btn, .pokeball-wrapper.open .start-btn { opacity: 1; transform: scale(1.05); pointer-events: auto; }

        /* CONTAINER DA BATALHA */
        .battle-container {
            width: 100%; max-width: 700px; height: 520px;
            background: linear-gradient(180deg, #14142f 0%, #07070e 100%);
            border: 4px solid #333366; border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.7);
            display: flex; flex-direction: column; position: relative;
            opacity: 0; transition: opacity 0.6s ease;
        }

        /* EFEITOS DE FLASH */
        .flash-yellow { background: #5a4b00 !important; }
        .flash-white { background: #333333 !important; }
        .flash-shadow { background: #3a005c !important; }
        .flash-damage { background: #520000 !important; }

        @keyframes shake {
            0%, 100% { transform: translate(0, 0); }
            10%, 30%, 50%, 70%, 90% { transform: translate(-8px, 4px); }
            20%, 40%, 60%, 80% { transform: translate(8px, -4px); }
        }
        .shake-effect { animation: shake 0.35s ease-in-out; }

        /* ARENA */
        .screen-area {
            flex: 1; display: flex; flex-direction: column; justify-content: space-between;
            padding: 20px; position: relative;
            background: radial-gradient(circle at bottom, rgba(241, 196, 15, 0.15) 0%, transparent 70%);
        }

        .status-hud {
            background: rgba(0, 0, 0, 0.7); border: 2px solid #444488;
            border-radius: 10px; padding: 10px; width: 240px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3); z-index: 5; transition: all 0.3s;
        }
        .status-hud.enemy { align-self: flex-start; margin-left: 20px; }
        .status-hud.player { align-self: flex-end; margin-right: 20px; }
        
        .poke-info { display: flex; justify-content: space-between; font-weight: bold; font-size: 0.9rem; margin-bottom: 5px; }
        .level { color: #ffcc00; }

        .hp-bar-bg { width: 100%; height: 12px; background: #333; border-radius: 6px; overflow: hidden; border: 1px solid #555; }
        .hp-bar-fill { width: 100%; height: 100%; background: #00ff66; transition: width 0.4s ease; }
        .hp-text { font-size: 0.75rem; text-align: right; margin-top: 3px; color: #aaa; }

        /* SPRITES */
        .pokemon-sprite {
            position: absolute; height: 130px; object-fit: contain;
            transition: transform 0.2s ease, filter 0.2s ease;
        }
        
        .enemy-sprite { top: 15px; right: 80px; animation: floatEnemy 3.5s ease-in-out infinite; }
        .player-sprite { bottom: 120px; left: 80px; animation: floatPlayer 3s ease-in-out infinite; height: 110px; }

        @keyframes floatEnemy { 0%, 100% { transform: translateY(0) scale(1); } 50% { transform: translateY(-8px) scale(1.02); } }
        @keyframes floatPlayer { 0%, 100% { transform: translateY(0) scale(1); } 50% { transform: translateY(-5px) scale(1.03); } }

        .attack-dash-left { transform: translate(-50px, 30px) scale(1.1); }
        .attack-dash-right { transform: translate(50px, -30px) scale(1.1); }
        .damage-blink { filter: matrix(1, 0, 0, 1, 0, 0) brightness(5) sepia(1) hue-rotate(-50deg) !important; }

        .shield-effect { border-color: #f1c40f !important; box-shadow: 0 0 15px #f1c40f !important; }

        /* LOG DE TEXTO */
        .battle-log {
            height: 75px; background: #0f0f1d; border-top: 3px solid #2d2d54;
            padding: 12px 20px; font-size: 0.95rem; color: #fff;
            display: flex; align-items: center; line-height: 1.4;
        }

        /* PAINEL DE CONTROLE */
        .control-panel {
            height: 120px; background: #181826; display: grid;
            grid-template-columns: repeat(2, 1fr); gap: 10px;
            padding: 12px; border-top: 2px solid #2d2d54;
        }

        .move-btn {
            background: #252547; border: 2px solid #3b3b73; color: white;
            border-radius: 8px; font-size: 1rem; font-weight: bold;
            cursor: pointer; transition: all 0.2s; display: flex;
            flex-direction: column; justify-content: center; align-items: center;
            border-left-width: 6px;
        }
        .move-btn:hover:not(:disabled) { background: #353566; filter: brightness(1.2); transform: scale(1.02); }
        .move-btn:disabled { background: #12121f; color: #444; border-color: #222 !important; cursor: not-allowed; }
        .move-btn span { font-size: 0.7rem; color: #aaa; font-weight: normal; margin-top: 2px; }

        /* Botão Jogar Novamente */
        .btn-restart {
            grid-column: span 2;
            background: linear-gradient(90deg, #00ffcc, #00bfff);
            border: none; color: #000; font-size: 1.2rem; font-weight: bold;
            border-radius: 8px; cursor: pointer; text-transform: uppercase;
            box-shadow: 0 0 15px rgba(0, 255, 204, 0.4);
            transition: transform 0.2s;
        }
        .btn-restart:hover { transform: scale(1.02); box-shadow: 0 0 25px rgba(0, 255, 204, 0.8); }

        .dev-signature {
            position: fixed; bottom: 12px; right: 15px; font-size: 0.75rem;
            font-family: monospace; color: rgba(241, 196, 15, 0.6);
            letter-spacing: 1.5px; text-shadow: 0 0 8px rgba(241, 196, 15, 0.3);
            user-select: none; z-index: 999;
        }
    </style>
</head>
<body>

    <div class="splash-screen" id="splashScreen">
        <div class="splash-container" id="splashContainer">
            <h1 class="splash-title">POKÉMON ARENA</h1>
            <div class="pokeball-wrapper" id="pokeball" onclick="togglePokeballMobile()">
                <div class="pokeball-part pokeball-top"></div>
                <div class="pokeball-center-btn"></div>
                <button class="start-btn" onclick="iniciarJogo(event)">INICIAR BATALHA</button>
                <div class="pokeball-part pokeball-bottom"></div>
            </div>
        </div>
    </div>

    <div class="battle-container" id="gameContainer">
        <div class="screen-area" id="arenaArea">
            <div class="status-hud enemy" id="enemyHud">
                <div class="poke-info"><span>Mewtwo</span><span class="level">Lv.100</span></div>
                <div class="hp-bar-bg"><div class="hp-bar-fill" id="enemyHpBar"></div></div>
                <div class="hp-text" id="enemyHpText">1000 / 1000</div>
            </div>

            <img src="https://img.pokemondb.net/sprites/black-white/anim/normal/mewtwo.gif" class="pokemon-sprite enemy-sprite" id="mewtwoSprite">
            <img src="https://img.pokemondb.net/sprites/black-white/anim/back-normal/pikachu.gif" class="pokemon-sprite player-sprite" id="pikachuSprite">

            <div class="status-hud player" id="playerHud">
                <div class="poke-info"><span>Pikachu</span><span class="level">Lv.100</span></div>
                <div class="hp-bar-bg"><div class="hp-bar-fill" id="playerHpBar"></div></div>
                <div class="hp-text" id="playerHpText">800 / 800</div>
            </div>
        </div>

        <div class="battle-log" id="logBox">
            O Mewtwo lendário apareceu! Vai, Pikachu!
        </div>

        <div class="control-panel" id="actionPanel">
            <!-- Gerado dinamicamente -->
        </div>
    </div>

    <div class="dev-signature">BY: DEV WHAGTON</div>

    <script>
        // ENGENHARIA DE SOM (Web Audio API)
        let audioCtx = null;

        function initAudio() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
        }

        function somAtaquePikachu() {
            initAudio();
            let osc = audioCtx.createOscillator();
            let gain = audioCtx.createGain();
            osc.type = 'sawtooth';
            osc.frequency.setValueAtTime(300, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(1200, audioCtx.currentTime + 0.15);
            gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
            osc.connect(gain); gain.connect(audioCtx.destination);
            osc.start(); osc.stop(audioCtx.currentTime + 0.15);
        }

        function somAtaqueMewtwo() {
            initAudio();
            let osc = audioCtx.createOscillator();
            let gain = audioCtx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(150, audioCtx.currentTime);
            osc.frequency.linearRampToValueAtTime(600, audioCtx.currentTime + 0.3);
            gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.3);
            osc.connect(gain); gain.connect(audioCtx.destination);
            osc.start(); osc.stop(audioCtx.currentTime + 0.3);
        }

        function somDanoMewtwo() {
            initAudio();
            let osc = audioCtx.createOscillator();
            let gain = audioCtx.createGain();
            osc.type = 'triangle';
            osc.frequency.setValueAtTime(120, audioCtx.currentTime);
            osc.frequency.linearRampToValueAtTime(40, audioCtx.currentTime + 0.2);
            gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.2);
            osc.connect(gain); gain.connect(audioCtx.destination);
            osc.start(); osc.stop(audioCtx.currentTime + 0.2);
        }

        function somDanoPikachu() {
            initAudio();
            let osc = audioCtx.createOscillator();
            let gain = audioCtx.createGain();
            osc.type = 'square';
            osc.frequency.setValueAtTime(220, audioCtx.currentTime);
            osc.frequency.setValueAtTime(180, audioCtx.currentTime + 0.08);
            gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.18);
            osc.connect(gain); gain.connect(audioCtx.destination);
            osc.start(); osc.stop(audioCtx.currentTime + 0.18);
        }

        function somEsquiva() {
            initAudio();
            let osc = audioCtx.createOscillator();
            let gain = audioCtx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(800, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(300, audioCtx.currentTime + 0.2);
            gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.2);
            osc.connect(gain); gain.connect(audioCtx.destination);
            osc.start(); osc.stop(audioCtx.currentTime + 0.2);
        }

        // VARIÁVEIS DE JOGO
        let player = { nome: "Pikachu", hp: 800, maxHp: 800, escudo: false };
        let enemy = { nome: "Mewtwo", hp: 1000, maxHp: 1000 };
        let batalhaAtActive = true;
        let ataquesNoPainel = [];

        const todosAtaquesPikachu = [
            { nome: "Thunderbolt", dano: 140, precisao: 1.0, desc: "Ataque Forte | Elétrico", cor: "#f1c40f" },
            { nome: "Quick Attack", dano: 60, precisao: 1.0, desc: "Ataque Fraco (Não erra) | Normal", cor: "#a8a878" },
            { nome: "Iron Tail", dano: 170, precisao: 0.75, desc: "Muito Forte (75% Chance) | Aço", cor: "#b8b8d0" },
            { nome: "Electro Ball", dano: 110, precisao: 1.0, desc: "Ataque Médio | Elétrico", cor: "#f1c40f" },
            { nome: "Volt Tackle", dano: 220, precisao: 1.0, recoil: 40, desc: "Extremo (Pikachu sofre recuo)", cor: "#e67e22" },
            { nome: "Double Team", dano: 0, precisao: 1.0, escudo: true, desc: "Esquiva do próximo golpe", cor: "#bdc3c7" },
            { nome: "Thunder", dano: 250, precisao: 0.6, desc: "Devastador (60% Chance) | Elétrico", cor: "#f1c40f" },
            { nome: "Nuzzle", dano: 40, precisao: 1.0, cura: 80, desc: "Muito Fraco + Cura HP", cor: "#f39c12" }
        ];

        const pikachuImg = document.getElementById('pikachuSprite');
        const mewtwoImg = document.getElementById('mewtwoSprite');
        const gameContainer = document.getElementById('gameContainer');
        const actionPanel = document.getElementById('actionPanel');

        function togglePokeballMobile() { document.getElementById('pokeball').classList.toggle('open'); }

        function iniciarJogo(event) {
            event.stopPropagation();
            initAudio(); // Ativa os drivers de áudio no clique
            const splash = document.getElementById('splashContainer');
            splash.style.opacity = '0';
            splash.style.transform = 'scale(1.2)';
            setTimeout(() => {
                document.getElementById('splashScreen').style.display = 'none';
                gameContainer.style.opacity = '1';
                gerarBotoesAleatorios();
            }, 500);
        }

        function gerarBotoesAleatorios() {
            if (!batalhaAtActive) return;
            actionPanel.innerHTML = '';
            let emparalhado = [...todosAtaquesPikachu].sort(() => 0.5 - Math.random());
            ataquesNoPainel = emparalhado.slice(0, 4);

            ataquesNoPainel.forEach((golpe, index) => {
                let btn = document.createElement('button');
                btn.className = 'move-btn';
                btn.style.borderLeftColor = golpe.cor;
                btn.innerHTML = `${golpe.nome}<span>${golpe.desc}</span>`;
                btn.onclick = () => jogarTurno(index);
                actionPanel.appendChild(btn);
            });
        }

        function dispararFlash(classeFlash) {
            document.body.classList.add(classeFlash);
            setTimeout(() => document.body.classList.remove(classeFlash), 150);
        }

        function tremerTelaPisque(sprite, somEfeito) {
            gameContainer.classList.add('shake-effect');
            sprite.classList.add('damage-blink');
            dispararFlash('flash-damage');
            if(somEfeito) somEfeito();
            
            setTimeout(() => {
                gameContainer.classList.remove('shake-effect');
                sprite.classList.remove('damage-blink');
            }, 350);
        }

        function jogarTurno(indexAtaque) {
            if (!batalhaAtActive) return;
            
            const botoes = document.querySelectorAll('.move-btn');
            botoes.forEach(b => b.disabled = true);

            let golpe = ataquesNoPainel[indexAtaque];
            let logMsg = "";

            // Pikachu Atacando
            pikachuImg.classList.add('attack-dash-right');
            somAtaquePikachu();
            setTimeout(() => pikachuImg.classList.remove('attack-dash-right'), 250);

            if (Math.random() > golpe.precisao) {
                logMsg = `Pikachu usou ${golpe.nome}, mas errou o alvo!`;
            } else {
                if (golpe.escudo) {
                    player.escudo = true;
                    document.getElementById('playerHud').classList.add('shield-effect');
                    logMsg = `Pikachu usou Double Team! Sua evasão aumentou absurdamente.`;
                    dispararFlash('flash-white');
                } else {
                    let danoFinal = golpe.dano;
                    
                    if (danoFinal > 0 && Math.random() < 0.25) {
                        danoFinal = Math.floor(danoFinal * 1.5);
                        logMsg = `Pikachu usou ${golpe.nome}! Foi um golpe extraordinariamente forte! (-${danoFinal} HP)`;
                    } else {
                        logMsg = `Pikachu usou ${golpe.nome}! (-${danoFinal} HP)`;
                    }

                    enemy.hp = Math.max(0, enemy.hp - danoFinal);
                    
                    // Som do Mewtwo levando dano
                    setTimeout(() => tremerTelaPisque(mewtwoImg, somDanoMewtwo), 200);

                    if (golpe.cura) {
                        player.hp = Math.min(player.maxHp, player.hp + golpe.cura);
                        logMsg += ` E curou sua energia!`;
                    }
                    if (golpe.recoil) {
                        player.hp = Math.max(0, player.hp - golpe.recoil);
                        logMsg += ` Mas o Pikachu sofreu dano de recuo (-${golpe.recoil} HP)!`;
                        setTimeout(() => tremerTelaPisque(pikachuImg, somDanoPikachu), 400);
                    }
                    
                    dispararFlash(golpe.cor === '#a8a878' ? 'flash-white' : 'flash-yellow');
                }
            }

            atualizarBarras();
            document.getElementById('logBox').innerText = logMsg;

            if (enemy.hp === 0) { finalizarJogo(true); return; }
            if (player.hp === 0) { finalizarJogo(false); return; }

            // Turno do Mewtwo
            setTimeout(() => {
                if (!batalhaAtActive) return;

                const golpesMewtwo = [
                    { nome: "Psystrike", dano: 150 },
                    { nome: "Aura Sphere", dano: 120 },
                    { nome: "Psychic", dano: 135 }
                ];
                let bossGolpe = golpesMewtwo[Math.floor(Math.random() * golpesMewtwo.length)];
                
                mewtwoImg.classList.add('attack-dash-left');
                somAtaqueMewtwo();
                setTimeout(() => mewtwoImg.classList.remove('attack-dash-left'), 250);

                setTimeout(() => {
                    dispararFlash('flash-shadow');
                    
                    if (player.escudo) {
                        document.getElementById('logBox').innerText = `Mewtwo usou ${bossGolpe.nome}, mas atingiu um clone! Pikachu esquivou!`;
                        somEsquiva();
                        player.escudo = false;
                        document.getElementById('playerHud').classList.remove('shield-effect');
                    } else {
                        player.hp = Math.max(0, player.hp - bossGolpe.dano);
                        document.getElementById('logBox').innerText = `Mewtwo contra-atacou com ${bossGolpe.nome} causando -${bossGolpe.dano} HP!`;
                        // Som do Pikachu levando dano
                        tremerTelaPisque(pikachuImg, somDanoPikachu);
                    }

                    atualizarBarras();

                    if (player.hp === 0) {
                        finalizarJogo(false);
                    } else {
                        setTimeout(() => {
                            if(batalhaAtActive) {
                                document.getElementById('logBox').innerText = "Os golpes mudaram! O que o Pikachu vai fazer?";
                                gerarBotoesAleatorios();
                            }
                        }, 1200);
                    }
                }, 200);

            }, 2000);
        }

        function atualizarBarras() {
            let pctPlayer = (player.hp / player.maxHp) * 100;
            const barPlayer = document.getElementById('playerHpBar');
            barPlayer.style.width = pctPlayer + "%";
            document.getElementById('playerHpText').innerText = `${player.hp} / ${player.maxHp}`;
            barPlayer.style.backgroundColor = pctPlayer < 20 ? "#ff3333" : pctPlayer < 50 ? "#ffcc00" : "#00ff66";

            let pctEnemy = (enemy.hp / enemy.maxHp) * 100;
            const barEnemy = document.getElementById('enemyHpBar');
            barEnemy.style.width = pctEnemy + "%";
            document.getElementById('enemyHpText').innerText = `${enemy.hp} / ${enemy.maxHp}`;
            barEnemy.style.backgroundColor = pctEnemy < 20 ? "#ff3333" : pctEnemy < 50 ? "#ffcc00" : "#00ff66";
        }

        function finalizarJogo(vitoria) {
            batalhaAtActive = false;
            setTimeout(() => {
                if (vitoria) {
                    document.getElementById('logBox').innerHTML = `<span style="color:#00ffcc; font-weight:bold; font-size: 0.95rem;">hee vc venceu parabens fique atento para mais atualizaçõe de WHAGTON.</span>`;
                } else {
                    document.getElementById('logBox').innerHTML = `<span style="color:#ff3333; font-weight:bold;">Pikachu esgotou suas energias... O Mewtwo venceu.</span>`;
                }
                actionPanel.innerHTML = `<button class="btn-restart" onclick="reiniciarJogo()">🔄 JOGAR NOVAMENTE</button>`;
            }, 1200);
        }

        function reiniciarJogo() {
            player.hp = player.maxHp;
            player.escudo = false;
            enemy.hp = enemy.maxHp;
            batalhaAtActive = true;
            document.getElementById('playerHud').classList.remove('shield-effect');
            
            atualizarBarras();
            document.getElementById('logBox').innerText = "O Mewtwo lendário bloqueia seu caminho! Vai, Pikachu!";
            gerarBotoesAleatorios();
        }
    </script>
</body>
</html>
