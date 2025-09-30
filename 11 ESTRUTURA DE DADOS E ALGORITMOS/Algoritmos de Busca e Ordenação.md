

Esses são os mais fundamentais. A escolha errada aqui pode impactar drasticamente a performance de uma aplicação.

- **Busca Linear (Linear Search)**
    
    - **Finalidade:** Encontrar um item em uma coleção percorrendo-a do início ao fim.
        
    - **Implicação para o Arquiteto:** É o método mais simples, mas ineficiente (O(n)). Um arquiteto deve reconhecer quando seu uso em grandes volumes de dados pode causar lentidão e latência, indicando a necessidade de uma estrutura de dados mais adequada (como um mapa hash) ou de pré-ordenação.
        
- **Busca Binária (Binary Search)**
    
    - **Finalidade:** Encontrar um item em uma coleção **já ordenada**, dividindo repetidamente a coleção pela metade.
        
    - **Implicação para o Arquiteto:** Extremamente eficiente (O(logn)). A implicação é que, para sistemas que realizam muitas buscas, o custo de manter uma coleção ordenada (com inserts/updates mais lentos) pode ser justificado pela velocidade das consultas. Isso influencia o design de índices em bancos de dados e buscas em memória.
        
- **Quick Sort / Merge Sort**
    
    - **Finalidade:** Algoritmos de ordenação eficientes.
        
    - **Implicação para o Arquiteto:** Representam a "base" de performance para ordenação (O(nlogn)). Um arquiteto deve saber que a ordenação de grandes datasets é uma operação custosa e deve ser evitada em tempo real se possível. Se for necessária, deve garantir que a implementação subjacente (da linguagem ou framework) use um algoritmo eficiente como estes.
        

### 2. Algoritmos em Estruturas de Dados

A forma como os dados são organizados define quais operações são rápidas ou lentas.

- **Hash Tables (Tabelas de Dispersão)**
    
    - **Finalidade:** Armazenar e recuperar dados em tempo (praticamente) constante (O(1)) usando um par chave-valor. É a base para Dicionários, Mapas e Sets.
        
    - **Implicação para o Arquiteto:** **Fundamental.** É a base para projetar **caches** (ex: Redis), indexação de dados, e qualquer cenário que precise de buscas ultrarrápidas por uma chave única. Entender o conceito de colisões de hash ajuda a prever casos de degradação de performance.
        
- **Árvores (Trees) - Especialmente B-Trees**
    
    - **Finalidade:** Estrutura de dados que organiza os dados de forma hierárquica e ordenada.
        
    - **Implicação para o Arquiteto:** As **B-Trees** são a base de praticamente todos os **índices de bancos de dados relacionais (SQL)**. Saber disso ajuda o arquiteto a projetar esquemas de banco de dados eficientes, entender o custo de criar um índice e por que algumas consultas são rápidas e outras não.
        
- **Filas (Queues) e Pilhas (Stacks)**
    
    - **Finalidade:** Gerenciar coleções de itens onde a ordem de entrada/saída é importante (FIFO - First-In, First-Out para filas; LIFO - Last-In, First-Out para pilhas).
        
    - **Implicação para o Arquiteto:** Filas são a base de toda a **comunicação assíncrona** e sistemas de mensageria (ex: RabbitMQ, SQS). Um arquiteto usa esse conceito para projetar sistemas resilientes, escaláveis e desacoplados, onde tarefas podem ser enfileiradas para processamento posterior.
        

### 3. Algoritmos de Grafos

Essenciais para modelar redes e relacionamentos.

- **Busca em Largura (BFS) e Busca em Profundidade (DFS)**
    
    - **Finalidade:** Percorrer ou buscar em uma estrutura de grafo ou árvore.
        
    - **Implicação para o Arquiteto:** Usado em "crawlers" de sites, análise de redes sociais (ex: encontrar todas as conexões de uma pessoa até um certo nível), resolução de dependências (ex: em gerenciadores de pacotes) e pathfinding (encontrar um caminho).
        
- **Algoritmo de Dijkstra / A***
    
    - **Finalidade:** Encontrar o caminho mais curto entre dois pontos em um grafo ponderado.
        
    - **Implicação para o Arquiteto:** Base para qualquer sistema de roteamento, desde o Google Maps e Waze até o roteamento de pacotes de dados em redes de computadores.
        

### 4. Algoritmos de Hashing e Criptografia

Fundamentais para segurança e integridade de dados.

- **Hashing (SHA-256, bcrypt, scrypt)**
    
    - **Finalidade:** Transformar uma entrada de dados em uma saída de tamanho fixo de forma (praticamente) irreversível.
        
    - **Implicação para o Arquiteto:** **Crucial para segurança.** Usado para armazenar senhas de forma segura (armazenamos o hash, não a senha), garantir a integridade de arquivos (checksums) e como base para outras estruturas como Hash Tables e Blockchains. O arquiteto deve saber a diferença entre algoritmos rápidos (SHA-256) e lentos (bcrypt), escolhendo o segundo para senhas.
        
- **Criptografia Simétrica (AES) e Assimétrica (RSA)**
    
    - **Finalidade:** Criptografar (codificar) e descriptografar (decodificar) dados para garantir confidencialidade.
        
    - **Implicação para o Arquiteto:** Essencial para proteger dados em trânsito (HTTPS/TLS usa ambos) e em repouso (criptografia de banco de dados). O arquiteto define quais dados precisam ser criptografados e qual abordagem (simétrica ou assimétrica) é adequada para cada caso de uso.
        

### 5. Algoritmos Distribuídos

Com a ascensão de microsserviços e sistemas em nuvem, estes são cada vez mais importantes.

- **Algoritmos de Consenso (Paxos, Raft)**
    
    - **Finalidade:** Permitir que um grupo de computadores em um sistema distribuído chegue a um acordo sobre um valor, mesmo com falhas na rede ou em alguns nós.
        
    - **Implicação para o Arquiteto:** Base para a construção de sistemas distribuídos confiáveis e tolerantes a falhas, como bancos de dados (ex: CockroachDB), sistemas de coordenação (ex: Zookeeper, etcd) e sistemas de filas.
        
- **Hashing Consistente (Consistent Hashing)**
    
    - **Finalidade:** Distribuir dados ou requisições entre um conjunto de servidores de forma que, ao adicionar ou remover um servidor, a quantidade de dados a serem remanejados seja mínima.
        
    - **Implicação para o Arquiteto:** Fundamental para projetar **caches distribuídos** e **bancos de dados NoSQL** (como Cassandra e DynamoDB) que sejam escaláveis horizontalmente.