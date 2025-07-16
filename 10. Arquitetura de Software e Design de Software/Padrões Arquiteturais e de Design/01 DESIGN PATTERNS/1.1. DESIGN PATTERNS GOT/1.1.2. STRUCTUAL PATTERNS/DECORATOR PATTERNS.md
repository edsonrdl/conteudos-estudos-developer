O padrão Decorator é um padrão estrutural que permite adicionar responsabilidades a objetos dinamicamente. Ele fornece uma alternativa flexível ao uso de subclasses para estender funcionalidades, permitindo que os comportamentos sejam combinados de forma mais dinâmica e modular.

### Estrutura do Decorator

- **Component**: Define a interface para os objetos que podem ter responsabilidades adicionais.
- **ConcreteComponent**: Implementa a interface `Component` e define um objeto ao qual podem ser adicionadas responsabilidades.
- **Decorator**: Mantém uma referência para um objeto `Component` e define uma interface que se conforma com a interface `Component`.
- **ConcreteDecorator**: Estende `Decorator` e adiciona responsabilidades ao objeto `Component`.

Diagrama UML CLASS

![[Pasted image 20250407125752.png]]
### Exemplo em Java

Vamos usar o exemplo de uma cafeteria onde diferentes tipos de bebidas podem ser decorados com diferentes complementos (como leite, açúcar, etc.).

Interface Component

```
public interface Beverage {
    String getDescription();
    double cost();
}

```

ConcreteComponent

```
public class Espresso implements Beverage {
    @Override
    public String getDescription() {
        return "Espresso";
    }

    @Override
    public double cost() {
        return 1.99;
    }
}

public class HouseBlend implements Beverage {
    @Override
    public String getDescription() {
        return "House Blend Coffee";
    }

    @Override
    public double cost() {
        return 0.89;
    }
}

```

Decorator

```
public abstract class CondimentDecorator implements Beverage {
    protected Beverage beverage;

    public CondimentDecorator(Beverage beverage) {
        this.beverage = beverage;
    }

    public abstract String getDescription();
}

```

ConcreteDecorator

```
public class Mocha extends CondimentDecorator {
    public Mocha(Beverage beverage) {
        super(beverage);
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Mocha";
    }

    @Override
    public double cost() {
        return beverage.cost() + 0.20;
    }
}

public class Whip extends CondimentDecorator {
    public Whip(Beverage beverage) {
        super(beverage);
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Whip";
    }

    @Override
    public double cost() {
        return beverage.cost() + 0.10;
    }
}

```

Client

```
public class CoffeeShop {
    public static void main(String[] args) {
        Beverage beverage = new Espresso();
        System.out.println(beverage.getDescription() + " $" + beverage.cost());

        Beverage beverage2 = new HouseBlend();
        beverage2 = new Mocha(beverage2);
        beverage2 = new Whip(beverage2);
        System.out.println(beverage2.getDescription() + " $" + beverage2.cost());
    }
}

```

### Explicação

1. **Component (Beverage)**: Define a interface `Beverage` que declara métodos para obter a descrição e o custo da bebida.
2. **ConcreteComponent (Espresso, HouseBlend)**: Implementa a interface `Beverage` representando tipos concretos de bebidas.
3. **Decorator (CondimentDecorator)**: Implementa a interface `Beverage` e mantém uma referência para um objeto `Beverage`.
4. **ConcreteDecorator (Mocha, Whip)**: Extende `CondimentDecorator` e adiciona responsabilidades adicionais (como `Mocha` e `Whip`) ao objeto `Beverage`.

### Saída Esperada

```
Espresso $1.99
House Blend Coffee, Mocha, Whip $1.19

```

### Benefícios e Considerações

Benefícios

1. **Flexibilidade**: Adiciona responsabilidades aos objetos de forma dinâmica, sem a necessidade de criar subclasses.
2. **Reutilização de Código**: Combina diferentes comportamentos de forma modular e reutilizável.
3. **Transparência**: Cada decorator é transparente para o cliente, mantendo a interface `Beverage`.

Considerações

1. **Complexidade**: Pode adicionar complexidade devido ao número de pequenos objetos que podem ser combinados.
2. **Ordens dos Decorators**: A ordem dos decorators pode ser importante e afetar o comportamento e o resultado final.

### Conclusão

O padrão Decorator é uma ferramenta poderosa para adicionar responsabilidades a objetos de forma flexível e dinâmica. Ele permite a composição de comportamentos de maneira modular, promovendo a reutilização de código e a manutenção do sistema. Ao entender e aplicar este padrão, você pode criar sistemas mais flexíveis e extensíveis.