
A Java Collections Framework (JCF) organiza estruturas de dados em **interfaces** e **implementações**.

- **Interfaces** definem o comportamento (contrato).
- **Implementações** entregam esse comportamento de maneiras diferentes (desempenho, ordenação, permissão de duplicatas etc.).

---

## 2. Interfaces Principais

|Interface|Permite duplicatas?|Mantém ordem?|Acesso por índice?|Uso principal|
|---|---|---|---|---|
|**List**|Sim|Sim (inserção)|Sim|Sequência ordenada|
|**Set**|Não|Opcional (depende da impl)|Não|Conjunto de elementos únicos|
|**Queue**|Sim|Sim (FIFO ou prioridade)|Não|Fila (FIFO) ou fila de prioridade (heap)|
|**Map**|(chave) Não / (valor) Sim|Não (depende da impl)|Não|Mapeamento chave→valor|

---

## 3. Listas (`List`)

Sequência ordenada que permite elementos repetidos.

### 3.1 `ArrayList`

- **Estrutura:** array dinâmico
- **Acesso:** índice O(1)
- **Inserção/remoção (no fim):** O(1) amortizado
- **Inserção/remoção (no meio):** O(n)

```java
java
CopiarEditar
List<String> nomes = new ArrayList<>();
nomes.add("Ana");
nomes.add("Bruno");
nomes.add("Ana");  // duplicata permitida
System.out.println(nomes.get(1)); // “Bruno”

```

### 3.2 `LinkedList`

- **Estrutura:** lista duplamente ligada
- **Acesso:** índice O(n)
- **Inserção/remoção:** O(1) em extremidades
- **Extra:** implementa também `Deque`

```java
java
CopiarEditar
List<Integer> fila = new LinkedList<>();
fila.add(10);
fila.add(20);
fila.addFirst(5);    // método da interface Deque
System.out.println(fila); // [5, 10, 20]

```

---

## 4. Conjuntos (`Set`)

Coleção de elementos **únicos**; ignora duplicatas.

### 4.1 `HashSet`

- **Base:** tabela de hash
- **Ordem:** indeterminada
- **Operações:** O(1) para add/contains/remove

```java
java
CopiarEditar
Set<String> cores = new HashSet<>();
cores.add("vermelho");
cores.add("verde");
cores.add("vermelho"); // ignorado
System.out.println(cores); // ordem imprevisível

```

### 4.2 `TreeSet`

- **Base:** árvore rubro-negra
- **Ordem:** natural ou via Comparator
- **Operações:** O(log n)

```java
java
CopiarEditar
Set<Integer> numeros = new TreeSet<>();
numeros.add(3);
numeros.add(1);
numeros.add(2);
System.out.println(numeros); // [1, 2, 3]

```

### 4.3 `LinkedHashSet`

- **Base:** hash + lista ligada
- **Ordem:** inserção
- **Operações:** O(1)

---

## 5. Filas (`Queue` / `Deque`)

Para processar itens em ordem **FIFO** ou por prioridade.

### 5.1 `ArrayDeque`

- **Implementa:** `Queue` e `Deque`
- **Operações nas pontas:** O(1)

```java
java
CopiarEditar
Deque<String> deque = new ArrayDeque<>();
deque.addLast("A");
deque.addFirst("B");
System.out.println(deque.poll());   // retira “B”

```

### 5.2 `PriorityQueue`

- **Ordem:** natural ou via Comparator
- **Remoção:** sempre o menor/maior elemento

```java
java
CopiarEditar
Queue<Integer> pq = new PriorityQueue<>();
pq.add(5);
pq.add(1);
pq.add(3);
System.out.println(pq.poll()); // 1 (menor)

```

---

## 6. Mapas (`Map`)

Armazena pares **chave→valor**. Chaves únicas; valores podem repetir.

### 6.1 `HashMap`

- **Base:** tabela de hash
- **Ordem:** indeterminada
- **Operações:** O(1)

```java
java
CopiarEditar
Map<String, Integer> idades = new HashMap<>();
idades.put("João", 30);
idades.put("Maria", 25);
System.out.println(idades.get("Maria")); // 25

```

### 6.2 `TreeMap`

- **Base:** árvore rubro-negra
- **Ordem:** ordenação das chaves
- **Operações:** O(log n)

```java
java
CopiarEditar
Map<String, String> capitals = new TreeMap<>();
capitals.put("BR", "Brasília");
capitals.put("US", "Washington");
System.out.println(capitals.keySet()); // [BR, US]

```

### 6.3 `LinkedHashMap`

- **Base:** hash + lista ligada
- **Ordem:** inserção ou acesso (mode LRU)

---

## 7. Resumo das Diferenças

- **ArrayList vs LinkedList**
    - Acesso rápido por índice (`ArrayList`) vs inserção/remoção eficiente nas pontas (`LinkedList`).
- **HashSet vs TreeSet**
    - Sem ordenação (`HashSet`) vs ordenação natural/comparator (`TreeSet`).
- **HashMap vs TreeMap**
    - Acesso rápido por chave (`HashMap`) vs chaves ordenadas (`TreeMap`).
- **ArrayDeque vs PriorityQueue**
    - Fila simples e deque (push/pop) eficiente vs fila de prioridade (heap).

---

## 8. Analogia para Fixar

- **List**: prateleira de livros (permite duplicatas, mantém ordem de colocação).
- **Set**: coleção de selos raros (não admite repetição).
- **Queue**: fila de banco (quem chega primeiro é atendido primeiro).
- **PriorityQueue**: fila de emergência (quem tem maior prioridade atende primeiro).
- **Map**: agenda telefônica (cada nome [chave] aponta para um número [valor]).