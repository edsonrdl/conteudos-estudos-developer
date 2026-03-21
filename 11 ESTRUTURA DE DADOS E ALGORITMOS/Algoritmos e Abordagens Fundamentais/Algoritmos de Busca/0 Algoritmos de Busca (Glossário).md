# 📚 Guia de Estudos: Algoritmos de Busca

> [!info] Visão Geral
> Algoritmos de busca são métodos computacionais desenhados para localizar um elemento específico dentro de uma estrutura de dados. A escolha arquitetural de qual algoritmo utilizar baseia-se num *trade-off* rigoroso entre a Complexidade de Tempo (Big-O), a necessidade de ordenação prévia dos dados e o consumo de memória. Entender o funcionamento interno destas buscas é o alicerce para otimizar queries em bancos de dados, desenhar caches eficientes e rotear mensagens em sistemas distribuídos.

---

## 1 Domínio: Busca Básica e Sequencial

### 1.1 Busca Linear (Sequential Search)
- **1.1.1. Funcionamento Interno e Iteração**
  - 1.1.1.1. **Mecânica:** O algoritmo itera elemento por elemento, do início ao fim da estrutura de dados, até encontrar o valor desejado ou atingir o final da coleção.
  - 1.1.1.2. **Complexidade (Big-O):** Tempo de Pior Caso é O(n), onde `n` é o número total de elementos. O consumo de memória (Espaço) é O(1), pois não requer estruturas auxiliares.
- **1.1.2. Aplicação em Produção**
  - 1.1.2.1. **Cenário Ideal:** Estruturas de dados não ordenadas (onde o custo de ordenar antes de buscar seria maior que O(n)), fluxos de dados contínuos (*streams* que não podem ser indexados no momento) ou coleções muito pequenas onde o *overhead* de algoritmos complexos não se justifica.
  - 1.1.2.2. **Custo Computacional:** Extremamente ineficiente para grandes volumes de dados. Uma busca linear numa tabela de 10 milhões de registos sem índice resulta num *Full Table Scan*, o que destrói a performance do I/O do banco de dados.

---

## 2 Domínio: Busca Logarítmica (Dividir para Conquistar)

### 2.1 Busca Binária (Binary Search)
- **2.1.1. Mecânica de Redução de Espaço**
  - 2.1.1.1. **Pré-requisito Crítico:** A estrutura de dados **deve** estar previamente ordenada (ex: arrays numéricos, índices B-Tree em bases de dados).
  - 2.1.1.2. **Funcionamento:** O algoritmo compara o valor alvo com o elemento central da coleção. Se o alvo for menor, descarta a metade superior; se for maior, descarta a metade inferior. O processo repete-se recursivamente ou iterativamente.
  - 2.1.1.3. **Complexidade (Big-O):** O(log n). Um array com 1 milhão de elementos ordenados requer no máximo ~20 comparações para encontrar qualquer valor.
- **2.1.2. Engenharia e Casos Extremos (Under the Hood)**
  - 2.1.2.1. **O Bug do Integer Overflow:** Implementações juniores calculam o meio como `mid = (low + high) / 2`. Em arrays gigantes (próximos ao limite de 2 bilhões do `int` em Java/C#), a soma `low + high` excede o limite do tipo primitivo, gerando um valor negativo e lançando um `ArrayIndexOutOfBoundsException`.
  - 2.1.2.2. **A Solução Arquitetural:** O cálculo profissional para encontrar o meio, que evita o overflow, é `mid = low + ((high - low) / 2)` ou o uso de *bit shifting* (`mid = (low + high) >>> 1`).

---

## 3 Domínio: Busca por Transformação de Chave (Acesso Direto)

### 3.1 Hashing (Tabelas Hash / Dicionários)
- **3.1.1. Matemática de Mapeamento na Memória**
  - 3.1.1.1. **Mecânica:** Em vez de "procurar", o algoritmo calcula matematicamente *onde* o dado deveria estar. Passa-se a chave de busca (ex: um e-mail) por uma **Função Hash**, que devolve um índice numérico. O algoritmo vai diretamente a esse índice de memória no array subjacente.
  - 3.1.1.2. **Complexidade (Big-O):** No melhor caso e na média, o tempo é O(1) (Tempo Constante). Não importa se há 10 ou 10 milhões de itens, o tempo de busca é praticamente o mesmo.
- **3.1.2. O Problema das Colisões e Degradação**
  - 3.1.2.1. **A Falha Inevitável:** Duas chaves diferentes podem gerar o mesmo índice numérico (Colisão).
  - 3.1.2.2. **Resolução por Encadeamento (Chaining):** Quando ocorre colisão, o índice do array não guarda apenas um valor, mas sim o ponteiro para uma Lista Ligada (*Linked List*). O algoritmo calcula o O(1) para achar o índice, e depois faz um O(n) na lista ligada menor.
  - 3.1.2.3. **Otimização Moderna (ex: Java 8+):** Se muitas colisões ocorrerem no mesmo índice (degradando a performance), a JVM converte automaticamente a Lista Ligada numa Árvore Red-Black (Red-Black Tree), reduzindo o pior caso de O(n) para O(log n).
  - 3.1.2.4. **Aplicação Real:** Caches distribuídos de altíssima performance (Redis, Memcached), session storages e indexação em memória primária.

---

## 4 Domínio: Busca em Estruturas Não-Lineares (Grafos e Árvores)

### 4.1 Busca em Largura (Breadth-First Search - BFS)
- **4.1.1. Expansão em Camadas**
  - 4.1.1.1. **Mecânica:** Explora o grafo/árvore nível por nível, visitando primeiro todos os vizinhos diretos (profundidade 1) antes de descer para os vizinhos dos vizinhos (profundidade 2).
  - 4.1.1.2. **Motor Interno (Fila):** Implementado obrigatoriamente utilizando uma estrutura de Fila (Queue - FIFO: First In, First Out) para guardar os nós a visitar a seguir.
  - 4.1.1.3. **Aplicação Prática:** Encontrar a rota mais curta em sistemas de GPS não-ponderados, motores de busca mapeando *crawlers* na web (links a 1 clique de distância), ou encontrar graus de separação em redes sociais (LinkedIn).

### 4.2 Busca em Profundidade (Depth-First Search - DFS)
- **4.1.1. Mergulho Profundo e Retrocesso (Backtracking)**
  - 4.1.1.1. **Mecânica:** O algoritmo vai o mais fundo possível num ramo do grafo até chegar a um beco sem saída (nó folha). Só então ele volta (faz o *backtrack*) para explorar os caminhos que deixou para trás.
  - 4.1.1.2. **Motor Interno (Pilha/Recursão):** Implementado nativamente usando a *Call Stack* do sistema (Recursão) ou estruturalmente com uma Pilha (Stack - LIFO: Last In, First Out).
  - 4.1.1.3. **Aplicação Prática:** Resolver labirintos, análise de dependências complexas (ex: ordenação topológica para descobrir a ordem correta de construir microsserviços onde um depende do outro), e varredura completa do sistema de arquivos de um Sistema Operacional.