O padrão State é um padrão comportamental que permite que um objeto altere seu comportamento quando seu estado interno muda. Isso é alcançado ao representar os diferentes estados do objeto como objetos separados e delegar as responsabilidades associadas a cada estado para esses objetos. O padrão State permite que um objeto mude seu comportamento dinamicamente à medida que seu estado interno muda, sem que a classe do objeto precise mudar.

### Estrutura do State

- **Context (Contexto)**: Define a interface para interagir com os estados. Mantém uma referência para uma instância de um estado concreto.
- **State (Estado)**: Interface que define os métodos para realizar as ações associadas a um estado específico.
- **ConcreteState (EstadoConcreto)**: Implementa a interface State e define o comportamento associado a um estado específico.
- **Client (Cliente)**: Usa o contexto para interagir com o estado.

**Diagrama UML CLASS**

![[Pasted image 20250407130221.png]]
### Exemplo em Java

Vamos considerar um exemplo de um controle remoto onde um dispositivo pode estar ligado, desligado ou em modo de espera. O controle remoto pode mudar o estado do dispositivo e responder de maneira diferente dependendo do estado atual do dispositivo.

State

```
public interface State {
    void pressButton();
}

public class OnState implements State {
    @Override
    public void pressButton() {
        System.out.println("Turning off the device");
    }
}

public class OffState implements State {
    @Override
    public void pressButton() {
        System.out.println("Turning on the device");
    }
}

public class StandbyState implements State {
    @Override
    public void pressButton() {
        System.out.println("Putting the device in standby mode");
    }
}

```

Context

```
public class Device {
    private State state;

    public Device() {
        state = new OffState();
    }

    public void setState(State state) {
        this.state = state;
    }

    public void pressButton() {
        state.pressButton();
    }
}

```

Client

```
public class StatePatternTest {
    public static void main(String[] args) {
        Device device = new Device();

        device.pressButton(); // Turn on the device
        device.setState(new StandbyState());
        device.pressButton(); // Put the device in standby mode
        device.setState(new OnState());
        device.pressButton(); // Turn off the device
    }
}

```

### Explicação

1. **State (Estado)**: Define a interface para os diferentes estados do dispositivo.
2. **ConcreteState (EstadoConcreto)**: Implementa a interface State e define o comportamento associado a um estado específico.
3. **Context (Contexto)**: Mantém uma referência para uma instância de um estado concreto e delega as operações para o estado atual.
4. **Client (Cliente)**: Cria uma instância do contexto e interage com ele para mudar o estado do dispositivo.

### Saída Esperada

Turning on the device

Putting the device in standby mode

Turning off the device

### Benefícios e Considerações

Benefícios

1. **Desacoplamento**: Permite que o objeto altere seu comportamento dinamicamente sem depender diretamente do estado atual.
2. **Simplicidade**: Torna o código mais simples, pois separa a lógica associada a diferentes estados em classes distintas.
3. **Facilidade de Adição de Estados**: Novos estados podem ser facilmente adicionados sem modificar o código existente.

Considerações

1. **Complexidade Adicional**: Introduz classes adicionais para representar diferentes estados, o que pode aumentar a complexidade do código.
2. **Visibilidade de Estados**: Nem sempre é fácil determinar todos os estados possíveis e as transições entre eles.

### Conclusão

O padrão State é uma ferramenta poderosa para implementar comportamentos dinâmicos em um objeto, permitindo que ele mude seu comportamento quando seu estado interno muda. Ele promove o encapsulamento do comportamento associado a diferentes estados em classes separadas, facilitando a manutenção e a extensão do código. No entanto, é importante ponderar a complexidade adicional introduzida pelo padrão em relação aos benefícios que ele proporciona.