O padrão Composite é um padrão estrutural que permite que você trate objetos individuais e composições de objetos de maneira uniforme. Este padrão é particularmente útil para representar hierarquias de partes e inteiros.

### Estrutura do Composite

- **Component**: Define a interface para todos os objetos na composição.
- **Leaf**: Representa objetos finais da composição (objetos simples).
- **Composite**: Representa objetos compostos (coleções de outros objetos).

Diagrama UML CLASS

![[Pasted image 20250407125621.png]]

### Exemplo em Java

Vamos considerar um exemplo onde temos uma estrutura de diretórios e arquivos em um sistema de arquivos.

Interface Component

```
public interface FileSystemComponent {
    void showDetails();
}

```

Leaf

```
public class File implements FileSystemComponent {
    private String name;

    public File(String name) {
        this.name = name;
    }

    @Override
    public void showDetails() {
        System.out.println("File: " + name);
    }
}

```

Composite

```
import java.util.ArrayList;
import java.util.List;

public class Directory implements FileSystemComponent {
    private String name;
    private List<filesystemcomponent> components = new ArrayList<>();

    public Directory(String name) {
        this.name = name;
    }

    public void addComponent(FileSystemComponent component) {
        components.add(component);
    }

    public void removeComponent(FileSystemComponent component) {
        components.remove(component);
    }

    @Override
    public void showDetails() {
        System.out.println("Directory: " + name);
        for (FileSystemComponent component : components) {
            component.showDetails();
        }
    }
}

```

Client

```
public class CompositePatternTest {
    public static void main(String[] args) {
        // Creating leaf nodes
        File file1 = new File("file1.txt");
        File file2 = new File("file2.txt");
        File file3 = new File("file3.txt");

        // Creating composite nodes
        Directory dir1 = new Directory("dir1");
        Directory dir2 = new Directory("dir2");

        // Building the tree structure
        dir1.addComponent(file1);
        dir1.addComponent(file2);

        dir2.addComponent(file3);
        dir2.addComponent(dir1); // Adding dir1 to dir2

        // Displaying the tree structure
        dir2.showDetails();
    }
}

```

### Explicação

1. **Component (FileSystemComponent)**: Define a interface `FileSystemComponent` que declara o método `showDetails`.
2. **Leaf (File)**: Implementa a interface `FileSystemComponent` representando um arquivo simples.
3. **Composite (Directory)**: Implementa a interface `FileSystemComponent` e contém uma coleção de componentes filhos, permitindo a adição e remoção de componentes.
4. **Client (CompositePatternTest)**: Cria e manipula a estrutura de árvore composta por arquivos e diretórios, tratando-os de forma uniforme.

### Saída Esperada

Directory: dir2

File: file3.txt

Directory: dir1

File: file1.txt

File: file2.txt

### Benefícios e Considerações

Benefícios

1. **Uniformidade**: Trata objetos individuais e composições de objetos de maneira uniforme.
2. **Flexibilidade**: Facilita a adição de novos componentes sem alterar o código existente.
3. **Simplificação**: Reduz a complexidade ao permitir que os clientes manipulem objetos simples e compostos de maneira uniforme.

Considerações

1. **Complexidade**: Pode adicionar complexidade ao design devido à necessidade de gerenciar a estrutura de árvore.
2. **Desempenho**: Pode impactar o desempenho se a árvore composta for muito profunda ou ampla, devido à navegação recursiva.

### Conclusão

O padrão Composite é uma poderosa técnica para construir e trabalhar com hierarquias de objetos. Ele promove um design flexível e extensível, permitindo que você trate composições e objetos individuais de maneira uniforme, facilitando a manutenção e expansão do sistema.