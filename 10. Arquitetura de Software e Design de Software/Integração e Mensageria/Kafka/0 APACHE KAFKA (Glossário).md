# 📚 Guia Completo de Estudos Apache Kafka

## 1. Introdução ao Apache Kafka

### 1.1. O que é Apache Kafka

- **Definição:** Plataforma distribuída de streaming de eventos
- **Características principais:**
    - Sistema de mensageria publish-subscribe
    - Armazenamento durável de streams de dados
    - Processamento de streams em tempo real
    - Alta taxa de throughput e baixa latência
- **Diferencial:** Commit log distribuído e replicado
- **Escala:** Processa trilhões de eventos por dia em empresas como LinkedIn

### 1.2. História e Evolução

- **2010:** Criado no LinkedIn por Jay Kreps, Neha Narkhede e Jun Rao
- **2011:** Open-sourced e doado para Apache Software Foundation
- **2012:** Graduação como projeto top-level Apache
- **2014:** Fundação da Confluent pelos criadores originais
- **2019:** Introdução do KRaft (Kafka Raft) para substituir ZooKeeper
- **2021-2024:** Adoção massiva em arquiteturas cloud-native e serverless
- **Versões importantes:**
    - 0.8: Replicação
    - 0.10: Kafka Streams
    - 2.8: KRaft preview
    - 3.x: Production-ready KRaft

### 1.3. Casos de Uso do Apache Kafka

- **Event Streaming:**
    - Processamento de eventos em tempo real
    - Event sourcing e CQRS
    - Arquiteturas event-driven
- **Integração de Sistemas:**
    - ETL/ELT pipelines
    - Data integration entre microserviços
    - CDC (Change Data Capture)
- **Processamento de Logs:**
    - Agregação centralizada de logs
    - Análise e monitoramento em tempo real
    - Auditoria e compliance
- **Microserviços:**
    - Comunicação assíncrona entre serviços
    - Saga pattern para transações distribuídas
    - Decoupling de sistemas
- **IoT e Telemetria:**
    - Ingestão de dados de sensores
    - Processamento de métricas em escala
- **Análise em Tempo Real:**
    - Dashboards real-time
    - Detecção de fraudes
    - Recomendações personalizadas

## 2. Arquitetura do Apache Kafka

### 2.1. Componentes Principais

#### 2.1.1. Broker

- **Definição:** Servidor Kafka que armazena e serve dados
- **Responsabilidades:**
    - Receber mensagens de producers
    - Armazenar mensagens em disco
    - Servir mensagens para consumers
    - Participar na replicação
- **Características:**
    - Identificado por broker.id único
    - Pode hospedar múltiplas partições
    - Gerencia leaders e followers
- **Configurações importantes:**

properties

```properties
  broker.id=0
  log.dirs=/var/kafka-logs
  num.network.threads=8
  num.io.threads=8
  socket.send.buffer.bytes=102400
  socket.receive.buffer.bytes=102400
```

#### 2.1.2. Topic

- **Definição:** Categoria ou feed name para mensagens
- **Estrutura:**
    - Nome único no cluster
    - Dividido em partições
    - Configurações específicas por tópico
- **Naming conventions:**
    - Use lowercase com hífens: `user-events`
    - Evite underscores e caracteres especiais
    - Prefixos por ambiente: `prod.user-events`
- **Configurações essenciais:**
    - retention.ms / retention.bytes
    - segment.bytes / segment.ms
    - cleanup.policy (delete/compact)
    - compression.type

#### 2.1.3. Partition

- **Definição:** Unidade de paralelismo e ordenação
- **Características:**
    - Ordenação garantida dentro da partição
    - Distribuição por key hash ou round-robin
    - Immutable append-only log
- **Estrutura interna:**
    - Segmentos de log
    - Index files
    - Time index
- **Cálculo do número ideal:**

```
  Partições = max(throughput/producer_rate, throughput/consumer_rate)
```

- **Limites práticos:**
    - 4000 partições por broker (máximo)
    - 200K partições por cluster

#### 2.1.4. Producer

- **Definição:** Cliente que publica mensagens nos tópicos
- **Componentes da mensagem:**
    - Key (opcional)
    - Value
    - Headers
    - Timestamp
    - Partition (calculada ou especificada)
- **Processo de envio:**
    1. Serialização
    2. Particionamento
    3. Batching
    4. Compressão
    5. Envio ao broker
- **Garantias de entrega:**
    - At most once (acks=0)
    - At least once (acks=1)
    - Exactly once (idempotent + transactions)

#### 2.1.5. Consumer

- **Definição:** Cliente que lê mensagens dos tópicos
- **Modelo de consumo:**
    - Pull-based (não push)
    - Offset tracking
    - Consumer groups para escalabilidade
- **Processo de leitura:**
    1. Subscribe/assign partições
    2. Poll loop
    3. Processar mensagens
    4. Commit offsets
- **Padrões de consumo:**
    - Sequential processing
    - Parallel processing
    - Batch processing

#### 2.1.6. ZooKeeper (e KRaft)

- **ZooKeeper - Papel tradicional:**
    - Metadata management
    - Controller election
    - Configuration management
    - Cluster membership
    - ACLs storage
- **Limitações do ZooKeeper:**
    - Componente externo adicional
    - Complexidade operacional
    - Limite de escalabilidade
- **KRaft (Kafka Raft) - Novo modelo:**
    - Metadata gerenciado internamente
    - Protocolo Raft para consenso
    - Eliminação de dependência externa
    - Melhor escalabilidade
    - Migração: ZooKeeper → KRaft

### 2.2. Como o Kafka Funciona

#### 2.2.1. Sistema de Pub/Sub

- **Modelo de comunicação:**
    - Producers publicam sem conhecer consumers
    - Consumers subscrevem sem conhecer producers
    - Desacoplamento total
- **Topic como canal:**
    - Múltiplos producers por tópico
    - Múltiplos consumers por tópico
    - Broadcasting e unicasting
- **Durabilidade:**
    - Mensagens persistidas em disco
    - Retention configurável
    - Replay capability

#### 2.2.2. Processamento Assíncrono

- **Benefícios:**
    - Não-bloqueante
    - Melhor utilização de recursos
    - Tolerância a falhas temporárias
- **Implementação:**
    - Producer callbacks
    - Consumer poll model
    - Batch processing
- **Patterns:**
    - Fire-and-forget
    - Request-reply (com correlation ID)
    - Event sourcing

### 2.3. Replicação e Tolerância a Falhas

- **Replication Factor:**
    - Número de cópias de cada partição
    - Padrão recomendado: 3
    - Trade-off: durabilidade vs storage
- **Leader-Follower Model:**
    - Um leader por partição
    - Followers replicam do leader
    - Eleição automática em falhas
- **ISR (In-Sync Replicas):**
    - Replicas sincronizadas
    - min.insync.replicas configuração
    - Garantia de durabilidade
- **Failover:**
    - Detecção automática de falhas
    - Eleição de novo leader
    - Rebalanceamento de partições

## 3. Tópicos no Kafka

### 3.1. O que são Tópicos

- **Conceito fundamental:**
    - Stream de registros categorizados
    - Similar a tabela em banco de dados
    - Multi-subscriber support
- **Características:**
    - Nome único por cluster
    - Configurações independentes
    - Schema opcional (Schema Registry)
- **Criação e gerenciamento:**

bash

```bash
  # Criar tópico
  kafka-topics.sh --create \
    --bootstrap-server localhost:9092 \
    --topic my-topic \
    --partitions 3 \
    --replication-factor 2
  
  # Listar tópicos
  kafka-topics.sh --list \
    --bootstrap-server localhost:9092
  
  # Descrever tópico
  kafka-topics.sh --describe \
    --bootstrap-server localhost:9092 \
    --topic my-topic
```

### 3.2. Particionamento de Tópicos

- **Estratégias de particionamento:**
    - Round-robin (sem key)
    - Hash-based (com key)
    - Custom partitioner
    - Sticky partitioning (otimização)
- **Considerações de design:**
    - Número de partições vs throughput
    - Ordenação por key
    - Hot partitions avoidance
- **Cálculo de partições:**

```
  Target Throughput (MB/s) = T
  Producer Throughput = P
  Consumer Throughput = C
  Partitions = max(T/P, T/C)
```

- **Reparticionamento:**
    - Adicionar partições (sempre possível)
    - Reduzir partições (não suportado)
    - Impacto na ordenação

### 3.3. Retenção de Mensagens

- **Políticas de retenção:**
    - **Time-based:** retention.ms
    - **Size-based:** retention.bytes
    - **Log compaction:** cleanup.policy=compact
- **Configurações:**

properties

```properties
  # Retenção por tempo (7 dias)
  retention.ms=604800000
  
  # Retenção por tamanho (1GB)
  retention.bytes=1073741824
  
  # Compactação
  cleanup.policy=compact
  min.cleanable.dirty.ratio=0.5
```

- **Segmentação:**
    - segment.bytes (default 1GB)
    - segment.ms (default 7 dias)
    - Impacto na performance

### 3.4. Replicação de Tópicos

- **Configuração de replicação:**

bash

```bash
  --replication-factor 3
  --config min.insync.replicas=2
```

- **Distribuição de réplicas:**
    - Rack awareness
    - Broker distribution
    - Manual reassignment
- **Monitoramento:**
    - Under-replicated partitions
    - ISR shrink/expand
    - Leader skew

## 4. Produtores no Kafka

### 4.1. O que é um Produtor

- **Definição e responsabilidades:**
    - Cliente que envia dados para Kafka
    - Serialização de dados
    - Escolha de partição
    - Retry logic
- **Ciclo de vida:**
    1. Criar producer instance
    2. Configurar propriedades
    3. Enviar mensagens
    4. Flush/close
- **APIs disponíveis:**
    - Java Client (nativo)
    - librdkafka (C/C++)
    - Clients para Python, Go, .NET, etc.

### 4.2. Envio de Mensagens para Tópicos

- **Métodos de envio:**

java

```java
  // Fire-and-forget
  producer.send(record);
  
  // Synchronous
  RecordMetadata metadata = producer.send(record).get();
  
  // Asynchronous com callback
  producer.send(record, new Callback() {
    public void onCompletion(RecordMetadata metadata, Exception e) {
      if(e != null) {
        e.printStackTrace();
      }
    }
  });
```

- **Batching:**
    - batch.size (bytes)
    - linger.ms (tempo de espera)
    - Trade-off: latência vs throughput
- **Compressão:**
    - none, gzip, snappy, lz4, zstd
    - compression.type configuração

### 4.3. Balanceamento de Carga com Partições

- **Default Partitioner:**
    - Sem key: round-robin/sticky
    - Com key: hash(key) % num_partitions
- **Custom Partitioner:**

java

```java
  public class CustomPartitioner implements Partitioner {
    public int partition(String topic, Object key, byte[] keyBytes,
                        Object value, byte[] valueBytes, Cluster cluster) {
      // Lógica customizada
      return partition;
    }
  }
```

- **Sticky Partitioner (2.4+):**
    - Melhor batching
    - Redução de latência
    - Distribuição mais uniforme

### 4.4. Confirmações de Envio (Acknowledgements)

- **Níveis de acks:**
    - **acks=0:** Sem confirmação (fire-and-forget)
    - **acks=1:** Leader confirma
    - **acks=all (-1):** Todos ISR confirmam
- **Configurações relacionadas:**

properties

```properties
  acks=all
  min.insync.replicas=2
  retries=2147483647
  max.in.flight.requests.per.connection=5
  enable.idempotence=true
```

- **Idempotência:**
    - Evita duplicatas em retries
    - Sequência garantida
    - Overhead mínimo

## 5. Consumidores no Kafka

### 5.1. O que é um Consumidor

- **Definição:**
    - Cliente que lê dados do Kafka
    - Pull-based model
    - Offset tracking
- **Características:**
    - Subscribe ou assign
    - Deserialização de dados
    - Processing guarantees

### 5.2. Grupos de Consumidores

- **Conceito:**
    - Conjunto de consumers cooperando
    - Load balancing automático
    - Fault tolerance
- **Distribuição de partições:**
    - Uma partição por consumer máximo
    - Rebalancing automático
    - Estratégias: Range, RoundRobin, Sticky, Cooperative
- **Configuração:**

properties

```properties
  group.id=my-consumer-group
  group.instance.id=consumer-1 # Para static membership
```

### 5.3. Leitura de Mensagens

- **Poll Loop Pattern:**

java

```java
  while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
    for (ConsumerRecord<String, String> record : records) {
      // Processar mensagem
    }
    consumer.commitSync();
  }
```

- **Configurações de fetch:**
    - fetch.min.bytes
    - fetch.max.wait.ms
    - max.partition.fetch.bytes
    - max.poll.records

### 5.4. Controle de Deslocamento (Offset)

- **Tipos de offset:**
    - Current offset (sendo lido)
    - Committed offset (confirmado)
    - Log-end offset (fim do log)
- **Offset reset strategies:**
    - earliest
    - latest
    - none
- **Storage:**
    - __consumer_offsets topic
    - Compacted topic

### 5.5. Commit Manual e Automático

- **Auto commit:**

properties

```properties
  enable.auto.commit=true
  auto.commit.interval.ms=5000
```

- **Manual commit:**

java

```java
  // Sync commit
  consumer.commitSync();
  
  // Async commit
  consumer.commitAsync();
  
  // Commit específico
  consumer.commitSync(Collections.singletonMap(partition, 
    new OffsetAndMetadata(lastOffset + 1)));
```

- **Trade-offs:**
    - Auto: simples mas risco de reprocessamento
    - Manual: controle fino mas complexidade

## 6. ZooKeeper e KRaft no Kafka

### 6.1. O Papel do ZooKeeper

- **Responsabilidades tradicionais:**
    - Armazenar metadata do cluster
    - Controller election
    - Broker registration
    - Topic configuration
    - ACLs e quotas
    - Consumer offsets (versões antigas)
- **Estrutura no ZooKeeper:**

```
  /kafka
    /brokers
      /ids
      /topics
    /controller
    /admin
    /config
```

### 6.2. Gerenciamento de Brokers e Partições

- **Registro de brokers:**
    - Ephemeral nodes
    - Heartbeat mechanism
    - Failure detection
- **Partition assignment:**
    - Leader/follower mapping
    - ISR list management
    - Reassignment operations

### 6.3. Coordenação de Eleições de Líder

- **Controller broker:**
    - Um controller por cluster
    - Gerencia todas eleições
    - Monitora broker failures
- **Eleição de partition leader:**
    - Preferred leader election
    - Unclean leader election
    - Min ISR requirements

### 6.4. Migração para KRaft

- **Benefícios do KRaft:**
    - Eliminação de dependência externa
    - Melhor escalabilidade (milhões de partições)
    - Recuperação mais rápida
    - Operação simplificada
- **Processo de migração:**
    1. Upgrade para versão compatível
    2. Dual-write mode
    3. Migration tools
    4. Cutover
    5. ZooKeeper removal

## 7. Processamento de Streams no Kafka

### 7.1. Introdução ao Kafka Streams

- **O que é:**
    - Biblioteca Java para processamento de streams
    - Parte do Apache Kafka
    - Sem cluster separado necessário
- **Características:**
    - Exactly-once semantics
    - Stateful processing
    - Windowing support
    - Interactive queries
- **Arquitetura:**
    - Stream tasks
    - Stream threads
    - State stores

### 7.2. Diferença entre Kafka Streams e Processamento Tradicional

- **Kafka Streams:**
    - Library, não framework
    - Embedded em aplicação
    - Scaling horizontal simples
    - Fault tolerance automática
- **vs Batch Processing:**
    - Continuous vs periodic
    - Low latency vs high throughput
    - Stream time vs processing time
- **vs Other Stream Processors:**
    - Simplicidade vs features
    - No cluster management
    - Tight Kafka integration

### 7.3. Operações de Streams

#### 7.3.1. Filtragem

java

```java
KStream<String, Order> orders = builder.stream("orders");
KStream<String, Order> largeOrders = orders.filter(
    (key, order) -> order.getAmount() > 1000
);
```

#### 7.3.2. Agregação

java

```java
KTable<String, Long> orderCounts = orders
    .groupByKey()
    .count(Materialized.as("order-counts-store"));

KTable<Windowed<String>, Long> windowedCounts = orders
    .groupByKey()
    .windowedBy(TimeWindows.of(Duration.ofMinutes(5)))
    .count();
```

#### 7.3.3. Junções de Fluxos

java

```java
// Stream-Stream Join
KStream<String, EnrichedOrder> enrichedOrders = orders.join(
    payments,
    (order, payment) -> new EnrichedOrder(order, payment),
    JoinWindows.of(Duration.ofMinutes(5))
);

// Stream-Table Join
KStream<String, CustomerOrder> customerOrders = orders.join(
    customers,
    (order, customer) -> new CustomerOrder(order, customer)
);
```

### 7.4. Windowing

- **Tipos de janelas:**
    - **Tumbling:** Não sobrepõem, tamanho fixo
    - **Hopping:** Sobrepõem, tamanho e avanço fixos
    - **Sliding:** Atualizam continuamente
    - **Session:** Baseadas em inatividade
- **Implementação:**

java

```java
  TimeWindows.of(Duration.ofMinutes(5)) // Tumbling
  TimeWindows.of(Duration.ofMinutes(10)).advanceBy(Duration.ofMinutes(5)) // Hopping
  SessionWindows.with(Duration.ofMinutes(5)) // Session
```

### 7.5. State Stores

- **Tipos:**
    - In-memory stores
    - Persistent stores (RocksDB)
    - Window stores
- **Gerenciamento:**
    - Changelog topics
    - State restoration
    - Standby replicas
- **Interactive Queries:**

java

```java
  ReadOnlyKeyValueStore<String, Long> store = 
    streams.store("store-name", QueryableStoreTypes.keyValueStore());
```

## 8. Administração do Kafka

### 8.1. Instalação e Configuração do Kafka

#### 8.1.1. Instalação com ZooKeeper

bash

```bash
# Download e extração
wget https://downloads.apache.org/kafka/3.6.0/kafka_2.13-3.6.0.tgz
tar -xzf kafka_2.13-3.6.0.tgz
cd kafka_2.13-3.6.0

# Iniciar ZooKeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

# Iniciar Kafka
bin/kafka-server-start.sh config/server.properties
```

#### 8.1.2. Configuração de Brokers

properties

```properties
# server.properties
broker.id=0
listeners=PLAINTEXT://localhost:9092
log.dirs=/var/kafka-logs
num.partitions=3
default.replication.factor=3
min.insync.replicas=2
log.retention.hours=168
log.segment.bytes=1073741824
zookeeper.connect=localhost:2181
```

### 8.2. Monitoramento e Gerenciamento

#### 8.2.1. Ferramentas de Monitoramento

- **JMX Metrics:**
    - Broker metrics
    - Producer/Consumer metrics
    - JVM metrics
- **Ferramentas:**
    - **Prometheus + Grafana:** Métricas e dashboards
    - **Burrow:** Consumer lag monitoring
    - **Kafka Manager/CMAK:** UI para administração
    - **Conduktor:** Plataforma completa
    - **AKHQ:** Web UI moderna
- **Métricas críticas:**
    - Messages in/out rate
    - Byte in/out rate
    - Request rate and latency
    - ISR shrink/expand rate
    - Consumer lag

#### 8.2.2. Monitoramento de Tópicos e Partições

bash

```bash
# Under-replicated partitions
kafka-topics.sh --describe --under-replicated-partitions \
  --bootstrap-server localhost:9092

# Consumer lag
kafka-consumer-groups.sh --describe \
  --group my-group \
  --bootstrap-server localhost:9092

# Partition distribution
kafka-log-dirs.sh --describe \
  --bootstrap-server localhost:9092
```

### 8.3. Segurança no Kafka

#### 8.3.1. Autenticação com SSL/TLS

properties

```properties
# Broker config
listeners=SSL://localhost:9093
ssl.keystore.location=/var/kafka/kafka.server.keystore.jks
ssl.keystore.password=password
ssl.key.password=password
ssl.truststore.location=/var/kafka/kafka.server.truststore.jks
ssl.truststore.password=password
ssl.client.auth=required
```

#### 8.3.2. SASL Authentication

- **Mecanismos suportados:**
    - PLAIN
    - SCRAM-SHA-256/512
    - GSSAPI (Kerberos)
    - OAUTHBEARER
- **Configuração SCRAM:**

bash

```bash
  kafka-configs.sh --bootstrap-server localhost:9092 \
    --alter --add-config 'SCRAM-SHA-256=[iterations=8192,password=alice-secret]' \
    --entity-type users --entity-name alice
```

#### 8.3.3. Controle de Acesso com ACLs

bash

```bash
# Permitir produtor
kafka-acls.sh --bootstrap-server localhost:9092 \
  --add --allow-principal User:alice \
  --operation Write --topic test-topic

# Permitir consumidor
kafka-acls.sh --bootstrap-server localhost:9092 \
  --add --allow-principal User:bob \
  --operation Read --topic test-topic \
  --group test-group
```

### 8.4. Escalabilidade

#### 8.4.1. Adição de Novos Brokers

1. **Preparar novo broker:**
    - Configurar broker.id único
    - Mesmo zookeeper.connect
    - Configurações compatíveis
2. **Iniciar broker**
3. **Redistribuir partições:**

bash

```bash
   kafka-reassign-partitions.sh --generate \
     --topics-to-move-json-file topics.json \
     --broker-list "0,1,2,3"
```

#### 8.4.2. Rebalanceamento de Partições

- **Criar plano de reassignment:**

```json
  {
    "partitions": [
      {
        "topic": "test",
        "partition": 0,
        "replicas": [1,2,3]
      }
    ]
  }
```

- **Executar reassignment:**

bash

```bash
  kafka-reassign-partitions.sh --execute \
    --reassignment-json-file reassignment.json \
    --bootstrap-server localhost:9092
```

- **Monitorar progresso:**

bash

```bash
  kafka-reassign-partitions.sh --verify \
    --reassignment-json-file reassignment.json \
    --bootstrap-server localhost:9092
```

## 9. Integração do Kafka com Outros Sistemas

### 9.1. Integração com Apache Flink

- **Flink Kafka Connector:**

java

```java
  FlinkKafkaConsumer<String> consumer = new FlinkKafkaConsumer<>(
    "topic",
    new SimpleStringSchema(),
    properties
  );
  
  DataStream<String> stream = env.addSource(consumer);
```

- **Features:**
    - Exactly-once semantics
    - Checkpointing
    - Backpressure handling
    - Event time processing
- **Use cases:**
    - Complex event processing
    - Real-time analytics
    - Machine learning pipelines

### 9.2. Integração com Apache Spark

- **Spark Structured Streaming:**

scala

```scala
  val df = spark
    .readStream
    .format("kafka")
    .option("kafka.bootstrap.servers", "localhost:9092")
    .option("subscribe", "topic")
    .load()
```

- **Features:**
    - Micro-batch processing
    - SQL queries on streams
    - ML integration
- **Spark Streaming (DStream):**
    - Direct approach
    - Receiver-based approach

### 9.3. Conectores Kafka Connect

#### 9.3.1. Source Connectors

- **JDBC Source:**

json

```json
  {
    "name": "jdbc-source",
    "config": {
      "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
      "connection.url": "jdbc:postgresql://localhost/test",
      "mode": "incrementing",
      "incrementing.column.name": "id",
      "topic.prefix": "postgres-"
    }
  }
```

- **Debezium CDC:**
    - MySQL, PostgreSQL, Oracle
    - MongoDB, SQL Server
    - Real-time change capture
- **File Source:**
    - CSV, JSON, Avro
    - Directory watching

#### 9.3.2. Sink Connectors

- **Elasticsearch Sink:**

json

```json
  {
    "name": "es-sink",
    "config": {
      "connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector",
      "connection.url": "http://localhost:9200",
      "topics": "logs",
      "type.name": "_doc"
    }
  }
```

- **S3 Sink:**
    - Partitioned by time
    - Multiple formats
    - Compression support
- **JDBC Sink:**
    - Upsert/insert modes
    - Schema evolution

### 9.4. REST Proxy e Schema Registry

- **REST Proxy:**
    - HTTP interface para Kafka
    - Produce/consume via REST
    - Útil para linguagens sem client nativo
- **Schema Registry:**
    - Centralização de schemas
    - Versionamento e evolução
    - Compatibilidade enforcement

## 10. Tópicos Avançados

### 10.1. Replicação entre Data Centers

- **MirrorMaker 2.0:**
    - Active-Active replication
    - Active-Passive replication
    - Offset translation
    - Topic renaming
- **Configuração:**

properties

```properties
  clusters = primary, backup
  primary.bootstrap.servers = primary:9092
  backup.bootstrap.servers = backup:9092
  
  primary->backup.enabled = true
  primary->backup.topics = .*
```

- **Considerações:**
    - Network latency
    - Bandwidth costs
    - Conflict resolution

### 10.2. Compactação de Log

- **Conceito:**
    - Mantém última mensagem por key
    - Útil para snapshots e caches
    - Reduz storage
- **Configuração:**

properties

```properties
  cleanup.policy=compact
  min.cleanable.dirty.ratio=0.5
  segment.ms=604800000
  delete.retention.ms=86400000
```

- **Use cases:**
    - Database changelogs
    - Cache materialization
    - Configuration distribution

### 10.3. Rebalanceamento de Consumidores

- **Triggers:**
    - Consumer join/leave
    - Partition count change
    - Subscription change
- **Estratégias:**
    - **Range:** Partições consecutivas
    - **RoundRobin:** Distribuição uniforme
    - **Sticky:** Minimiza movimento
    - **Cooperative:** Incremental rebalancing
- **Configuração:**

properties

```properties
  partition.assignment.strategy=org.apache.kafka.clients.consumer.CooperativeStickyAssignor
  session.timeout.ms=10000
  max.poll.interval.ms=300000
```

### 10.4. Kafka Streams vs. Apache Flink

|Aspecto|Kafka Streams|Apache Flink|
|---|---|---|
|Deployment|Library (embedded)|Cluster|
|Complexity|Simples|Complexo|
|Features|Básico-intermediário|Avançado|
|Latency|Low ms|Sub-ms|
|State Management|RocksDB|Pluggable|
|SQL Support|KSQL (separado)|Table API nativo|
|Use Cases|Microserviços, apps médias|Big data, ML, CEP|

### 10.5. Otimização de Desempenho

#### 10.5.1. Configurações de Tópicos

properties

```properties
# Otimizações de performance
compression.type=lz4
segment.bytes=1073741824
min.insync.replicas=2
unclean.leader.election.enable=false

# Otimizações para throughput
batch.size=32768
linger.ms=5
buffer.memory=33554432
```

#### 10.5.2. Tuning de Produtores e Consumidores

- **Producer tuning:**

properties

```properties
  # Throughput
  batch.size=65536
  linger.ms=10
  compression.type=lz4
  buffer.memory=67108864
  
  # Latência
  batch.size=0
  linger.ms=0
  compression.type=none
```

- **Consumer tuning:**

properties

```properties
  # Throughput
  fetch.min.bytes=50000
  fetch.max.wait.ms=500
  max.partition.fetch.bytes=1048576
  
  # Latência
  fetch.min.bytes=1
  fetch.max.wait.ms=0
```

### 10.6. Transações

- **Producer transactions:**

java

```java
  producer.initTransactions();
  try {
    producer.beginTransaction();
    producer.send(record1);
    producer.send(record2);
    producer.commitTransaction();
  } catch (Exception e) {
    producer.abortTransaction();
  }
```

- **Consumer configuration:**

properties

```properties
  isolation.level=read_committed
  enable.auto.commit=false
```

- **Exactly-once semantics:**
    - Idempotent producers
    - Transactional messaging
    - Stream processing guarantees

## 11. Casos de Uso e Boas Práticas

### 11.1. Event Sourcing

- **Conceito:**
    - Armazenar eventos, não estado
    - Rebuild de estado a partir de eventos
    - Auditoria completa
- **Implementação com Kafka:**
    - Topics como event stores
    - Compacted topics para snapshots
    - Kafka Streams para projeções
- **Exemplo prático:**

java

```java
  // Evento
  OrderCreated event = new OrderCreated(orderId, customerId, items);
  producer.send(new ProducerRecord<>("orders-events", orderId, event));
  
  // Projeção
  KTable<String, Order> orders = ordersEvents
    .groupByKey()
    .aggregate(
      Order::new,
      (orderId, event, order) -> order.apply(event),
      Materialized.as("orders-store")
    );
```

### 11.2. CQRS (Command Query Responsibility Segregation)

- **Arquitetura:**
    - Commands via Kafka
    - Query models via projections
    - Eventual consistency
- **Implementação:**
    - Command topics
    - Event topics
    - View materialization com Kafka Streams/Connect
- **Benefícios:**
    - Escalabilidade independente
    - Modelos otimizados
    - Flexibilidade

### 11.3. Processamento em Tempo Real

- **Patterns:**
    - Stream enrichment
    - Windowed aggregations
    - Pattern detection
    - Alerting
- **Exemplos:**
    - Detecção de fraude
    - Monitoramento de IoT
    - Real-time dashboards
    - Recomendações

### 11.4. Boas Práticas de Desenvolvimento com Kafka

- **Design de topics:**
    - Um tópico por tipo de evento
    - Naming consistente: `<namespace>.<entity>.<event>`
    - Evitar topics muito granulares
- **Mensagens:**
    - Use schemas (Avro/Protobuf)
    - Inclua metadata em headers
    - Keys significativas para ordenação
- **Error handling:**
    - Dead letter queues
    - Retry topics
    - Circuit breakers
- **Testing:**
    - EmbeddedKafka para testes
    - Testcontainers
    - Consumer lag simulation

### 11.5. Boas Práticas de Administração e Escalabilidade

- **Capacity planning:**

```
  Storage = (msg/day × size × retention × RF) / compression
  Network = peak msg/sec × size × (RF + consumers)
```

- **Monitoramento essencial:**
    - Under-replicated partitions = 0
    - Consumer lag crescente
    - Disk usage < 85%
    - Network utilization < 80%
- **Deployment:**
    - Automatização com IaC (Terraform)
    - GitOps para configurações
    - Blue-green deployments
- **Disaster Recovery:**
    - Backup de configurações
    - Multi-DC replication
    - RTO/RPO definidos
    - Testes regulares de DR

## 12. Padrões Arquiteturais com Kafka

### 12.1. Microserviços Event-Driven

- **Comunicação assíncrona:**
    - Decoupling de serviços
    - Resiliência a falhas
    - Escalabilidade independente
- **Patterns:**
    - Event notification
    - Event-carried state transfer
    - Event collaboration
- **Implementação:**
    - Topic por agregado
    - Schema registry obrigatório
    - Consumer groups por serviço

### 12.2. Saga Pattern

- **Coordenação de transações distribuídas:**
    - Choreography-based sagas
    - Orchestration-based sagas
    - Compensating transactions
- **Implementação com Kafka:**

java

```java
  // Choreography
  orderService.on("OrderCreated", event -> {
    // Reserve inventory
    publish("InventoryReserved", ...);
  });
  
  paymentService.on("InventoryReserved", event -> {
    // Process payment
    publish("PaymentProcessed", ...);
  });
```

### 12.3. Data Mesh

- **Princípios:**
    - Domain ownership
    - Data as product
    - Self-serve platform
    - Federated governance
- **Kafka como backbone:**
    - Topics por domínio
    - Schema registry por domínio
    - Cross-domain events
    - Data discovery

### 12.4. Lambda Architecture

- **Camadas:**
    - Batch layer (histórico)
    - Speed layer (real-time)
    - Serving layer
- **Kafka role:**
    - Fonte única de verdade
    - Feed para batch e streaming
    - Reprocessamento capability

## 13. Cloud e Kubernetes

### 13.1. Amazon MSK (Managed Streaming for Kafka)

- **Tipos de cluster:**
    - **Provisioned:** Controle total
    - **Serverless:** Totalmente gerenciado
- **Features:**
    - Auto-scaling
    - AWS integration
    - IAM authentication
    - MSK Connect
- **Configuração com Terraform:**

hcl

```hcl
  resource "aws_msk_cluster" "kafka" {
    cluster_name = "production-kafka"
    kafka_version = "3.6.0"
    number_of_broker_nodes = 3
    
    broker_node_group_info {
      instance_type = "kafka.m5.large"
      storage_info {
        ebs_storage_info {
          volume_size = 1000
        }
      }
    }
    
    client_authentication {
      sasl {
        iam = true
      }
    }
  }
```

### 13.2. Confluent Cloud

- **Features:**
    - Global deployment
    - 99.99% SLA
    - ksqlDB included
    - Managed connectors
- **Pricing model:**
    - Pay per use
    - Ingress/egress charges
    - Storage costs

### 13.3. Kubernetes com Strimzi

- **Operator pattern:**

yaml

```yaml
  apiVersion: kafka.strimzi.io/v1beta2
  kind: Kafka
  metadata:
    name: production-cluster
  spec:
    kafka:
      replicas: 3
      listeners:
        - name: tls
          port: 9093
          type: internal
          tls: true
      storage:
        type: persistent-claim
        size: 100Gi
      config:
        default.replication.factor: 3
        min.insync.replicas: 2
    zookeeper:
      replicas: 3
      storage:
        type: persistent-claim
        size: 10Gi
```

## 14. Troubleshooting e Debugging

### 14.1. Problemas Comuns e Soluções

#### Consumer Lag Crescente

- **Causas:**
    - Consumer lento
    - Rebalancing frequente
    - Configuração inadequada
- **Soluções:**
    - Aumentar paralelismo (mais consumers)
    - Otimizar processamento
    - Ajustar session.timeout.ms
    - Verificar max.poll.records

#### Under-replicated Partitions

- **Causas:**
    - Broker down
    - Disk failure
    - Network issues
- **Soluções:**
    - Verificar broker health
    - Aumentar replica.lag.time.max.ms
    - Forced leader election

#### Out of Memory

- **Causas:**
    - Muitas partições
    - Buffer overflow
    - State stores grandes
- **Soluções:**
    - Aumentar heap size
    - Reduzir buffer.memory
    - Configurar RocksDB

### 14.2. Tools de Debugging

- **kafka-dump-log:**

bash

```bash
  kafka-dump-log.sh --files /var/kafka-logs/topic-0/00000000000000000000.log \
    --print-data-log
```

- **kafka-consumer-groups:**

bash

```bash
  # Resetar offset
  kafka-consumer-groups.sh --bootstrap-server localhost:9092 \
    --group my-group --reset-offsets --to-earliest \
    --topic test --execute
```

- **JMX/JConsole:**
    - Heap analysis
    - Thread dumps
    - MBean inspection

## 15. Performance e Benchmarking

### 15.1. Ferramentas de Teste

bash

```bash
# Producer performance test
kafka-producer-perf-test.sh \
  --topic test \
  --num-records 1000000 \
  --record-size 1024 \
  --throughput -1 \
  --producer-props bootstrap.servers=localhost:9092

# Consumer performance test
kafka-consumer-perf-test.sh \
  --bootstrap-server localhost:9092 \
  --topic test \
  --messages 1000000
```

### 15.2. Métricas de Performance

- **Throughput:**
    - MB/sec in/out
    - Messages/sec
    - Records/sec per partition
- **Latency:**
    - End-to-end latency
    - Produce latency
    - Fetch latency
- **Utilization:**
    - CPU usage
    - Memory usage
    - Disk I/O
    - Network I/O

### 15.3. Otimizações de Hardware

- **Disk:**
    - SSD para melhor latência
    - RAID 10 para performance
    - XFS ou ext4 filesystem
- **Network:**
    - 10 Gbps+ para produção
    - Dedicated network para replication
- **Memory:**
    - 32-64 GB para brokers
    - Page cache optimization
- **CPU:**
    - 8-16 cores mínimo
    - Disable CPU power saving

## 16. Projetos Práticos Sugeridos

### 16.1. Sistema de Logs Distribuído

- Topics por aplicação
- Aggregation com Kafka Streams
- Sink para Elasticsearch
- Dashboard com Kibana

### 16.2. Pipeline de Data Lake

- CDC com Debezium
- Transformação com Kafka Streams
- Sink para S3/HDFS
- Catalogação com Glue/Hive

### 16.3. Sistema de Notificações Real-time

- Eventos de múltiplas fontes
- Rules engine
- Delivery channels (email, SMS, push)
- Tracking e analytics

### 16.4. E-commerce Event-Driven

- Order management
- Inventory tracking
- Payment processing
- Shipping coordination
- Analytics e reporting

## 17. Recursos de Estudo e Certificação

### 17.1. Livros Essenciais

1. **"Kafka: The Definitive Guide"** (Narkhede, Shapira, Palino)
2. **"Kafka Streams in Action"** (Bill Bejeck)
3. **"Mastering Kafka Streams and ksqlDB"** (Mitch Seymour)
4. **"Building Event-Driven Microservices"** (Adam Bellemare)

### 17.2. Cursos e Treinamentos

- **Confluent Training:** Developer, Administrator, Streams
- **Udemy:** Apache Kafka Series (Stephane Maarek)
- **Coursera:** Big Data Analysis with Scala and Spark
- **LinkedIn Learning:** Apache Kafka Essential Training

### 17.3. Certificações

- **CCDAK:** Confluent Certified Developer for Apache Kafka
    - 60 questões
    - 90 minutos
    - 70% para passar
- **CCAAK:** Confluent Certified Administrator for Apache Kafka
    - Foco em operações
    - Troubleshooting
    - Security

### 17.4. Comunidade e Eventos

- **Kafka Summit:** Conferência anual
- **Meetups locais:** Kafka User Groups
- **Online:**
    - Confluent Community Slack
    - Apache Kafka mailing lists
    - Stack Overflow
    - Reddit r/apachekafka

## 18. Roadmap de Aprendizado Recomendado

### Fase 1: Fundamentos (4 semanas)

- ✅ Conceitos básicos
- ✅ Instalação local
- ✅ Producers/Consumers simples
- ✅ Topics e partições

### Fase 2: Intermediário (4 semanas)

- ✅ Replicação e durabilidade
- ✅ Consumer groups
- ✅ Performance básica
- ✅ Segurança básica

### Fase 3: Avançado (6 semanas)

- ✅ Kafka Streams
- ✅ Kafka Connect
- ✅ Schema Registry
- ✅ Operações em produção

### Fase 4: Especialização (4 semanas)

- ✅ Cloud deployments
- ✅ Kubernetes
- ✅ Patterns arquiteturais
- ✅ Troubleshooting avançado

### Fase 5: Maestria (Contínuo)

- ✅ Contribuições open source
- ✅ Certificações
- ✅ Projetos complexos
- ✅ Mentoria e ensino