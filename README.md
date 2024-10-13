<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cadastro de Funcionário</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            color: #212529;
        }
    </style>
</head>
<body>
    <div class="container mt-5">
        <h1 class="mb-4">Cadastro de Funcionário</h1>
        <form id="funcionarioForm">
            <div class="mb-3">
                <label for="nome" class="form-label">Nome Completo:</label>
                <input type="text" class="form-control" id="nome" name="nome" required>
            </div>
            <div class="mb-3">
                <label for="dataNascimento" class="form-label">Data de Nascimento:</label>
                <input type="date" class="form-control" id="dataNascimento" name="dataNascimento" required>
            </div>
            <div class="mb-3">
                <label for="funcao" class="form-label">Função:</label>
                <input type="text" class="form-control" id="funcao" name="funcao" required>
            </div>
            <div class="mb-3">
                <label for="telefone" class="form-label">Telefone:</label>
                <input type="tel" class="form-control" id="telefone" name="telefone" required>
            </div>
            <div class="mb-3">
                <label for="corFundo" class="form-label">Cor de Fundo:</label>
                <select class="form-select" id="corFundo" name="corFundo">
                    <option value="clara">Clara</option>
                    <option value="escura">Escura</option>
                </select>
            </div>
            <div class="mb-3">
                <label for="email" class="form-label">E-mail:</label>
                <input type="email" class="form-control" id="email" name="email" required>
            </div>
            <div class="mb-3">
                <label for="salarioLiquido" class="form-label">Salário Líquido:</label>
                <input type="number" class="form-control" id="salarioLiquido" name="salarioLiquido" step="0.01" required>
            </div>
            <div class="mb-3">
                <label for="salarioBruto" class="form-label">Salário Bruto:</label>
                <input type="number" class="form-control" id="salarioBruto" name="salarioBruto" step="0.01" required>
            </div>
            <button type="submit" class="btn btn-primary">Cadastrar</button>
        </form>
        <div id="dadosFuncionario" class="mt-5" style="display:none;">
            <h2>Dados do Funcionário</h2>
            <ul id="dadosList" class="list-group"></ul>
            <p id="fatoDataNascimento" class="mt-3"></p>
        </div>
    </div>
    <script>
        class Funcionario {
            constructor(nomeCompleto, dataNascimento, funcao, telefone, corFundo, email, salarioLiquido, salarioBruto) {
                this.nomeCompleto = nomeCompleto;
                this.dataNascimento = dataNascimento;
                this.funcao = funcao;
                this.telefone = telefone;
                this.corFundo = corFundo;
                this.email = email;
                this.salarioLiquido = salarioLiquido;
                this.salarioBruto = salarioBruto;
            }
            calculaDesconto() {
                return this.salarioBruto - this.salarioLiquido;
            }
        }
        document.getElementById("funcionarioForm").addEventListener("submit", async function(event) {
            event.preventDefault();
            const nome = document.getElementById("nome").value;
            const dataNascimento = document.getElementById("dataNascimento").value;
            const funcao = document.getElementById("funcao").value;
            const telefone = document.getElementById("telefone").value;
            const corFundo = document.getElementById("corFundo").value;
            const email = document.getElementById("email").value;
            const salarioLiquido = parseFloat(document.getElementById("salarioLiquido").value);
            const salarioBruto = parseFloat(document.getElementById("salarioBruto").value);
            const funcionario = new Funcionario(nome, dataNascimento, funcao, telefone, corFundo, email, salarioLiquido, salarioBruto);
            // Save background preference in localStorage
            localStorage.setItem("corFundo", corFundo);
            // Fetch information about the date of birth
            let factAboutDate = "Não foi possível obter informações sobre esta data.";
            try {
                const response = await fetch(`http://numbersapi.com/${new Date(dataNascimento).getMonth() + 1}/${new Date(dataNascimento).getDate()}/date`);
                factAboutDate = await response.text();
            } catch (error) {
                console.error("Erro ao obter informações da API:", error);
            }
            // Display the data
            const dadosList = document.getElementById("dadosList");
            dadosList.innerHTML = `
                <li class="list-group-item">Nome: ${funcionario.nomeCompleto}</li>
                <li class="list-group-item">Data de Nascimento: ${funcionario.dataNascimento}</li>
                <li class="list-group-item">Função: ${funcionario.funcao}</li>
                <li class="list-group-item">Telefone: ${funcionario.telefone}</li>
                <li class="list-group-item">Cor de Fundo: ${funcionario.corFundo}</li>
                <li class="list-group-item">E-mail: ${funcionario.email}</li>
                <li class="list-group-item">Salário Líquido: R$ ${funcionario.salarioLiquido.toFixed(2)}</li>
                <li class="list-group-item">Salário Bruto: R$ ${funcionario.salarioBruto.toFixed(2)}</li>
                <li class="list-group-item">Desconto: R$ ${funcionario.calculaDesconto().toFixed(2)}</li>
            `;
            document.getElementById("fatoDataNascimento").textContent = `Fato sobre a data de nascimento: ${factAboutDate}`;
            document.getElementById("dadosFuncionario").style.display = "block";
            // Apply the background color
            document.body.style.backgroundColor = corFundo === 'clara' ? '#f8f9fa' : '#343a40';
            document.body.style.color = corFundo === 'clara' ? '#212529' : '#f8f9fa';
        });
        // Set background color based on localStorage preference
        const savedCorFundo = localStorage.getItem("corFundo");
        if (savedCorFundo) {
            document.body.style.backgroundColor = savedCorFundo === 'clara' ? '#f8f9fa' : '#343a40';
            document.body.style.color = savedCorFundo === 'clara' ? '#212529' : '#f8f9fa';
            document.getElementById("corFundo").value = savedCorFundo;
        }
    </script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
