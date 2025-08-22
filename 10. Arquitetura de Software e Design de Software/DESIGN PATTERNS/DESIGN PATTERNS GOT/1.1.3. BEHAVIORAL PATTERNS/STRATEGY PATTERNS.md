O padrão Strategy é um padrão comportamental que permite que um algoritmo seja selecionado em tempo de execução a partir de uma família de algoritmos encapsulados e intercambiáveis. Ele permite que o algoritmo varie independentemente dos clientes que o utilizam.

### Estrutura do Strategy

- **Context (Contexto)**: Mantém uma referência para um objeto Strategy e pode alterar o algoritmo usado em tempo de execução.
- **Strategy (Estratégia)**: Interface comum para todos os algoritmos suportados.
- **ConcreteStrategy (EstratégiaConcreta)**: Implementações específicas dos algoritmos.

Diagrama UIML CLASS

![[Pasted image 20250407130244.png]]
### Exemplo em Java

Vamos considerar um exemplo de um sistema de pagamento onde diferentes estratégias de pagamento podem ser selecionadas (por exemplo, cartão de crédito, PayPal, transferência bancária) para realizar um pagamento.

Strategy

```
public interface PaymentStrategy {
    void pay(double amount);
}

public class CreditCardPayment implements PaymentStrategy {
    private String cardNumber;
    private String expiryDate;
    private String cvv;

    public CreditCardPayment(String cardNumber, String expiryDate, String cvv) {
        this.cardNumber = cardNumber;
        this.expiryDate = expiryDate;
        this.cvv = cvv;
    }

    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " using credit card.");
    }
}

public class PayPalPayment implements PaymentStrategy {
    private String email;
    private String password;

    public PayPalPayment(String email, String password) {
        this.email = email;
        this.password = password;
    }

    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " using PayPal.");
    }
}

```

Context

```
public class ShoppingCart {
    private PaymentStrategy paymentStrategy;

    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void checkout(double amount) {
        paymentStrategy.pay(amount);
    }
}

```

Client

```
public class StrategyPatternTest {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();

        // Seleciona a estratégia de pagamento como cartão de crédito
        cart.setPaymentStrategy(new CreditCardPayment("1234567890123456", "12/25", "123"));
        cart.checkout(100.0);

        // Muda a estratégia de pagamento para PayPal
        cart.setPaymentStrategy(new PayPalPayment("example@example.com", "password"));
        cart.checkout(200.0);
    }
}

```

### Explicação

1. **Strategy (Estratégia)**: Define a interface comum para todos os algoritmos suportados.
2. **ConcreteStrategy (EstratégiaConcreta)**: Implementa a interface Strategy e fornece implementações específicas para os algoritmos.
3. **Context (Contexto)**: Mantém uma referência para um objeto Strategy e pode alterar a estratégia em tempo de execução.
4. **Client (Cliente)**: Cria uma instância do contexto e define a estratégia a ser usada.

### Saída Esperada

Paying 100.0 using credit card.

Paying 200.0 using PayPal.

### Benefícios e Considerações

Benefícios

1. **Desacoplamento**: Separa a lógica de negócios do código cliente, permitindo que o cliente selecione diferentes algoritmos sem modificar seu código.
2. **Reutilização**: Permite a reutilização de algoritmos em diferentes contextos.
3. **Facilidade de Extensão**: Novos algoritmos podem ser adicionados facilmente, pois não interferem no código existente.

Considerações

1. **Complexidade Adicional**: Introduz classes adicionais para representar diferentes estratégias, o que pode aumentar a complexidade do código.
2. **Escolha da Estratégia**: A escolha da estratégia correta pode depender do contexto e pode não ser trivial.
3. **Visibilidade de Estratégias**: Nem sempre é fácil determinar todas as estratégias disponíveis.

### Conclusão

O padrão Strategy é uma ferramenta útil para separar a lógica de negócios do código cliente e permitir a seleção de diferentes algoritmos em tempo de execução. Ele promove a flexibilidade e a reutilização de código ao encapsular algoritmos intercambiáveis em objetos separados. No entanto, é importante considerar a complexidade adicional introduzida pelo padrão em relação aos benefícios que ele proporciona.