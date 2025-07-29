
### Como o Heap decide ou o que faz um objeto ir para a Old Generation?

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
### Como o Garbage Collector usa essas gerações?

O GC usa um modelo de **coleta por gerações**, onde ele trata de diferentes regiões da heap de maneiras diferentes:

- **Young Generation** (incluindo **Eden**, **S0**, **S1**):
    - Os objetos são criados na **Eden** e, frequentemente, morrem rapidamente.
    - Quando um objeto morre, ele é removido e a memória é liberada.
    - Se um objeto sobrevive, ele é promovido para a **Old Generation**.
- **Old Generation**:
    - Objetos que sobrevivem por muito tempo na **Young Generation** são promovidos para a **Old Generation**, onde a coleta de lixo é mais cara, pois envolve objetos que provavelmente estão em uso por mais tempo.

### Exemplo do funcionamento do Eden:

- Suponha que você cria vários objetos em um sistema que usa o modelo de **Generational Garbage Collection**:

```java
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
### **Resumo**:

- Um **objeto não é mais referenciado** quando não há mais variáveis ou referências apontando para ele, tornando-o acessível para o **Garbage Collector**.
- **Referências novas** podem ser atribuídas a um objeto de várias maneiras, incluindo atribuição direta ou passagem de parâmetros.
- O GC **decide** mover um objeto para a **Old Generation** quando ele **sobrevive** a várias coletas de lixo na **Young Generation**, o que indica que ele tem uma vida útil longa. Esse movimento ocorre através de várias coletas de lixo (Minor GC) até que o objeto atinja um **limite de sobrevivência**, momento no qual ele é promovido para a **Old Generation**, onde as coletas de lixo são mais custosas.