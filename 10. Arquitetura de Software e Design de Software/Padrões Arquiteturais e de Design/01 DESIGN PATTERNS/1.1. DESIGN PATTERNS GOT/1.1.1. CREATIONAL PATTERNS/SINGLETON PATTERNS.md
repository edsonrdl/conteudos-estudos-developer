O padrão Singleton é um padrão de design criacional que garante que uma classe tenha apenas uma instância e fornece um ponto global de acesso a essa instância. Esse padrão é útil quando é necessário um controle rigoroso sobre o acesso a uma única instância de um objeto, como em casos de gerenciamento de recursos ou controle de configuração.

- **Singleton**: A classe que é responsável por criar e manter sua única instância.

Diagrama UML CLASS
![[Pasted image 20250407125134.png]]

### Exemplo em Java

Vamos considerar um exemplo de um gerenciador de logs onde queremos garantir que apenas uma instância do gerenciador de logs exista em toda a aplicação.

## Implementação

```java
public class Logger {
    // Instância única da classe Logger
    private static Logger instance;

    // Construtor privado para impedir instanciamento
    private Logger() {
    }

    // Método público para obter a instância única
    public static Logger getInstance() {
        if (instance == null) {
            synchronized (Logger.class) {
                if (instance == null) {
                    instance = new Logger();
                }
            }
        }
        return instance;
    }

    // Método para registrar uma mensagem
    public void log(String message) {
        System.out.println("Log: " + message);
    }
}

```

## Client

```java
public class SingletonPatternTest {
    public static void main(String[] args) {
        // Obter a única instância do Logger
        Logger logger1 = Logger.getInstance();
        Logger logger2 = Logger.getInstance();

        // Registrar mensagens usando a instância do Logger
        logger1.log("Primeira mensagem");
        logger2.log("Segunda mensagem");

        // Verificar se ambas as referências apontam para a mesma instância
        System.out.println("Logger1 e Logger2 são a mesma instância? " + (logger1 == logger2));
    }
}

```

## Explicação

1. **Construtor Privado**: O construtor da classe `Logger` é privado para impedir que a classe seja instanciada diretamente fora dela.
2. **Instância Única**: A classe `Logger` mantém uma referência estática privada para sua única instância.
3. **Método `getInstance`**: O método `getInstance` garante que apenas uma instância da classe `Logger` seja criada. A verificação `if (instance == null)` dentro de um bloco `synchronized` garante que a instância seja criada de forma segura em um ambiente multithread.
4. **Método `log`**: Um método de exemplo que pode ser chamado na instância do `Logger`.

### Saída Esperada

Log: Primeira mensagem

Log: Segunda mensagem

Logger1 e Logger2 são a mesma instância? true

### Benefícios e Considerações

Benefícios

1. **Controle de Acesso**: Garante que apenas uma instância da classe exista, proporcionando um ponto global de acesso.
2. **Redução de Uso de Recursos**: Evita a criação de múltiplas instâncias desnecessárias, economizando recursos.
3. **Facilidade de Acesso**: Fornece um ponto global de acesso à instância única.

Considerações

1. **Dificuldade de Testes**: Pode ser difícil testar classes Singleton porque elas controlam sua própria instância e podem manter estado entre os testes.
2. **Ambientes Multithread**: É necessário cuidado adicional para garantir que o padrão Singleton funcione corretamente em ambientes multithread.
3. **Responsabilidade Única**: A classe Singleton pode violar o princípio de responsabilidade única se ela realizar mais do que apenas gerenciar sua própria instância.

**Casos para Uso**

### Conclusão

O padrão Singleton é uma ferramenta útil para garantir que apenas uma instância de uma classe exista e fornecer um ponto global de acesso a essa instância. Ele é particularmente útil para gerenciamento de recursos e configurações globais. No entanto, é importante considerar as dificuldades de teste e os cuidados necessários em ambientes multithread ao implementar o padrão Singleton.