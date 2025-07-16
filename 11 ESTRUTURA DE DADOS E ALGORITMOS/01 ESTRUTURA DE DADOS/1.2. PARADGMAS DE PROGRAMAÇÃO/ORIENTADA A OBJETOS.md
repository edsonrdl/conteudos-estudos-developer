## 1. O que é Orientação a Objetos

Organiza o código em **objetos** que combinam **estado** (atributos) e **comportamento** (métodos). Cada objeto representa uma entidade do domínio e interage com outros via mensagens (chamadas de método).

> Analogia: cada objeto é como um funcionário numa empresa, com suas próprias funções (métodos) e informações (atributos); eles colaboram para cumprir tarefas.

---

## 2. Características principais

1. **Encapsulamento**
    - Esconde dados internos; expõe apenas o que é necessário via métodos públicos.
2. **Herança**
    - Permite que uma classe (subclasse) reutilize ou estenda código de outra (superclasse).
3. **Polimorfismo**
    - Objetos de diferentes classes podem ser tratados de forma uniforme, desde que compartilhem uma interface ou classe base.
4. **Abstração**
    - Define estruturas de alto nível que focam em “o que” o objeto faz, não “como” internamente.

---

## 3. Exemplo em Java

```java
// Superclasse
public abstract class Animal {
    private String nome;
    public Animal(String nome) { this.nome = nome; }
    public String getNome() { return nome; }
    public abstract void emitirSom();
}

// Subclasse
public class Cachorro extends Animal {
    public Cachorro(String nome) { super(nome); }
    @Override
    public void emitirSom() {
        System.out.println(getNome() + " diz: Au au!");
    }
}

// Uso
public class Main {
    public static void main(String[] args) {
        List<Animal> pets = List.of(
            new Cachorro("Rex"),
            new Gato("Miau")      // outra subclasse de Animal
        );
        for (Animal a : pets) {
            a.emitirSom();       // polimorfismo em ação
        }
    }
}

```

- **Encapsulamento**: atributo `nome` privado.
- **Herança**: `Cachorro` estende `Animal`.
- **Polimorfismo**: lista de `Animal` chamando `emitirSom()`.

---

## 4. Vantagens

- **Organização**
    - Agrupa dados e comportamentos, facilitando a leitura e manutenção.
- **Reuso de código**
    - Herança e composição permitem evitar duplicação.
- **Flexibilidade**
    - Polimorfismo e interfaces simplificam trocas de implementação.
- **Modelagem natural**
    - Reflete conceitos do mundo real, tornando requisitos mais claros.

---

## 5. Desvantagens

- **Sobrecarga**
    - Pode gerar hierarquias complexas e verbosas.
- **Acoplamento excessivo**
    - Má modelagem leva a dependências difíceis de alterar.
- **Consumo de memória**
    - Objetos carregam metadados e overhead de runtime (GC, vtables).
- **Curva de aprendizado**
    - Padrões (Factory, Strategy, Observer) exigem estudo para aplicar bem.

---

## 6. Quando usar

- Projetos de médio e grande porte, com domínios ricos em entidades e relações.
- Sistemas que evoluem constantemente e requerem extensão de funcionalidades.
- Código em que a clareza do modelo de negócio é fundamental (ex.: ERP, CRM, jogos).