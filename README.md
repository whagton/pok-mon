<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Batalha Pokémon</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>

    <div id="start-screen" class="screen">
        <h1>Duelo Pokémon</h1>
        <div id="pokebola" onclick="abrirPokebola()">
            <div id="top" class="poke-top"></div>
            <div id="bottom" class="poke-bottom"></div>
        </div>
        <button id="btn-iniciar" onclick="irParaSelecao()" style="display:none;">Iniciar Aventura</button>
    </div>

    <div id="select-screen" class="screen" style="display:none;">
        <h2>Escolha seu Pokémon:</h2>
        <div id="options-container"></div>
    </div>

    <div id="game-ui" class="screen" style="display:none;">
        <div id="battle-field">
            <div class="side">
                <p id="enemy-name-txt"></p>
                <div class="hp-container"><div id="enemy-hp-bar"></div></div>
                <p id="enemy-hp-text"></p>
                <img id="enemy-sprite" src="" alt="Inimigo">
            </div>
            <div class="side">
                <p id="player-name-txt"></p>
                <div class="hp-container"><div id="player-hp-bar"></div></div>
                <p id="player-hp-text"></p>
                <img id="player-sprite" src="" alt="Jogador">
            </div>
        </div>
        <div id="battle-dialog-box">A batalha começou!</div>
        <div id="moves-container"></div>
    </div>

    <script src="script.js"></script>
</body>
</html>
body { font-family: sans-serif; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; }
.screen { text-align: center; }

/* Design da Pokebola */
#pokebola { width: 100px; height: 100px; cursor: pointer; margin: 20px auto; border: 4px solid black; border-radius: 50%; overflow: hidden; position: relative; }
.poke-top { height: 50%; background: red; border-bottom: 4px solid black; transition: transform 0.5s; }
.poke-bottom { height: 50%; background: white; transition: transform 0.5s; }
.open-top { transform: translateY(-100%); }
.open-bottom { transform: translateY(100%); }

/* UI de Batalha */
.hp-container { width: 200px; height: 20px; background: #ddd; border: 2px solid black; margin: 5px auto; }
#player-hp-bar, #enemy-hp-bar { height: 100%; background: lime; width: 100%; transition: width 0.5s; }
.poke-card { display: inline-block; cursor: pointer; border: 1px solid #333; margin: 10px; padding: 10px; }
const pokesData = {
    'PIKACHU': { id: 25, moves: ['Choque do Trovão', 'Investida'] },
    'CHARIZARD': { id: 6, moves: ['Lança-Chamas', 'Ataque de Asa'] },
    'GRENINJA': { id: 658, moves: ['Shuriken de Água', 'Corte Noturno'] },
    'LUCARIO': { id: 448, moves: ['Esfera de Aura', 'Velocidade Extrema'] }
};

let playerHP = 3000, enemyHP = 3000, maxHP = 3000, playerPokemon = "", enemyPokemon = "", turnoJogador = true;

function abrirPokebola() {
    document.getElementById('top').classList.add('open-top');
    document.getElementById('bottom').classList.add('open-bottom');
    setTimeout(() => { document.getElementById('btn-iniciar').style.display = 'inline-block'; }, 500);
}

function irParaSelecao() {
    document.getElementById('start-screen').style.display = 'none';
    document.getElementById('select-screen').style.display = 'block';
    const container = document.getElementById('options-container');
    container.innerHTML = "";
    for(let name in pokesData) {
        container.innerHTML += `<div class="poke-card" onclick="iniciarBatalha('${name}')">
            <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokesData[name].id}.png">
            <p>${name}</p></div>`;
    }
}

function iniciarBatalha(nome) {
    playerPokemon = nome;
    const pokes = Object.keys(pokesData);
    enemyPokemon = pokes[Math.floor(Math.random() * pokes.length)];
    
    document.getElementById('select-screen').style.display = 'none';
    document.getElementById('game-ui').style.display = 'block';
    
    document.getElementById('player-name-txt').innerText = playerPokemon;
    document.getElementById('enemy-name-txt').innerText = enemyPokemon;
    document.getElementById('player-sprite').src = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokesData[playerPokemon].id}.png`;
    document.getElementById('enemy-sprite').src = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokesData[enemyPokemon].id}.png`;
    
    atualizarBarras();
    gerarBotoes();
}

function atualizarBarras() {
    document.getElementById('player-hp-bar').style.width = (playerHP/maxHP)*100 + "%";
    document.getElementById('enemy-hp-bar').style.width = (enemyHP/maxHP)*100 + "%";
    document.getElementById('player-hp-text').innerText = `${playerHP}/${maxHP}`;
    document.getElementById('enemy-hp-text').innerText = `${enemyHP}/${maxHP}`;
}

function gerarBotoes() {
    const container = document.getElementById('moves-container');
    container.innerHTML = "";
    pokesData[playerPokemon].moves.forEach(m => {
        container.innerHTML += `<button onclick="atacar('${m}')">${m}</button>`;
    });
}

function atacar(nome) {
    if (!turnoJogador) return;
    const dano = Math.floor(Math.random() * 500) + 200;
    enemyHP = Math.max(0, enemyHP - dano);
    atualizarBarras();
    
    if (enemyHP <= 0) {
        alert("Vitória!");
        location.reload();
    } else {
        turnoJogador = false;
        setTimeout(ataqueInimigo, 1000);
    }
}

function ataqueInimigo() {
    const dano = Math.floor(Math.random() * 500) + 200;
    playerHP = Math.max(0, playerHP - dano);
    atualizarBarras();
    if (playerHP <= 0) {
        alert("Derrota!");
        location.reload();
    }
    turnoJogador = true;
}
