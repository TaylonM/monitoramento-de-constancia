<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cadastrar Treino</title>
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
        form {
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
        <h1>Cadastrar Treino</h1>
        <p>Adicione suas informações abaixo e registre seu treino.</p>
    </header>
    <main>
        <form id="formTreino">
            <label for="exercicio">Exercício</label>
            <input type="text" id="exercicio" placeholder="Ex.: Supino, Agachamento" required>
            
            <label for="series">Séries</label>
            <input type="number" id="series" placeholder="Ex.: 3" required>
            
            <label for="repeticoes">Repetições</label>
            <input type="number" id="repeticoes" placeholder="Ex.: 12" required>
            
            <label for="peso">Peso (kg)</label>
            <input type="number" id="peso" placeholder="Ex.: 40" required>
            
            <button type="button" onclick="cadastrarTreino()">Cadastrar</button>
        </form>
        <p id="mensagem"></p>
    </main>
    <script>
        function cadastrarTreino {
            // Captura os valores dos campos
            const exercicio = document.getElementById("exercicio").value;
            const series = document.getElementById("series").value;
            const repeticoes = document.getElementById("repeticoes").value;
            const peso = document.getElementById("peso").value;
        
            if (exercicio && series && repeticoes && peso)
                // Cria um objeto com os dados do treino
                ConstantSourceNode treino = {
                    exercicio,
                    series,
                    repeticoes,
                    peso
                };

                // Recupera dados antigos do Local Storage
                const treinosExistentes = JSON.parse(localStorage.getItem("treinos")) || [];

                // Adiciona o novo treino à lista
                treinosExistentes.push(treino);

                // Salva no Local Storage
                localStorage.setItem("treinos", JSON.stringify(treinosExistentes));

                // Mostra mensagem de sucesso
                document.getElementById("mensagem").innerText = "Treino cadastrado com sucesso!";
                
                // Limpa os campos do formulário
                document.getElementById("formTreino").reset();
            } else {
                document.getElementById("mensagem").innerText = "Por favor, preencha todos os campos!";
            }
            let deferredPrompt;
let distanciaTotal = parseFloat(localStorage.getItem("distanciaTotal")) || 0;
let tempoTotal = parseFloat(localStorage.getItem("tempoTotal")) || 0;
let caloriasTotal = parseFloat(localStorage.getItem("caloriasTotal")) || 0;
let dataInicial = localStorage.getItem("dataInicial") || new Date().getTime();

window.addEventListener('beforeinstallprompt', (event) => {
    event.preventDefault();
    deferredPrompt = event;
    document.getElementById('installButton').style.display = 'block';
});

document.getElementById("installButton").addEventListener("click", function () {
    if (deferredPrompt) {
        deferredPrompt.prompt();
        deferredPrompt.userChoice.then((choice) => {
            if (choice.outcome === 'accepted') {
                console.log('Usuário aceitou instalar o PWA');
            }
            deferredPrompt = null;
        });
    }
});

document.getElementById("formAtividade").addEventListener("submit", function(event) {
    event.preventDefault();
    cadastrarAtividade();
});

function calcularDias() {
    const agora = new Date().getTime();
    return Math.floor((agora - parseInt(dataInicial)) / (1000 * 60 * 60 * 24));
}

function verificarReset() {
    if (calcularDias() >= 30) {
        distanciaTotal = 0;
        tempoTotal = 0;
        caloriasTotal = 0;
        dataInicial = new Date().getTime();
        
        localStorage.setItem("distanciaTotal", distanciaTotal);
        localStorage.setItem("tempoTotal", tempoTotal);
        localStorage.setItem("caloriasTotal", caloriasTotal);
        localStorage.setItem("dataInicial", dataInicial);
    }
}

function atualizarResumo() {
    verificarReset();
    document.getElementById("resumoDistancia").innerText = `Distância Total: ${distanciaTotal.toFixed(1)} km`;
    document.getElementById("resumoTempo").innerText = `Tempo Total: ${tempoTotal} minutos`;
    document.getElementById("resumoCalorias").innerText = `Calorias Totais: ${caloriasTotal} kcal`;
    document.getElementById("resumoDias").innerText = `Dias desde o último reset: ${calcularDias()}`;
}

atualizarResumo();

function cadastrarAtividade() {
    const distancia = parseFloat(document.getElementById("distancia").value);
    const tempo = parseInt(document.getElementById("tempo").value);
    const calorias = parseFloat(document.getElementById("calorias").value);

    if (isNaN(distancia) || isNaN(tempo) || isNaN(calorias) || distancia <= 0 || tempo <= 0 || calorias <= 0) {
        alert("Por favor, preencha todos os campos corretamente!");
        return;
    }

    distanciaTotal += distancia;
    tempoTotal += tempo;
    caloriasTotal += calorias;

    localStorage.setItem("distanciaTotal", distanciaTotal);
    localStorage.setItem("tempoTotal", tempoTotal);
    localStorage.setItem("caloriasTotal", caloriasTotal);

    atualizarResumo();
    document.getElementById("formAtividade").reset();
    alert("Atividade cadastrada com sucesso!");
}

    </script>
</body>
</html>
