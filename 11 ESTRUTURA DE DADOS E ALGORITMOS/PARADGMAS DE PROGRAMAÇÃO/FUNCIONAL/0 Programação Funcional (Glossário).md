# 📚 Guia de Estudos: Programação Funcional

> [!info] Visão Geral
> A Programação Funcional (FP) é um paradigma de programação declarativa que trata a computação como a avaliação de funções matemáticas. O seu núcleo baseia-se em evitar a mutação de estado e os efeitos colaterais (*side effects*). Na arquitectura de software moderna, a FP é a principal ferramenta para garantir *Thread-Safety* nativo em sistemas de altíssima concorrência, permitindo o processamento paralelo em múltiplos núcleos de CPU sem a necessidade de bloqueios complexos (*Locks* ou *Mutexes*).

---

## 1 Domínio: Princípios Fundamentais e Matemáticos

### 1.1 Funções Puras e Transparência Referencial
- **1.1.1. Funções Puras (Pure Functions)**
  - 1.1.1.1. **Determinismo:** Uma função é pura se, ao receber os mesmos argumentos, retornar absolutamente sempre o mesmo resultado. O seu comportamento não depende de variáveis globais, tempo do sistema ou estado externo.
  - 1.1.1.2. **Ausência de Efeitos Colaterais (Side Effects):** A função não altera nada fora do seu escopo local. Não escreve em bases de dados, não altera variáveis globais, nem escreve no console (I/O).
- **1.1.2. Transparência Referencial**
  - 1.1.2.1. **Mecânica:** É a propriedade que garante que uma expressão pode ser substituída pelo seu resultado sem alterar o comportamento do programa.
  - 1.1.2.2. **Vantagem em Produção (Memoization):** Permite optimizações extremas a nível de compilador e a criação de *caches* eficientes, pois o sistema sabe que a chamada `CalcularImposto(100)` será sempre `15`, podendo simplesmente devolver o valor guardado na memória.

### 1.2 Imutabilidade e Estado
- **1.2.1. O Fim da Mutação In-Place**
  - 1.2.1.1. **Mecânica:** Em vez de alterar o valor de uma variável existente (mutação), uma nova estrutura de dados é criada com a alteração aplicada. O estado original permanece intacto.
  - 1.2.1.2. **Trade-off de Desempenho (Garbage Collection):** Criar cópias constantes de objectos gera um *overhead* massivo na alocação de memória e pressão sobre o *Garbage Collector*.
- **1.2.2. Partilha Estrutural (Structural Sharing)**
  - 1.2.2.1. **A Solução para a Performance:** Linguagens funcionais (ou bibliotecas como Immutable.js) não copiam o objecto inteiro. Utilizam estruturas de dados persistentes (como *Tries* - Árvores de Prefixos), onde a nova versão do objecto partilha a grande maioria dos ponteiros de memória com a versão antiga, copiando apenas o nó que foi alterado.

---

## 2 Domínio: Funções como Cidadãos de Primeira Classe

### 2.1 Funções de Ordem Superior (Higher-Order Functions)
- **2.1.1. Passagem e Retorno de Funções**
  - 2.1.1.1. **Mecânica:** Funções podem receber outras funções como argumentos ou retornar funções como resultado (implementado via *Lambdas* ou *Delegates*).
  - 2.1.1.2. **Map, Filter, Reduce:** Os três pilares do processamento de colecções e *Streams*. Isolam a lógica de iteração (o laço `for`) da lógica de negócio.

### 2.2 Currying e Aplicação Parcial
- **2.2.1. Decomposição de Argumentos**
  - 2.2.1.1. **Currying:** É a técnica de transformar uma função que recebe múltiplos argumentos (ex: `f(a, b, c)`) numa sequência de funções que recebem um único argumento (ex: `f(a)(b)(c)`).
  - 2.2.1.2. **Aplicação Parcial (Partial Application):** Consiste em fixar alguns argumentos de uma função, gerando uma nova função com menor aridade (menos parâmetros). 
  - 2.2.1.3. **Uso Arquitectural:** Excelente para a injeção de dependências estática e configuração de fábricas (*Factories*) sem recorrer a frameworks pesados de DI.

---

## 3 Domínio: Controlo de Fluxo e Recursão

### 3.1 O Fim dos Laços de Repetição
- **3.1.1. Recursão como Padrão Primário**
  - 3.1.1.1. **Mecânica:** Na FP pura, não existem laços `for` ou `while` (pois eles dependem da mutação de uma variável de controlo, como `i++`). A iteração é feita chamando a própria função.
- **3.1.2. Tail Call Optimization (TCO)**
  - 3.1.2.1. **O Perigo do Stack Overflow:** A recursão padrão empilha chamadas na memória (*Call Stack*), o que fatalmente leva a um *StackOverflowError* em iterações longas.
  - 3.1.2.2. **A Solução no Compilador (TCO):** Se a chamada recursiva for a **última** instrução a ser executada na função, compiladores modernos (como no Scala ou Node.js em modo estrito) não adicionam um novo quadro à pilha. Eles reutilizam o actual, permitindo recursão infinita com complexidade de memória O(1). (*Nota: O Java nativo ainda não suporta TCO ao nível da JVM*).

---

## 4 Domínio: Gestão de Efeitos Colaterais (Side Effects)

### 4.1 Abstracções Funcionais Avançadas (Monads e Functors)
- **4.1.1. Functors (Mapeamento de Contexto)**
  - 4.1.1.1. **Conceito:** Qualquer tipo de dados que implemente a função `map`. É um "embrulho" (Wrapper) que contém um valor e permite que aplique uma função a esse valor sem o tirar do embrulho. (Ex: Trabalhar com um valor dentro de uma `List` ou de uma `Promise`).
- **4.1.2. Monads (Encadeamento Sequencial)**
  - 4.1.2.1. **Conceito:** São *Functors* que implementam uma operação de achatamento (`flatMap` ou `bind`). Eles permitem encadear operações que retornam o próprio contexto, lidando de forma elegante com efeitos colaterais.
  - 4.1.2.2. **O Padrão `Optional` / `Maybe`:** Em vez de retornar `null` (o "erro de um bilião de dólares") e espalhar `if (x != null)` pelo código, o método retorna um `Optional<User>`. O encadeamento funcional só prossegue se o valor existir, blindando a arquitectura contra `NullReferenceExceptions`.
  - 4.1.2.3. **O Padrão `Result` / `Either`:** Usado para tratamento de erros sem usar blocos `try/catch`. O objecto encapsula ou o Sucesso (Right) ou a Falha (Left), forçando o compilador e o desenvolvedor a tratar ambos os casos explicitamente.

---

## 5 Domínio: Avaliação Arquitectural em Produção

### 5.1 Adopção em Sistemas Modernos
- **5.1.1. Coreografia e Processamento Paralelo**
  - 5.1.1.1. A combinação de Imutabilidade e Funções Puras é o pilar de frameworks de processamento distribuído (como o Apache Spark ou o Hadoop). Se a função não altera o estado global, pode ser executada num nó local ou distribuída por 1000 servidores na nuvem (AWS EMR) sem problemas de concorrência.
- **5.1.2. Arquitectura Orientada a Eventos (EDA)**
  - 5.1.2.1. A FP encaixa perfeitamente no padrão de *Event Sourcing*, onde o estado actual de uma aplicação não é guardado, mas sim calculado através da redução (*reduce*) de um fluxo histórico de eventos imutáveis.
- **5.1.3. O Pragmatismo Moderno (Híbrido)**
  - 5.1.3.1. Construir uma aplicação Web I/O-Bound *inteiramente* em FP estrita (como no Haskell) gera uma curva de aprendizagem inviável para equipas normais. O padrão actual recomendado (usado em C#, Java, TS) é o modelo "Núcleo Funcional, Casca Imperativa" (*Functional Core, Imperative Shell*), onde as regras de negócio centrais são puras e imutáveis, enquanto as camadas de repositório e *controllers* gerem a mutação necessária para interagir com a base de dados e a rede.