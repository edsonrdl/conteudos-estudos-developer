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

### Tipos de Garbage Collectors:

1. **Serial GC**: Coleta a memória de forma simples, adequada para aplicações menores.
2. **Parallel GC**: Usa múltiplas threads para realizar a coleta de lixo em paralelo, melhorando a performance em máquinas com múltiplos núcleos.
3. **CMS (Concurrent Mark-Sweep)**: Foca em minimizar a pausa de coleta, trabalhando de forma concorrente com o programa em execução.
4. **G1 GC (Garbage First)**: Otimiza o tempo de pausa e a coleta, ideal para grandes heaps (memórias) e com foco em performance de latência.

### O que é o GC Root?

**GC Root** é um conceito importante dentro do **Garbage Collection**. Ele é o conjunto de referências **acessíveis diretamente** pelo programa, ou seja, **os pontos de partida** para o GC identificar quais objetos estão em uso.

Os **GC Roots** podem ser:

- **Variáveis locais**: Referências armazenadas em pilhas de chamadas (stack).
- **Variáveis estáticas**: Referências associadas a campos de classes.
- **Objetos ativos**: Objetos que são parte da execução do programa, como threads ou conexões de rede.
- **Objetos em registros nativos**: Como ponteiros em código nativo (JNI).

### O papel do GC Root

Os GC Roots são usados pelo GC para determinar quais objetos são acessíveis no gráfico de objetos. A partir desses pontos de referência, o GC começa a **marcar** todos os objetos acessíveis. Se um objeto não for alcançável a partir de nenhum GC Root, ele será considerado "não usado" e será removido.

### Exemplo:

Suponha que temos o seguinte código Java:

```java
java
CopiarEditar
class Exemplo {
    private String texto;

    public Exemplo(String texto) {
        this.texto = texto;
    }
}

public class Main {
    public static void main(String[] args) {
        Exemplo obj = new Exemplo("Garbage Collector");
    }
}

```

- Aqui, o **obj** é uma referência criada na **pilha** de execução (stack) no método `main`. Assim, o `obj` seria um **GC Root**, pois o GC pode acessar esse objeto diretamente.
- Se o objeto `obj` não fosse mais acessível (por exemplo, se a variável fosse apagada), o GC identificaria que o objeto pode ser coletado.

### Em resumo:

- **Garbage Collector (GC)** é responsável por liberar a memória de objetos não utilizados.
- **GC Root** são as referências acessíveis diretamente pelo código e são usadas pelo GC para identificar quais objetos podem ser removidos da memória.

---

![[img-garbage-collector.png]]
1. **GC Root**:
    - **GC Root** (raiz do garbage collector) está no topo da imagem, representando os pontos iniciais de acesso aos objetos que o GC irá rastrear.
    - Eles são os **pontos de partida** que o coletor de lixo usa para identificar quais objetos ainda são acessíveis, ou seja, referenciados diretamente.
2. **GC Roots Set**:
    - A coleção de objetos que são diretamente acessados a partir do **GC Root**. O GC começa a partir desses objetos para "marcar" todos os objetos acessíveis.
3. **Heap Memory**:
    - A memória heap é onde os objetos são alocados dinamicamente em tempo de execução. Ela é dividida em diferentes gerações, com o objetivo de otimizar a coleta de lixo.
4. **Young Generation**:
    - A **Young Generation** é onde os objetos recém-criados são armazenados. Ela é dividida em três partes:
        - **Eden**: Onde os objetos são inicialmente alocados.
        - **S0** e **S1**: Espaços usados durante a coleta de lixo (normalmente alternados entre si).
5. **Old Generation**:
    - A **Old Generation** é onde os objetos que sobreviveram várias coletas de lixo na **Young Generation** são movidos. Eles são considerados de longa duração, e a coleta na Old Generation é mais cara em termos de performance.
6. **Permanent Generation**:
    - Essa parte da memória era usada para armazenar metadados e classes em versões antigas do Java. Em versões mais recentes, isso foi substituído pela **Metaspace**.

### Como o Garbage Collector usa essas gerações?

O GC usa um modelo de **coleta por gerações**, onde ele trata de diferentes regiões da heap de maneiras diferentes:

- **Young Generation** (incluindo **Eden**, **S0**, **S1**):
    - Os objetos são criados na **Eden** e, frequentemente, morrem rapidamente.
    - Quando um objeto morre, ele é removido e a memória é liberada.
    - Se um objeto sobrevive, ele é promovido para a **Old Generation**.
- **Old Generation**:
    - Objetos que sobrevivem por muito tempo na **Young Generation** são promovidos para a **Old Generation**, onde a coleta de lixo é mais cara, pois envolve objetos que provavelmente estão em uso por mais tempo.

---
### **Como funciona a Stack:**

A **stack** (pilha) é uma estrutura de dados usada para armazenar **informações temporárias** durante a execução de um programa. Ela é usada principalmente para gerenciar o **fluxo de controle** e as **variáveis locais** de cada thread. Vamos entender seu funcionamento:

1. **Estrutura LIFO (Last In, First Out)**:
    - A **stack** funciona de forma **LIFO**, o que significa que o último item a ser inserido é o primeiro a ser removido. Isso é como uma pilha de pratos, onde você sempre tira o prato de cima primeiro.
2. **Uso em métodos e variáveis locais**:
    - Cada vez que um método é chamado, o sistema empilha um **frame** na pilha, que contém:
        - **Variáveis locais**: Todas as variáveis declaradas dentro do método.
        - **Parâmetros**: Os valores passados para o método.
        - **Endereço de retorno**: O ponto de onde o método foi chamado, para que o controle do programa retorne ao local correto após o término do método.
3. **Desempilhamento após execução**:
    - Quando o método termina, o **frame** correspondente é removido (desempilhado), e o controle retorna para o local onde o método foi chamado.
4. **Tamanho fixo**:
    - A **stack** tem um tamanho limitado. Se o número de chamadas de método (recursão ou profundidade de pilha) exceder esse limite, ocorre um **stack overflow**, que é um erro indicando que o espaço na pilha acabou.

### Exemplo:

Em Java, algo assim acontece:

```java
java
CopiarEditar
public void metodoA() {
    int x = 10;
    metodoB();
}

public void metodoB() {
    int y = 20;
}

```

- Quando `metodoA()` é chamado, um **frame** é empilhado na stack contendo `x = 10` e o endereço de retorno.
- Quando `metodoB()` é chamado dentro de `metodoA()`, outro **frame** é empilhado contendo `y = 20` e o endereço de retorno para `metodoA()`.
- Quando `metodoB()` termina, o **frame** de `metodoB()` é desempilhado, e o controle volta para `metodoA()`.
- Quando `metodoA()` termina, o **frame** de `metodoA()` é desempilhado, retornando ao ponto original.

---

### **Como funciona o Eden (no Garbage Collection)**:

O **Eden** é uma área específica da memória heap usada no modelo de **Garbage Collection** para armazenar objetos **novos**. Ele faz parte da **Young Generation**, que é uma das áreas da memória heap onde o GC foca para otimizar o gerenciamento de memória. Vamos entender seu funcionamento:

1. **Young Generation**:
    - A **Young Generation** é dividida em três partes:
        - **Eden**: Onde os objetos recém-criados são inicialmente alocados.
        - **Survivor Space 0 (S0)** e **Survivor Space 1 (S1)**: Espaços usados para armazenar objetos que sobreviveram a coletas de lixo anteriores.
2. **Processo de alocação e coleta de lixo**:
    - **Alocação**: Quando um objeto é criado, ele é alocado na **Eden**. Normalmente, a maior parte dos objetos é criada aqui.
    - **Coleta de lixo (Minor GC)**: A cada coleta de lixo, o GC examina a **Young Generation** para liberar a memória de objetos não utilizados.
        - Quando o **Minor GC** é executado, o **Eden** é varrido. Os objetos que não são mais acessíveis (não referenciados) são **removidos**.
        - Os objetos que **sobrevivem** à coleta de lixo na **Eden** são movidos para os **Survivor Spaces (S0 ou S1)**.
3. **Promoção para a Old Generation**:
    - Objetos que sobrevivem a várias coletas de lixo na **Young Generation** são eventualmente promovidos para a **Old Generation**. Isso acontece porque, em geral, objetos que sobrevivem por várias coletas são considerados **de longa duração**.
    - A promoção para a **Old Generation** é feita de maneira mais cara, ou seja, o GC realiza uma coleta mais custosa para liberar espaço na **Old Generation**.
4. **Objetos temporários**:
    - A **Eden** é basicamente uma área temporária onde objetos de vida curta são alocados, e sua coleta de lixo é rápida, já que a maioria dos objetos criados ali não vive por muito tempo.

### Exemplo do funcionamento do Eden:

- Suponha que você cria vários objetos em um sistema que usa o modelo de **Generational Garbage Collection**:

```java
java
CopiarEditar
public class Exemplo {
    public static void main(String[] args) {
        for (int i = 0; i < 100000; i++) {
            new Object();
        }
    }
}

```

- Todos os 100.000 objetos criados serão inicialmente alocados na **Eden**.
- Durante a **Minor GC**, o GC irá verificar a **Eden** e limpar todos os objetos que não estão mais sendo referenciados, deixando espaço para novos objetos.
- Se algum desses objetos sobreviver à **Minor GC**, ele será movido para um dos **Survivor Spaces**.
- Se o objeto sobreviver por várias coletas, ele será promovido para a **Old Generation**.

---

### **Resumo**:

- **Stack**: É uma área de memória onde são armazenadas as variáveis locais e o fluxo de controle dos métodos. É uma estrutura de dados LIFO, e tem um limite de tamanho.
- **Eden**: É uma parte da **Young Generation** na memória heap, usada para alocar objetos recém-criados. O GC faz coletas frequentes nessa área (Minor GC), removendo objetos não utilizados e movendo os que sobrevivem para os **Survivor Spaces** ou promovendo-os para a **Old Generation**.

### O que faz um objeto **não ser mais referenciado**?

Um objeto **não é mais referenciado** quando **não há mais referências a ele** no código. Isso significa que o objeto não pode mais ser acessado diretamente. Em termos simples, é como se o objeto estivesse **isolado** e o sistema não pudesse mais localizá-lo. Quando isso acontece, o **Garbage Collector (GC)** pode identificar que esse objeto pode ser **coletado** e sua memória **liberada**.

Existem várias situações em que um objeto pode deixar de ser referenciado:

1. **Variáveis locais saem de escopo**:
    
    - Quando uma variável local que apontava para um objeto sai do escopo (por exemplo, ao final de um método), o objeto que ela referenciava pode ser **desreferenciado**.
    
    Exemplo:
    
    ```java
    java
    CopiarEditar
    public void meuMetodo() {
        MyObject obj = new MyObject(); // Referência a um objeto
    } // Aqui, obj sai de escopo e o objeto MyObject pode ser coletado.
    
    ```
    
2. **Objetos referenciados por outras variáveis são sobrescritos**:
    
    - Se uma variável **sobrescrever** a referência para outro objeto, o objeto anteriormente referenciado pode deixar de ser acessado.
    
    Exemplo:
    
    ```java
    java
    CopiarEditar
    MyObject obj1 = new MyObject();
    MyObject obj2 = new MyObject(); // obj1 perde a referência ao primeiro objeto
    obj1 = obj2; // Agora obj1 aponta para o mesmo objeto que obj2, e o objeto antigo é desreferenciado
    
    ```
    
3. **Referências se tornam nulas**:
    
    - Se um objeto é explicitamente atribuído a `null`, ele é desreferenciado, e o GC pode identificá-lo como pronto para ser coletado.
    
    Exemplo:
    
    ```java
    java
    CopiarEditar
    MyObject obj = new MyObject();
    obj = null; // Agora, o objeto previamente referenciado por 'obj' não tem mais referência
    
    ```
    
4. **Ciclos de referência (em linguagens com GC como Java)**:
    
    - Em alguns casos, dois objetos podem se referenciar mutuamente, mas se não há mais referências externas a esse ciclo, o GC pode detectar que ambos os objetos podem ser coletados, mesmo que ainda se "referenciem" entre si.

### O que faz um objeto **ganhar uma nova referência** e perder a anterior?

Um objeto **ganha uma nova referência** quando uma variável ou outra referência passa a apontar para ele. Isso pode ocorrer de várias maneiras:

1. **Atribuição direta**:
    
    - Quando você atribui um objeto a uma nova variável, essa variável passa a **referenciar** o mesmo objeto.
    
    Exemplo:
    
    ```java
    java
    CopiarEditar
    MyObject obj1 = new MyObject(); // obj1 referencia o primeiro objeto
    MyObject obj2 = obj1; // obj2 agora também referencia o mesmo objeto que obj1
    
    ```
    
2. **Passagem de objeto como parâmetro**:
    
    - Quando um objeto é passado como argumento para um método, o parâmetro do método passa a **referenciar** o mesmo objeto.
    
    Exemplo:
    
    ```java
    java
    CopiarEditar
    public void processarObjeto(MyObject obj) {
        // 'obj' agora referencia o mesmo objeto que foi passado
    }
    
    ```
    
3. **Substituição de referência**:
    
    - Se uma variável é atribuída para referenciar um **outro objeto**, a referência anterior é perdida.
    
    Exemplo:
    
    ```java
    java
    CopiarEditar
    MyObject obj1 = new MyObject();
    MyObject obj2 = new MyObject();
    obj1 = obj2; // obj1 perde a referência ao primeiro objeto e passa a referenciar obj2
    
    ```
    

### Como o **Heap** decide ou o que faz um objeto ir para a **Old Generation**?

No **Garbage Collection (GC)** de linguagens como **Java**, o processo de movimentação de objetos entre as gerações (**Young Generation** e **Old Generation**) segue algumas regras baseadas na **idade dos objetos**. A principal motivação para mover objetos para a **Old Generation** é a **duração de vida do objeto**.

Aqui está o processo de como isso acontece:

1. **Young Generation**:
    - Os objetos começam na **Young Generation**, mais especificamente na **Eden**, onde são alocados quando são criados.
    - Durante a coleta de lixo, os objetos que **sobrevivem** a várias **coletas** (principalmente a **Minor GC**) podem ser promovidos para a **Old Generation**.
2. **Minor GC**:
    - A **Young Generation** passa por coletas de lixo mais frequentes e rápidas, chamadas de **Minor GC**. Durante essas coletas:
        - Os objetos que **sobrevivem** à coleta na **Eden** são movidos para o **Survivor Space** (S0 ou S1).
        - Se esses objetos ainda sobreviverem a várias coletas, eles são eventualmente **promovidos** para a **Old Generation**, porque isso significa que esses objetos têm uma **vida útil mais longa** e não morrem rapidamente.
3. **Critérios para promover objetos para a Old Generation**:
    - **Idade do objeto**: Se um objeto sobreviver a várias coletas (geralmente 15 a 20), ele é promovido para a **Old Generation**.
    - **Threshold de sobrevivência**: Se um objeto passa por várias **coletas menores** e não é removido, ele é promovido, pois o GC presume que ele provavelmente está em uso por mais tempo.
4. **Old Generation**:
    - A **Old Generation** é onde os objetos de **longa duração** são armazenados. Como esses objetos permanecem por mais tempo na memória, a coleta de lixo é mais cara aqui.
    - A coleta de lixo na **Old Generation** (geralmente chamada de **Major GC** ou **Full GC**) envolve uma análise mais profunda e é mais custosa em termos de performance, porque o GC tem que examinar todos os objetos na **Old Generation** para ver quais ainda são acessíveis.
5. *Eden e os **Survivor Spaces**:
    - Objetos em **Eden** são frequentemente removidos durante as coletas de lixo rápidas. No entanto, os que **sobrevivem** por várias coletas são **movidos** para os **Survivor Spaces** e, eventualmente, para a **Old Generation**.

### **Resumo**:

- Um **objeto não é mais referenciado** quando não há mais variáveis ou referências apontando para ele, tornando-o acessível para o **Garbage Collector**.
- **Referências novas** podem ser atribuídas a um objeto de várias maneiras, incluindo atribuição direta ou passagem de parâmetros.
- O GC **decide** mover um objeto para a **Old Generation** quando ele **sobrevive** a várias coletas de lixo na **Young Generation**, o que indica que ele tem uma vida útil longa. Esse movimento ocorre através de várias coletas de lixo (Minor GC) até que o objeto atinja um **limite de sobrevivência**, momento no qual ele é promovido para a **Old Generation**, onde as coletas de lixo são mais custosas.

O **GC Root**, **Stack**, **Heap** e **Eden** estão interconectados no processo de gerenciamento de memória e coleta de lixo (Garbage Collection) em sistemas que utilizam **Java** ou outras linguagens com Garbage Collector (GC). Abaixo está uma explicação detalhada de cada um desses componentes e a **linha do tempo** de um objeto no gerenciamento de memória, que inclui o papel do **Eden**.

### Relação entre GC Root, Stack, Heap e Eden:

1. **GC Root**:
    
    - **GC Root** são referências acessíveis diretamente pelo código, como variáveis locais, variáveis estáticas, threads e outros objetos ativos. Eles servem como **pontos de partida** para o **Garbage Collector** identificar objetos "vivos", ou seja, objetos que ainda podem ser acessados e que não serão coletados.
        
    - **Relação**: O GC Root é o ponto inicial de rastreamento que ajuda o **GC** a identificar **quais objetos estão vivos** e, portanto, quais objetos devem ser preservados na memória. Sem o GC Root, o GC não saberia por onde começar a busca.
        
2. **Stack**:
    
    - A **stack** (pilha) é onde as **referências locais** para objetos são armazenadas, junto com o controle de execução (como os **endereços de retorno** de métodos). Ela é usada para gerenciar chamadas de métodos e variáveis locais.
        
    - **Relação com GC Root**: Os objetos referenciados nas variáveis locais da **stack** podem ser acessados diretamente a partir do **GC Root**. Quando a execução de um método termina e o escopo da variável local sai de escopo, a referência é eliminada e o objeto pode ser elegível para coleta de lixo, se não for mais acessado por outras referências.
        
3. **Heap**:
    
    - O **heap** é onde os **objetos dinâmicos** são alocados durante a execução do programa. Ele é dividido em várias partes, incluindo a **Young Generation** e a **Old Generation**. O **Eden** é uma parte da **Young Generation**.
        
    - **Relação com o GC Root e Stack**: A **heap** contém os objetos alocados dinamicamente, e o GC começa a partir do GC Root para identificar quais objetos na heap estão sendo usados. Os objetos referenciados pela **stack** ou outras referências da **GC Root** são marcados como acessíveis e não serão coletados, mesmo que estejam na **heap**.
        
4. **Eden**:
    
    - O **Eden** é uma parte da **Young Generation** da **heap** onde os objetos recém-criados são alocados inicialmente. Quando ocorre a **coleta de lixo (Minor GC)**, o **Eden** é limpo, e os objetos que sobreviverem são movidos para os **Survivor Spaces** e, eventualmente, para a **Old Generation** se sobreviverem por várias coletas.
        
    - **Relação com o GC Root**: O GC Root ajuda a identificar se os objetos na **Young Generation**, especificamente no **Eden**, estão sendo referenciados e devem ser mantidos ou se podem ser coletados.
        

### Linha do Tempo de um Objeto no Processo de Garbage Collection

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

# Criação de instância e referências

Quando dizemos **criar uma instância de um objeto**, estamos falando do momento em que o programa:

1. **Aloca memória** na **Heap** (especificamente em **Eden**)
2. **Executa o construtor** desse objeto
3. **Retorna** uma **referência** (ponteiro) para essa área de memória

---

## 1. Alocação no Eden

- Ao chamar `new MinhaClasse(...)`, a JVM reserva espaço em **Eden** (Young Generation).
- Essa região é otimizada para alocação rápida e para coletas frequentes (Minor GC).

> Analogia: Eden é como uma bancada onde peças recém-chegadas ficam até serem usadas ou descartadas.

---

## 2. Execução do construtor

- O runtime inicializa os campos de instância e executa o código dentro do construtor.
- Se o objeto referencia outros objetos, os ponteiros são atribuídos aos campos internos.

---

## 3. Atribuição da referência

```java
java
CopiarEditar
MinhaClasse obj = new MinhaClasse();

```

- A variável `obj` recebe o **endereço** (referência) do objeto em **Eden**.
- Essa variável mora na **Stack** (frame do método) ou em um campo de outro objeto.
- Enquanto `obj` estiver em escopo, ele é um **GC Root** indireto, mantendo o objeto vivo.

> Analogia: A variável local é um crachá que indica “onde” a peça está na bancada.

---

## 4. Uso e ciclo de vida

- **Vivo (alcançável):** Enquanto existir pelo menos **uma referência ativa**, o GC nunca coleta o objeto.
- **Perda de referência:** Se `obj` sair de escopo ou receber `null`/outra referência, a ligação se quebra.
- **Elegível para coleta:** Quando **nenhuma** referência aponta para o objeto, ele pode ser removido na próxima GC.

---

## 5. Linha do tempo completa

|Etapa|Região|Descrição|
|---|---|---|
|**1. Instanciação**|Eden|Aloca memória + chama construtor|
|**2. Recebe referência**|Stack/Heap|Variável local ou campo estático aponta para o objeto|
|**3. Uso em runtime**|Heap|Manipula o objeto através da referência|
|**4. Perda de referência**|Stack/Roots|Variável sai de escopo ou é sobrescrita (`= null` ou `=` outro)|
|**5. Coleta de lixo**|Young/Old Gen|Minor GC limpa Eden; sobreviventes vão para Survivor/Old Gen|

---

### Resumo

- **Instanciar** = `new` → memória em Eden + construtor → retorna referência.
- **Referência** = ponteiro armazenado na Stack (ou em outro objeto).
- Enquanto existir referência viva, o objeto “vive”; sem ela, fica pronto para coleta.

# Ponteiros e Referências: Visão Geral

## 1. O que é um ponteiro?

- **Ponteiro** (C/C++): variável cujo valor é o **endereço de memória** de outra variável.
    
- **Referência** (Java/C#): conceito parecido, mas **seguro** — não expõe o endereço numérico nem permite aritmética de ponteiro.
    

## 2. Para que servem

1. **Acesso indireto**
    
    - Permite ler/escrever dados sem usar o nome direto da variável.
        
2. **Alocação dinâmica**
    
    - Em C: `malloc`/`free` para reservar e liberar memória em tempo de execução.
        
3. **Estruturas encadeadas**
    
    - Listas ligadas, árvores e grafos usam ponteiros para conectar nós.
        
4. **Passagem por referência**
    
    - Funções podem modificar variáveis externas sem retorno:
        
        c
        
        CopiarEditar
        
        `void incrementa(int *p) {     (*p)++; } int main() {     int a = 1;     incrementa(&a);     // a agora vale 2 }`
        
5. **Eficiência**
    
    - Evita cópias grandes: basta passar o endereço.
        

## 3. Operações comuns em C/C++

- **Obter endereço**:
    
    `int x = 10; int *p = &x;`
    
- **Desreferenciar**:
    
    `printf("%d\n", *p);  // imprime 10`
    
- **Aritmética de ponteiros**:
    
    `int v[3] = {1,2,3}; int *q = v;   // aponta para v[0] q++;          // agora aponta para v[1]`
    

## 4. Segurança e riscos

- **Ponteiro nulo** (`NULL`)
    
- **Dangling pointer**: aponta para memória liberada
    
- **Buffer overflow**: escrever além do limite
    

## 5. Referências em linguagens gerenciadas

- **Java/C#** não expõem ponteiros; usam referências gerenciadas.
    
- Exemplo Java:
    
    `MinhaClasse obj = new MinhaClasse();`
    
    - Internamente guarda um endereço, mas você só vê `obj`.
        

## 6. Diferenças principais

|Característica|C/C++ (ponteiro)|Java/C# (referência)|
|---|---|---|
|Exibe endereço?|Sim (número)|Não|
|Aritmética de ponteiro|Sim|Não|
|Segurança|Baixa (manual)|Alta (GC gerencia)|
|Alocação dinâmica|`malloc`/`free`|`new` + coleta automática|
|Estruturas encadeadas|Listas, árvores|Objetos e coleções|

## 7. Resumo

- **Ponteiro**: endereço explícito em C/C++.
    
- **Referência**: ponteiro seguro em Java/C#.
    
- Usados para **alocação dinâmica**, **estruturas encadeadas** e **passagem eficiente de dados**.
# 📚 **Como funciona a memória da JVM, classes, objetos, primitivas e o Garbage Collector**

---

## 🧩 **1. Visão Geral da Memória da JVM**

A memória da JVM é dividida em áreas principais:

### 🧠 Stack (Pilha)

- Guarda **contexto de execução** (métodos ativos, variáveis locais e referências).
- Criada por **Thread** (cada thread tem sua própria Stack).
- Desalocação automática (saiu do escopo, limpa).
- **Sem GC** (garbage collector não atua aqui).

> 🧵 Analogia: imagine a Stack como uma pilha de pratos – quando você empilha (push) um novo método, ele vai para o topo; ao terminar, você remove (pop).

---

### 🧊 Heap (Monte)

- Guarda todos os **objetos** criados com `new`, incluindo arrays (mesmo de tipos primitivos).
- Compartilhada por todas as threads.
- Monitorada pelo **Garbage Collector (GC)**.
- **Dividida em gerações**:
    - **Young Generation** (Eden + Survivor)
    - **Old Generation**
    - (Opcional: **Humongous** em G1 GC)

---

### 📁 Metaspace (antigo PermGen)

- Armazena **metadados da classe** (`Class`, `Method`, `Field`, etc.).
- Substituiu o PermGen no Java 8.
- Fora da Heap – usa memória nativa.
- Cresce dinamicamente.

---

## 🧱 **2. Onde ficam classes, objetos e primitivos?**

|Elemento|Onde fica?|Observação|
|---|---|---|
|`Pessoa.class`|Metaspace|Definição da classe|
|`Pessoa p = new Pessoa();`|Stack (referência) + Heap (objeto)|Instância|
|`int x = 5;`|Stack|Valor primitivo|
|`int[] arr = new int[10];`|Stack (referência) + Heap (array)|Arrays são objetos|
|`List<String> nomes = new ArrayList<>();`|Stack (referência) + Heap (objeto)|Lista na Heap|

---

## 🧮 **3. GC e a Heap: Como o Garbage Collector funciona**

### 📍 _Young Generation (Geração Jovem)_

- **Eden Space**: onde objetos recém-criados são alocados.
- **Survivor Spaces (S0 e S1)**: onde objetos sobreviventes de coletas anteriores vão temporariamente.

> ✅ Objetos que sobrevivem a várias coletas são promovidos para a Old Generation.

### 🔁 _Old Generation (Geração Antiga)_

- Armazena objetos que viveram mais tempo.
- Coletado com menor frequência (mas mais caro).

### 🧨 _Tipos de Garbage Collection_

|Tipo GC|Indicado para|Como funciona|
|---|---|---|
|Serial|Aplicações pequenas|Um único thread GC|
|Parallel|Alto throughput|Vários threads|
|CMS (deprecated)|Baixa latência|Coleta concorrente|
|G1 (default hoje)|Grandes heaps|Divide a heap em regiões dinâmicas|

---

## 📌 **Exemplo de execução:**

```java
java
CopiarEditar
public class Main {
    public static void main(String[] args) {
        Pessoa p = new Pessoa();
        int idade = 30;
        int[] idades = new int[3];
    }
}

```

### Passo a passo do que acontece:

1. **Classe `Pessoa` carregada**
    - Vai para o **Metaspace** (uma vez por ClassLoader)
2. **Variável `p` é criada**
    - Referência vai para a **Stack**
    - `new Pessoa()` é alocado na **Heap** (Young → Eden)
3. **Variável `idade` é criada**
    - Vai direto para a **Stack** (primitivo local)
4. **Array `int[]` é criado**
    - Referência está na **Stack**
    - Conteúdo do array na **Heap**

---

## 🔄 **Como o GC coleta objetos**

### Exemplo:

```java
java
CopiarEditar
Pessoa p = new Pessoa(); // na Heap
p = null;                // perdeu referência

```

- O objeto ainda está na Heap.
- Como **nenhuma referência** aponta para ele, será **marcado para coleta** na próxima GC (dependendo da política).

---

## 🧠 Dica para lembrar:

> 🧼 GC só coleta o que não está mais referenciado – não importa se está na Young ou na Old.

---

## 🧪 Comparação: Pessoa vs Array

|Característica|`Pessoa p = new Pessoa()`|`int[] a = new int[5]`|
|---|---|---|
|Onde vive|Heap|Heap|
|Referência|Stack|Stack|
|Coletado por GC|Sim|Sim|
|Tipo de objeto|Customizado (classe)|Array (objeto especial)|
|Precisa de `new`|Sim|Sim|

---

## 🧱 Estrutura visual simplificada

```
plaintext
CopiarEditar
THREAD (Stack)
├─ p → Referência ──┐
├─ idade = 30       │
└─ idades →─────────┘
         |
         V
       HEAP
       ├─ Pessoa { ... }
       └─ int[] { 0, 0, 0 }

METASPACE
├─ Pessoa.class
├─ Main.class
└─ ...

```

---

## 🧠 Analogia Final:

- 🧠 **Stack = sua mente no momento** (o que você está pensando agora)
- 📦 **Heap = sua mochila** (onde você guarda objetos e pode consultar depois)
- 🏛️ **Metaspace = biblioteca** (onde as definições das coisas estão armazenadas)
- 🧽 **GC = faxineiro** que limpa sua mochila quando você esquece algo lá por muito tempo