Em Java, toda expressão lambda implementa uma _functional interface_ — uma interface com exatamente um método abstrato. As mais comuns:

- **`Predicate<T>`**: recebe `T`, retorna `boolean`.
- **`Function<T,R>`**: recebe `T`, retorna `R`.
- **`Consumer<T>`**: recebe `T`, retorna `void`.
- **`Supplier<T>`**: não recebe nada, retorna `T`.

```java
// Predicate: testa se um número é par
Predicate<Integer> isEven = n -> n % 2 == 0;

// Function: converte Integer em String
Function<Integer, String> toString = n -> "Número: " + n;

// Consumer: imprime no console
Consumer<String> printer = s -> System.out.println(s);

// Supplier: fornece uma data/hora atual
Supplier<LocalDateTime> now = () -> LocalDateTime.now();

```

---

## 2. Streams + lambdas: pipeline de dados

Imagine uma lista de inteiros; queremos **filtrar**, **transformar** e **coletar** resultados:

```java
java
CopiarEditar
List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6);

List<String> resultado = numeros.stream()
    .filter(n -> n % 2 == 0)             // Predicate<Integer>
    .map(n -> n * n)                     // Function<Integer,Integer>
    .map(n2 -> "Quadrado: " + n2)        // Function<Integer,String>
    .peek(System.out::println)          // Consumer<String> (para debug)
    .collect(Collectors.toList());       // coleta num List<String>

// resultado == ["Quadrado: 4", "Quadrado: 16", "Quadrado: 36"]

```

- **`.stream()`** cria o fluxo.
- **`.filter(...)`** descarta ímpares.
- **`.map(...)`** aplica transformação (ao quadrado e depois prefixo).
- **`.peek(...)`** inspeciona sem alterar.
- **`.collect(...)`** materializa a lista final.

---

## 3. Composição de funções

Você pode **encadear** `Function` usando `.andThen()` ou `.compose()`:

```java
java
CopiarEditar
Function<Integer, Integer> timesTwo    = x -> x * 2;
Function<Integer, Integer> plusTen     = x -> x + 10;

// primeiro timesTwo, depois plusTen
Function<Integer, Integer> pipeline1 = timesTwo.andThen(plusTen);
System.out.println(pipeline1.apply(5));  // (5*2)+10 = 20

// primeiro plusTen, depois timesTwo
Function<Integer, Integer> pipeline2 = timesTwo.compose(plusTen);
System.out.println(pipeline2.apply(5));  // (5+10)*2 = 30

```

---

## 4. Optional com lambdas

`Optional<T>` traz métodos funcionais para tratar valor que pode estar ausente:

```java
java
CopiarEditar
Optional<String> talvezNome = Optional.of("Leo");

String resultado = talvezNome
    .filter(nome -> nome.length() > 3)           // testa tamanho
    .map(String::toUpperCase)                    // converte a maiúsculas
    .orElse("DESCONHECIDO");                     // valor padrão

// resultado == "LEO" (pois length==3 não >3, cai no orElse)

```

---

## 5. Exemplo completo: processando objetos

Suponha uma classe `Produto`:

```java
java
CopiarEditar
public record Produto(String nome, BigDecimal preco) { }

```

Queremos:

1. Filtrar produtos com preço > 100.
2. Extrair apenas o nome.
3. Ordenar alfabeticamente.
4. Imprimir cada nome.

```java
java
CopiarEditar
List<Produto> produtos = List.of(
    new Produto("Caneta", new BigDecimal("5.50")),
    new Produto("Caderno", new BigDecimal("120.00")),
    new Produto("Mochila", new BigDecimal("250.00"))
);

produtos.stream()
    .filter(p -> p.preco().compareTo(new BigDecimal("100")) > 0)  // preço >100
    .map(Produto::nome)                                            // pega nome
    .sorted()                                                      // ordena
    .forEach(System.out::println);                                // imprime

// Saída:
// Caderno
// Mochila

```

---

## 6. Vantagens do estilo funcional com lambdas

- **Código conciso**: elimina loops e variáveis temporárias.
- **Legibilidade**: o pipeline mostra claramente os passos de transformação.
- **Imutabilidade**: evita efeitos colaterais, pois não modifica a coleção original.
- **Paralelismo fácil**: basta trocar `.stream()` por `.parallelStream()`.

## 7. Limitações

- **Depuração**: o stack trace pode mostrar apenas o pipeline genérico, não cada etapa.
- **Sobrecarga**: criar muitos objetos intermediários (streams, lambdas) pode impactar performance em loops muito intensivos.
- **Curva de aprendizado**: exige pensar em funções puras e composição, não em laços imperativos.