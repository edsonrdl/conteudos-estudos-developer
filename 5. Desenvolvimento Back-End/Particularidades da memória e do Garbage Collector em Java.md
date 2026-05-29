## 🧠 1. **Stack e Heap são gerenciadas pela JVM**

- A JVM cria a **Stack por Thread**, usada para variáveis locais e chamadas de métodos.
- A **Heap é única e compartilhada** por todas as threads.
- O desenvolvedor **não controla diretamente** onde o objeto será alocado, **mas sabe que `new` vai sempre para a Heap**.

---

## 🧩 2. **Classes e Metaspace**

- Toda classe `.java` é compilada em bytecode `.class`, e carregada na JVM dinamicamente via um **ClassLoader**.
- As **definições de classe** (`Pessoa.class`, `List.class`) são mantidas no **Metaspace**.
- Desde Java 8, o **Metaspace usa memória nativa**, e **não faz parte da Heap**.

---

## 🛠️ 3. **Objetos são sempre criados com `new`**

```java
java
CopiarEditar
Pessoa p = new Pessoa();

```

- A referência `p` está na **Stack**
- O objeto `new Pessoa()` está na **Heap**
- A Heap pode crescer até o limite definido (padrão ou via `Xmx`)

---

## 📥 4. **Objetos sempre na Heap, inclusive arrays**

```java
java
CopiarEditar
int[] valores = new int[10];

```

- `valores`: referência na Stack
- Array inteiro: na Heap, mesmo que os valores sejam primitivos

---

## 🎯 5. **Tipos primitivos vs tipos por referência**

|Tipo|Onde fica (se local)|Observações|
|---|---|---|
|`int idade = 10`|Stack|Primitivo puro|
|`Integer idade = 10`|Heap (autoboxing)|Objeto, coleta pelo GC|
|`String nome = "João"`|Heap (literal pool ou new)|String é especial|
|`Pessoa p = new Pessoa()`|Stack + Heap|Referência + Objeto|

---

## 📦 6. **String Pool (intern pool)**

```java
java
CopiarEditar
String a = "teste";
String b = "teste";
String c = new String("teste");

```

- `a == b` → `true` (ambos apontam para o **pool de Strings**)
- `a == c` → `false` (c é um novo objeto fora do pool)
- `c.intern()` → força `c` a ir para o pool

**Motivo**: otimização de memória, pois Strings são imutáveis.

---

## 🧹 7. **Garbage Collector (GC) em Java**

- **Automático**: você **não precisa dar `free()` ou `delete`**.
- **Coleta por raiz de referência (GC Roots)**: a JVM traça referências a partir de:
    - Variáveis na Stack
    - Referências estáticas
    - Referências JNI
- Se um objeto não é mais referenciado, ele **é elegível** para GC.

---

### 🔄 Como funciona:

1. Objeto criado → vai para **Eden (Young Gen)**
2. Sobrevive a alguns GC menores → **Survivor Spaces**
3. Sobrevive mais → vai para **Old Gen**
4. GC decide quando coletar a **Old Gen** (mais custoso)

---

## 🧪 8. **Tipos de referência em Java**

Java tem 4 tipos de referências que o GC trata de formas diferentes:

|Tipo|Coletável?|Usado para...|
|---|---|---|
|**Strong**|Não|Normal (padrão)|
|**Soft**|Sim (quando memória acabar)|Caches|
|**Weak**|Sim (em próximo GC)|Mapas fracos (`WeakHashMap`)|
|**Phantom**|Sim (após GC, antes de remoção final)|Monitoração de limpeza|

---

## 🏗️ 9. **Parâmetros de configuração de memória**

Você pode ajustar a JVM com:

```bash
bash
CopiarEditar
java -Xms512m -Xmx1024m -XX:MetaspaceSize=128m

```

|Parâmetro|Função|
|---|---|
|`-Xms`|Tamanho inicial da Heap|
|`-Xmx`|Tamanho máximo da Heap|
|`-XX:MetaspaceSize`|Tamanho inicial do Metaspace|

---

## 🧰 10. **Objetos estáticos e vazamentos de memória**

- Objetos referenciados por **campos `static` nunca são coletados**, a não ser que a classe seja descarregada (muito raro).
- Isso pode gerar **memory leak** se usar listas estáticas sem limpar.

---

## 🧵 11. **ThreadLocal e vazamentos**

```java
java
CopiarEditar
ThreadLocal<MyObject> local = new ThreadLocal<>();

```

- Cada thread guarda uma cópia do objeto.
- Se não for removido com `remove()`, pode **impedir coleta**, principalmente em pools de threads reaproveitadas (Ex: Tomcat).

---

## 🛑 12. **Cuidados com finalizadores (deprecated)**

- `finalize()` **não deve ser usado**: é imprevisível e pode **atrasar** a coleta.
- Java recomenda usar `try-with-resources` e `AutoCloseable`.

---

## ✅ Boas práticas em Java sobre memória

1. **Evite manter referências vivas desnecessárias**
2. **Prefira coleções fracas (`WeakHashMap`) quando aplicável**
3. **Use `try-with-resources`** para fechar recursos (IO, JDBC)
4. **Evite uso abusivo de `static`**
5. **Use profiling (ex: VisualVM, JFR, YourKit)** para analisar consumo de memória

# 🧹 **Comparativo dos Garbage Collectors do Java**

|GC|Tipo|Aplicação ideal|Coleta paralela?|Coleta concorrente?|Baixa latência?|Throughput alto?|
|---|---|---|---|---|---|---|
|**Serial GC**|**Parado/Simples**|Aplicações pequenas, testes, sistemas embarcados|❌ Não|❌ Não|❌|✅|
|**Parallel GC**|**Paralelo (Throughput)**|Aplicações que priorizam throughput|✅ Sim|❌ Não|❌|✅✅|
|**CMS (obsoleto)**|**Concorrente (Low Pause)**|Aplicações interativas (ex: web)|✅ Sim|✅ Sim|✅|✅|
|**G1 GC**|**Região/Balanceado**|JVMs grandes, aplicações mistas|✅ Sim|✅ Sim|✅|✅✅|
|**ZGC / Shenandoah**|**Pausas Ultra-baixas**|Aplicações grandes com latência mínima|✅ Sim|✅ Sim (em background)|✅✅|✅|

---

## 🔍 **1. Serial GC** (usado com `XX:+UseSerialGC`)

### Como funciona:

- Usa **uma única thread** para coletar.
- **Pausa completamente** a aplicação durante o GC (`Stop-the-world`).

### Aplicação ideal:

- Dispositivos com **pouca CPU/memória**
- **Apps de linha de comando, scripts**

### Analogia:

> Como varrer uma sala fechando a porta: ninguém entra até que você termine.

---

## ⚡ **2. Parallel GC** (default até o Java 8 – `XX:+UseParallelGC`)

### Como funciona:

- Usa **múltiplas threads** para fazer a coleta.
- Pausa a aplicação, mas a coleta é mais rápida.

### Aplicação ideal:

- **Serviços de backend**, aplicações de **batch**, jobs longos.
- Prioriza **throughput** (velocidade de processamento total), **não latência**.

---

## 🧠 **3. CMS (Concurrent Mark-Sweep GC)** (obsoleto a partir do Java 9+)

### Como funciona:

- Divide o ciclo em partes **concorrentes e pausadas**.
- Usa threads paralelas para **marcar e varrer objetos mortos** enquanto a aplicação roda.

### Aplicação ideal:

- Sistemas **interativos** (web servers, REST APIs)
- Situações onde **latência** é mais importante que throughput

> 🚨 Depreciado no Java 9+ e removido no Java 14.

---

## ⚖️ **4. G1 GC (Garbage First)** (default no Java 9+)

### Como funciona:

- Divide a Heap em **pequenas regiões (regions)** ao invés de grandes blocos.
- Foca em **coletar primeiro as regiões com mais lixo**.
- Coleta **concorrente e incremental**.
- **Controla pausas com metas predefinidas** (`XX:MaxGCPauseMillis=200`).

### Aplicação ideal:

- JVMs com **mais de 4 GB de Heap**
- **Servidores mistos**, que querem **equilíbrio** entre throughput e latência.

> ✅ Recomendado para a maioria dos apps modernos

---

## 🚀 **5. ZGC (Z Garbage Collector)** – `XX:+UseZGC`

**e**

## 🚀 **Shenandoah GC** – `XX:+UseShenandoahGC`

### Como funcionam:

- GCs de **pausa ultra-baixa** (em milissegundos)
- Usam **barreiras de leitura/gravação**
- Trabalham com **heap gigantes** (> TB)
- **Coletam o lixo sem travar** a aplicação

### Aplicação ideal:

- **Aplicações em tempo real**
- **Sistemas financeiros**, trading, APIs críticas
- Geração de eventos ao vivo

### Requisitos:

- ZGC: Java 11+
- Shenandoah: Java 12+ (nativo no OpenJDK)

---

## 📊 Comparativo técnico visual

|GC|Paralelo|Concorrente|Region-based|Tempo de pausa típico|
|---|---|---|---|---|
|Serial|❌|❌|❌|Alto|
|Parallel|✅|❌|❌|Médio/Alto|
|CMS|✅|✅|❌|Médio|
|G1|✅|✅|✅|Baixo/Médio|
|ZGC|✅|✅|✅|Milissegundos (~10ms)|
|Shenandoah|✅|✅|✅|Milissegundos (~5-10ms)|

---

## ⚙️ Como definir na JVM?

```bash
bash
CopiarEditar
java -XX:+UseSerialGC        -jar app.jar
java -XX:+UseParallelGC      -jar app.jar
java -XX:+UseG1GC            -jar app.jar
java -XX:+UseZGC             -jar app.jar
java -XX:+UseShenandoahGC    -jar app.jar

```

---

## 🧠 Qual GC usar?

|Requisito|GC recomendado|
|---|---|
|Baixa latência (< 100ms)|ZGC, Shenandoah|
|Alta performance (batch)|Parallel GC|
|Equilíbrio entre latência e desempenho|G1 GC|
|Ambientes simples ou limitados|Serial GC|

---

## 🧪 Dica de ouro

Sempre **teste seu app com o GC escolhido**, usando ferramentas como:

- `jstat`, `jmap`, `jconsole`
- VisualVM
- Java Flight Recorder (JFR)
- GC logs: `Xlog:gc*` no Java 11+