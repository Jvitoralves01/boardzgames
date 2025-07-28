<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
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
  }

  button:hover {
    background-color: #9b30ff;
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
    <h1>Controle de Jogos - BoardzGamePub</h1>
  </header>

  <main>
    <label>Nome do Jogo:</label>
    <input type="text" id="nome" placeholder="Ex: Dixit" required />

    <label>Mesa:</label>
    <input type="text" id="mesa" placeholder="Ex: 3" required />

    <label>Valor da Locação:</label>
    <select id="valor" required>
      <option value="2">🔴 LATÃO – R$ 2</option>
      <option value="4">🟡 BRONZE – R$ 4</option>
      <option value="5">🔵 PRATA – R$ 5</option>
      <option value="10">🟡🟡 OURO – R$ 10</option>
      <option value="15">🔵🔵 PLATINA – R$ 15</option>
    </select>

    <button onclick="cadastrarJogo()">Cadastrar Jogo</button>

    <label style="margin-top: 30px;">Pesquisar por Mesa:</label>
    <input type="text" id="pesquisaMesa" placeholder="Digite o número da mesa" oninput="filtrarMesa()" />

    <table id="tabela">
      <thead>
        <tr>
          <th>Mesa</th>
          <th>Jogo</th>
          <th>Valor</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody id="corpoTabela">
      </tbody>
    </table>

    <div id="listaTotais"></div>

    <div style="margin-top: 30px; text-align: center;">
      <label>Fechar Mesa:</label><br>
      <input type="text" id="fecharMesaInput" placeholder="Digite o número da mesa" style="padding: 30px; width: 200px;">
      <button onclick="fecharMesa()">Fechar Mesa</button>
    </div>
  </main>

  <script>
    let registros = [];

    function cadastrarJogo() {
      const nome = document.getElementById('nome').value.trim();
      const mesa = document.getElementById('mesa').value.trim();
      const valor = parseFloat(document.getElementById('valor').value);

      if (!nome || !mesa) {
        alert('Preencha todos os campos.');
        return;
      }

      registros.push({ nome, mesa, valor });
      atualizarTabela();

      document.getElementById('nome').value = '';
      document.getElementById('mesa').value = '';
      document.getElementById('valor').selectedIndex = 0;
    }

    function atualizarTabela(filtro = '') {
      const corpo = document.getElementById('corpoTabela');
      corpo.innerHTML = '';

      const filtrados = filtro
        ? registros.filter(r => r.mesa.includes(filtro))
        : registros;

      filtrados.forEach((r, index) => {
        const linha = document.createElement('tr');
        linha.innerHTML = `
          <td>${r.mesa}</td>
          <td>${r.nome}</td>
          <td>R$ ${r.valor.toFixed(2)}</td>
          <td>
            <button onclick="editarJogo(${index})">Editar</button>
            <button onclick="removerJogo(${index})">Remover</button>
          </td>
        `;
        corpo.appendChild(linha);
      });

      atualizarTotais();
    }

    function filtrarMesa() {
      const filtro = document.getElementById('pesquisaMesa').value.trim();
      atualizarTabela(filtro);
    }

    function removerJogo(index) {
      registros.splice(index, 1);
      atualizarTabela();
    }

    function editarJogo(index) {
      const jogo = registros[index];
      const novoNome = prompt('Novo nome do jogo:', jogo.nome);
      const novoValor = parseFloat(prompt('Novo valor:', jogo.valor));
      if (novoNome && !isNaN(novoValor)) {
        registros[index].nome = novoNome;
        registros[index].valor = novoValor;
        atualizarTabela();
      }
    }

    function atualizarTotais() {
      const totaisPorMesa = {};
      registros.forEach(r => {
        if (!totaisPorMesa[r.mesa]) totaisPorMesa[r.mesa] = 0;
        totaisPorMesa[r.mesa] += r.valor;
      });

      const divTotais = document.getElementById('listaTotais');
      divTotais.innerHTML = '<h3>Totais por Mesa:</h3>';
      for (let mesa in totaisPorMesa) {
        divTotais.innerHTML += `<p>Mesa ${mesa}: <strong>R$ ${totaisPorMesa[mesa].toFixed(2)}</strong></p>`;
      }
    }

    function fecharMesa() {
      const mesaParaFechar = document.getElementById('fecharMesaInput').value.trim();
      if (!mesaParaFechar) {
        alert('Digite o número da mesa que deseja fechar.');
        return;
      }

      const confirmacao = confirm(`Tem certeza que deseja fechar a Mesa: ${mesaParaFechar}?`);
      if (!confirmacao) return;

      registros = registros.filter(r => r.mesa !== mesaParaFechar);
      atualizarTabela();
      document.getElementById('fecharMesaInput').value = '';
    }
  </script>
</body>
</html>
