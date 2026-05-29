### **Formas de Usar Subconsultas**

### 1. **Subconsulta Escalar** (ou Subconsulta em uma única linha)

Uma subconsulta escalar retorna **apenas um valor** (uma linha e uma coluna). Ela pode ser usada em qualquer lugar onde você utilizaria um valor individual, como em cláusulas `SELECT`, `WHERE`, ou `HAVING`.

**Exemplo**:

```sql
-- Selecionando o nome do cliente com o maior valor de pedido
SELECT nome_cliente
FROM clientes
WHERE id = (
    SELECT cliente_id
    FROM pedidos
    ORDER BY valor DESC
    LIMIT 1
);

```

- **Uso**: Retornar um único valor para ser usado em uma condição.
- **Quando usar**: Quando você precisa de um valor único para ser comparado com os registros da consulta externa.

---

### 2. **Subconsulta de Coluna** (ou Subconsulta em uma lista de valores)

Uma subconsulta de coluna retorna **uma lista de valores**. Ela pode ser usada em cláusulas `IN`, `NOT IN`, `ANY`, `ALL`.

**Exemplo**:

```sql
-- Selecionando clientes que fizeram pedidos no ano de 2025
SELECT nome_cliente
FROM clientes
WHERE id IN (
    SELECT cliente_id
    FROM pedidos
    WHERE EXTRACT(YEAR FROM data_pedido) = 2025
);

```

- **Uso**: Comparar valores de uma coluna com um conjunto de resultados da subconsulta.
- **Quando usar**: Quando você quer filtrar os registros da consulta externa com base em um conjunto de valores de outra tabela.

---

### 3. **Subconsulta Correlacionada**

Uma subconsulta correlacionada é uma subconsulta que **depende** dos valores da consulta externa. Cada linha da consulta externa é usada para comparar com a subconsulta interna. Esse tipo de subconsulta é executado **para cada linha** da consulta externa.

**Exemplo**:

```sql
-- Selecionando clientes que têm pedidos cujo valor é maior que a média dos pedidos
SELECT nome_cliente
FROM clientes c
WHERE EXISTS (
    SELECT 1
    FROM pedidos p
    WHERE p.cliente_id = c.id
    GROUP BY p.cliente_id
    HAVING AVG(p.valor) > 1000
);

```

- **Uso**: Quando você precisa que a subconsulta dependa de cada linha da consulta externa.
- **Quando usar**: Quando a condição da subconsulta envolve um valor calculado ou um filtro dinâmico baseado em cada linha da consulta externa.

---

### 4. **Subconsulta em `FROM` (ou Subconsulta de Tabela)**

Uma subconsulta no `FROM` cria uma **tabela temporária** que pode ser usada pela consulta externa, similar a uma tabela derivada. O resultado da subconsulta é tratado como uma tabela que pode ser referenciada na consulta externa.

**Exemplo**:

```sql
-- Selecionando a soma dos valores de pedidos por cliente, considerando apenas os pedidos com valor maior que 500
SELECT cliente_id, SUM(valor) AS total_pedidos
FROM (
    SELECT cliente_id, valor
    FROM pedidos
    WHERE valor > 500
) AS pedidos_filtrados
GROUP BY cliente_id;

```

- **Uso**: Quando você precisa aplicar uma operação em um conjunto de dados antes de realizar uma operação principal.
- **Quando usar**: Quando a subconsulta pode ser tratada como uma tabela intermediária para ser manipulada pela consulta externa.

---

### **Melhores Formas de Usar Subconsultas**

Agora que vimos as formas básicas de usar subconsultas, vamos analisar as **melhores práticas** para utilizá-las de forma eficiente.

---

### **1. Evitar Subconsultas Desnecessárias**

Sempre que possível, evite subconsultas em casos simples. As subconsultas podem ser poderosas, mas muitas vezes há alternativas mais simples e eficientes. Por exemplo, um `JOIN` pode ser mais eficiente do que uma subconsulta em muitos casos.

**Exemplo:**

- **Subconsulta**:
    
    ```sql
    SELECT nome_cliente
    FROM clientes
    WHERE id IN (
        SELECT cliente_id
        FROM pedidos
        WHERE EXTRACT(YEAR FROM data_pedido) = 2025
    );
    ```
    
- **Alternativa com `JOIN`**:
    
    ```sql
    SELECT DISTINCT c.nome_cliente
    FROM clientes c
    JOIN pedidos p ON c.id = p.cliente_id
    WHERE EXTRACT(YEAR FROM p.data_pedido) = 2025;
    ```
    
    O `JOIN` pode ser mais eficiente do que usar uma subconsulta com `IN`, especialmente em grandes volumes de dados.
    

---

### **2. Subconsultas Correlacionadas: Use com Cautela**

As subconsultas correlacionadas podem ser muito poderosas, mas também são mais **caras** em termos de performance, pois são executadas para **cada linha** da consulta externa.

- **Evite subconsultas correlacionadas em grandes tabelas** sempre que possível, pois elas podem resultar em muitos acessos ao banco de dados e diminuir a performance. Em vez disso, considere a utilização de `JOINs` ou agregações.

**Exemplo de subconsulta correlacionada:**

```sql
-- Selecionando clientes que têm pedidos cujo valor é maior que a média
SELECT nome_cliente
FROM clientes c
WHERE EXISTS (
    SELECT 1
    FROM pedidos p
    WHERE p.cliente_id = c.id
    GROUP BY p.cliente_id
    HAVING AVG(p.valor) > 1000
);

```

Em vez disso, poderia ser reescrito com `JOIN` e agregação, o que pode ser mais eficiente:

```sql
SELECT c.nome_cliente
FROM clientes c
JOIN pedidos p ON p.cliente_id = c.id
GROUP BY c.id
HAVING AVG(p.valor) > 1000;

```

---

### **3. Evitar Subconsultas em `SELECT`**

Evite usar subconsultas dentro da cláusula `SELECT`, especialmente quando elas retornam resultados grandes. Isso pode causar um grande impacto de performance.

**Evite:**

```sql
SELECT nome_cliente,
       (SELECT COUNT(*) FROM pedidos p WHERE p.cliente_id = c.id) AS total_pedidos
FROM clientes c;

```

**Alternativa com `JOIN` e `GROUP BY`:**

```sql
SELECT c.nome_cliente, COUNT(p.id) AS total_pedidos
FROM clientes c
LEFT JOIN pedidos p ON p.cliente_id = c.id
GROUP BY c.id;

```

---

### **4. Use Subconsultas em `FROM` para Criar Tabelas Temporárias**

As subconsultas no `FROM` são úteis para criar tabelas temporárias, onde você pode fazer manipulações adicionais sem afetar as tabelas originais.

**Exemplo:**

``` sql
SELECT cliente_id, SUM(valor) AS total_vendas
FROM (
    SELECT cliente_id, valor
    FROM pedidos
    WHERE valor > 500
) AS pedidos_filtrados
GROUP BY cliente_id;

```

**Vantagem**: Em vez de repetir a lógica de filtragem de dados na consulta principal, você usa uma subconsulta para tratar os dados como uma tabela intermediária, o que pode ser mais claro e eficiente em alguns casos.

---

### **5. Use `EXISTS` para Verificação de Existência**

Se você deseja verificar a **existência** de registros sem precisar dos dados da subconsulta, use `EXISTS`. Isso é especialmente útil para melhorar a performance em consultas complexas, pois o banco de dados pode parar a execução da subconsulta assim que encontrar um registro.

**Exemplo**:

```sql
-- Verificando clientes que têm pedidos
SELECT * FROM clientes c
WHERE EXISTS (
    SELECT 1
    FROM pedidos p
    WHERE p.cliente_id = c.id
);

```

---

### **Conclusão**

- **Melhores práticas para subconsultas**:
    1. **Evite subconsultas desnecessárias**: Prefira `JOINs` em vez de subconsultas sempre que possível.
    2. **Use `EXISTS` para verificar a existência**: Quando você não precisa dos dados da subconsulta, apenas a existência de registros, use `EXISTS`.
    3. **Subconsultas correlacionadas**: Use com cautela em grandes tabelas. Elas podem ser ineficientes.
    4. **Use subconsultas no `FROM` para criar tabelas temporárias**: Isso pode ser útil quando você precisa manipular dados temporários de forma clara.
    5. **Evite subconsultas em `SELECT`**: Se possível, use `JOINs` e agregações para evitar sobrecarregar o banco de dados.