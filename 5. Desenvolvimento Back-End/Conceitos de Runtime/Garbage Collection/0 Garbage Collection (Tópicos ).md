# 📚 Guia de Estudos: Garbage Collection
#tag #backend #runtime #memoria #gc

> [!info] Visão Geral
> Garbage Collection é o mecanismo automático de gerenciamento de memória presente nos runtimes modernos (JVM, CLR, V8, CPython). Entender o GC é essencial para diagnosticar problemas de latência, memory leaks lógicos e comportamento de performance em sistemas Java, .NET, Python e JavaScript.

---

## 1 Domínio: Fundamentos e Modelo de Memória

### 1.1 O que é Garbage Collection
- **1.1.1. Conceito e Problema que Resolve**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/1. Fundamentos e Modelo de Memória/1.1 O que é Garbage Collection/1.1.1. Conceito e Problema que Resolve/1.1.1.1. GC como mecanismo automático, problema do gerenciamento manual e os erros que ele elimina|1.1.1.1. GC como mecanismo automático, problema do gerenciamento manual e os erros que ele elimina.]]
- **1.1.2. Responsabilidade do Runtime**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/1. Fundamentos e Modelo de Memória/1.1 O que é Garbage Collection/1.1.2. Responsabilidade do Runtime/1.1.2.1. GC pertence ao runtime (JVM, CLR, V8, CPython), não à linguagem — linguagem gerenciada vs runtime gerenciado|1.1.2.1. GC pertence ao runtime (JVM, CLR, V8, CPython), não à linguagem — linguagem gerenciada vs runtime gerenciado.]]

### 1.2 Modelo de Vida dos Objetos
- **1.2.1. Reachability e GC Roots**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/1. Fundamentos e Modelo de Memória/1.2 Modelo de Vida dos Objetos/1.2.1. Reachability e GC Roots/1.2.1.1. Objetos alcançáveis vs inacessíveis, GC Roots e grafo de referências|1.2.1.1. Objetos alcançáveis vs inacessíveis, GC Roots e grafo de referências.]]
- **1.2.2. Stack, Heap e Alocação**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/1. Fundamentos e Modelo de Memória/1.2 Modelo de Vida dos Objetos/1.2.2. Stack, Heap e Alocação/1.2.2.1. Stack vs Heap, alocação de objetos, ponteiros de referência e Escape Analysis|1.2.2.1. Stack vs Heap, alocação de objetos, ponteiros de referência e Escape Analysis.]]

---

## 2 Domínio: Como o GC Atua

### 2.1 Ciclo de Coleta
- **2.1.1. Quando o GC é Acionado**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/2. Como o GC Atua/2.1 Ciclo de Coleta/2.1.1. Quando o GC é Acionado/2.1.1.1. Eventos de alocação, pressão de memória e saturação do Young Generation|2.1.1.1. Eventos de alocação, pressão de memória e saturação do Young Generation.]]
- **2.1.2. Tipos de Coleta**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/2. Como o GC Atua/2.1 Ciclo de Coleta/2.1.2. Tipos de Coleta/2.1.2.1. Minor GC, Major GC e Full GC — diferenças, quando ocorrem e impactos|2.1.2.1. Minor GC, Major GC e Full GC — diferenças, quando ocorrem e impactos.]]
- **2.1.3. Stop-the-World**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/2. Como o GC Atua/2.1 Ciclo de Coleta/2.1.3. Stop-the-World/2.1.3.1. Pausas STW, impacto em threads de aplicação, coleta concorrente vs paralela|2.1.3.1. Pausas STW, impacto em threads de aplicação, coleta concorrente vs paralela.]]

### 2.2 Gerações de Memória
- **2.2.1. Hipótese Geracional**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/2. Como o GC Atua/2.2 Gerações de Memória/2.2.1. Hipótese Geracional/2.2.1.1. Young Generation, Old Generation, promoção de objetos e a premissa most objects die young|2.2.1.1. Young Generation, Old Generation, promoção de objetos e a premissa "most objects die young".]]

---

## 3 Domínio: Algoritmos de GC

### 3.1 Algoritmos Fundamentais
- **3.1.1. Mark-and-Sweep e Mark-and-Compact**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/3. Algoritmos de GC/3.1 Algoritmos Fundamentais/3.1.1. Mark-and-Sweep e Mark-and-Compact/3.1.1.1. Marcação de objetos vivos, varredura dos inacessíveis, compactação e fragmentação|3.1.1.1. Marcação de objetos vivos, varredura dos inacessíveis, compactação e fragmentação.]]
- **3.1.2. Copying Collection e Reference Counting**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/3. Algoritmos de GC/3.1 Algoritmos Fundamentais/3.1.2. Copying Collection e Reference Counting/3.1.2.1. Cópia entre espaços (Survivor), contagem de referências e o problema de ciclos|3.1.2.1. Cópia entre espaços (Survivor), contagem de referências e o problema de ciclos.]]
- **3.1.3. Generational e Concurrent GC**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/3. Algoritmos de GC/3.1 Algoritmos Fundamentais/3.1.3. Generational e Concurrent GC/3.1.3.1. GC geracional como estratégia dominante, coleta concorrente e incremental|3.1.3.1. GC geracional como estratégia dominante, coleta concorrente e incremental.]]

---

## 4 Domínio: GC por Plataforma

### 4.1 Implementações
- **4.1.1. JVM (Java)**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/4. GC por Plataforma/4.1 Implementações/4.1.1. JVM (Java)/4.1.1.1. G1 GC, ZGC, Shenandoah — coletores, eden e survivor spaces, Metaspace e tuning|4.1.1.1. G1 GC, ZGC, Shenandoah — coletores, eden/survivor spaces, Metaspace e tuning.]]
- **4.1.2. CLR (.NET)**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/4. GC por Plataforma/4.1 Implementações/4.1.2. CLR (.NET)/4.1.2.1. Gerações 0, 1 e 2, Large Object Heap (LOH), Workstation GC vs Server GC|4.1.2.1. Gerações 0/1/2, Large Object Heap (LOH), Workstation GC vs Server GC.]]
- **4.1.3. V8 (JavaScript) e CPython**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/4. GC por Plataforma/4.1 Implementações/4.1.3. V8 (JavaScript) e CPython/4.1.3.1. V8 incremental marking e scavenge, CPython reference counting e cycle detector|4.1.3.1. V8 incremental marking/scavenge, CPython reference counting e cycle detector.]]

---

## 5 Domínio: Performance e Trade-offs

### 5.1 Tuning e Problemas
- **5.1.1. Latência vs Throughput**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/5. Performance e Trade-offs/5.1 Tuning e Problemas/5.1.1. Latência vs Throughput/5.1.1.1. Trade-offs fundamentais do GC, heap sizing, escolha de coletor e análise de GC logs|5.1.1.1. Trade-offs fundamentais do GC, heap sizing, escolha de coletor e análise de GC logs.]]
- **5.1.2. Padrões de Código Problemáticos**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/5. Performance e Trade-offs/5.1 Tuning e Problemas/5.1.2. Padrões de Código Problemáticos/5.1.2.1. Memory leaks lógicos, objetos de vida longa, caches sem descarte e closures retendo referências|5.1.2.1. Memory leaks lógicos, objetos de vida longa, caches sem descarte e closures retendo referências.]]
- **5.1.3. Quando GC Vira Gargalo**
  - [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/5. Performance e Trade-offs/5.1 Tuning e Problemas/5.1.3. Quando GC Vira Gargalo/5.1.3.1. Sistemas low-latency e near real-time — object pooling, off-heap memory e alternativas sem GC|5.1.3.1. Sistemas low-latency/near real-time — object pooling, off-heap memory e alternativas sem GC.]]

---

> **Links Relacionados:**
> Java
> C#
> Python
> JavaScript
> Node.js
