# atvmega




<?php
// Função para validar o nome
function validarNome($nome) {
    if (strlen($nome) < 2 || !preg_match("/^[a-zA-ZÀ-ÿ\s]+$/", $nome)) {
        return false;
    }
    return true;
}

// Definir os jogos disponíveis
$jogos = [
    "Mega-Sena" => ["min" => 6, "max" => 20, "preco" => 4.50, "numeros" => 60],
    "Quina" => ["min" => 5, "max" => 15, "preco" => 1.50, "numeros" => 80],
    "Lotomania" => ["min" => 50, "max" => 50, "preco" => 2.50, "numeros" => 100],
    "Lotofácil" => ["min" => 15, "max" => 18, "preco" => 2.00, "numeros" => 25]
];

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    // Coletar os dados do formulário
    $nome = $_POST['nome'];
    $jogoEscolhido = $_POST['jogo'];
    $qtdDezenas = $_POST['qtdDezenas'];
    $qtdApostas = $_POST['qtdApostas'];

    if (!validarNome($nome)) {
        echo "<p>Erro: O nome deve conter apenas letras e ter pelo menos 2 caracteres.</p>";
        exit;
    }

    // Selecionar o jogo escolhido
    $jogo = $jogos[$jogoEscolhido];

    // No caso da Lotomania, a quantidade de dezenas é fixada como 50.
    if ($jogoEscolhido == 'Lotomania') {
        $qtdDezenas = 50;
    }

    // Validar a quantidade de dezenas
    if ($qtdDezenas < $jogo['min'] || $qtdDezenas > $jogo['max']) {
        echo "<p>Erro: O número de dezenas deve estar entre {$jogo['min']} e {$jogo['max']}.</p>";
        exit;
    }

    // Função para gerar apostas
    function gerarAposta($jogo, $qtdDezenas) {
        $numeros = range(1, $jogo['numeros']);
        shuffle($numeros);
        $aposta = array_slice($numeros, 0, $qtdDezenas); 
        return $aposta;
    }

    // Exibir os resultados
    echo "<h3>Cadastro realizado com sucesso para: $nome</h3>";

    $total = 0;
    for ($i = 1; $i <= $qtdApostas; $i++) {
        $aposta = gerarAposta($jogo, $qtdDezenas);
        sort($aposta);
        echo "Aposta $i: " . implode(", ", $aposta) . "<br>";
        $total += $jogo['preco'];
    }

    echo "<p>Total gasto: R$ " . number_format($total, 2, ',', '.') . "</p>";
}
?>

<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cadastro e Apostas</title>
</head>
<body>

<h2>Cadastro de Apostas</h2>

<form method="POST">
    <label for="nome">Nome:</label><br>
    <input type="text" id="nome" name="nome" required><br><br>

    <label for="jogo">Escolha um jogo:</label><br>
    <select id="jogo" name="jogo">
        <option value="Mega-Sena">Mega-Sena</option>
        <option value="Quina">Quina</option>
        <option value="Lotomania">Lotomania</option>
        <option value="Lotofácil">Lotofácil</option>
    </select><br><br>

    <label for="qtdDezenas">Quantas dezenas você deseja jogar?</label><br>
    <input type="number" id="qtdDezenas" name="qtdDezenas" required min="5" max="50"><br><br>

    <label for="qtdApostas">Quantas apostas você deseja gerar?</label><br>
    <input type="number" id="qtdApostas" name="qtdApostas" required min="1"><br><br>

    <input type="submit" value="Gerar Apostas">
</form>

</body>
</html> 
