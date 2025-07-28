<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Cadastro de Jogos - Pub Boardgames</title>
  <style>
    :root {
      --cor-primaria: #6a1b9a;
      --cor-secundaria: #f3e5f5;
      --cor-fundo: #f8f8f8;
    }

    * {
      box-sizing: border-box;
    }

    body {
      font-family: Arial, sans-serif;
      background-color: var(--cor-fundo);
      margin: 0;
      padding: 0;
    }

    header {
      background-color: var(--cor-primaria);
      color: white;
      text-align: center;
      padding: 20px;
    }

    header img {
      max-height: 80px;
    }

    main {
      max-width: 1000px;
      margin: 20px auto;
      padding: 20px;
      background-color: white;
      box-shadow: 0 0 10px #ccc;
      border-radius: 10px;
    }

    h1, h2 {
      text-align: center;
      color: var(--cor-primaria);
    }

    form {
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    label {
      font-weight: bold;
    }

    input[type="text"], select {
      padding: 10px;
      font-size: 1em;
      border: 1px solid #ccc;
      border-radius: 5px;
    }

    button {
      background-color: var(--cor-primaria);
      color: white;
      padding: 10px;
      border: none;
      border-radius: 5px;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      background-color: #8e24aa;
    }

    .filtro {
      margin-top: 30px;
      text-align: center;
    }

    .filtro input {
      width: 300px;
      max-width: 100%;
      padding: 10px;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
    }

    th, td {
      border: 1px solid #ddd;
      padding: 12px;
      text-align: center;
    }

    th {
      background-color: var(--cor-primaria);
      color: white;
    }

    td button {
      margin: 0 5px;
      background-color: #d32f2f;
    }

    td button.edit {
      background-color: #0288d1;
    }

    .totais {
      margin-top: 30px;
      padding: 15px;
      background-color: var(--cor-secundaria);
      border-radius: 10px;
    }

    @media (max-width: 600px) {
      form, .filtro {
        flex-direction: column;
        align-items: stretch;
      }

      input, select, button {
        width: 100%;
      }

      table, thead, tbody, th, td, tr {
        font-size: 0.9em;
      }
    }
  </style>
</head>
<body>

<header>
  <img src="images.jpg" alt="Logo do Pub">
  <h1>Cadastro de Jogos - Pub de Boardgames</h1>
</header>

<main>
  <form id="jogoForm">
    <label>Mesa:</label>
    <input type="text" id="mesa" required>

    <label>Nome do Jogo:</label>
    <input type="text" id="nome" required>

    <label>Valor da Locação:</label>
    <select id="valor" required>
      <option value="2">🔴 LATÃO – R$ 2</option>
      <option value="4">🟡 BRONZE – R$ 4</option>
      <option value="5">🔵 PRATA – R$ 5</option>
      <option value="10">🟡🟡 OURO – R$ 10</option>
      <option value="15">🔵🔵 PLATINA – R$ 15</option>
    </select>

    <button type="submit">Cadastrar Jogo</button>
  </form>

  <div class="filtro">
    <label>🔍 Pesquisar por número da mesa:</label><br>
    <input type="text" id="filtroMesa" placeholder="Digite o número da mesa...">
  </div>

  <table id="tabelaJogos">
    <thead>
      <tr>
        <th>Mesa</th>
        <th>Nome do Jogo</th>
        <th>Valor (R$)</th>
        <th>Ações</th>
      </tr>
    </thead>
    <tbody>
    </tbody>
  </table>

  <div class="totais" id="valoresPorMesa">
    <h2>Total por Mesa</h2>
    <ul id="listaTotais"></ul>
  </div>
</main>

<script>
  const form = document.getElementById('jogoForm');
  const tabela = document.querySelector('#tabelaJogos tbody');
  const filtroMesa = document.getElementById('filtroMesa');
  const listaTotais = document.getElementById('listaTotais');

  let registros = [];

  function atualizarTabela() {
    tabela.innerHTML = '';
    registros.forEach((registro, index) => {
      const linha = document.createElement('tr');
      linha.innerHTML = `
        <td>${registro.mesa}</td>
        <td>${registro.nome}</td>
        <td>R$ ${registro.valor.toFixed(2)}</td>
        <td>
          <button class="edit" onclick="editarRegistro(${index})">Editar</button>
          <button onclick="removerRegistro(${index})">Remover</button>
        </td>
      `;
      tabela.appendChild(linha);
    });
    atualizarTotais();
  }

  function atualizarTotais() {
    const totais = {};
    registros.forEach(r => {
      totais[r.mesa] = (totais[r.mesa] || 0) + r.valor;
    });

    listaTotais.innerHTML = '';
    for (const mesa in totais) {
      const item = document.createElement('li');
      item.textContent = `Mesa ${mesa}: R$ ${totais[mesa].toFixed(2)}`;
      listaTotais.appendChild(item);
    }
  }

  form.addEventListener('submit', function (event) {
    event.preventDefault();
    const mesa = document.getElementById('mesa').value.trim();
    const nome = document.getElementById('nome').value.trim();
    const valor = parseFloat(document.getElementById('valor').value);

    registros.push({ mesa, nome, valor });
    form.reset();
    atualizarTabela();
  });

  filtroMesa.addEventListener('input', function () {
    const filtro = filtroMesa.value.toLowerCase();
    const linhas = tabela.getElementsByTagName('tr');

    for (let i = 0; i < linhas.length; i++) {
      const mesa = linhas[i].cells[0].textContent.toLowerCase();
      linhas[i].style.display = mesa.includes(filtro) ? '' : 'none';
    }
  });

  function removerRegistro(index) {
    registros.splice(index, 1);
    atualizarTabela();
  }

  function editarRegistro(index) {
    const reg = registros[index];
    document.getElementById('mesa').value = reg.mesa;
    document.getElementById('nome').value = reg.nome;
    document.getElementById('valor').value = reg.valor;
    registros.splice(index, 1);
    atualizarTabela();
  }
</script>

</body>
</html>
