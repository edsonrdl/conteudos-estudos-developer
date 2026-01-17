Claro! O padrão Bridge é um padrão estrutural que desacopla uma abstração de sua implementação, permitindo que os dois variem independentemente. Ele é particularmente útil quando as classes de abstração e implementação precisam ser estendidas por hierarquias independentes.

### Estrutura do Bridge

- **Abstraction**: Define a interface abstrata e mantém uma referência para o objeto Implementor.
- **RefinedAbstraction**: Estende a interface Abstraction.
- **Implementor**: Define a interface para a implementação da classe.
- **ConcreteImplementor**: Implementa a interface Implementor.

Diagrama UML CLASS

[![[Pasted image 20250407125530.png]]
### Exemplo em Java

Vamos usar o exemplo de formas (Shapes) e cores (Colors). As formas podem ser implementadas de várias maneiras (por exemplo, círculos, quadrados), e as cores também podem variar (por exemplo, vermelho, verde).

Interface Implementor

```
public interface Color {
    void applyColor();
}

```

ConcreteImplementor

```
public class RedColor implements Color {
    @Override
    public void applyColor() {
        System.out.println("Applying red color");
    }
}

public class GreenColor implements Color {
    @Override
    public void applyColor() {
        System.out.println("Applying green color");
    }
}

```

Abstraction

```
public abstract class Shape {
    protected Color color;

    public Shape(Color color) {
        this.color = color;
    }

    abstract void draw();
}

```

RefinedAbstraction

```
public class Circle extends Shape {
    public Circle(Color color) {
        super(color);
    }

    @Override
    void draw() {
        System.out.print("Drawing Circle with ");
        color.applyColor();
    }
}

public class Square extends Shape {
    public Square(Color color) {
        super(color);
    }

    @Override
    void draw() {
        System.out.print("Drawing Square with ");
        color.applyColor();
    }
}

```

Client

```
public class BridgePatternTest {
    public static void main(String[] args) {
        Shape redCircle = new Circle(new RedColor());
        Shape greenSquare = new Square(new GreenColor());

        redCircle.draw();
        greenSquare.draw();
    }
}

```

### Explicação

1. **Implementor (Color)**: Define a interface `Color` que será implementada por classes concretas `RedColor` e `GreenColor`.
2. **ConcreteImplementor (RedColor, GreenColor)**: Implementam a interface `Color` e fornecem a lógica concreta para aplicar cores específicas.
3. **Abstraction (Shape)**: Define a interface `Shape` e mantém uma referência para um objeto `Color`.
4. **RefinedAbstraction (Circle, Square)**: Extendem a classe `Shape` e implementam o método `draw` para desenhar formas específicas com a cor fornecida.

### Conclusão

O padrão Bridge permite que você varie tanto as formas quanto as cores de maneira independente, facilitando a extensão do sistema. Novas formas ou cores podem ser adicionadas sem alterar as classes existentes, promovendo um design mais flexível e escalável.