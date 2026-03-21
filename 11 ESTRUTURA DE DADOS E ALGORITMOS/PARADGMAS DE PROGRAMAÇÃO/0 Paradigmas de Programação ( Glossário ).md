# 📚 Guia de Estudos: Paradigmas de Programação

> [!info] Visão Geral
> Um paradigma de programação dita como estruturamos o raciocínio e gerenciamos o estado e o controle de fluxo na memória. Este guia mapeia desde a evolução do controle de fluxo básico (Estruturado/Procedural) até a modelagem de negócios complexos (OOP) e o tratamento de altíssima concorrência e fluxos de dados (Reativo/Funcional).

---

## 1 Domínio: Fundamentos de Controle (A Base)

### 1.1 Programação Imperativa
- **1.1.1. O "Como" Fazer (Controle de Estado Explícito)**
  - 1.1.1.1. **Mecânica:** O desenvolvedor dita o passo a passo exato da máquina e altera explicitamente os estados das variáveis na memória.
  - 1.1.1.2. **Vantagem:** Controle granular sobre a alocação de CPU e memória. Muito próximo de como o hardware realmente funciona.
  - 1.1.1.3. **Desvantagem:** Alta verbosidade e dificuldade extrema em gerenciar concorrência devido à mutação constante de estado compartilhado.

### 1.2 Programação Estruturada
- **1.2.1. O Fim do Código "Espaguete" (Teorema de Böhm-Jacopini)**
  - 1.2.1.1. **Mecânica:** Baseia-se em três estruturas de controle lógicas: **Sequência** (linha após linha), **Seleção** (`if`/`else`, `switch`) e **Iteração** (`for`, `while`).
  - 1.2.1.2. **O Salto Evolutivo:** Substituiu os caóticos comandos de salto incondicional (`GOTO`), que tornavam o código impossível de rastrear e debugar.

### 1.3 Programação Procedural
- **1.3.1. Reuso via Sub-rotinas**
  - 1.3.1.1. **Mecânica:** É um paradigma imperativo que agrupa instruções sequenciais em blocos nomeados chamados de procedimentos, rotinas ou funções (ex: C, Pascal).
  - 1.3.1.2. **Limitação em Escala:** Os dados (estado) fluem livremente entre as funções, ou residem em variáveis globais. À medida que o sistema cresce, fica impossível rastrear qual função alterou qual variável, gerando forte acoplamento (efeitos colaterais em cascata).

---

## 2 Domínio: Modelagem Rica e Comportamento

### 2.1 Programação Orientada a Objetos (OOP)
- **2.1.1. Encapsulamento e Ocultação de Informação**
  - 2.1.1.1. **Mecânica:** Une o estado (atributos) e o comportamento (métodos) na mesma estrutura de memória (o Objeto), isolando-os do mundo exterior.
  - 2.1.1.2. **Polimorfismo (Dynamic Dispatch):** Em runtime, as linguagens usam tabelas virtuais para descobrir qual método chamar, permitindo injetar dependências dinamicamente (base para Clean Architecture e SOLID).
  - 2.1.1.3. **Trade-off:** Ideal para modelar regras de negócio ricas (Domain-Driven Design), mas a herança mal utilizada (violação do Princípio de Liskov) gera sistemas rígidos e difíceis de refatorar.

---

## 3 Domínio: Paradigmas Declarativos (O "Quê" Fazer)

### 3.1 Programação Funcional (FP)
- **3.1.1. Imutabilidade e Funções Puras**
  - 3.1.1.1. **Função Pura:** Dado o mesmo input, sempre retorna o mesmo output e não causa *Side Effects* (ex: não altera banco de dados ou variáveis globais).
  - 3.1.1.2. **Imutabilidade:** Não se atualiza o estado de um objeto em memória; cria-se um novo objeto com o estado modificado.
  - 3.1.1.3. **Vantagem em Concorrência:** Sem mutação de estado compartilhada, não há condição de corrida (*race condition*). O código é nativamente *Thread-Safe*.
  - 3.1.1.4. **Ferramental:** Uso intenso de funções como Cidadãos de Primeira Classe (passadas como parâmetros - *Lambdas*) em operações de Map, Filter, Reduce.

---

## 4 Domínio: Tempo, Assincronismo e Fluxo (Asynchronous)

### 4.1 Event-Driven (Orientação a Eventos)
- **4.1.1. Inversão do Fluxo de Controle**
  - 4.1.1.1. **Mecânica:** O fluxo do programa é determinado pela ocorrência de eventos externos (ações de usuário, mensagens em filas SQS/RabbitMQ, retornos de I/O de rede).
  - 4.1.1.2. **Loop de Eventos:** O coração do Node.js e interfaces gráficas (React, Android). O sistema "escuta" eventos e aciona as funções de *callback* registradas assim que eles ocorrem.
  - 4.1.1.3. **Vantagem:** Desacoplamento brutal. O publicador de um evento não precisa saber quem o escuta (Padrão Pub/Sub).

### 4.2 Programação Reativa
- **4.2.1. Fluxos de Dados Assíncronos (Data Streams)**
  - 4.2.1.1. **Mecânica:** É uma abstração declarativa construída *em cima* da arquitetura orientada a eventos. Trata tudo (cliques de mouse, requisições HTTP, consultas em banco) como *Streams* (fluxos contínuos de dados) que podem ser filtrados, mapeados e combinados.
  - 4.2.1.2. **Non-blocking I/O:** O sistema não prende uma *Thread* esperando uma resposta de banco de dados. Ele reage passivamente quando o dado chega (ex: Spring WebFlux, RxJS, Project Reactor).
  - 4.2.1.3. **Desvantagem (Debug e Complexidade):** O fluxo de execução não é sequencial, quebrando a linearidade do *Stack Trace* em caso de exceções e gerando alta carga cognitiva para a equipe de manutenção.