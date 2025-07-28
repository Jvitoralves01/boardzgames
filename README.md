<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Controle de Jogos - Boardzgame Pub</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: white;
      color: #333;
      margin: 0;
      padding: 20px;
    }

    header {
      display: flex;
      align-items: center;
      gap: 10px;
      margin-bottom: 30px;
    }

    header img {
      height: 50px;
    }

    h1 {
      margin: 0;
      font-size: 28px;
      color: #4B0082;
    }

    main {
      max-width: 900px;
      margin: auto;
      background-color: #f0f0f0;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    }

    label {
      display: block;
      margin-top: 10px;
      font-weight: bold;
    }

    input, select, button {
      padding: 10px;
      border-radius: 5px;
      border: 1px solid #ccc;
      margin-top: 5px;
      width: 100%;
      box-sizing: border-box;
      font-size: 16px;
    }

    button {
      background-color: #800080;
      color: white;
      cursor: pointer;
      margin-top: 15px;
      border: none;
      transition: background-color 0.3s ease;
    }

    button:hover {
      background-color: #9b30ff;
    }

    button.remover-btn {
      background-color: #cc0000;
      padding: 6px 12px;
      margin: 0;
      font-size: 14px;
      border-radius: 5px;
      width: auto;
      cursor: pointer;
    }

    button.remover-btn:hover {
      background-color: #ff4d4d;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
      background-color: white;
    }

    th, td {
      padding: 8px;
      border-bottom: 1px solid #ccc;
      text-align: center;
    }

    #listaTotais {
      margin-top: 30px;
    }

    @media (max-width: 600px) {
      table, thead, tbody, th, td, tr {
        display: block;
      }

      td {
        text-align: right;
        padding-left: 50%;
        position: relative;
      }

      td::before {
        content: attr(data-label);
        position: absolute;
        left: 10px;
        text-align: left;
        font-weight: bold;
      }
    }
  </style>
</head>
<body>
  <header>
    <img src="images.jpg" alt="Logo" />
    <h1>Controle Boardgames</h1>
  </header>
  <main>
    <label for="mesa">Número da Mesa</label>
    <input type="text" id="mesa" placeholder="..." />

    <label for="nome">Nome do Jogo</label>
    <input type="text" id="nome" placeholder="..." />

    <label for="valor">Valor da Locação</label>
    <select id="valor">
      <option value="2">🔴 LATÃO – R$ 2</option>
      <option value="4">🟡 BRONZE – R$ 4</option>
      <option value="5">🔵 PRATA – R$ 5</option>
      <option value="10">🟡🟡 OURO – R$ 10</option>
      <option value="15">🔵🔵 PLATINA – R$ 15</option>
    </select>

    <button onclick="cadastrarJogo()">Lançar Jogo</button>

    <label for="pesquisaMesa">Pesquisar por Mesa</label>
    <input
      type="text"
      id="pesquisaMesa"
      placeholder="Digite o Nº da mesa"
      oninput="filtrarJogos()"
    />

    <table>
      <thead>
        <tr>
          <th>Mesa</th>
          <th>Nome do Jogo</th>
          <th>Valor (R$)</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody id="tabela"></tbody>
    </table>

    <div id="listaTotais"></div>

    <div style="margin-top: 20px; text-align: center;">
      <label>Fechar Mesa:</label><br />
      <input
        type="text"
        id="fecharMesaInput"
        placeholder="Digite o Nº da mesa"
        style="padding: 10px; width: 200px;"
      />
      <button onclick="fecharMesa()">Fechar Mesa</button>
    </div>

    <div id="historico" style="margin-top: 40px;"></div>
  </main>

  <script>
    let registros = [];
    let historicoFechamentos = [];

    function cadastrarJogo() {
      const mesaInput = document.getElementById('mesa');
      const nomeInput = document.getElementById('nome');
      const valorSelect = document.getElementById('valor');

      const mesa = mesaInput.value.trim();
      const nome = nomeInput.value.trim();
      const valor = parseFloat(valorSelect.value);

      if (!mesa || !nome) {
        alert('Preencha todos os campos.');
        return;
      }

      registros.push({ mesa, nome, valor });
      atualizarTabela();

      mesaInput.value = '';
      nomeInput.value = '';
      mesaInput.focus();
    }

    function removerJogo(index) {
      if (confirm('Confirma a remoção deste jogo?')) {
        registros.splice(index, 1);
        atualizarTabela();
      }
    }

    function atualizarTabela() {
      filtrarJogos();
    }

    function filtrarJogos() {
      const filtro = document
        .getElementById('pesquisaMesa')
        .value.trim()
        .toLowerCase();
      const tabela = document.getElementById('tabela');
      tabela.innerHTML = '';

      // Filtra registros que contenham o filtro no número da mesa (case insensitive)
      const registrosFiltrados = registros.filter((r) =>
        r.mesa.toLowerCase().includes(filtro)
      );

      if (registrosFiltrados.length === 0) {
        tabela.innerHTML = `<tr><td colspan="4">Nenhum jogo encontrado para a busca.</td></tr>`;
        document.getElementById('listaTotais').innerHTML = '';
        return;
      }

      registrosFiltrados.forEach((r, i) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${r.mesa}</td>
          <td>${r.nome}</td>
          <td>R$ ${r.valor.toFixed(2)}</td>
          <td><button class="remover-btn" onclick="removerJogo(${i})">Remover</button></td>
        `;
        tabela.appendChild(tr);
      });

      atualizarTotaisFiltrados(registrosFiltrados);
    }

    function atualizarTotaisFiltrados(registrosFiltrados) {
      const totais = {};
      registrosFiltrados.forEach((r) => {
        if (!totais[r.mesa]) totais[r.mesa] = 0;
        totais[r.mesa] += r.valor;
      });

      const div = document.getElementById('listaTotais');
      div.innerHTML = '<h3>Total por Mesa (Filtro)</h3>';
      for (let mesa in totais) {
        div.innerHTML += `Mesa ${mesa}: <strong>R$ ${totais[mesa].toFixed(2)}</strong><br>`;
      }
    }

    function fecharMesa() {
      const mesaParaFechar = document.getElementById('fecharMesaInput').value.trim();
      if (!mesaParaFechar) {
        alert('Digite o nº mesa para fechar.');
        return;
      }

      const registrosMesa = registros.filter((r) => r.mesa === mesaParaFechar);
      if (registrosMesa.length === 0) {
        alert(`Nenhum jogo encontrado para a Mesa ${mesaParaFechar}.`);
        return;
      }

      const totalMesa = registrosMesa.reduce((acc, cur) => acc + cur.valor, 0);
      alert(`Mesa ${mesaParaFechar} fechada com total de R$ ${totalMesa.toFixed(2)}.`);

      historicoFechamentos.push({
        mesa: mesaParaFechar,
        total: totalMesa,
        jogos: registrosMesa.map((r) => r.nome),
      });

      registros = registros.filter((r) => r.mesa !== mesaParaFechar);
      atualizarTabela();
      atualizarHistorico();
      document.getElementById('fecharMesaInput').value = '';
      document.getElementById('pesquisaMesa').value = ''; // Limpa filtro ao fechar mesa
    }

    function atualizarHistorico() {
      const historicoDiv = document.getElementById('historico');
      historicoDiv.innerHTML = '<h3>🧾 Histórico de Mesas Fechadas</h3>';

      historicoFechamentos.forEach((item) => {
        const div = document.createElement('div');
        div.style.marginBottom = '10px';
        div.style.padding = '10px';
        div.style.border = '1px solid #ccc';
        div.style.backgroundColor = '#fff';
        div.innerHTML = `
          <strong>Mesa ${item.mesa}</strong><br>
          Jogos: ${item.jogos.join(', ')}<br>
          Total Pago por jogador: <strong>R$ ${item.total.toFixed(2)}</strong>
        `;
        historicoDiv.appendChild(div);
      });
    }
  </script>
</body>
</html>
