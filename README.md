<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Pokémon Battle Simulator</title>
    <style>
        body, html { margin: 0; padding: 0; height: 100%; background: #0d0d1a; color: white; font-family: sans-serif; display: flex; justify-content: center; align-items: center; overflow: hidden; }
        
        /* Estilos da Pokébola */
        #start-screen { display: flex; flex-direction: column; align-items: center; }
        .pokeball-container { width: 150px; height: 150px; cursor: pointer; position: relative; }
        .p-top, .p-bottom { width: 150px; height: 75px; background: red; border: 6px solid #000; position: absolute; transition: 0.8s; }
        .p-top { top: 0; border-radius: 75px 75px 0 0; z-index: 2; }
        .p-bottom { bottom: 0; background: white; border-radius: 0 0 75px 75px; border-top: none; }
        .open-top { transform: translateY(-60px); }
        .open-bottom { transform: translateY(60px); }
        #btn-iniciar { display: none; margin-top: 80px; padding: 10px 20px; cursor: pointer; background: #ffcc00; font-weight: bold; border-radius: 10px; border: none; }

        /* Telas */
        #select-screen, #game-ui { display: none; text-align: center; }
        .pokemon-options { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; }
        .poke-card { background: #222; padding: 20px; border-radius: 10px; cursor: pointer; border: 2px solid #444; }
        .poke-card:hover { border-color: #ffcc00; }
    </style>
</head>
<body>

    <div id="start-screen">
        <div class="pokeball-container" onclick="abrirPokebola()">
            <div class="p-top" id="top"></div>
            <div class="p-bottom" id="bottom"></div>
        </div>
        <button id="btn-iniciar" onclick="irParaSelecao()">INICIAR BATALHA</button>
    </div>

    <div id="select-screen">
        <h2>ESCOLHA SEU POKÉMON</h2>
        <div class="pokemon-options">
            <div class="poke-card" onclick="iniciarBatalha('Pikachu')">PIKACHU</div>
            <div class="poke-card" onclick="iniciarBatalha('Charizard')">CHARIZARD</div>
            <div class="poke-card" onclick="iniciarBatalha('Greninja')">GRENINJA</div>
        </div>
    </div>

    <div id="game-ui">
        <h1 id="status-batalha">Batalha Iniciada!</h1>
        <button onclick="location.reload()">REINICIAR JOGO</button>
    </div>

    <script>
        function abrirPokebola() {
            document.getElementById('top').classList.add('open-top');
            document.getElementById('bottom').classList.add('open-bottom');
            setTimeout(() => {
                document.getElementById('btn-iniciar').style.display = 'block';
            }, 500);
        }

        function irParaSelecao() {
            document.getElementById('start-screen').style.display = 'none';
            document.getElementById('select-screen').style.display = 'block';
        }

        function iniciarBatalha(nome) {
            document.getElementById('select-screen').style.display = 'none';
            document.getElementById('game-ui').style.display = 'block';
            document.getElementById('status-batalha').innerText = "Você escolheu " + nome + "! A batalha começou!";
        }
    </script>
</body>
</html>
