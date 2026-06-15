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
            position: relative;
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
        .shield-effect { border-color: #
