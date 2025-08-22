O **Garbage Collector (GC)** é um mecanismo de gerenciamento de memória que automatiza a **limpeza de objetos não utilizados** na memória de uma aplicação. Ele tem o papel de identificar e liberar a memória ocupada por objetos que não são mais acessados, evitando vazamentos de memória e melhorando a performance do sistema.

Aqui está uma visão detalhada do **Garbage Collector**:

### O que é o Garbage Collector?

O GC é um componente de gerenciamento de memória presente na maioria das linguagens de programação modernas, como Java, Python, C#, etc. Ele **automáticamente gerencia a alocação e desalocação de memória** de objetos, sem que o programador precise se preocupar diretamente com isso.

O GC identifica quando um objeto não é mais utilizado e, assim, pode liberar a memória ocupada por ele. Isso é importante para evitar **vazamentos de memória**, que ocorrem quando a memória não é liberada adequadamente.

### Como funciona o Garbage Collector?

1. **Identificação de objetos não referenciados**:
    - O GC rastreia todos os objetos criados em memória.
    - Ele verifica se esses objetos são ainda "acessíveis" por outras partes do código.
    - Quando um objeto não é mais acessível, ele é considerado **inutilizável** e, portanto, pode ser removido.
2. **Ciclos de coleta de lixo**:
    - O processo de coleta de lixo é executado periodicamente, mas seu momento e frequência dependem de diferentes algoritmos de GC.
    - **Geração de objetos**: O GC muitas vezes divide os objetos em **gerações**. A ideia é que objetos jovens (recém-criados) são mais propensos a morrer rapidamente, enquanto objetos mais antigos (de longa duração) permanecem mais tempo. Esse modelo é utilizado no algoritmo **Generational Garbage Collection**.
3. **Algoritmos de GC**:
    - **Mark and Sweep**: Marca todos os objetos acessíveis e remove os objetos não marcados.
    - **Stop-the-world**: O GC pode parar a execução do programa enquanto faz a coleta de lixo.
    - **Generational GC**: Divide a memória em diferentes gerações (nova, velha) para otimizar a coleta de objetos.

![[img-garbage-collector.png]]
### Tipos de Garbage Collectors:

1. **Serial GC**: Coleta a memória de forma simples, adequada para aplicações menores.
2. **Parallel GC**: Usa múltiplas threads para realizar a coleta de lixo em paralelo, melhorando a performance em máquinas com múltiplos núcleos.
3. **CMS (Concurrent Mark-Sweep)**: Foca em minimizar a pausa de coleta, trabalhando de forma concorrente com o programa em execução.
4. **G1 GC (Garbage First)**: Otimiza o tempo de pausa e a coleta, ideal para grandes heaps (memórias) e com foco em performance de latência.

5. **GC Root**:
    
    - **GC Root** são referências acessíveis diretamente pelo código, como variáveis locais, variáveis estáticas, threads e outros objetos ativos. Eles servem como **pontos de partida** para o **Garbage Collector** identificar objetos "vivos", ou seja, objetos que ainda podem ser acessados e que não serão coletados.
    -  **GC Root** (raiz do garbage collector) está no topo da imagem, representando os pontos iniciais de acesso aos objetos que o GC irá rastrear.
    - Eles são os **pontos de partida** que o coletor de lixo usa para identificar quais objetos ainda são acessíveis, ou seja, referenciados diretamente.
    - **Relação**: O GC Root é o ponto inicial de rastreamento que ajuda o **GC** a identificar **quais objetos estão vivos** e, portanto, quais objetos devem ser preservados na memória. Sem o GC Root, o GC não saberia por onde começar a busca.
        
6. **Stack**:
    
    - A **stack** (pilha) é onde as **referências locais** para objetos são armazenadas, junto com o controle de execução (como os **endereços de retorno** de métodos). Ela é usada para gerenciar chamadas de métodos e variáveis locais.
        
    - **Relação com GC Root**: Os objetos referenciados nas variáveis locais da **stack** podem ser acessados diretamente a partir do **GC Root**. Quando a execução de um método termina e o escopo da variável local sai de escopo, a referência é eliminada e o objeto pode ser elegível para coleta de lixo, se não for mais acessado por outras referências.
        
7. **Heap**:
    
    - O **heap** é onde os **objetos dinâmicos** são alocados durante a execução do programa. Ele é dividido em várias partes, incluindo a **Young Generation** e a **Old Generation**. O **Eden** é uma parte da **Young Generation**.
    - **Young Generation**:
        
    - A **Young Generation** é onde os objetos recém-criados são armazenados. Ela é dividida em três partes:
        
        - **Eden**: Onde os objetos são inicialmente alocados.
        - - O **Eden** é uma parte da **Young Generation** da **heap** onde os objetos recém-criados são alocados inicialmente. Quando ocorre a **coleta de lixo (Minor GC)**, o **Eden** é limpo, e os objetos que sobreviverem são movidos para os **Survivor Spaces** e, eventualmente, para a **Old Generation** se sobreviverem por várias coletas.
	    - **Relação com o GC Root**: O GC Root ajuda a identificar se os objetos na **Young Generation**, especificamente no **Eden**, estão sendo referenciados e devem ser mantidos ou se podem ser coletados.
        - **S0** e **S1**: Espaços usados durante a coleta de lixo (normalmente alternados entre si).
        
    - **Relação com o GC Root e Stack**: A **heap** contém os objetos alocados dinamicamente, e o GC começa a partir do GC Root para identificar quais objetos na heap estão sendo usados. Os objetos referenciados pela **stack** ou outras referências da **GC Root** são marcados como acessíveis e não serão coletados, mesmo que estejam na **heap**.
        
    - **Old Generation**:
    - A **Old Generation** é onde os objetos que sobreviveram várias coletas de lixo na **Young Generation** são movidos. Eles são considerados de longa duração, e a coleta na Old Generation é mais cara em termos de performance.


---

### Linha do Tempo de um Objeto no Processo de Garbage Collection:

Abaixo está uma linha do tempo de como um objeto passa por diferentes fases dentro da memória heap, incluindo a interação com o **GC Root**, **Stack**, **Heap** e **Eden**.

1. **Criação do Objeto**:
    
    - O objeto é criado e alocado na **Young Generation**, especificamente no **Eden**.
        
    - **Stack**: Uma referência local para esse objeto pode ser criada na **stack** (variável local).
        
    - **GC Root**: A referência à variável local na **stack** pode se tornar parte do **GC Root**, pois a variável local está acessível a partir do GC.
        
2. **Durante a Execução**:
    
    - O objeto reside na **Young Generation (Eden)**. A cada execução de um **Minor GC**, o objeto que não for referenciado será coletado.
        
    - Se o objeto **sobrevive** ao **Minor GC**, ele será movido para um **Survivor Space (S0 ou S1)**.
        
    - Se o objeto continua sendo referenciado e sobreviver por várias coletas, ele pode ser promovido para a **Old Generation**, caso o GC determine que ele tem uma **vida longa**.
        
3. **Quando o Objeto é Coletado**:
    
    - Quando o objeto deixa de ser referenciado (não há mais ponteiros do **GC Root** ou **stack** apontando para ele), ele se torna elegível para coleta de lixo. O **GC** pode coletá-lo durante a próxima execução de um **Minor GC** ou **Major GC**.
        
4. **Promovendo o Objeto**:
    
    - Após várias coletas de lixo e se o objeto sobrevive a várias execuções de **Minor GC**, ele é promovido para a **Old Generation**.
        
    - Objetos na **Old Generation** são limpos durante o **Major GC**, que é mais caro em termos de performance. A coleta na **Old Generation** verifica se o objeto está acessível através do **GC Root**.
        
5. **Fim da Vida do Objeto**:
    
    - Se, após várias coletas de lixo, o objeto não for mais acessado e não houver referências no **GC Root** ou na **stack**, ele será finalmente **coletado** e sua memória será liberada.
        

### Resumo:

- **GC Root**: Referências acessíveis diretamente, usadas para marcar objetos "vivos".
    
- **Stack**: Onde as variáveis locais que referenciam objetos são armazenadas, facilitando a rastreabilidade pelo **GC Root**.
    
- **Heap**: Onde os objetos são armazenados. A **Young Generation** (e **Eden**) é a área onde objetos recém-criados começam.
    
- **Eden**: A primeira área onde os objetos são alocados. Objetos que sobrevivem a **coletas menores** são movidos para **Survivor Spaces** ou promovidos para a **Old Generation**.



