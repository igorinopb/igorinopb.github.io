<?php
class Funcionario {
    private $nomeCompleto;
    private $dataNascimento;
    private $funcao;
    private $telefone;
    private $corFundo;
    private $email;
    private $salarioLiquido;
    private $salarioBruto;

    // Métodos GET
    public function setNomeCompleto($nomeCompleto) { $this->nomeCompleto = $nomeCompleto; }
    public function getNomeCompleto() { return $this->nomeCompleto; }
    public function getDataNascimento() { return $this->dataNascimento; }
    public function getFuncao() { return $this->funcao; }
    public function getTelefone() { return $this->telefone; }
    public function getCorFundo() { return $this->corFundo; }
    public function getEmail() { return $this->email; }
    public function getSalarioLiquido() { return $this->salarioLiquido; }
    public function getSalarioBruto() { return $this->salarioBruto; }

    // Métodos SET
    public function setNomeCompleto($nomeCompleto) { $this->nomeCompleto = $nomeCompleto; }
    public function setDataNascimento($dataNascimento) { $this->dataNascimento = $dataNascimento; }
    public function setFuncao($funcao) { $this->funcao = $funcao; }
    public function setTelefone($telefone) { $this->telefone = $telefone; }
    public function setCorFundo($corFundo) { $this->corFundo = $corFundo; }
    public function setEmail($email) { $this->email = $email; }
    public function setSalarioLiquido($salarioLiquido) { $this->salarioLiquido = $salarioLiquido; }
    public function setSalarioBruto($salarioBruto) { $this->salarioBruto = $salarioBruto; }

    public function calculaDesconto() {
        return $this->salarioBruto - $this->salarioLiquido;
    }
}

// Processamento do formulário
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $funcionario = new Funcionario();
    $funcionario->setNomeCompleto($_POST['nome']);
    $funcionario->setDataNascimento($_POST['dataNascimento']);
    $funcionario->setFuncao($_POST['funcao']);
    $funcionario->setTelefone($_POST['telefone']);
    $funcionario->setCorFundo($_POST['corFundo']);
    $funcionario->setEmail($_POST['email']);
    $funcionario->setSalarioLiquido($_POST['salarioLiquido']);
    $funcionario->setSalarioBruto($_POST['salarioBruto']);

    // Salvar preferência de cor no cookie
    setcookie('corFundo', $_POST['corFundo'], time() + (86400 * 30), "/"); // 30 dias

    // Obter informação sobre a data de nascimento via API
    $dataNascimento = $funcionario->getDataNascimento();
    $numbersApiUrl = "http://numbersapi.com/" . date('m/d', strtotime($dataNascimento)) . "/date";
    $factAboutDate = @file_get_contents($numbersApiUrl) ?: "Não foi possível obter informações sobre esta data.";
}

// Verificar se existe um cookie com a preferência de cor
$corFundoPreferida = isset($_COOKIE['corFundo']) ? $_COOKIE['corFundo'] : 'clara';
?>

<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cadastro de Funcionário</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: <?php echo $corFundoPreferida === 'clara' ? '#f8f9fa' : '#343a40'; ?>;
            color: <?php echo $corFundoPreferida === 'clara' ? '#212529' : '#f8f9fa'; ?>;
        }
    </style>
</head>
<body>
    <div class="container mt-5">
        <h1 class="mb-4">Cadastro de Funcionário</h1>
        <form method="post" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>">
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

        <?php if (isset($funcionario)): ?>
            <h2 class="mt-5">Dados do Funcionário</h2>
            <ul class="list-group">
                <li class="list-group-item">Nome: <?php echo $funcionario->getNomeCompleto(); ?></li>
                <li class="list-group-item">Data de Nascimento: <?php echo $funcionario->getDataNascimento(); ?></li>
                <li class="list-group-item">Função: <?php echo $funcionario->getFuncao(); ?></li>
                <li class="list-group-item">Telefone: <?php echo $funcionario->getTelefone(); ?></li>
                <li class="list-group-item">Cor de Fundo: <?php echo $funcionario->getCorFundo(); ?></li>
                <li class="list-group-item">E-mail: <?php echo $funcionario->getEmail(); ?></li>
                <li class="list-group-item">Salário Líquido: R$ <?php echo number_format($funcionario->getSalarioLiquido(), 2, ',', '.'); ?></li>
                <li class="list-group-item">Salário Bruto: R$ <?php echo number_format($funcionario->getSalarioBruto(), 2, ',', '.'); ?></li>
                <li class="list-group-item">Desconto: R$ <?php echo number_format($funcionario->calculaDesconto(), 2, ',', '.'); ?></li>
            </ul>
            <p class="mt-3">Fato sobre a data de nascimento: <?php echo $factAboutDate; ?></p>
        <?php endif; ?>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
