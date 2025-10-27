O padrão Command é um padrão comportamental que transforma uma solicitação em um objeto, permitindo que você parametrize clientes com diferentes solicitações, enfileire ou registre solicitações, e implemente operações que podem ser desfeitas. Ele encapsula todas as informações necessárias para executar uma ação ou acionar um evento em um objeto.

### Estrutura do Command

- **Command**: Declara uma interface para executar uma operação.
- **ConcreteCommand**: Implementa a interface `Command` e define a ligação entre uma ação e um receptor.
- **Client**: Cria um objeto `ConcreteCommand` e define o receptor do comando.
- **Invoker**: Pede ao comando para executar a solicitação.
- **Receiver**: Sabe como realizar as operações associadas em uma solicitação.

Diagrama UML CLASS

![[Pasted image 20250407130037.png]]
### Exemplo em Java

Vamos considerar um exemplo de um sistema de controle remoto onde diferentes comandos podem ser executados, como ligar e desligar uma luz.

Command

```
public interface Command {
    void execute();
    void undo();
}

```

ConcreteCommand

```
public class LightOnCommand implements Command {
    private Light light;

    public LightOnCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.on();
    }

    @Override
    public void undo() {
        light.off();
    }
}

public class LightOffCommand implements Command {
    private Light light;

    public LightOffCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.off();
    }

    @Override
    public void undo() {
        light.on();
    }
}

```

Receiver

```
public class Light {
    public void on() {
        System.out.println("The light is on");
    }

    public void off() {
        System.out.println("The light is off");
    }
}

```

Invoker

```
public class RemoteControl {
    private Command command;

    public void setCommand(Command command) {
        this.command = command;
    }

    public void pressButton() {
        command.execute();
    }

    public void pressUndo() {
        command.undo();
    }
}

```

Client

```
public class CommandPatternTest {
    public static void main(String[] args) {
        Light livingRoomLight = new Light();

        Command lightOn = new LightOnCommand(livingRoomLight);
        Command lightOff = new LightOffCommand(livingRoomLight);

        RemoteControl remote = new RemoteControl();

        // Turn on the light
        remote.setCommand(lightOn);
        remote.pressButton();

        // Turn off the light
        remote.setCommand(lightOff);
        remote.pressButton();

        // Undo the last operation (turn off the light)
        remote.pressUndo();
    }
}

```

### Explicação

1. **Command (Command)**: Define a interface `Command` com métodos `execute` e `undo`.
2. **ConcreteCommand (LightOnCommand, LightOffCommand)**: Implementa a interface `Command` e define as ações para ligar e desligar a luz, respectivamente.
3. **Receiver (Light)**: Implementa as operações associadas a ligar e desligar a luz.
4. **Invoker (RemoteControl)**: Invoca comandos `execute` e `undo` através de métodos `pressButton` e `pressUndo`.
5. **Client (CommandPatternTest)**: Cria instâncias de `Light`, `LightOnCommand`, `LightOffCommand` e `RemoteControl`, configurando e executando comandos.

### Saída Esperada

The light is on

The light is off

The light is on

### Benefícios e Considerações

Benefícios

1. **Desacoplamento**: Desacopla o objeto que invoca a operação do objeto que executa a operação.
2. **Flexibilidade**: Novos comandos podem ser adicionados sem mudar o código existente.
3. **Undo/Redo**: Facilita a implementação de funcionalidades de undo/redo.

Considerações

1. **Complexidade**: Pode aumentar a complexidade do código devido à introdução de várias novas classes.
2. **Sobrecarga**: Pode introduzir overhead se houver um grande número de comandos muito simples.

### Conclusão

O padrão Command é uma poderosa ferramenta para encapsular solicitações como objetos, promovendo o desacoplamento e a flexibilidade. Ele é particularmente útil em sistemas onde operações precisam ser registradas, enfileiradas ou desfeitas. Com o Command, é fácil adicionar novas operações e manter a manutenção do código, ao custo de uma possível complexidade adicional.