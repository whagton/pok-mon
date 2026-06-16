<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Batalha Pokémon</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>

    <div id="start-screen" class="screen">
        <h1>Bem-vindo ao Duelo Pokémon</h1>
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
        <div id="battle-dialog-box"></div>
        <div id="moves-container"></div>
    </div>

    <script src="script.js"></script>
</body>
</html>
