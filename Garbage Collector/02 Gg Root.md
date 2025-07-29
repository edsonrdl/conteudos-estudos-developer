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


---
O **GC Root**, **Stack**, **Heap** e **Eden** estão interconectados no processo de gerenciamento de memória e coleta de lixo (Garbage Collection) em sistemas que utilizam **Java** ou outras linguagens com Garbage Collector (GC). Abaixo está uma explicação detalhada de cada um desses componentes e a **linha do tempo** de um objeto no gerenciamento de memória, que inclui o papel do **Eden**.