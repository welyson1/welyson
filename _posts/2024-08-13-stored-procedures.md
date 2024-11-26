---
title: "Stored Procedures"
date: 2024-08-13 00:00:00:0000
categories: [SQL]
tags: [sql]
---

Procedimentos armazenados (Stored Procedures) é a tradução desse conceito. **São conjuntos de instruções SQL que são armazenados no seu banco de dados e que podem ser executados com frequência.**

Em um Stored Procedure, você pode encapsular regras de inserção (`INSERT`), regras de atualização (`UPDATE`), regras de deleção (`DELETE`) e diversos outros conceitos. No caso do `SELECT`, ela é muito utilizada com parâmetros, você pode definir parâmetros na consulta de entrada e saída na consulta.

Recomendo concentrar seus esforços em **manipular lógicas mais complexas que são realizadas repetidamente.**

A maior diferença entre uma procedure e uma função é que a procedure você executa usando `EXEC`, ao contrário da função que você chama através de um `SELECT`.

Mencionei isso porque é exatamente nesse contexto que elas devem ser utilizadas. Muitos acabam se confundindo e criando Procedures para armazenar tabelas, não que isso esteja errado, mas existem opções específicas para lidar com isso.

**Os principais benefícios de se criar uma Stored Procedure são:**
- **Desempenho e pré-compilação:** Faço questão de adicionar esse no primeiro. Os procedimentos armazenados são pré-compilados e otimizados pelo SQL Server.
- **Segurança sempre:** Você pode restringir os acessos aos dados e tabelas, ele não escrever um `INSERT INTO` com algum erro, pode ter um validador de valores.
- **Manutenção:** Centralizam a lógica de negócios no banco de dados, vai facilitar uma manutenção centralizada.

**Sintaxe de criação:**

```sql
CREATE PROCEDURE nome_stored_procedure
    @parametro1 int,
    @parametro2 VARCHAR(50)
AS
BEGIN
    INSERT INTO tabela (id, descricao)
    VALUES (@parametro1, @parametro2)
END
```

**Sintaxe de execução:**

```sql
EXEC nome_stored_procedure 1, 'Descrição do novo produto'
```

## Criar uma Stored Procedure para Inserir um Novo Produto

```sql
CREATE PROCEDURE inserir_produto
    @produto VARCHAR(50),
    @descricao VARCHAR(100),
    @data_criacao DATE
AS
BEGIN
    INSERT INTO produtos (produto, descricao, data_criacao)
    VALUES (@produto, @descricao, @data_criacao);
END
```
Para inserir é só realizar o `EXEC` passando os parâmetros: `@produto VARCHAR(50)`, `descricao VARCHAR(100)` e `@data_criacao DATE`

```sql
EXEC inserir_produto 'Product F', 'Essa é a descrição do Produto F', '2023-05-01';
```

Se consultarmos a tabela `produtos` vamos ver o `Product F` adicionado:
- ![insert](/assets/images/2024-08-13-stored-procedures/insert.png)

Você pode melhorar e utilizar a sua procedure de várias maneiras como:
- Colocar um validador de data;
- Criar uma Trigger utilizando essa procedure;
- Adicionar a procedure em uma etapa do Job ETL ou pipeline;
- Retornar erros personalizados para o usuário: `TRY`, `CATCH`; 
- Criar transações no escopo da procedure: `TRANSACTION`, `COMMIT`, `ROLLBACK`.

Pode evitar muita dor de cabeça encapsulando essas lógicas.

## Criar uma Stored Procedure para Obter Vendas por Produto
Como eu mencionei, você pode criar uma procedure que retorne um resultado de uma consulta, o grande diferencial é você enviar um parâmetro.

```sql
CREATE PROCEDURE vendas_por_produto
    @produto_id int
AS
BEGIN
    SELECT id, date, valor
    FROM vendas
    WHERE produto_id = @produto_id
END
```

Para retornar a consullta você executa `EXEC` da mesma maneira:

```sql
EXEC vendas_por_produto 2
```
- *2 é product_id que enviamos para a consulta como um parâmetro.*

**Resultado:**
- ![select](/assets/images/2024-08-13-stored-procedures/select.png)

Você deve estar se perguntando: *"Qual eu uso agora? Views, Funções ou Procedures?"*

**Comparação com Views e Table-Valued Functions:**
- **Views:** Views são consultas podem ser reutilizadas como se fossem tabelas virtuais. A grande diferença é que não aceitam parâmetros na sua lógica.
- **Funções de Tabela (Table-Valued Functions):** Nesse caso, elas retornam uma tabela como resultado e podem aceitar parâmetros. As stored procedures são mais utilizadas em executar lógica de negócio complexa, enquanto as funções de tabela são mais eficientes para cálculos ou manipulações que retornam um conjunto de resultados.

## Criar uma Stored Procedure com Retorno de Valor
Isso é muito utilizado em pipelines e jobs ETL, muitas vezes precisamos de um valor da base de dados para preencher uma variável em nosso pipeline, nesses casos, podemos utilizar uma procedure.

```sql
CREATE PROCEDURE contar_produtos_vendidos_hoje
AS
BEGIN
    DECLARE 
		@contagem INT
    SELECT 
		@contagem = COUNT(*)
    FROM vendas
	WHERE date = GETDATE()
    RETURN @contagem
END
```
Depois é só executar `EXEC` e consultar o valor de retorno, pode fazer utilizando o `SELECT`, que retorna uma coluna, ou o `PRINT`, que retorna o valor printado na tela:

```sql
DECLARE @total INT

EXEC @total = contar_produtos_vendidos_hoje

SELECT @total [Total]

-- or

PRINT @total
```

## Atualizando e Removendo Procedures
Para alterar uma stored procedure, você pode usar a instrução `ALTER PROCEDURE`:

```sql
ALTER PROCEDURE contar_produtos_vendidos_hoje
AS
BEGIN
    DECLARE 
		@contagem INT
    SELECT 
		@contagem = COUNT(*)
    FROM vendas
    RETURN @contagem
END
```

Para excluir uma stored procedure, você pode usar a instrução `DROP PROCEDURE`:

```sql
DROP PROCEDURE contar_produtos_vendidos_hoje
```

Aconselho a focar seus esforços em criar lógicas de negócio com validadores: adicionar um `TRY`, `CATCH` para lidar com exceções, utilizar transações `TRANSACTION`, `COMMIT`, `ROLLBACK` e definir um escopo claro e fechado para a lógica de negócio.

Com Stored Procedures, o céu é o limite!

---

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
