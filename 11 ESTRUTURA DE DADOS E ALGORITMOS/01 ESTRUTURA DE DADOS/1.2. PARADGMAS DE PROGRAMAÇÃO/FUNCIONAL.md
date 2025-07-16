## 1. O que é o Paradigma Funcional

No funcional, o foco está em **funções puras** e **imutabilidade**.

- **Função pura**: dado um mesmo input, sempre retorna o mesmo output e **não** causa efeitos colaterais (não modifica variáveis externas, nem I/O).
- **Imutabilidade**: estruturas de dados não mudam após criadas; operações geram **novas** estruturas.

> Analogia: é como uma fábrica de biscoitos onde cada máquina recebe uma massa, processa e devolve uma nova massa, sem alterar a original.

---

## 2. Características principais

1. **Primeira-classe**: funções são tratadas como valores — podem ser passadas como parâmetro, retornadas ou armazenadas em variáveis.
2. **Composição**: você “cola” várias funções menores para construir comportamentos complexos.
3. **Funções de alta-ordem**: recebem ou retornam outras funções (ex.: `map`, `filter`, `fold`).
4. **Expressões em vez de comandos**: tudo é expressão e retorna valor, não instrução que modifica estado.
5. **Recursão**: laços `for`/`while` tendem a ser substituídos por chamadas recursivas ou por funções de redução.

---

## 3. Exemplo em Java (uso de Streams)

```java
java
CopiarEditar
List<Integer> numeros = List.of(1, 2, 3, 4, 5);

// Função pura: não altera 'numeros', gera nova lista
List<Integer> quadradosPares = numeros.stream()
    .filter(n -> n % 2 == 0)       // filtra pares
    .map(n -> n * n)               // eleva ao quadrado
    .collect(Collectors.toList()); // coleta numa nova lista

// quadradosPares == [4, 16]

```

- `filter` e `map` são funções de alta-ordem.
- A lista original `numeros` permanece intacta.

---

## 4. Exemplo em Haskell

```haskell
haskell
CopiarEditar
-- Função pura
quadradosPares :: [Int] -> [Int]
quadradosPares xs = map (^2) (filter even xs)

-- No REPL:
-- > quadradosPares [1,2,3,4,5]
-- [4,16]

```

- Sintaxe declarativa: descreve _o que_ fazer.
- Não há variáveis mutáveis nem laços imperativos.

---

## 5. Vantagens

1. **Legibilidade e concisão**
    - Fluxos de transformação claros; reduz boilerplate de controle de fluxo.
2. **Facilidade de testes**
    - Funções puras são determinísticas e testáveis isoladamente.
3. **Imutabilidade**
    - Evita bugs de concorrência e efeitos colaterais indesejados.
4. **Reuso e composição**
    - Peças de lógica podem ser combinadas em novos comportamentos sem reescrever código.
5. **Paralelismo mais seguro**
    - Sem estado compartilhado mutável, é trivial paralelizar operações em coleções.

---

## 6. Desvantagens

1. **Curva de aprendizado**
    - Pensar em imutabilidade, recursão e composição exige mudança de mentalidade.
2. **Possível overhead de memória**
    - Criação de muitas estruturas imutáveis pode consumir mais memória.
3. **Recursão profunda**
    - Se não houver otimização de _tail recursion_, pode causar _stack overflow_.
4. **Interoperabilidade**
    - Código puramente funcional pode “soar estranho” em projetos orientados a objetos ou imperativos.

---

## 7. Quando usar

- **Transformação de coleções** e **pipelines de dados** (ETL, processamento em lote).
- **Sistemas concorrentes** ou distribuídos, onde imutabilidade reduz condições de corrida.
- **Domínios matemáticos** e de **linguagens de domínio** (DSLs), onde funções puras facilitam raciocínio.