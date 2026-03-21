# 📚 Guia de Estudos: Erros Críticos em Programação

> [!info] Visão Geral
> Este guia técnico apresenta uma análise completa dos erros mais críticos em programação que desenvolvedores de todos os níveis devem conhecer. O foco é mapear problemas estruturais e arquiteturais que impactam significativamente a qualidade do código e podem levar a falhas críticas em produção, abrangendo desde orientação a objetos até vazamentos de memória e concorrência.

---

## 1 Domínio: Orientação a Objetos e Contratos

### 1.1 Override e Sobrescrita de Métodos
- **1.1.1. Problemas de assinatura incorreta e @Override**
  - 1.1.1.1. **Erro Comum:** Incompatibilidade de assinatura (ex: esquecer de passar o parâmetro no método sobrescrito, quebrando a herança).
  - 1.1.1.2. **Explicação Técnica:** O compilador exige correspondência exata. A anotação `@Override` força a verificação em tempo de compilação, evitando que o método seja tratado como uma simples sobrecarga.
  - 1.1.1.3. **Detecção:** Erro de compilação, alertas da IDE e ferramentas como SpotBugs.

### 1.2 Princípio de Substituição de Liskov (LSP)
- **1.2.1. Violação por Fortalecimento de Pré-condições**
  - 1.2.1.1. **Erro Grave:** Uma classe filha (ex: `Square`) não pode exigir condições mais estritas (ex: proibir larguras negativas) do que a classe pai (ex: `Rectangle`) exigia.
  - 1.2.1.2. **Explicação Técnica:** O código cliente que consome a interface pai vai quebrar inesperadamente em *runtime* ao se deparar com a regra da classe filha.
  - 1.2.1.3. **Design Correto:** Criar abstrações limpas (ex: interface `Shape` independente) em vez de forçar heranças matemáticas que não se traduzem bem em comportamento de software.

### 1.3 Visibilidade e Tratamento de Exceções
- **1.3.1. Redução de Visibilidade**
  - 1.3.1.1. **Erro:** Sobrescrever um método `public` da classe pai com um método `protected` ou `private` na classe filha.
  - 1.3.1.2. **Prevenção:** Uso de SonarQube (Regra S1161) e SpotBugs para análise de herança assimétrica.

---

## 2 Domínio: Tipagem, Conversão e Memória

### 2.1 Cast e Type Conversion
- **2.1.1. ClassCastException e Casting Inseguro**
  - 2.1.1.1. **Downcasting Incorreto:** Tentar forçar um objeto genérico para um tipo específico sem validação prévia.
  - 2.1.1.2. **Explicação Técnica:** O sistema de tipos (ex: Java) força a verificação em runtime. Se a suposição falha, a JVM lança a exceção e derruba a thread.
  - 2.1.1.3. **Solução Segura:** Utilizar *Pattern Matching* (ex: `if (obj instanceof Integer integer)`).

### 2.2 Performance e Autoboxing
- **2.2.1. Boxing/Unboxing Automático Excessivo**
  - 2.2.1.1. **Problema de Performance:** Adicionar milhões de primitivos (ex: `int`) em coleções de objetos (ex: `List<Integer>`) força a linguagem a instanciar objetos em background.
  - 2.2.1.2. **Impacto:** Pode ser 20x mais lento e gera pressão severa no *Garbage Collector*.
  - 2.2.1.3. **Solução:** Para volumes massivos, usar arrays primitivos (ex: `int[]`).

### 2.3 Genéricos (Generics)
- **2.3.1. Type Erasure e Perda de Informação**
  - 2.3.1.1. **Conflito de Sobrecarga:** Métodos como `process(List<String>)` e `process(List<Integer>)` causam erro de compilação porque, em runtime, ambos viram apenas `process(List)`.
  - 2.3.1.2. **Perda Runtime:** Não é possível usar *Reflection* pura para descobrir o tipo `<T>` de uma lista instanciada devido ao apagamento de tipo.

---

## 3 Domínio: Mapeamento de Objetos e Estruturas

### 3.1 Cópias de Objetos (Copying)
- **3.1.1. Shallow vs Deep Copy**
  - 3.1.1.1. **Problema (Shallow Copy):** Copiar um objeto transferindo apenas a referência dos seus objetos internos. Modificar a cópia altera o original.
  - 3.1.1.2. **Solução (Deep Copy):** Criar instâncias inteiramente novas para todos os nós filhos. Em JS: `JSON.parse(JSON.stringify(obj))` ou `_.cloneDeep()`. Em Java: Construtores de cópia explícitos instanciando novas listas.

### 3.2 Referências Circulares
- **3.2.1. Infinite Recursion em Serialização**
  - 3.2.1.1. **Problema:** Objeto A tem lista de B, e B tem lista de A. Bibliotecas como Jackson entram em loop infinito gerando `StackOverflowError`.
  - 3.2.1.2. **Soluções:** Uso de anotações como `@JsonIdentityInfo` ou a dupla `@JsonManagedReference` / `@JsonBackReference` para quebrar o ciclo no mapeamento.

### 3.3 Performance de Mapeamento (Reflection)
- **3.3.1. Gargalos em Reflection-based Mapping**
  - 3.3.1.1. **Análise:** Acessar atributos via Reflection pura é até 2x mais lento que o acesso direto ou código gerado.
  - 3.3.1.2. **Otimização:** Uso de Cache de Metadados (`ConcurrentHashMap` guardando os `Method`) ou adoção de frameworks de geração de código em tempo de compilação (ex: MapStruct).

---

## 4 Domínio: Concorrência e Gerenciamento de Recursos

### 4.1 Vazamentos de Memória (Memory Leaks)
- **4.1.1. Static Collections sem Limpeza**
  - 4.1.1.1. **Erro:** Usar `Map` ou `List` estáticos como "cache" sem política de expiração. O Garbage Collector nunca limpa esses objetos.
  - 4.1.1.2. **Solução:** Utilizar `WeakReference` para permitir a coleta ou delegar para bibliotecas de cache especializadas (ex: Redis, Ehcache).
  - 4.1.1.3. **Detecção:** Analisadores de *Heap Dump* (VisualVM, Eclipse MAT, JProfiler).

### 4.2 Problemas de Concorrência
- **4.2.1. Race Conditions**
  - 4.2.1.1. **Erro:** Operações `count++` não são atômicas (Lê, Modifica, Grava). Múltiplas threads sobrescrevem os valores umas das outras.
  - 4.2.1.2. **Solução:** Uso de classes atômicas (ex: `AtomicInteger.incrementAndGet()`).
- **4.2.2. Deadlocks (Ordenação de Locks)**
  - 4.2.2.1. **Erro:** Thread 1 bloqueia Recurso A e pede o B; Thread 2 bloqueia Recurso B e pede o A. Ambas travam para sempre.
  - 4.2.2.2. **Solução:** Forçar uma ordem global de bloqueio (ex: sempre bloquear pelo menor ID primeiro).

### 4.3 Tratamento de Exceções e Recursos
- **4.3.1. Anti-padrão Finally Inadequado**
  - 4.3.1.1. **Erro:** Chamar `.close()` direto no bloco `finally`. Se o fechamento falhar, a nova exceção engole/mascara a exceção original que causou o problema.
  - 4.3.1.2. **Solução:** Adotar blocos `Try-with-resources` genéricos, onde a linguagem gerencia o fechamento seguro e o empilhamento correto de exceções suprimidas.

---

## 5 Domínio: Ferramental, Processos e Métricas

### 5.1 Static Analysis Tools (Prevenção Automatizada)
- **5.1.1. SonarQube e SpotBugs**
  - 5.1.1.1. **SonarQube:** Integração CI/CD, Quality Gates configuráveis, detecção de vulnerabilidades e dívida técnica.
  - 5.1.1.2. **SpotBugs:** Análise de bytecode Java focada em padrões crônicos de bugs (ex: casts inseguros, herança assimétrica).
- **5.1.2. ESLint e Analyzers**
  - 5.1.2.1. **ESLint:** Regras built-in com auto-fix para o ecossistema JS/TS.

### 5.2 Mapeamento por Nível de Experiência
- **5.2.1. Júnior (0-2 anos):** Falha em `finally`, downcasting, shallow copy acidental.
- **5.2.2. Pleno (2-5 anos):** Problemas de performance com autoboxing, confusão com *type erasure*, violações sutis de LSP.
- **5.2.3. Sênior (3+ anos):** Edge cases em concorrência, design problemático de APIs type-safe, vazamentos de memória sutis.

### 5.3 Métricas de Sucesso e Code Review
- **5.3.1. KPIs de Qualidade**
  - 5.3.1.1. Densidade de Defeitos (< 0.5 bugs / 1000 LOC).
  - 5.3.1.2. Cobertura de Testes (> 80%).
  - 5.3.1.3. Dívida Técnica mantida abaixo de 5%.
- **5.3.2. Checklist Essencial de Review**
  - 5.3.2.1. Tratamento exaustivo de edge cases e exceções.
  - 5.3.2.2. Nenhuma duplicidade de código sem justificativa.
  - 5.3.2.3. Validação de *Thread Safety* e uso correto de *Try-with-resources*.