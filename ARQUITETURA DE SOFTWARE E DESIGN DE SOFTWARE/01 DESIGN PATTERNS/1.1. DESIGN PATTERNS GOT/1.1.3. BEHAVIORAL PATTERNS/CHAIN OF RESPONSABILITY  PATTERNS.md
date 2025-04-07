O padrão Chain of Responsibility é um padrão comportamental que permite que um pedido seja processado por uma série de handlers (manipuladores) até que um deles consiga processá-lo. Cada handler decide se processa o pedido ou o passa para o próximo handler na cadeia.

### Estrutura do Chain of Responsibility

- **Handler**: Define a interface para manipular pedidos.
- **ConcreteHandler**: Implementa a interface Handler e trata pedidos específicos ou os passa adiante.
- **Client**: Inicia os pedidos para serem tratados pela cadeia de handlers.

### Exemplo em Java

Vamos considerar um exemplo onde temos um sistema de suporte técnico com diferentes níveis de suporte: suporte básico, suporte intermediário e suporte avançado. Cada nível de suporte pode processar certos tipos de pedidos e, caso não consiga, passa o pedido para o próximo nível.

Handler

```java
public abstract class SupportHandler {
    protected SupportHandler nextHandler;

    public void setNextHandler(SupportHandler nextHandler) {
        this.nextHandler = nextHandler;
    }

    public abstract void handleRequest(String request);
}
```

ConcreteHandler

```java
public class BasicSupportHandler extends SupportHandler {
    @Override
    public void handleRequest(String request) {
        if (request.equals("basic")) {
            System.out.println("BasicSupportHandler: Handling basic request.");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);
        }
    }
}

public class IntermediateSupportHandler extends SupportHandler {
    @Override
    public void handleRequest(String request) {
        if (request.equals("intermediate")) {
            System.out.println("IntermediateSupportHandler: Handling intermediate request.");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);
        }
    }
}

public class AdvancedSupportHandler extends SupportHandler {
    @Override
    public void handleRequest(String request) {
        if (request.equals("advanced")) {
            System.out.println("AdvancedSupportHandler: Handling advanced request.");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);
        } else {
            System.out.println("No handler available for request: " + request);
        }
    }
}

```

Client

```java
public class ChainOfResponsibilityTest {
    public static void main(String[] args) {
        // Create handlers
        SupportHandler basic = new BasicSupportHandler();
        SupportHandler intermediate = new IntermediateSupportHandler();
        SupportHandler advanced = new AdvancedSupportHandler();

        // Set the chain of responsibility
        basic.setNextHandler(intermediate);
        intermediate.setNextHandler(advanced);

        // Handle requests
        System.out.println("Request: basic");
        basic.handleRequest("basic");

        System.out.println("\\nRequest: intermediate");
        basic.handleRequest("intermediate");

        System.out.println("\\nRequest: advanced");
        basic.handleRequest("advanced");

        System.out.println("\\nRequest: unknown");
        basic.handleRequest("unknown");
    }
}

```

### Explicação

1. **Handler (SupportHandler)**: Define a interface `SupportHandler` que declara o método `handleRequest` e mantém uma referência ao próximo handler na cadeia.
2. **ConcreteHandler (BasicSupportHandler, IntermediateSupportHandler, AdvancedSupportHandler)**: Implementa a interface `SupportHandler` e trata tipos específicos de pedidos. Se não puderem tratar o pedido, eles o passam para o próximo handler na cadeia.
3. **Client (ChainOfResponsibilityTest)**: Configura a cadeia de handlers e envia pedidos para o primeiro handler da cadeia.

### Saída Esperada

```
Request: basic
BasicSupportHandler: Handling basic request.

Request: intermediate
IntermediateSupportHandler: Handling intermediate request.

Request: advanced
AdvancedSupportHandler: Handling advanced request.

Request: unknown
No handler available for request: unknown

```

### Benefícios e Considerações

Benefícios

1. **Desacoplamento**: Desacopla o remetente do pedido dos seus receptores, permitindo que múltiplos handlers processem pedidos.
2. **Flexibilidade**: Permite adicionar ou modificar handlers na cadeia sem alterar outros elementos da cadeia.
3. **Responsabilidade**: Cada handler tem uma única responsabilidade, promovendo o princípio da responsabilidade única.

Considerações

1. **Garantia de Processamento**: Não há garantia de que o pedido será processado a menos que um handler na cadeia possa tratá-lo.
2. **Depuração**: Pode ser difícil depurar e rastrear como os pedidos são processados, especialmente em cadeias longas.

### Conclusão

O padrão Chain of Responsibility é uma solução elegante para cenários onde múltiplos handlers podem processar um pedido. Ele promove o desacoplamento e a responsabilidade única, tornando o sistema mais flexível e fácil de manter. No entanto, é importante garantir que o pedido será eventualmente processado e gerenciar a complexidade potencial de cadeias longas de handlers.