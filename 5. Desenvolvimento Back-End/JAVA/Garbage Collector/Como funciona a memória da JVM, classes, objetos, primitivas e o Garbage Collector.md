# 📚 **Como funciona a memória da JVM, classes, objetos, primitivas e o Garbage Collector**

---

## 🧩 **1. Visão Geral da Memória da JVM**

A memória da JVM é dividida em áreas principais:

### 🧠 Stack (Pilha)

- Guarda **contexto de execução** (métodos ativos, variáveis locais e referências).
- Criada por **Thread** (cada thread tem sua própria Stack).
- Desalocação automática (saiu do escopo, limpa).
- **Sem GC** (garbage collector não atua aqui).

> 🧵 Analogia: imagine a Stack como uma pilha de pratos – quando você empilha (push) um novo método, ele vai para o topo; ao terminar, você remove (pop).

---

### 🧊 Heap (Monte)

- Guarda todos os **objetos** criados com `new`, incluindo arrays (mesmo de tipos primitivos).
- Compartilhada por todas as threads.
- Monitorada pelo **Garbage Collector (GC)**.
- **Dividida em gerações**:
    - **Young Generation** (Eden + Survivor)
    - **Old Generation**
    - (Opcional: **Humongous** em G1 GC)

---

### 📁 Metaspace (antigo PermGen)

- Armazena **metadados da classe** (`Class`, `Method`, `Field`, etc.).
- Substituiu o PermGen no Java 8.
- Fora da Heap – usa memória nativa.
- Cresce dinamicamente.

---

## 🧱 **2. Onde ficam classes, objetos e primitivos?**

|Elemento|Onde fica?|Observação|
|---|---|---|
|`Pessoa.class`|Metaspace|Definição da classe|
|`Pessoa p = new Pessoa();`|Stack (referência) + Heap (objeto)|Instância|
|`int x = 5;`|Stack|Valor primitivo|
|`int[] arr = new int[10];`|Stack (referência) + Heap (array)|Arrays são objetos|
|`List<String> nomes = new ArrayList<>();`|Stack (referência) + Heap (objeto)|Lista na Heap|

---

## 🧮 **3. GC e a Heap: Como o Garbage Collector funciona**

### 📍 _Young Generation (Geração Jovem)_

- **Eden Space**: onde objetos recém-criados são alocados.
- **Survivor Spaces (S0 e S1)**: onde objetos sobreviventes de coletas anteriores vão temporariamente.

> ✅ Objetos que sobrevivem a várias coletas são promovidos para a Old Generation.

### 🔁 _Old Generation (Geração Antiga)_

- Armazena objetos que viveram mais tempo.
- Coletado com menor frequência (mas mais caro).

### 🧨 _Tipos de Garbage Collection_

|Tipo GC|Indicado para|Como funciona|
|---|---|---|
|Serial|Aplicações pequenas|Um único thread GC|
|Parallel|Alto throughput|Vários threads|
|CMS (deprecated)|Baixa latência|Coleta concorrente|
|G1 (default hoje)|Grandes heaps|Divide a heap em regiões dinâmicas|

---

## 📌 **Exemplo de execução:**

```java
public class Main {
    public static void main(String[] args) {
        Pessoa p = new Pessoa();
        int idade = 30;
        int[] idades = new int[3];
    }
}

```

### Passo a passo do que acontece:

1. **Classe `Pessoa` carregada**
    - Vai para o **Metaspace** (uma vez por ClassLoader)
2. **Variável `p` é criada**
    - Referência vai para a **Stack**
    - `new Pessoa()` é alocado na **Heap** (Young → Eden)
3. **Variável `idade` é criada**
    - Vai direto para a **Stack** (primitivo local)
4. **Array `int[]` é criado**
    - Referência está na **Stack**
    - Conteúdo do array na **Heap**

---

## 🔄 **Como o GC coleta objetos**

### Exemplo:

```java
Pessoa p = new Pessoa(); // na Heap
p = null;                // perdeu referência

```

- O objeto ainda está na Heap.
- Como **nenhuma referência** aponta para ele, será **marcado para coleta** na próxima GC (dependendo da política).

---

## 🧠 Dica para lembrar:

> 🧼 GC só coleta o que não está mais referenciado – não importa se está na Young ou na Old.

---

## 🧪 Comparação: Pessoa vs Array

|Característica|`Pessoa p = new Pessoa()`|`int[] a = new int[5]`|
|---|---|---|
|Onde vive|Heap|Heap|
|Referência|Stack|Stack|
|Coletado por GC|Sim|Sim|
|Tipo de objeto|Customizado (classe)|Array (objeto especial)|
|Precisa de `new`|Sim|Sim|

---

## 🧱 Estrutura visual simplificada

```
THREAD (Stack)
├─ p → Referência ──┐
├─ idade = 30       │
└─ idades →─────────┘
         |
         V
       HEAP
       ├─ Pessoa { ... }
       └─ int[] { 0, 0, 0 }

METASPACE
├─ Pessoa.class
├─ Main.class
└─ ...

```

---

## 🧠 Analogia Final:

- 🧠 **Stack = sua mente no momento** (o que você está pensando agora)
- 📦 **Heap = sua mochila** (onde você guarda objetos e pode consultar depois)
- 🏛️ **Metaspace = biblioteca** (onde as definições das coisas estão armazenadas)
- 🧽 **GC = faxineiro** que limpa sua mochila quando você esquece algo lá por muito tempo