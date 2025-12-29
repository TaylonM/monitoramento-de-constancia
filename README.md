<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Registro de Atividades</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #00c6ff, #0072ff);
            color: #fff;
            margin: 0;
            padding: 0;
        }

        main {
            max-width: 500px;
            margin: 40px auto;
            background: rgba(255, 255, 255, 0.1);
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.3);
        }

        h2 {
            text-align: center;
            margin-bottom: 20px;
            font-size: 1.8em;
            color: #ffeb3b;
        }

        label {
            display: block;
            margin-top: 15px;
            font-weight: bold;
        }

        input {
            width: 100%;
            padding: 12px;
            margin-top: 8px;
            border: none;
            border-radius: 8px;
            font-size: 1em;
            outline: none;
        }

        button {
            width: 100%;
            padding: 12px;
            margin-top: 20px;
            border: none;
            border-radius: 8px;
            font-size: 1.1em;
            font-weight: bold;
            cursor: pointer;
            transition: 0.3s;
        }

        button[type="submit"] {
            background: #ff9800;
            color: #fff;
        }

        button[type="submit"]:hover {
            background: #e68900;
        }

        #installButton {
            background: #4caf50;
            color: #fff;
            display: none;
        }

        #installButton:hover {
            background: #3e8e41;
        }

        #resetButton {
            background: #f44336;
            color: #fff;
        }

        #resetButton:hover {
            background: #d32f2f;
        }

        .resumo {
            margin-top: 30px;
            background: rgba(0,0,0,0.2);
            padding: 20px;
            border-radius: 10px;
        }

        .resumo p {
            font-size: 1.1em;
            margin: 10px 0;
        }

        .atividade-dia {
            background: rgba(255,255,255,0.2);
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
        }

        .atividade-dia h3 {
            margin: 0 0 10px 0;
            color: #ffeb3b;
        }

        .historico {
            margin-top: 20px;
        }

        .historico h4 {
            margin: 10px 0;
            color: #ffeb3b;
        }

        .historico ul {
            list-style: none;
            padding: 0;
            margin: 0;
        }

        .historico li {
            background: rgba(255,255,255,0.12);
            margin: 8px 0;
            padding: 10px;
            border-radius: 8px;
            font-size: 0.95em;
        }
    </style>
</head>
<body>
<main>
    <form id="formAtividade">
        <label for="distancia">Distância (km)</label>
        <input type="number" id="distancia" placeholder="Ex.: 5" step="0.1" min="0.1" required>

        <label for="tempo">Tempo (min)</label>
        <input type="number" id="tempo" placeholder="Ex.: 30" min="1" required>

        <label for="calorias">Calorias Queimadas</label>
        <input type="number" id="calorias" placeholder="Ex.: 250" min="1" required>

        <button type="submit">🚀 Cadastrar Atividade</button>
    </form>

    <button id="installButton">📲 Instalar Aplicativo</button>
    <button id="resetButton">🔄 Resetar Progresso</button>

    <div class="resumo">
        <div class="atividade-dia">
            <h3>🏃‍♂️ Atividade do Dia</h3>
            <p id="atividadeDistancia">Distância: 0 km</p>
            <p id="atividadeTempo">Tempo: 0 minutos</p>
            <p id="atividadeCalorias">Calorias: 0 kcal</p>
        </div>

        <h2>📊 Resumo Acumulado</h2>
        <p id="resumoDistancia">Distância Total: 0 km</p>
        <p id="resumoTempo">Tempo Total: 0 minutos</p>
        <p id="resumoCalorias">Calorias Totais: 0 kcal</p>
        <p id="resumoDias">Dias acumulados: 0</p>

        <div class="historico">
            <h4>🗂️ Últimas atividades</h4>
            <ul id="listaHistorico"></ul>
        </div>
    </div>
</main>

<script>
    // Utilidades
    const hojeISO = () => new Date().toISOString().slice(0, 10); // YYYY-MM-DD

    let deferredPrompt;

    // Carregar do localStorage com fallback seguro
    const getNumber = (key, def = 0) => {
        const v = localStorage.getItem(key);
        const n = Number(v);
        return Number.isFinite(n) ? n : def;
    };

    let distanciaTotal = getNumber("distanciaTotal", 0);
    let tempoTotal = getNumber("tempoTotal", 0);
    let caloriasTotal = getNumber("caloriasTotal", 0);
    let diasAcumulados = getNumber("diasAcumulados", 0);

    // dataInicial: garante que seja salvo na primeira execução
    let dataInicialRaw = localStorage.getItem("dataInicial");
    if (!dataInicialRaw) {
        dataInicialRaw = String(Date.now());
        localStorage.setItem("dataInicial", dataInicialRaw);
    }
    let dataInicial = Number(dataInicialRaw);

    // Histórico e atividade do dia
    let atividades = [];
    try {
        const raw = localStorage.getItem("atividades");
        atividades = raw ? JSON.parse(raw) : [];
        if (!Array.isArray(atividades)) atividades = [];
    } catch {
        atividades = [];
    }

    let atividadeDia = { distancia: 0, tempo: 0, calorias: 0 };
    try {
        const adRaw = localStorage.getItem("atividadeDia");
        atividadeDia = adRaw ? JSON.parse(adRaw) : atividadeDia;
        if (typeof atividadeDia !== 'object' || atividadeDia === null) {
            atividadeDia = { distancia: 0, tempo: 0, calorias: 0 };
        }
    } catch {
        atividadeDia = { distancia: 0, tempo: 0, calorias: 0 };
    }

    // Se houver atividade de hoje no histórico, usa como atividade do dia
    const deHoje = atividades.filter(a => a.data === hojeISO()).slice(-1)[0];
    if (deHoje) {
        atividadeDia = { distancia: deHoje.distancia, tempo: deHoje.tempo, calorias: deHoje.calorias };
        localStorage.setItem("atividadeDia", JSON.stringify(atividadeDia));
    }

    // PWA install
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

    // Eventos do formulário e reset
    document.getElementById("formAtividade").addEventListener("submit", function(event) {
        event.preventDefault();
        cadastrarAtividade();
    });

    document.getElementById("resetButton").addEventListener("click", function () {
        if (confirm("Tem certeza que deseja zerar todo o progresso?")) {
            zerarProgresso();
            atualizarResumo();
            alert("✅ Progresso zerado com sucesso!");
        }
    });

    // Funções de cálculo e reset
    function calcularDias() {
        const agora = Date.now();
        return Math.floor((agora - dataInicial) / (1000 * 60 * 60 * 24));
    }

    function verificarReset() {
        if (calcularDias() >= 30) {
            zerarProgresso();
        }
    }

    function zerarProgresso() {
        distanciaTotal = 0;
        tempoTotal = 0;
        caloriasTotal = 0;
        diasAcumulados = 0;
        atividadeDia = { distancia: 0, tempo: 0, calorias: 0 };
        atividades = [];
        dataInicial = Date.now();

        localStorage.setItem("distanciaTotal", String(distanciaTotal));
        localStorage.setItem("tempoTotal", String(tempoTotal));
        localStorage.setItem("caloriasTotal", String(caloriasTotal));
        localStorage.setItem("diasAcumulados", String(diasAcumulados));
        localStorage.setItem("atividadeDia", JSON.stringify(atividadeDia));
        localStorage.setItem("atividades", JSON.stringify(atividades));
        localStorage.setItem("dataInicial", String(dataInicial));
    }

    // UI
    function atualizarResumo() {
        verificarReset();

        // Atividade do dia
        document.getElementById("atividadeDistancia").innerText =
            `Distância: ${Number(atividadeDia.distancia || 0).toFixed(1)} km`;
        document.getElementById("atividadeTempo").innerText =
            `Tempo: ${Number(atividadeDia.tempo || 0)} minutos`;
        document.getElementById("atividadeCalorias").innerText =
            `Calorias: ${Number(atividadeDia.calorias || 0)} kcal`;

        // Acumulados
        document.getElementById("resumoDistancia").innerText =
            `Distância Total: ${Number(distanciaTotal).toFixed(1)} km`;
        document.getElementById("resumoTempo").innerText =
            `Tempo Total: ${Number(tempoTotal)} minutos`;
        document.getElementById("resumoCalorias").innerText =
            `Calorias Totais: ${Number(caloriasTotal)} kcal`;
        document.getElementById("resumoDias").innerText =
            `Dias acumulados: ${Number(diasAcumulados)}`;

        // Histórico (últimas 5)
        const lista = document.getElementById("listaHistorico");
        lista.innerHTML = "";
        const ultimas = atividades.slice(-5).reverse();
        ultimas.forEach((a) => {
            const li = document.createElement("li");
            li.textContent = `${a.data} — ${a.distancia.toFixed(1)} km • ${a.tempo} min • ${a.calorias} kcal`;
            lista.appendChild(li);
        });
    }

    atualizarResumo();

    // Cadastro
    function cadastrarAtividade() {
        const distancia = Number(document.getElementById("distancia").value);
        const tempo = Number(document.getElementById("tempo").value);
        const calorias = Number(document.getElementById("calorias").value);

        if (!Number.isFinite(distancia) || !Number.isFinite(tempo) || !Number.isFinite(calorias) ||
            distancia <= 0 || tempo <= 0 || calorias <= 0) {
            alert("Por favor, preencha todos os campos corretamente!");
            return;
        }

        // Atualiza acumulados
        distanciaTotal = Number(distanciaTotal) + distancia;
        tempoTotal = Number(tempoTotal) + tempo;
        caloriasTotal = Number(caloriasTotal) + calorias;

        // Atividade do dia (sempre sobrescreve com a última do dia)
        atividadeDia = { distancia, tempo, calorias };

        // Atualiza dias acumulados: conta um dia se ainda não houve atividade hoje
        const jaTeveHoje = atividades.some(a => a.data === hojeISO());
        if (!jaTeveHoje) {
            diasAcumulados = Number(diasAcumulados) + 1;
        }

        // Salva no histórico
        atividades.push({
            data: hojeISO(),
            distancia,
            tempo,
            calorias
        });

        // Persistência
        localStorage.setItem("distanciaTotal", String(distanciaTotal));
        localStorage.setItem("tempoTotal", String(tempoTotal));
        localStorage.setItem("caloriasTotal", String(caloriasTotal));
        localStorage.setItem("diasAcumulados", String(diasAcumulados));
        localStorage.setItem("atividadeDia", JSON.stringify(atividadeDia));
        localStorage.setItem("atividades", JSON.stringify(atividades));

        atualizarResumo();
        document.getElementById("formAtividade").reset();
        alert("✅ Atividade cadastrada com sucesso!");
    }
</script>
</body>
</html>

