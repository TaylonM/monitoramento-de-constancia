<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Monitor de Progresso</title>
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
            
            <button type="button" onclick="cadastrarAtividade()">Cadastrar</button>
        </form>
        <div class="resumo">
            <h2>Resumo Acumulado</h2>
            <p id="resumoDistancia">Distância Total: 0 km</p>
            <p id="resumoTempo">Tempo Total: 0 minutos</p>
            <p id="resumoCalorias">Calorias Totais: 0 kcal</p>
        </div>
    </main>
    <script>
        // Inicializa os valores acumulados
        let distanciaTotal = parseFloat(localStorage.getItem("distanciaTotal")) || 0;
        let tempoTotal = parseFloat(localStorage.getItem("tempoTotal")) || 0;
        let caloriasTotal = parseFloat(localStorage.getItem("caloriasTotal")) || 0;

        // Atualiza o resumo na página
        function atualizarResumo() {
            document.getElementById("resumoDistancia").innerText = `Distância Total: ${distanciaTotal.toFixed(1)} km`;
            document.getElementById("resumoTempo").innerText = `Tempo Total: ${tempoTotal} minutos`;
            document.getElementById("resumoCalorias").innerText = `Calorias Totais: ${caloriasTotal} kcal`;
        }

        atualizarResumo();

        // Cadastra uma nova atividade
        function cadastrarAtividade() {
            const distancia = parseFloat(document.getElementById("distancia").value);
            const tempo = parseInt(document.getElementById("tempo").value);
            const calorias = parseFloat(document.getElementById("calorias").value);

            if (distancia && tempo && calorias) {
                // Soma os valores ao total
                distanciaTotal += distancia;
                tempoTotal += tempo;
                caloriasTotal += calorias;

                // Armazena os novos valores no Local Storage
                localStorage.setItem("distanciaTotal", distanciaTotal);
                localStorage.setItem("tempoTotal", tempoTotal);
                localStorage.setItem("caloriasTotal", caloriasTotal);

                // Atualiza o resumo na página
                atualizarResumo();

                // Limpa os campos do formulário
                document.getElementById("formAtividade").reset();

                alert("Atividade cadastrada com sucesso!");
            } else {
                alert("Por favor, preencha todos os campos!");
            }
        }
    </script>
</body>
</html>
