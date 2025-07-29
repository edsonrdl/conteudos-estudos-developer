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

### O que faz um objeto **não ser mais referenciado**?

Um objeto **não é mais referenciado** quando **não há mais referências a ele** no código. Isso significa que o objeto não pode mais ser acessado diretamente — ele fica “isolado” e o sistema não consegue mais localizá‑lo. Nesse caso, o **Garbage Collector (GC)** pode identificar que ele está pronto para **coleta** e liberar sua memória.

### Situações comuns

1. **Variáveis locais saem de escopo**
    
    Quando uma variável local que apontava para um objeto sai do escopo (por exemplo, ao final de um método), aquele objeto pode ficar sem referência.
    
    ```java
    
    
    public void meuMetodo() {
        MyObject obj = new MyObject(); // cria e referencia o objeto
    } // aqui, obj sai de escopo e o MyObject pode ser coletado
    
    ```
    
2. **Referência sobrescrita**
    
    Se você atribui um novo valor à variável, ela deixa de apontar para o objeto original.
    
    ```java
    
    MyObject obj1 = new MyObject();
    MyObject obj2 = new MyObject();
    
    obj1 = obj2;
    // agora obj1 e obj2 referenciam o mesmo objeto (o primeiro MyObject fica sem referência)
    
    ```
    
3. **Referência definida como `null`**
    
    Atribuir `null` “quebra” a referência ao objeto.
    
    ```java
    
    
    MyObject obj = new MyObject();
    obj = null;
    // o objeto criado não tem mais nenhuma referência
    
    ```
    
4. **Ciclos de referência**
    
    Mesmo que dois objetos se referenciem mutuamente, se **nenhuma** referência externa apontar para eles, o GC pode detectá‑los como coletáveis.
    

---

### O que faz um objeto **ganhar uma nova referência** e perder a anterior?

Um objeto **ganha uma nova referência** quando uma variável (ou outro ponteiro) começa a apontar para ele. Ao mesmo tempo, se essa variável já apontava para outro objeto, a referência anterior é perdida.

### Exemplos

1. **Atribuição direta**
    
    Duas variáveis podem referenciar o mesmo objeto.
    
    ```java
    MyObject obj1 = new MyObject(); // obj1 aponta para o objeto
    MyObject obj2 = obj1;           // obj2 também aponta para o mesmo objeto
    
    ```
    
2. **Passagem como parâmetro**
    
    Ao chamar um método, o parâmetro interno passa a referenciar o objeto passado.
    
    ```java
    public void processarObjeto(MyObject obj) {
        // o parâmetro 'obj' referencia o mesmo objeto que foi passado
    }
    
    ```
    
3. **Substituição de referência**
    
    Atribuir outra instância faz a variável perder a referência antiga.
    
    ```java
    
    
    MyObject obj1 = new MyObject();
    MyObject obj2 = new MyObject();
    
    obj1 = obj2;
    // obj1 deixa de apontar para o primeiro objeto e passa a apontar para obj2
    
    ```
### Resumo

- **Instanciar** = `new` → memória em Eden + construtor → retorna referência.
- **Referência** = ponteiro armazenado na Stack (ou em outro objeto).
- Enquanto existir referência viva, o objeto “vive”; sem ela, fica pronto para coleta.