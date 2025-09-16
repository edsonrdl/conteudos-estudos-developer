# Guia Completo: Estruturas de Dados em Java

Java oferece um rico conjunto de estruturas de dados através do Collections Framework, cada uma otimizada para diferentes cenários de uso. Este guia abrangente apresenta **conceitos fundamentais, implementações práticas e análises de performance** para todas as principais estruturas, desde arrays básicos até estruturas avançadas como árvores e grafos.

A escolha correta da estrutura pode **melhorar dramaticamente a performance** - HashMap oferece busca O(1) vs O(n) do ArrayList, enquanto TreeMap mantém dados ordenados automaticamente. **Saber quando usar cada estrutura é fundamental** para escrever código Java eficiente e escalável.

## Estruturas lineares fundamentais

### Arrays e ArrayList: a base das coleções

**Arrays** são estruturas de tamanho fixo com acesso direto por índice, enquanto **ArrayList** oferece redimensionamento automático com rica API. ArrayList internamente usa arrays, expandindo capacidade quando necessário através de realocação.

```java
import java.util.ArrayList;
import java.util.Arrays;

public class ArrayVsArrayList {
    public static void main(String[] args) {
        // Array tradicional - tamanho fixo
        String[] arrayFixo = new String[5];
        arrayFixo[0] = "Java";
        arrayFixo[1] = "Python";
        
        // ArrayList - dinâmico com capacity otimizado
        ArrayList<String> linguagens = new ArrayList<>(10); // Capacity inicial
        
        // Operações principais - todas O(1) para acesso/modificação
        linguagens.add("Java");      // Adiciona no final
        linguagens.add("Python");   
        linguagens.add(1, "C++");    // Insere na posição 1 - O(n)
        
        // Acesso e modificação por índice - O(1)
        String primeira = linguagens.get(0);
        linguagens.set(0, "Kotlin");
        
        // Busca linear - O(n)
        boolean temJava = linguagens.contains("Java");
        int indice = linguagens.indexOf("Python");
        
        // Remoção - O(n) por necessitar deslocar elementos
        linguagens.remove("C++");        // Remove por valor
        String removida = linguagens.remove(0); // Remove por índice
        
        System.out.println("Linguagens: " + linguagens);
        System.out.println("Tamanho: " + linguagens.size());
    }
}
```

**ArrayList é ideal para**: acesso frequente por índice, iteração sequencial, operações de leitura predominantes, quando memória é consideração importante. **Evite quando**: há muitas inserções no início, tamanho varia drasticamente sem padrão previsível.

### LinkedList: flexibilidade com ponteiros

**LinkedList** implementa lista duplamente ligada onde cada elemento (nó) contém dados e referências para nós anterior e próximo. Oferece inserção e remoção O(1) nas extremidades, mas acesso por índice O(n).

```java
import java.util.LinkedList;

public class LinkedListExample {
    public static void main(String[] args) {
        LinkedList<String> tarefas = new LinkedList<>();
        
        // Operações eficientes nas extremidades - O(1)
        tarefas.addFirst("Tarefa urgente");    // Início
        tarefas.addLast("Tarefa normal");      // Final
        tarefas.add("Outra tarefa");           // Final (equivale addLast)
        
        // Acesso eficiente apenas nas extremidades - O(1)
        String primeira = tarefas.getFirst();
        String ultima = tarefas.getLast();
        
        // Remoção eficiente nas extremidades - O(1)
        String removidaPrimeira = tarefas.removeFirst();
        String removidaUltima = tarefas.removeLast();
        
        // CUIDADO: acesso por índice é O(n)
        String terceira = tarefas.get(2); // Percorre lista até posição 2
        
        System.out.println("Primeira tarefa: " + primeira);
        System.out.println("Tarefas restantes: " + tarefas);
    }
}
```

**LinkedList é ideal para**: inserções/remoções frequentes no início ou fim, implementação de pilhas/filas, quando não há necessidade de acesso aleatório. **Evite quando**: precisa de acesso por índice, memória é limitada (overhead de ponteiros), fazendo iteração com get(i) em loop.

### Stack: estrutura LIFO essencial

**Stack** implementa pilha com princípio LIFO (Last In, First Out). Internamente usa Vector (array dinâmico sincronizado). Para uso moderno, prefira Deque com ArrayDeque.

```java
import java.util.Stack;
import java.util.ArrayDeque;
import java.util.Deque;

public class PilhaExemplo {
    
    public static void validarParenteses() {
        String expressao = "((2 + 3) * [4 - 1])";
        Stack<Character> pilha = new Stack<>();
        
        for (char c : expressao.toCharArray()) {
            // Empilha caracteres de abertura
            if (c == '(' || c == '[' || c == '{') {
                pilha.push(c);
            } 
            // Verifica correspondência para fechamento
            else if (c == ')' || c == ']' || c == '}') {
                if (pilha.isEmpty() || !combina(pilha.pop(), c)) {
                    System.out.println("Parênteses inválidos!");
                    return;
                }
            }
        }
        
        boolean valida = pilha.isEmpty();
        System.out.println("Expressão " + (valida ? "válida" : "inválida"));
    }
    
    private static boolean combina(char abertura, char fechamento) {
        return (abertura == '(' && fechamento == ')') ||
               (abertura == '[' && fechamento == ']') ||
               (abertura == '{' && fechamento == '}');
    }
    
    // Implementação moderna usando ArrayDeque
    public static void exemploModerno() {
        Deque<String> pilha = new ArrayDeque<>(); // Mais eficiente que Stack
        
        pilha.push("Primeiro");   // Empilha
        pilha.push("Segundo");
        pilha.push("Terceiro");
        
        System.out.println("Topo: " + pilha.peek()); // Visualiza sem remover
        
        // Desempilha todos
        while (!pilha.isEmpty()) {
            System.out.println("Removeu: " + pilha.pop());
        }
    }
    
    public static void main(String[] args) {
        validarParenteses();
        exemploModerno();
    }
}
```

**Stack é essencial para**: validação de sintaxe, controle de chamadas de função, operações "desfazer", avaliação de expressões pós-fixas, navegação com histórico.

### Queue e Deque: processamento sequencial

**Queue** implementa fila FIFO (First In, First Out) para processamento em ordem. **Deque** (double-ended queue) permite inserção e remoção em ambas extremidades, funcionando como fila ou pilha.

```java
import java.util.*;

public class FilaExemplo {
    
    // Sistema de atendimento usando filas com prioridade
    public static void sistemaAtendimento() {
        Queue<String> filaComum = new ArrayDeque<>();
        Queue<String> filaPreferencial = new ArrayDeque<>();
        
        // Chegada de clientes
        filaComum.offer("João");
        filaPreferencial.offer("Maria (gestante)");
        filaComum.offer("Pedro");
        filaPreferencial.offer("José (idoso)");
        
        System.out.println("=== ATENDIMENTO ===");
        
        // Atende preferenciais primeiro, depois comuns
        while (!filaPreferencial.isEmpty() || !filaComum.isEmpty()) {
            String proximo = null;
            
            if (!filaPreferencial.isEmpty()) {
                proximo = filaPreferencial.poll(); // Remove e retorna primeiro
                System.out.println("Atendendo preferencial: " + proximo);
            } else if (!filaComum.isEmpty()) {
                proximo = filaComum.poll();
                System.out.println("Atendendo comum: " + proximo);
            }
        }
    }
    
    // ArrayDeque vs LinkedList para filas
    public static void comparacaoPerformance() {
        final int OPERACOES = 100_000;
        
        // ArrayDeque - recomendado para filas
        long inicio = System.currentTimeMillis();
        Queue<Integer> arrayDeque = new ArrayDeque<>();
        
        for (int i = 0; i < OPERACOES; i++) {
            arrayDeque.offer(i);
        }
        for (int i = 0; i < OPERACOES; i++) {
            arrayDeque.poll();
        }
        long tempoArrayDeque = System.currentTimeMillis() - inicio;
        
        // LinkedList como fila
        inicio = System.currentTimeMillis();
        Queue<Integer> linkedQueue = new LinkedList<>();
        
        for (int i = 0; i < OPERACOES; i++) {
            linkedQueue.offer(i);
        }
        for (int i = 0; i < OPERACOES; i++) {
            linkedQueue.poll();
        }
        long tempoLinkedList = System.currentTimeMillis() - inicio;
        
        System.out.println("ArrayDeque: " + tempoArrayDeque + "ms");
        System.out.println("LinkedList: " + tempoLinkedList + "ms");
        System.out.println("ArrayDeque é " + (tempoLinkedList/tempoArrayDeque) + "x mais rápido");
    }
    
    public static void main(String[] args) {
        sistemaAtendimento();
        comparacaoPerformance();
    }
}
```

**Queue/Deque são ideais para**: sistemas de processamento de tarefas, algoritmos BFS, controle de requisições web, implementação de cache LRU, qualquer cenário que necessite processamento FIFO.

## Maps: estruturas chave-valor otimizadas

### HashMap: performance O(1) com hashing

**HashMap** é a implementação padrão para mapeamentos chave-valor, usando hash table com encadeamento para resolução de colisões. Oferece operações O(1) médio mas não garante ordem.

```java
import java.util.*;

public class HashMapExemplo {
    
    public static void demonstrarOperacoes() {
        // Inicialização com capacity para evitar rehashing
        Map<String, Integer> idades = new HashMap<>(16, 0.75f);
        
        // Operações básicas - todas O(1) médio
        idades.put("Ana", 25);
        idades.put("Bruno", 30);
        idades.put("Carlos", 28);
        idades.put("Ana", 26); // Substitui valor anterior
        
        // Busca e verificação
        Integer idadeAna = idades.get("Ana");
        boolean temBruno = idades.containsKey("Bruno");
        boolean temIdade30 = idades.containsValue(30);
        
        // Operações úteis do Java 8+
        idades.putIfAbsent("Diana", 22);    // Só adiciona se chave não existe
        idades.remove("Carlos", 28);        // Remove apenas se chave=valor
        
        System.out.println("Idades: " + idades);
        
        // Iteração eficiente
        for (Map.Entry<String, Integer> entry : idades.entrySet()) {
            System.out.println(entry.getKey() + " tem " + entry.getValue() + " anos");
        }
    }
    
    // Caso de uso: contador de frequências
    public static void contadorPalavras() {
        String[] texto = {"java", "python", "java", "javascript", "java", "python"};
        Map<String, Integer> frequencias = new HashMap<>();
        
        // Padrão moderno com merge
        for (String palavra : texto) {
            frequencias.merge(palavra, 1, Integer::sum);
        }
        
        System.out.println("Frequências: " + frequencias);
        // Saída: {java=3, python=2, javascript=1}
    }
    
    // Cache simples com expiração
    static class CacheSimples<K, V> {
        private final Map<K, CacheItem<V>> cache = new HashMap<>();
        private final long tempoExpiracao;
        
        public CacheSimples(long tempoExpiracaoMs) {
            this.tempoExpiracao = tempoExpiracaoMs;
        }
        
        public void put(K chave, V valor) {
            long agora = System.currentTimeMillis();
            cache.put(chave, new CacheItem<>(valor, agora + tempoExpiracao));
        }
        
        public V get(K chave) {
            CacheItem<V> item = cache.get(chave);
            if (item == null) return null;
            
            if (System.currentTimeMillis() > item.expiracao) {
                cache.remove(chave);
                return null;
            }
            
            return item.valor;
        }
        
        private static class CacheItem<V> {
            final V valor;
            final long expiracao;
            
            CacheItem(V valor, long expiracao) {
                this.valor = valor;
                this.expiracao = expiracao;
            }
        }
    }
    
    public static void main(String[] args) {
        demonstrarOperacoes();
        contadorPalavras();
    }
}
```

**HashMap é ideal para**: caches gerais, índices de busca rápida, contadores de frequência, mapeamentos onde ordem não importa. **Performance**: O(1) médio para get/put/remove, O(n) espaço.

### TreeMap: dados sempre ordenados

**TreeMap** usa árvore Red-Black (auto-balanceada) mantendo chaves sempre ordenadas. Operações O(log n) mas oferece navegação ordenada e operações de range eficientes.

```java
import java.util.*;

public class TreeMapExemplo {
    
    public static void demonstrarOrdenacao() {
        TreeMap<String, Double> salarios = new TreeMap<>();
        
        // Inserção desordenada
        salarios.put("Diana", 7500.0);
        salarios.put("Ana", 6000.0);
        salarios.put("Carlos", 8200.0);
        salarios.put("Bruno", 5500.0);
        
        System.out.println("Salários ordenados por nome:");
        salarios.forEach((nome, salario) -> 
            System.out.println(nome + ": R$ " + salario));
        // Saída sempre alfabética: Ana, Bruno, Carlos, Diana
    }
    
    public static void operacoesNavegacao() {
        TreeMap<Integer, String> produtos = new TreeMap<>();
        produtos.put(100, "Notebook");
        produtos.put(250, "Monitor");
        produtos.put(50, "Mouse");
        produtos.put(500, "Smartphone");
        
        // Operações de navegação específicas - O(log n)
        System.out.println("Produto mais barato: " + produtos.firstEntry());
        System.out.println("Produto mais caro: " + produtos.lastEntry());
        
        // Operações de range
        SortedMap<Integer, String> faixaMedia = produtos.subMap(100, 300);
        System.out.println("Produtos entre R$100-300: " + faixaMedia);
        
        SortedMap<Integer, String> baratos = produtos.headMap(200);
        SortedMap<Integer, String> caros = produtos.tailMap(200);
        System.out.println("Produtos abaixo R$200: " + baratos);
        System.out.println("Produtos R$200+: " + caros);
        
        // Busca por proximidade
        Integer menorQue200 = produtos.floorKey(199); // Maior ≤ 199
        Integer maiorQue200 = produtos.ceilingKey(201); // Menor ≥ 201
        System.out.println("Menor que R$200: " + produtos.get(menorQue200));
        System.out.println("Maior que R$200: " + produtos.get(maiorQue200));
    }
    
    // Exemplo: sistema de ranking
    public static void sistemaRanking() {
        // TreeMap com ordem customizada (decrescente por pontuação)
        TreeMap<Integer, String> ranking = new TreeMap<>(Collections.reverseOrder());
        
        ranking.put(1500, "Jogador A");
        ranking.put(2100, "Jogador B");
        ranking.put(1800, "Jogador C");
        ranking.put(2300, "Jogador D");
        
        System.out.println("=== RANKING ===");
        int posicao = 1;
        for (Map.Entry<Integer, String> entry : ranking.entrySet()) {
            System.out.println(posicao + "º lugar: " + entry.getValue() + 
                             " (" + entry.getKey() + " pontos)");
            posicao++;
        }
    }
    
    public static void main(String[] args) {
        demonstrarOrdenacao();
        System.out.println();
        operacoesNavegacao();
        System.out.println();
        sistemaRanking();
    }
}
```

**TreeMap é ideal para**: dados que precisam estar ordenados, operações de range (buscar por faixa), rankings e leaderboards, sistemas que necessitam de primeiro/último elemento. **Performance**: O(log n) para operações básicas.

### LinkedHashMap: ordem de inserção preservada

**LinkedHashMap** combina HashMap com lista duplamente ligada, preservando ordem de inserção ou acesso. Base perfeita para implementação de LRU cache.

```java
import java.util.*;

public class LinkedHashMapExemplo {
    
    // LRU Cache implementação profissional
    public static class LRUCache<K, V> extends LinkedHashMap<K, V> {
        private final int capacidadeMaxima;
        
        public LRUCache(int capacidade) {
            // capacity, loadFactor, accessOrder=true (move para final quando acessado)
            super(capacidade, 0.75f, true);
            this.capacidadeMaxima = capacidade;
        }
        
        @Override
        protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
            return size() > capacidadeMaxima; // Remove automaticamente quando excede
        }
        
        public void exibirStatus() {
            System.out.println("Cache: " + this);
            System.out.println("Tamanho: " + size() + "/" + capacidadeMaxima);
        }
    }
    
    public static void demonstrarLRUCache() {
        LRUCache<String, String> cache = new LRUCache<>(3);
        
        System.out.println("=== TESTE LRU CACHE ===");
        
        cache.put("user1", "João");
        cache.put("user2", "Maria");
        cache.put("user3", "Pedro");
        cache.exibirStatus(); // {user1=João, user2=Maria, user3=Pedro}
        
        // Acessa user1 - move para o final (mais recente)
        cache.get("user1");
        cache.exibirStatus(); // {user2=Maria, user3=Pedro, user1=João}
        
        // Adiciona user4 - remove user2 (menos usado recentemente)
        cache.put("user4", "Ana");
        cache.exibirStatus(); // {user3=Pedro, user1=João, user4=Ana}
        
        // Acessa user3 novamente
        cache.get("user3");
        cache.exibirStatus(); // {user1=João, user4=Ana, user3=Pedro}
    }
    
    public static void ordemInsercaoVsAcesso() {
        System.out.println("=== ORDEM DE INSERÇÃO ===");
        LinkedHashMap<String, String> insercao = new LinkedHashMap<>();
        insercao.put("primeiro", "1");
        insercao.put("segundo", "2");
        insercao.put("terceiro", "3");
        
        insercao.get("primeiro"); // Acesso não altera ordem
        System.out.println("Após acessar 'primeiro': " + insercao);
        
        System.out.println("=== ORDEM DE ACESSO ===");
        LinkedHashMap<String, String> acesso = new LinkedHashMap<>(16, 0.75f, true);
        acesso.put("primeiro", "1");
        acesso.put("segundo", "2");
        acesso.put("terceiro", "3");
        
        acesso.get("primeiro"); // Move 'primeiro' para o final
        System.out.println("Após acessar 'primeiro': " + acesso);
    }
    
    public static void main(String[] args) {
        demonstrarLRUCache();
        System.out.println();
        ordemInsercaoVsAcesso();
    }
}
```

**LinkedHashMap é ideal para**: LRU cache, quando ordem de inserção importa, sistemas que precisam de iteração previsível, implementações que requerem tanto performance quanto ordem.

## Sets: coleções de elementos únicos

### HashSet: velocidade para unicidade

**HashSet** internamente usa HashMap (elementos como chaves, valor dummy) garantindo unicidade com operações O(1). Não preserva ordem de inserção.

```java
import java.util.*;

public class HashSetExemplo {
    
    public static void operacoesBasicas() {
        Set<String> linguagens = new HashSet<>();
        
        // Adiciona elementos - duplicatas ignoradas automaticamente
        linguagens.add("Java");
        linguagens.add("Python");
        linguagens.add("JavaScript");
        linguagens.add("Java"); // Ignorado - já existe
        
        System.out.println("Linguagens únicas: " + linguagens);
        System.out.println("Total: " + linguagens.size()); // 3, não 4
        
        // Verificação rápida - O(1)
        if (linguagens.contains("Python")) {
            System.out.println("Python está incluído!");
        }
        
        // Remoção - O(1)
        linguagens.remove("JavaScript");
        System.out.println("Após remoção: " + linguagens);
    }
    
    // Operações de conjuntos matemáticos
    public static void operacoesConjuntos() {
        Set<Integer> pares = new HashSet<>(Arrays.asList(2, 4, 6, 8, 10));
        Set<Integer> multiplos3 = new HashSet<>(Arrays.asList(3, 6, 9, 12));
        
        System.out.println("Pares: " + pares);
        System.out.println("Múltiplos de 3: " + multiplos3);
        
        // União (todos elementos de ambos conjuntos)
        Set<Integer> uniao = new HashSet<>(pares);
        uniao.addAll(multiplos3);
        System.out.println("União: " + uniao); // {2, 3, 4, 6, 8, 9, 10, 12}
        
        // Interseção (elementos em ambos conjuntos)
        Set<Integer> intersecao = new HashSet<>(pares);
        intersecao.retainAll(multiplos3);
        System.out.println("Interseção: " + intersecao); // {6}
        
        // Diferença (elementos apenas no primeiro conjunto)
        Set<Integer> diferenca = new HashSet<>(pares);
        diferenca.removeAll(multiplos3);
        System.out.println("Diferença (pares - múltiplos3): " + diferenca); // {2, 4, 8, 10}
        
        // Diferença simétrica (elementos em apenas um dos conjuntos)
        Set<Integer> simetrica = new HashSet<>(uniao);
        simetrica.removeAll(intersecao);
        System.out.println("Diferença simétrica: " + simetrica); // {2, 3, 4, 8, 9, 10, 12}
    }
    
    // Caso prático: detecção de duplicatas
    public static void detectarDuplicatas() {
        List<String> emails = Arrays.asList(
            "joao@email.com", "maria@email.com", "joao@email.com", 
            "pedro@email.com", "maria@email.com", "ana@email.com"
        );
        
        Set<String> emailsUnicos = new HashSet<>();
        Set<String> duplicatas = new HashSet<>();
        
        for (String email : emails) {
            if (!emailsUnicos.add(email)) { // add retorna false se já existe
                duplicatas.add(email);
            }
        }
        
        System.out.println("E-mails únicos: " + emailsUnicos);
        System.out.println("Duplicatas encontradas: " + duplicatas);
    }
    
    public static void main(String[] args) {
        operacoesBasicas();
        System.out.println();
        operacoesConjuntos();
        System.out.println();
        detectarDuplicatas();
    }
}
```

### TreeSet: conjunto ordenado automaticamente

**TreeSet** mantém elementos automaticamente ordenados usando árvore Red-Black. Oferece operações de navegação como first(), last() e subSet().

```java
import java.util.*;

public class TreeSetExemplo {
    
    public static void ordenacaoAutomatica() {
        TreeSet<String> nomes = new TreeSet<>();
        
        // Inserção desordenada
        nomes.add("Diana");
        nomes.add("Ana");
        nomes.add("Carlos");
        nomes.add("Bruno");
        
        System.out.println("Nomes ordenados: " + nomes);
        // Sempre em ordem: [Ana, Bruno, Carlos, Diana]
        
        // Operações de navegação - O(log n)
        System.out.println("Primeiro: " + nomes.first());
        System.out.println("Último: " + nomes.last());
        
        // Subconjuntos
        SortedSet<String> inicioAteC = nomes.headSet("C");
        SortedSet<String> deCAteF = nomes.subSet("C", "F");
        SortedSet<String> deCParaFinal = nomes.tailSet("C");
        
        System.out.println("Até 'C': " + inicioAteC);
        System.out.println("De 'C' até 'F': " + deCAteF);
        System.out.println("De 'C' em diante: " + deCParaFinal);
    }
    
    // TreeSet com Comparator personalizado
    public static void comparadorCustomizado() {
        // Ordenação por comprimento de string, depois alfabética
        TreeSet<String> palavras = new TreeSet<>((s1, s2) -> {
            int comp = Integer.compare(s1.length(), s2.length());
            return comp != 0 ? comp : s1.compareTo(s2);
        });
        
        palavras.add("Java");
        palavras.add("C");
        palavras.add("JavaScript");
        palavras.add("Python");
        palavras.add("Go");
        
        System.out.println("Ordenado por comprimento: " + palavras);
        // [C, Go, Java, Python, JavaScript]
    }
    
    // Exemplo: sistema de notas com ranking
    public static void sistemaNotas() {
        // Classe para representar estudante com nota
        record Estudante(String nome, double nota) implements Comparable<Estudante> {
            @Override
            public int compareTo(Estudante outro) {
                // Ordena por nota decrescente, depois por nome
                int comp = Double.compare(outro.nota, this.nota);
                return comp != 0 ? comp : this.nome.compareTo(outro.nome);
            }
        }
        
        TreeSet<Estudante> ranking = new TreeSet<>();
        ranking.add(new Estudante("Ana", 8.5));
        ranking.add(new Estudante("Bruno", 9.2));
        ranking.add(new Estudante("Carlos", 7.8));
        ranking.add(new Estudante("Diana", 9.2)); // Mesma nota que Bruno
        
        System.out.println("=== RANKING DE NOTAS ===");
        int posicao = 1;
        for (Estudante estudante : ranking) {
            System.out.println(posicao + "º: " + estudante.nome() + 
                             " (nota: " + estudante.nota() + ")");
            posicao++;
        }
    }
    
    public static void main(String[] args) {
        ordenacaoAutomatica();
        System.out.println();
        comparadorCustomizado();
        System.out.println();
        sistemaNotas();
    }
}
```

**TreeSet é ideal para**: dados que devem estar sempre ordenados, operações de range em conjuntos, rankings e leaderboards, quando precisa de first/last frequentemente.

## Estruturas de dados avançadas

### PriorityQueue: filas com prioridade

**PriorityQueue** implementa heap binária mantendo elemento de maior prioridade sempre no topo. Essencial para algoritmos como Dijkstra e sistemas de agendamento.

```java
import java.util.*;

public class PriorityQueueExemplo {
    
    // Sistema de agendamento de tarefas por prioridade
    static class Tarefa implements Comparable<Tarefa> {
        String nome;
        int prioridade; // 1 = mais urgente
        long deadline;
        
        public Tarefa(String nome, int prioridade, long deadline) {
            this.nome = nome;
            this.prioridade = prioridade;
            this.deadline = deadline;
        }
        
        @Override
        public int compareTo(Tarefa outra) {
            // Primeira critério: prioridade (menor número = maior prioridade)
            int comp = Integer.compare(this.prioridade, outra.prioridade);
            if (comp != 0) return comp;
            
            // Segundo critério: deadline mais próximo
            return Long.compare(this.deadline, outra.deadline);
        }
        
        @Override
        public String toString() {
            return nome + " (prioridade: " + prioridade + ")";
        }
    }
    
    public static void sistemaAgendamento() {
        PriorityQueue<Tarefa> filaExecucao = new PriorityQueue<>();
        
        long agora = System.currentTimeMillis();
        
        // Adiciona tarefas em ordem aleatória
        filaExecucao.offer(new Tarefa("Backup dados", 3, agora + 86400000)); // +1 dia
        filaExecucao.offer(new Tarefa("Corrigir bug crítico", 1, agora + 3600000)); // +1 hora
        filaExecucao.offer(new Tarefa("Deploy aplicação", 1, agora + 1800000)); // +30 min
        filaExecucao.offer(new Tarefa("Reunião equipe", 2, agora + 7200000)); // +2 horas
        
        System.out.println("=== EXECUÇÃO POR PRIORIDADE ===");
        while (!filaExecucao.isEmpty()) {
            Tarefa proxima = filaExecucao.poll(); // Remove e retorna de maior prioridade
            System.out.println("Executando: " + proxima);
        }
    }
    
    // Algoritmo de Dijkstra simplificado para menor caminho
    static class No implements Comparable<No> {
        int vertice;
        int distancia;
        
        No(int vertice, int distancia) {
            this.vertice = vertice;
            this.distancia = distancia;
        }
        
        @Override
        public int compareTo(No outro) {
            return Integer.compare(this.distancia, outro.distancia);
        }
    }
    
    public static void exemploAlgoritmo() {
        // Simula algoritmo de Dijkstra usando PriorityQueue
        PriorityQueue<No> filaPrioridade = new PriorityQueue<>();
        
        // Adiciona vértices com suas distâncias do início
        filaPrioridade.offer(new No(0, 0));   // Vértice inicial
        filaPrioridade.offer(new No(1, 4));   // Distância 4
        filaPrioridade.offer(new No(2, 2));   // Distância 2
        filaPrioridade.offer(new No(3, 6));   // Distância 6
        filaPrioridade.offer(new No(4, 1));   // Distância 1
        
        System.out.println("=== PROCESSAMENTO DIJKSTRA ===");
        while (!filaPrioridade.isEmpty()) {
            No atual = filaPrioridade.poll();
            System.out.println("Processando vértice " + atual.vertice + 
                             " com distância " + atual.distancia);
        }
    }
    
    public static void main(String[] args) {
        sistemaAgendamento();
        System.out.println();
        exemploAlgoritmo();
    }
}
```

**PriorityQueue é essencial para**: algoritmos de grafos (Dijkstra, A*), sistemas de agendamento, simulações de eventos, merge de dados ordenados. **Performance**: O(log n) inserção/remoção, O(1) peek.

### Árvores binárias: estruturas hierárquicas

Árvores binárias organizam dados hierarquicamente onde cada nó tem no máximo dois filhos. **Binary Search Tree (BST)** mantém propriedade de ordem: filho esquerdo < pai < filho direito.

```java
public class ArvoreBinaria<T extends Comparable<T>> {
    
    private class No {
        T valor;
        No esquerdo, direito;
        
        No(T valor) {
            this.valor = valor;
        }
    }
    
    private No raiz;
    
    // Inserção mantendo propriedade BST - O(log n) médio
    public void inserir(T valor) {
        raiz = inserirRecursivo(raiz, valor);
    }
    
    private No inserirRecursivo(No no, T valor) {
        if (no == null) {
            return new No(valor);
        }
        
        int comp = valor.compareTo(no.valor);
        if (comp < 0) {
            no.esquerdo = inserirRecursivo(no.esquerdo, valor);
        } else if (comp > 0) {
            no.direito = inserirRecursivo(no.direito, valor);
        }
        
        return no; // Valor duplicado ignorado
    }
    
    // Busca eficiente - O(log n) médio
    public boolean buscar(T valor) {
        return buscarRecursivo(raiz, valor);
    }
    
    private boolean buscarRecursivo(No no, T valor) {
        if (no == null) return false;
        
        int comp = valor.compareTo(no.valor);
        if (comp == 0) return true;
        
        return comp < 0 ? buscarRecursivo(no.esquerdo, valor) 
                        : buscarRecursivo(no.direito, valor);
    }
    
    // Travessia in-order: esquerdo -> raiz -> direito (resulta em ordem crescente)
    public void percorrerInOrder() {
        System.out.print("In-order: ");
        percorrerInOrder(raiz);
        System.out.println();
    }
    
    private void percorrerInOrder(No no) {
        if (no != null) {
            percorrerInOrder(no.esquerdo);
            System.out.print(no.valor + " ");
            percorrerInOrder(no.direito);
        }
    }
    
    // Travessia por nível (BFS) usando Queue
    public void percorrerPorNivel() {
        if (raiz == null) return;
        
        Queue<No> fila = new LinkedList<>();
        fila.offer(raiz);
        
        System.out.print("Por nível: ");
        while (!fila.isEmpty()) {
            No atual = fila.poll();
            System.out.print(atual.valor + " ");
            
            if (atual.esquerdo != null) fila.offer(atual.esquerdo);
            if (atual.direito != null) fila.offer(atual.direito);
        }
        System.out.println();
    }
    
    // Encontra altura da árvore
    public int altura() {
        return calcularAltura(raiz);
    }
    
    private int calcularAltura(No no) {
        if (no == null) return -1;
        
        int alturaEsquerda = calcularAltura(no.esquerdo);
        int alturaDireita = calcularAltura(no.direito);
        
        return 1 + Math.max(alturaEsquerda, alturaDireita);
    }
    
    // Exemplo de uso
    public static void main(String[] args) {
        ArvoreBinaria<Integer> arvore = new ArvoreBinaria<>();
        
        // Inserção
        int[] valores = {50, 30, 70, 20, 40, 60, 80};
        for (int valor : valores) {
            arvore.inserir(valor);
        }
        
        // Diferentes travessias
        arvore.percorrerInOrder();    // 20 30 40 50 60 70 80
        arvore.percorrerPorNivel();   // 50 30 70 20 40 60 80
        
        // Busca
        System.out.println("Buscar 40: " + arvore.buscar(40)); // true
        System.out.println("Buscar 25: " + arvore.buscar(25)); // false
        
        // Estatísticas
        System.out.println("Altura da árvore: " + arvore.altura()); // 2
    }
}
```

**BST é ideal para**: sistemas de busca eficiente, índices de banco de dados, implementação de sets/maps ordenados, qualquer cenário que necessite de busca O(log n) com dados ordenados.

### Grafos: modelagem de relacionamentos

Grafos representam relacionamentos entre entidades através de vértices (nós) conectados por arestas. Essencial para redes sociais, rotas, dependências.

```java
import java.util.*;

public class Grafo {
    private Map<Integer, List<Integer>> adjacencias;
    
    public Grafo() {
        this.adjacencias = new HashMap<>();
    }
    
    // Adiciona vértice
    public void adicionarVertice(int vertice) {
        adjacencias.putIfAbsent(vertice, new ArrayList<>());
    }
    
    // Adiciona aresta (não-direcionada)
    public void adicionarAresta(int origem, int destino) {
        adicionarVertice(origem);
        adicionarVertice(destino);
        
        adjacencias.get(origem).add(destino);
        adjacencias.get(destino).add(origem);
    }
    
    // Busca em Profundidade (DFS) - usa Stack implícita (recursão)
    public void dfs(int inicio) {
        Set<Integer> visitados = new HashSet<>();
        System.out.print("DFS a partir de " + inicio + ": ");
        dfsRecursivo(inicio, visitados);
        System.out.println();
    }
    
    private void dfsRecursivo(int vertice, Set<Integer> visitados) {
        visitados.add(vertice);
        System.out.print(vertice + " ");
        
        for (int vizinho : adjacencias.getOrDefault(vertice, new ArrayList<>())) {
            if (!visitados.contains(vizinho)) {
                dfsRecursivo(vizinho, visitados);
            }
        }
    }
    
    // Busca em Largura (BFS) - usa Queue explícita
    public void bfs(int inicio) {
        Set<Integer> visitados = new HashSet<>();
        Queue<Integer> fila = new LinkedList<>();
        
        fila.offer(inicio);
        visitados.add(inicio);
        
        System.out.print("BFS a partir de " + inicio + ": ");
        while (!fila.isEmpty()) {
            int atual = fila.poll();
            System.out.print(atual + " ");
            
            for (int vizinho : adjacencias.getOrDefault(atual, new ArrayList<>())) {
                if (!visitados.contains(vizinho)) {
                    visitados.add(vizinho);
                    fila.offer(vizinho);
                }
            }
        }
        System.out.println();
    }
    
    // Encontra menor caminho usando BFS
    public List<Integer> menorCaminho(int origem, int destino) {
        if (origem == destino) return Arrays.asList(origem);
        
        Map<Integer, Integer> pais = new HashMap<>();
        Set<Integer> visitados = new HashSet<>();
        Queue<Integer> fila = new LinkedList<>();
        
        fila.offer(origem);
        visitados.add(origem);
        pais.put(origem, null);
        
        while (!fila.isEmpty()) {
            int atual = fila.poll();
            
            for (int vizinho : adjacencias.getOrDefault(atual, new ArrayList<>())) {
                if (!visitados.contains(vizinho)) {
                    visitados.add(vizinho);
                    pais.put(vizinho, atual);
                    fila.offer(vizinho);
                    
                    if (vizinho == destino) {
                        return reconstruirCaminho(pais, origem, destino);
                    }
                }
            }
        }
        
        return null; // Não há caminho
    }
    
    private List<Integer> reconstruirCaminho(Map<Integer, Integer> pais, int origem, int destino) {
        List<Integer> caminho = new ArrayList<>();
        Integer atual = destino;
        
        while (atual != null) {
            caminho.add(atual);
            atual = pais.get(atual);
        }
        
        Collections.reverse(caminho);
        return caminho;
    }
    
    // Detecta se há ciclo no grafo
    public boolean temCiclo() {
        Set<Integer> visitados = new HashSet<>();
        
        for (int vertice : adjacencias.keySet()) {
            if (!visitados.contains(vertice)) {
                if (dfsDetectaCiclo(vertice, -1, visitados)) {
                    return true;
                }
            }
        }
        return false;
    }
    
    private boolean dfsDetectaCiclo(int vertice, int pai, Set<Integer> visitados) {
        visitados.add(vertice);
        
        for (int vizinho : adjacencias.getOrDefault(vertice, new ArrayList<>())) {
            if (!visitados.contains(vizinho)) {
                if (dfsDetectaCiclo(vizinho, vertice, visitados)) {
                    return true;
                }
            } else if (vizinho != pai) {
                return true; // Encontrou ciclo
            }
        }
        return false;
    }
    
    public static void main(String[] args) {
        Grafo grafo = new Grafo();
        
        // Criando grafo de rede social simples
        grafo.adicionarAresta(1, 2); // Alice - Bruno
        grafo.adicionarAresta(1, 3); // Alice - Carlos
        grafo.adicionarAresta(2, 4); // Bruno - Diana
        grafo.adicionarAresta(3, 4); // Carlos - Diana
        grafo.adicionarAresta(4, 5); // Diana - Eva
        
        // Diferentes tipos de travessia
        grafo.dfs(1); // DFS a partir de 1: 1 2 4 3 5
        grafo.bfs(1); // BFS a partir de 1: 1 2 3 4 5
        
        // Menor caminho
        List<Integer> caminho = grafo.menorCaminho(1, 5);
        System.out.println("Menor caminho de 1 para 5: " + caminho);
        
        // Detecção de ciclo
        System.out.println("Grafo tem ciclo: " + grafo.temCiclo());
    }
}
```

**Grafos são essenciais para**: redes sociais (sugestão de amigos), sistemas de rotas (GPS), análise de dependências, redes de computadores, qualquer sistema que modele relacionamentos.

## Análise de performance e melhores práticas

### Tabela comparativa de complexidades

|**Estrutura**|**Inserção**|**Busca**|**Remoção**|**Acesso**|**Espaço**|
|---|---|---|---|---|---|
|**ArrayList**|O(1) amortizado|O(n)|O(n)|O(1)|O(n)|
|**LinkedList**|O(1) nas extremidades|O(n)|O(1) com referência|O(n)|O(n)|
|**HashMap**|O(1) médio|O(1) médio|O(1) médio|N/A|O(n)|
|**TreeMap**|O(log n)|O(log n)|O(log n)|N/A|O(n)|
|**HashSet**|O(1) médio|O(1) médio|O(1) médio|N/A|O(n)|
|**TreeSet**|O(log n)|O(log n)|O(log n)|N/A|O(n)|
|**PriorityQueue**|O(log n)|O(n)|O(log n)|N/A|O(n)|

### Quando usar cada estrutura

**Use ArrayList quando**: precisar de acesso aleatório frequente, fizer principalmente leitura, iteração sequencial for comum, memória for consideração importante.

**Use LinkedList quando**: inserções/remoções no início/fim forem frequentes, não precisar de acesso por índice, implementar pilhas/filas, tamanho variar drasticamente.

**Use HashMap quando**: precisar de busca rápida O(1), chaves não precisarem estar ordenadas, for implementar cache ou índices.

**Use TreeMap quando**: dados precisarem estar sempre ordenados, necessitar de operações de range, implementar rankings, precisar de navegação ordenada.

**Use HashSet quando**: precisar garantir unicidade com performance, fazer operações de conjuntos, detectar duplicatas rapidamente.

**Use TreeSet quando**: elementos precisarem estar ordenados automaticamente, necessitar de operações de range em conjuntos, implementar rankings únicos.

### Práticas de otimização essenciais

```java
// ✅ FAÇA: Initialize com capacidade apropriada
List<String> items = new ArrayList<>(1000); // Evita realocações
Map<String, Object> cache = new HashMap<>(expectedSize * 4 / 3); // Account for load factor

// ❌ EVITE: Capacidade padrão para grandes datasets
List<String> items = new ArrayList<>(); // Vai realocar múltiplas vezes

// ✅ FAÇA: Use StringBuilder para concatenação em loop
StringBuilder sb = new StringBuilder();
for (String s : strings) {
    sb.append(s);
}

// ❌ EVITE: Concatenação de strings em loop
String result = "";
for (String s : strings) {
    result += s; // Cria nova string a cada iteração
}

// ✅ FAÇA: Use enhanced for loop para iteração
for (String item : list) {
    process(item);
}

// ❌ EVITE: Iterator manual desnecessário ou get(i) em LinkedList
for (int i = 0; i < linkedList.size(); i++) {
    process(linkedList.get(i)); // O(n²) para LinkedList!
}
```

### Implementação de estruturas customizadas versus Collections Framework

**Use Collections Framework quando**: performance padrão for adequada, manutenibilidade for importante, equipe não tiver expertise em estruturas customizadas, aplicação for de complexidade média.

**Considere estruturas customizadas quando**: performance for crítica e mensurável, tiver requisitos específicos (ex: tipos primitivos), precisar otimizar uso de memória, implementar algoritmos especializados.

```java
// Exemplo: contador de inteiros otimizado vs HashMap
public class ContadorOtimizado {
    private int[] contadores = new int[1000]; // Array direto para range conhecido
    
    public void incrementar(int chave) {
        if (chave >= 0 && chave < contadores.length) {
            contadores[chave]++;
        }
    }
    
    public int obter(int chave) {
        return (chave >= 0 && chave < contadores.length) ? contadores[chave] : 0;
    }
}

// vs HashMap tradicional
Map<Integer, Integer> contador = new HashMap<>();
contador.merge(chave, 1, Integer::sum); // Mais lento por boxing/unboxing
```

**A escolha da estrutura de dados correta pode impactar dramaticamente a performance** - HashMap vs ArrayList para busca pode ser diferença entre O(1) e O(n). **Comece sempre com as estruturas padrão do Java**, profile a aplicação, e otimize apenas onde comprovadamente necessário com benchmarks reais.

Este guia fornece base sólida para escolher e usar eficientemente as estruturas de dados Java, balanceando teoria com aplicação prática para diferentes níveis de complexidade e requisitos de performance.