O padrão Proxy é um padrão estrutural que fornece um substituto ou um representante de outro objeto para controlar o acesso a este. Existem várias variações do padrão Proxy, incluindo Remote Proxy, Virtual Proxy, Protection Proxy, entre outros.

### Estrutura do Proxy

- **Subject**: Interface comum para RealSubject e Proxy.
- **RealSubject**: Objeto real que o proxy representa.
- **Proxy**: Mantém uma referência para o RealSubject e controla o acesso a ele.

### Exemplo em Java

Vamos considerar um exemplo onde temos uma interface de "Image" que é implementada tanto por uma classe concreta `RealImage` quanto por um `ProxyImage`. O `ProxyImage` controlará a criação e o carregamento da `RealImage`.

Subject

```
public interface Image {
    void display();
}

```

RealSubject

```
public class RealImage implements Image {
    private String filename;

    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();
    }

    private void loadFromDisk() {
        System.out.println("Loading " + filename);
    }

    @Override
    public void display() {
        System.out.println("Displaying " + filename);
    }
}

```

Proxy

```
public class ProxyImage implements Image {
    private RealImage realImage;
    private String filename;

    public ProxyImage(String filename) {
        this.filename = filename;
    }

    @Override
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(filename);
        }
        realImage.display();
    }
}

```

Client

```
public class ProxyPatternTest {
    public static void main(String[] args) {
        Image image1 = new ProxyImage("test_10mb.jpg");
        Image image2 = new ProxyImage("test_20mb.jpg");

        // Image will be loaded from disk
        image1.display();
        System.out.println("");

        // Image will not be loaded from disk
        image1.display();
        System.out.println("");

        // Image will be loaded from disk
        image2.display();
        System.out.println("");

        // Image will not be loaded from disk
        image2.display();
    }
}

```

### Explicação

1. **Subject (Image)**: Define a interface `Image` que declara o método `display`.
2. **RealSubject (RealImage)**: Implementa a interface `Image` e contém a lógica real de carregamento e exibição da imagem.
3. **Proxy (ProxyImage)**: Implementa a interface `Image` e controla o acesso ao objeto `RealImage`. Ele cria o objeto `RealImage` apenas quando necessário (quando o método `display` é chamado pela primeira vez).

### Saída Esperada

Loading test_10mb.jpg

Displaying test_10mb.jpg

Displaying test_10mb.jpg

Loading test_20mb.jpg

Displaying test_20mb.jpg

Displaying test_20mb.jpg

### Benefícios e Considerações

Benefícios

1. **Controle de Acesso**: Pode controlar o acesso ao objeto real, adicionando funcionalidades como autenticação, controle de acesso, etc.
2. **Carga sob Demanda**: Útil para carregar objetos pesados somente quando necessário, economizando recursos.
3. **Desempenho**: Pode melhorar o desempenho ao diferir operações custosas até que sejam absolutamente necessárias.

Considerações

1. **Complexidade Adicional**: Introduz uma camada extra de indirection, o que pode adicionar complexidade ao sistema.
2. **Manutenção**: O código do Proxy precisa ser mantido em sincronia com o RealSubject, o que pode aumentar o esforço de manutenção.

### Conclusão

O padrão Proxy é uma ferramenta poderosa para controlar o acesso a objetos e gerenciar recursos de maneira eficiente. Ele oferece uma camada adicional que pode ser usada para diferir a criação de objetos pesados, adicionar controle de acesso, e mais, enquanto mantém a interface uniforme para os clientes.