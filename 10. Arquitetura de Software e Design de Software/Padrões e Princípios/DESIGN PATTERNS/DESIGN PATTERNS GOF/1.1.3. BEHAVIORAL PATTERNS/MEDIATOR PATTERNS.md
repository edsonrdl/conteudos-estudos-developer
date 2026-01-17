O padrão Mediator é um padrão comportamental que promove o acoplamento fraco entre objetos, centralizando as interações entre eles através de um objeto mediador. Em vez de os objetos se comunicarem diretamente uns com os outros, eles se comunicam através do mediador, reduzindo assim a dependência entre eles.

### Estrutura do Mediator

- **Mediator**: Define uma interface para comunicação entre objetos.
- **ConcreteMediator**: Implementa a interface Mediator e coordena as interações entre os objetos.
- **Colleague**: Define uma interface para comunicação com outros colegas através do mediador.
- **ConcreteColleague**: Implementa a interface Colleague e interage com outros colegas através do mediador.

### Exemplo em Java

Vamos considerar um exemplo de um sistema de chat onde os usuários podem enviar mensagens uns para os outros. O Mediator vai coordenar o envio e a recepção de mensagens entre os usuários.

Mediator

```
public interface ChatMediator {
    void sendMessage(String message, User user);
    void addUser(User user);
}

public class ChatMediatorImpl implements ChatMediator {
    private List<user> users;

    public ChatMediatorImpl() {
        this.users = new ArrayList<>();
    }

    @Override
    public void sendMessage(String message, User user) {
        for (User u : users) {
            if (u != user) {
                u.receive(message);
            }
        }
    }

    @Override
    public void addUser(User user) {
        users.add(user);
    }
}

```

Colleague

```
public abstract class User {
    protected ChatMediator mediator;
    protected String name;

    public User(ChatMediator mediator, String name) {
        this.mediator = mediator;
        this.name = name;
    }

    public abstract void send(String message);
    public abstract void receive(String message);
}

```

ConcreteColleague

```
public class BasicUser extends User {
    public BasicUser(ChatMediator mediator, String name) {
        super(mediator, name);
    }

    @Override
    public void send(String message) {
        System.out.println(name + " sends: " + message);
        mediator.sendMessage(message, this);
    }

    @Override
    public void receive(String message) {
        System.out.println(name + " receives: " + message);
    }
}

```

Client

```
public class MediatorPatternTest {
    public static void main(String[] args) {
        ChatMediator mediator = new ChatMediatorImpl();

        User user1 = new BasicUser(mediator, "User 1");
        User user2 = new BasicUser(mediator, "User 2");
        User user3 = new BasicUser(mediator, "User 3");

        mediator.addUser(user1);
        mediator.addUser(user2);
        mediator.addUser(user3);

        user1.send("Hello, everyone!");
    }
}

```

### Explicação

1. **Mediator (ChatMediator)**: Define a interface `ChatMediator` para comunicação entre usuários.
2. **ConcreteMediator (ChatMediatorImpl)**: Implementa a interface `ChatMediator` e coordena as interações entre os usuários.
3. **Colleague (User)**: Define a interface `User` para comunicação com outros usuários através do mediador.
4. **ConcreteColleague (BasicUser)**: Implementa a interface `User` e interage com outros usuários através do mediador.
5. **Client (MediatorPatternTest)**: Cria instâncias de usuários, registra-os no mediador e envia mensagens entre eles.

### Saída Esperada

User 1 sends: Hello, everyone!

User 2 receives: Hello, everyone!

User 3 receives: Hello, everyone!

### Benefícios e Considerações

Benefícios

1. **Desacoplamento**: Reduz o acoplamento entre os objetos, permitindo que eles se comuniquem indiretamente através do mediador.
2. **Centralização**: Centraliza a lógica de comunicação em um único local, facilitando a manutenção e a compreensão do código.
3. **Flexibilidade**: Facilita a adição de novos mediadores ou colegas sem modificar o código existente.

Considerações

1. **Complexidade Adicional**: Pode introduzir complexidade adicional devido à necessidade de criar e gerenciar um objeto mediador.
2. **Single Point of Failure**: O mediador pode se tornar um ponto único de falha, se não for implementado corretamente.

### Conclusão

O padrão Mediator é uma maneira eficaz de promover o desacoplamento entre objetos, facilitando a comunicação indireta entre eles. Ele centraliza a lógica de comunicação em um único local, simplificando a manutenção e a expansão do sistema. No entanto, é importante equilibrar a complexidade adicional que o padrão pode introduzir com os benefícios de desacoplamento e flexibilidade que ele proporciona.