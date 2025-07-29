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
