O padrão Flyweight é um padrão estrutural que permite que um grande número de objetos seja compartilhado de forma eficiente, utilizando a memória de maneira otimizada. O objetivo principal é minimizar o uso de memória compartilhando o máximo possível de dados entre objetos semelhantes.

### Estrutura do Flyweight

- **Flyweight**: Interface comum para os objetos que podem ser compartilhados.
- **ConcreteFlyweight**: Implementa a interface Flyweight e compartilha o estado interno.
- **UnsharedConcreteFlyweight**: Objeto que não pode ser compartilhado.
- **FlyweightFactory**: Cria e gerencia objetos Flyweight, garantindo que os objetos sejam compartilhados corretamente.
- **Client**: Mantém referências para os objetos Flyweight e interage com eles.

Diagram UML CLASS

![[Pasted image 20250407125818.png]]
### Exemplo em Java

Vamos considerar um exemplo de um editor de texto onde cada caractere pode ser considerado um objeto. Usando o padrão Flyweight, podemos compartilhar a representação de cada caractere para economizar memória.

Flyweight

```
public interface CharacterFlyweight {
    void display(int row, int column);
}

```

ConcreteFlyweight

```
public class CharacterConcreteFlyweight implements CharacterFlyweight {
    private final char character;

    public CharacterConcreteFlyweight(char character) {
        this.character = character;
    }

    @Override
    public void display(int row, int column) {
        System.out.println("Character: " + character + " at (" + row + ", " + column + ")");
    }
}

```

FlyweightFactory

```
import java.util.HashMap;
import java.util.Map;

public class CharacterFlyweightFactory {
    private Map<character,> flyweights = new HashMap<>();

    public CharacterFlyweight getFlyweight(char character) {
        CharacterFlyweight flyweight = flyweights.get(character);
        if (flyweight == null) {
            flyweight = new CharacterConcreteFlyweight(character);
            flyweights.put(character, flyweight);
        }
        return flyweight;
    }
}

```

Client

```
public class FlyweightPatternTest {
    public static void main(String[] args) {
        CharacterFlyweightFactory factory = new CharacterFlyweightFactory();

        String document = "Hello, Flyweight Pattern!";
        int row = 0;

        for (int i = 0; i < document.length(); i++) {
            CharacterFlyweight flyweight = factory.getFlyweight(document.charAt(i));
            flyweight.display(row, i);
        }
    }
}

```

### Explicação

1. **Flyweight (CharacterFlyweight)**: Define a interface `CharacterFlyweight` que declara o método `display`.
2. **ConcreteFlyweight (CharacterConcreteFlyweight)**: Implementa a interface `CharacterFlyweight` e armazena o estado intrínseco (o próprio caractere).
3. **FlyweightFactory (CharacterFlyweightFactory)**: Gerencia a criação e o compartilhamento dos objetos `CharacterFlyweight`. Garante que um objeto seja criado uma vez e reutilizado sempre que necessário.
4. **Client (FlyweightPatternTest)**: Usa o `CharacterFlyweightFactory` para obter instâncias de `CharacterFlyweight` e exibi-las no documento.

### Saída Esperada

Character: H at (0, 0)

Character: e at (0, 1)

Character: l at (0, 2)

Character: l at (0, 3)

Character: o at (0, 4)

Character: , at (0, 5)

Character: at (0, 6)

Character: F at (0, 7)

Character: l at (0, 8)

Character: y at (0, 9)

Character: w at (0, 10)

Character: e at (0, 11)

Character: i at (0, 12)

Character: g at (0, 13)

Character: h at (0, 14)

Character: t at (0, 15)

Character: at (0, 16)

Character: P at (0, 17)

Character: a at (0, 18)

Character: t at (0, 19)

Character: t at (0, 20)

Character: e at (0, 21)

Character: r at (0, 22)

Character: n at (0, 23)

Character: ! at (0, 24)

### Benefícios e Considerações

Benefícios

1. **Economia de Memória**: Reduz significativamente o uso de memória ao compartilhar objetos semelhantes.
2. **Eficiência**: Melhora a performance de sistemas que precisam manipular um grande número de objetos pequenos.

Considerações

1. **Complexidade Adicional**: Introduz complexidade no código ao gerenciar a criação e o compartilhamento de objetos.
2. **Equilíbrio de Estados**: Necessidade de balancear o estado intrínseco (compartilhado) e o estado extrínseco (não compartilhado).

### Conclusão

O padrão Flyweight é eficaz para otimizar o uso de memória e melhorar a performance em sistemas que manipulam muitos objetos semelhantes. Ele promove o compartilhamento de objetos para economizar recursos, mantendo a flexibilidade e a capacidade de gerenciar estados individuais quando necessário.