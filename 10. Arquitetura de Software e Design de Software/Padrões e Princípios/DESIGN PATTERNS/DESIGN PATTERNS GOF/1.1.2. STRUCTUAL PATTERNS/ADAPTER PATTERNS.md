O padrão Adapter (Adaptador) é um padrão estrutural que permite que objetos com interfaces incompatíveis trabalhem juntos. Ele funciona como um intermediário que traduz ou adapta uma interface de uma classe para outra interface que um cliente espera. Vamos explorar o padrão Adapter com mais detalhes, incluindo uma explicação teórica, exemplos práticos e diferentes variações de implementação.

### Explicação Teórica

Motivação

Imagine que você está desenvolvendo um sistema e precisa integrar uma biblioteca ou um componente de terceiros. No entanto, a interface deste componente é diferente da interface esperada pelo seu sistema. O padrão Adapter resolve este problema criando uma classe adaptadora que converte a interface do componente de terceiros para a interface esperada pelo sistema.

Estrutura

A estrutura do padrão Adapter inclui os seguintes componentes:

1. **Target**: Define a interface que o cliente usa.
2. **Adapter**: Adapta a interface do Adaptee para a interface Target.
3. **Adaptee**: Define uma interface existente que precisa ser adaptada.
4. **Client**: Interage com os objetos através da interface Target.

### Exemplo Prático

Vamos considerar um exemplo onde temos um sistema de desenho que espera objetos com uma interface `Shape`. No entanto, queremos integrar uma biblioteca de terceiros que possui uma classe `LegacyRectangle` com uma interface diferente.

### Diferentes Variações de Implementação

Adapter de Classe (Herança)

Em linguagens que suportam herança múltipla, um adaptador de classe pode ser criado herdando tanto a interface Target quanto a interface Adaptee.

Adapter de Objeto (Composição)

O exemplo acima demonstra um adaptador de objeto, onde o adaptador contém uma instância do adaptee e delega as chamadas de métodos apropriados a ele. Este é o método mais comum em linguagens que não suportam herança múltipla.

Diagrama UML CLASS

![[Pasted image 20250407125432.png]]

### Exemplo em Java

Vamos ver um exemplo similar em Java.

Interface Target

```
public interface Shape {
    void draw();
}

```

Adaptee

```
public class LegacyRectangle {
    private int x1, y1, x2, y2;

    public LegacyRectangle(int x1, int y1, int x2, int y2) {
        this.x1 = x1;
        this.y1 = y1;
        this.x2 = x2;
        this.y2 = y2;
    }

    public void drawRectangle() {
        System.out.println("Drawing rectangle from (" + x1 + ", " + y1 + ") to (" + x2 + ", " + y2 + ")");
    }
}

```

Adapter

```
public class RectangleAdapter implements Shape {
    private LegacyRectangle legacyRectangle;

    public RectangleAdapter(LegacyRectangle legacyRectangle) {
        this.legacyRectangle = legacyRectangle;
    }

    @Override
    public void draw() {
        legacyRectangle.drawRectangle();
    }
}

```

Client

```
public class Main {
    public static void main(String[] args) {
        LegacyRectangle legacyRectangle = new LegacyRectangle(1, 1, 10, 10);
        Shape rectangleAdapter = new RectangleAdapter(legacyRectangle);
        rectangleAdapter.draw();  // Output: Drawing rectangle from (1, 1) to (10, 10)
    }
}

```

### Benefícios e Considerações

Benefícios

1. **Reusabilidade**: Permite reutilizar classes existentes mesmo que suas interfaces não correspondam.
2. **Flexibilidade**: Facilita a adaptação de uma classe a uma nova interface sem alterar a classe existente.
3. **Simplicidade**: Permite que clientes trabalhem com uma interface uniforme.

Considerações

1. **Complexidade**: Pode adicionar complexidade adicional ao sistema se não for usado de maneira criteriosa.
2. **Performance**: Pode introduzir uma pequena sobrecarga de desempenho devido à chamada de métodos adicionais.

### Conclusão

O padrão Adapter é uma ferramenta poderosa para integrar componentes de terceiros ou classes com interfaces incompatíveis em um sistema existente. Ao entender e aplicar este padrão, você pode melhorar a flexibilidade e reusabilidade do seu código, facilitando a manutenção e evolução do sistema.