<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Monitor de Progresso</title>
    <link rel="manifest" href="manifest.json"> <!-- Manifest JSON já vinculado -->
    <style>
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(to bottom, #4CAF50, #81C784);
            color: white;
            text-align: center;
            margin: 0;
            padding: 0;
        }
        header {
            padding: 40px;
        }
        form, .resumo {
            background-color: white;
            color: #333;
            padding: 20px;
            border-radius: 10px;
            max-width: 400px;
            margin: 20px auto;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        form label {
            display: block;
            margin: 10px 0 5px;
            text-align: left;
        }
        form input, form button {
            width: 100%;
            padding: 10px;
            margin: 5px 0 15px;
            border: none;
            border-radius: 5px;
            font-size: 1em;
        }
        form button {
            background-color: #388E3C;
            color: white;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }
        form button:hover {
            background-color: #2E7D32;
        }
        #installButton {
            display: none;
            background-color: #FFA000;
            color: white;
            padding: 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 15px;
        }
    </style>
</head>
<body>
    <header>
        <h1>Monitor de Progresso</h1>
        <p>Cadastre suas corridas ou caminhadas e acompanhe os resultados acumulados!</p>
    </header>
    <main>
        <form id="formAtividade">
            <label for="distancia">Distância (km)</label>
            <input type="number" id="distancia" placeholder="Ex.: 5.0" step="0.1" required>
            
            <label for="tempo">Tempo (min)</label>
            <input type="number" id="tempo" placeholder="Ex.: 30" required>
            
            <label for="calorias">Calorias Queimadas</label>
            <input type="number" id="calorias" placeholder="Ex.: 250" required>
            
            <button type="submit">Cadastrar</button>
        </form>
        <button id="installButton">Instalar Aplicativo</button>
        <div class="resumo">
            <h2>Resumo Acumulado</h2>
            <p id="resumoDistancia">Distância Total: 0 km</p>
            <p id="resumoTempo">Tempo Total: 0 minutos</p>
            <p id="resumoCalorias">Calorias Totais: 0 kcal</p>
            <p id="resumoDias">Dias desde o último reset: 0</p>
        </div>
    </main>
    <script src="script.js"></script> <!-- Script já vinculado -->
</body>
</html>
