# Análise de Vendas no Varejo – Projeto SQL

## Visão Geral do Projeto

**Título do Projeto**: Análise de Vendas no Varejo
**Database**: `sql-project-p1`

Este projeto foi desenvolvido para demonstrar habilidades e técnicas em SQL, comumente utilizadas por analistas de dados para explorar, limpar e analisar dados de vendas no varejo. Ele envolve a criação de um banco de dados, análise exploratória (EDA) e respostas a perguntas de negócios por meio de consultas SQL.

## Objetivos

1. **Criar um banco de dados de vendas no varejo**: Criar e popular o banco com os dados fornecidos.
2. **Limpeza de Dados**: Identificar e remover registros com valores nulos.
3. **Análise Exploratória (EDA)**: Compreender a estrutura e características do conjunto de dados.
4. **Análise de Negócio**: Responder a perguntas estratégicas com consultas SQL.

## Estrutura do Projeto

### 1. Configuração do Banco de Dados

- **O projeto começa com a criação de um banco de dados chamado `sql-project-p1`.
- **Criação da Tabela**: Uma tabela chamada `retail_sales` é criada para armazenar os dados de vendas. A estrutura da tabela inclui colunas para ID da transação, data da venda, hora da venda, ID do cliente, gênero, idade, categoria do produto, quantidade vendida, preço por unidade, custo das mercadorias vendidas (COGS) e valor total da venda.


```sql
CREATE DATABASE sql-project-p1;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Exploração e Limpeza dos Dados

- **Quantidade de Registros**: Quantidade total de registros.
- **Clientes Únicos**: Quantidade de clientes distintos.
- **Categorias**: Lista de categorias únicas de produtos.
- **Valores Nulos**: Verificar e remover registros com campos NULL.

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

### 3. Análise de Dados e Resultados

1. **Escreva uma consulta SQL para recuperar todas as colunas das vendas realizadas em '2022-11-05'**:
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **Escreva uma consulta SQL para recuperar todas as transações em que a categoria seja 'Clothing' (Roupas) e a quantidade vendida seja maior que 4 no mês de novembro de 2022**:
```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity >= 4
```

3. **Escreva uma consulta SQL para calcular as vendas totais (total_sale) para cada categoria**:
```sql
SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY 1
```

4. **Escreva uma consulta SQL para encontrar a idade média dos clientes que compraram produtos da categoria 'Beauty' (Beleza)**:
```sql
SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty'
```

5. **Escreva uma consulta SQL para encontrar todas as transações em que o valor total da venda (total_sale) seja maior que 1000**:
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000
```

6. **Escreva uma consulta SQL para encontrar o número total de transações (transaction_id) realizadas por cada gênero em cada categoria**:
```sql
SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM retail_sales
GROUP 
    BY 
    category,
    gender
ORDER BY 1
```

7. **Escreva uma consulta SQL para calcular a média de vendas de cada mês. Descubra qual foi o mês com maior média de vendas em cada ano**:
```sql
SELECT 
       year,
       month,
    avg_sale
FROM 
(    
SELECT 
    EXTRACT(YEAR FROM sale_date) as year,
    EXTRACT(MONTH FROM sale_date) as month,
    AVG(total_sale) as avg_sale,
    RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales
GROUP BY 1, 2
) as t1
WHERE rank = 1
```

8. **Escreva uma consulta SQL para encontrar os 5 principais clientes com base no maior valor total de vendas**:
```sql
SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```

9. **Escreva uma consulta SQL para encontrar o número de clientes únicos que compraram produtos de cada categoria**:
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category
```

10. **Escreva uma consulta SQL para criar os turnos do dia (exemplo: manhã < 12h, tarde entre 12h e 17h, noite > 17h) e calcular o número de pedidos por turno**:
```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
```

## Descobertas

- **Demografia dos Clientes**: O conjunto de dados inclui clientes de várias faixas etárias, com vendas distribuídas entre diferentes categorias, como Roupas e Beleza.
- **Transações de Alto Valor**: Diversas transações tiveram valor total de venda acima de 1000, indicando compras de alto padrão.
- **Tendências de Vendas**: A análise mensal revela variações nas vendas, ajudando a identificar os períodos de maior movimento.
- **Insights sobre os Clientes**: A análise identifica os clientes que mais gastam e as categorias de produtos mais populares.

## Conclusão

Este projeto explora de forma prática diversas etapas fundamentais do trabalho com dados — desde a criação e estruturação do banco, passando pela limpeza e análise exploratória, até a elaboração de consultas SQL voltadas para a resolução de problemas de negócio. As descobertas geradas a partir das análises contribuem diretamente para a tomada de decisões estratégicas, oferecendo uma visão aprofundada sobre padrões de vendas, perfil dos clientes e desempenho dos produtos.

