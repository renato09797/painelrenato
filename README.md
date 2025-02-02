<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel de Pedidos</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-image: url('fundo.jpg'); /* Caminho para a imagem de fundo */
            background-size: cover;
            background-position: center;
            color: white;
        }

        h1, h2 {
            color: rgb(12, 11, 11);
            text-align: center;
        }

        .menu {
            display: flex;
            justify-content: center;
            background-color: #333;
            padding: 10px;
        }

        .menu a {
            color: rgb(107, 107, 107);
            padding: 14px 20px;
            text-decoration: none;
            text-align: center;
        }

        .menu a:hover {
            background-color: #575757;
        }

        .container {
            width: 80%;
            max-width: 600px;
            padding: 20px;
            border: 1px solid #ccc;
            margin-top: 20px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            background-color: rgba(255, 255, 255, 0.9);
            color: black;
            margin-left: auto;
            margin-right: auto;
        }

        .input-section, .pedidos-section {
            margin-bottom: 20px;
        }

        .input-section input[type="text"], .input-section textarea {
            width: 100%;
            padding: 8px;
            margin-top: 10px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
        }

        .input-section button {
            width: 100%;
            padding: 10px;
            background-color: #007bff;
            color: white;
            border: none;
            cursor: pointer;
        }

        .pedido {
            background-color: #f9f9f9;
            padding: 10px;
            border: 1px solid #ddd;
            margin-bottom: 10px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            align-items: center; /* Centraliza o conteúdo do pedido */
        }

        .pedido button {
            background-color: #ff4d4d;
            color: white;
            border: none;
            padding: 5px 10px;
            cursor: pointer;
        }

        #contadorExclusoes {
            margin-top: 20px;
            background-color: #007bff;
            color: white;
            padding: 10px;
            border: none;
            cursor: pointer;
        }

        .hidden {
            display: none;
        }

    </style>
</head>
<body>
    <h1>Painel de Pedidos</h1>

    <!-- Menu de navegação -->
    <div class="menu">
        <a href="javascript:void(0);" onclick="mostrarSeção('registrar')">Registrar Novo Pedido</a>
        <a href="javascript:void(0);" onclick="mostrarSeção('pedidos')">Pedidos Registrados</a>
    </div>

    <!-- Seção para Registrar Novo Pedido -->
    <div class="container input-section hidden" id="registrar">
        <h2>Registrar Novo Pedido</h2>
        <input type="text" id="pedidoInput" placeholder="Digite o pedido">
        <textarea id="observacaoInput" placeholder="Observações (ex.: Para entrega)"></textarea>
        <div>
            <label>
                <input type="checkbox" id="entregaCheck"> Pedido para entrega
            </label>
        </div>
        <input type="text" id="enderecoInput" placeholder="Digite o endereço de entrega" style="display:none;">
        <button onclick="registrarPedido()">Registrar Pedido</button>
    </div>

    <!-- Seção para Pedidos Registrados -->
    <div class="container pedidos-section hidden" id="pedidos">
        <h2>Pedidos Registrados</h2>
        <div id="painelPedidos"></div>
        <button id="contadorExclusoes" onclick="mostrarIndicador()">Ver Pedidos Excluídos por Dia</button>
    </div>

    <script>
        // Função para mostrar ou ocultar as seções
        function mostrarSeção(seção) {
            var todasSeções = document.querySelectorAll('.container');
            todasSeções.forEach(function(seção) {
                seção.classList.add('hidden');
            });
            document.getElementById(seção).classList.remove('hidden');
        }

        // Função para registrar um novo pedido
        function registrarPedido() {
            var pedidoInput = document.getElementById('pedidoInput');
            var pedidoDescricao = pedidoInput.value;
            var observacaoInput = document.getElementById('observacaoInput').value;
            var entregaCheck = document.getElementById('entregaCheck').checked;
            var enderecoInput = document.getElementById('enderecoInput').value;

            if (pedidoDescricao.trim() !== "") {
                var pedido = {
                    descricao: pedidoDescricao,
                    observacao: observacaoInput,
                    paraEntrega: entregaCheck,
                    endereco: enderecoInput,
                    data: new Date().toLocaleString()
                };

                var chavePedido = 'pedido_' + new Date().getTime();
                localStorage.setItem(chavePedido, JSON.stringify(pedido));
                pedidoInput.value = ""; // Limpa o campo de entrada
                exibirPedidos(); // Atualiza a lista de pedidos
            } else {
                alert('Por favor, digite uma descrição para o pedido!');
            }
        }

        // Função para excluir um pedido
        function excluirPedido(chave) {
            var descricaoPedido = localStorage.getItem(chave);
            var dadosPedido = JSON.parse(descricaoPedido);
            var dataHoraExclusao = new Date().toLocaleString();
            var dadosExcluido = {
                descricao: dadosPedido.descricao,
                dataHora: dataHoraExclusao
            };

            localStorage.setItem('excluido_' + new Date().getTime(), JSON.stringify(dadosExcluido));
            localStorage.removeItem(chave);
            exibirPedidos(); // Atualiza a lista de pedidos
        }

        // Função para exibir os pedidos registrados
        function exibirPedidos() {
            var painelPedidos = document.getElementById('painelPedidos');
            painelPedidos.innerHTML = ""; // Limpa a área de pedidos

            for (var i = 0; i < localStorage.length; i++) {
                var chave = localStorage.key(i);
                if (chave.startsWith('pedido_')) {
                    var dadosPedido = JSON.parse(localStorage.getItem(chave));
                    var pedidoDiv = document.createElement('div');
                    pedidoDiv.className = 'pedido';
                    pedidoDiv.innerHTML = `
                        <div><strong>Pedido:</strong> ${dadosPedido.descricao}</div>
                        <div><strong>Data:</strong> ${dadosPedido.data}</div>
                        <div><strong>Entrega:</strong> ${dadosPedido.paraEntrega ? 'Sim' : 'Não'}</div>
                        ${dadosPedido.paraEntrega ? `<div><strong>Endereço:</strong> ${dadosPedido.endereco}</div>` : ''}
                        <button onclick="excluirPedido('${chave}')">Excluir</button>
                    `;
                    painelPedidos.appendChild(pedidoDiv);
                }
            }
        }

        // Função para mostrar a quantidade de pedidos excluídos hoje
        function mostrarIndicador() {
            var contador = 0;
            var hoje = new Date().toLocaleDateString();

            for (var i = 0; i < localStorage.length; i++) {
                var chaveExcluido = localStorage.key(i);
                if (chaveExcluido.startsWith('excluido_')) {
                    var dadosExcluido = JSON.parse(localStorage.getItem(chaveExcluido));
                    if (dadosExcluido.dataHora.startsWith(hoje)) {
                        contador++;
                    }
                }
            }

            alert('Pedidos excluídos hoje: ' + contador);
        }

        // Exibe a seção de "Registrar Novo Pedido" por padrão ao carregar a página
        window.onload = function() {
            mostrarSeção('registrar');
        };

        // Exibe o campo de endereço apenas se o pedido for para entrega
        document.getElementById('entregaCheck').addEventListener('change', function() {
            document.getElementById('enderecoInput').style.display = this.checked ? 'block' : 'none';
        });
    </script>
</body>
</html>
