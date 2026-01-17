O padrão Memento é um padrão comportamental que permite capturar e armazenar o estado interno de um objeto em um determinado momento, sem violar o encapsulamento, e restaurá-lo posteriormente, se necessário. Ele permite que você salve e restaure o estado de um objeto sem revelar os detalhes da implementação interna.

### Estrutura do Memento

- **Originator**: Cria um objeto Memento para capturar seu estado interno e usa o Memento para restaurar seu estado anterior.
- **Memento**: Armazena o estado interno de um objeto Originator em um determinado momento.
- **Caretaker**: Responsável por armazenar e gerenciar os objetos Memento.

Diagrama UML CLASS

![[Pasted image 20250407130131.png]]
### Exemplo em Java

Vamos considerar um exemplo de um editor de texto onde um usuário pode escrever, editar e desfazer a edição usando o padrão Memento.

Memento

```
public class EditorMemento {
    private final String content;

    public EditorMemento(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }
}

```

Originator

```
public class Editor {
    private String content;

    public void write(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }

    public EditorMemento save() {
        return new EditorMemento(content);
    }

    public void restore(EditorMemento memento) {
        content = memento.getContent();
    }
}

```

Caretaker

```
import java.util.Stack;

public class History {
    private final Stack<editormemento> mementos = new Stack<>();

    public void push(EditorMemento memento) {
        mementos.push(memento);
    }

    public EditorMemento pop() {
        return mementos.pop();
    }
}

```

Client

```
public class MementoPatternTest {
    public static void main(String[] args) {
        Editor editor = new Editor();
        History history = new History();

        // Escrever e salvar o estado inicial
        editor.write("Primeira linha");
        history.push(editor.save());

        // Escrever e salvar o segundo estado
        editor.write("Segunda linha");
        history.push(editor.save());

        // Desfazer a última edição
        editor.restore(history.pop());
        System.out.println("Estado atual: " + editor.getContent());

        // Desfazer novamente
        editor.restore(history.pop());
        System.out.println("Estado atual: " + editor.getContent());
    }
}

```

### Explicação

1. **Memento (EditorMemento)**: Armazena o estado interno do editor em um determinado momento.
2. **Originator (Editor)**: Mantém o estado interno atual e pode criar mementos e restaurar seu estado a partir deles.
3. **Caretaker (History)**: Mantém uma pilha de mementos, permitindo que o editor desfaça alterações anteriores.
4. **Client (MementoPatternTest)**: Usa o editor para escrever e salvar estados, e o history para desfazer edições anteriores.

### Saída Esperada

Estado atual: Primeira linha

Estado atual: Primeira linhaSegunda linha

### Benefícios e Considerações

Benefícios

1. **Desacoplamento**: Permite salvar e restaurar o estado de um objeto sem expor a sua estrutura interna.
2. **Recuperação de Estado**: Facilita a implementação de funcionalidades de desfazer e refazer.
3. **Encapsulamento Preservado**: Mantém o encapsulamento do objeto Originator, uma vez que o Memento só pode ser criado e acessado por ele.

Considerações

1. **Overhead de Memória**: Pode aumentar o uso de memória ao armazenar múltiplos estados do objeto.
2. **Complexidade Adicional**: Introduz classes adicionais e uma estrutura de gerenciamento de mementos, o que pode aumentar a complexidade do código.

### Conclusão

O padrão Memento é uma ferramenta poderosa para salvar e restaurar o estado de objetos em um sistema de forma transparente, sem violar o encapsulamento. Ele é especialmente útil para implementar funcionalidades de desfazer e refazer. No entanto, é importante considerar o overhead de memória e a complexidade adicional que o padrão pode introduzir.