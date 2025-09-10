# Soluções para Dual Write em Microserviços

O problema de dual write em arquiteturas de microserviços tem quatro soluções principais bem estabelecidas: [Autenticado](https://authzed.com/blog/the-dual-write-problem) **Transactional Outbox oferece o melhor equilíbrio** entre simplicidade e confiabilidade para a maioria dos casos, enquanto **Change Data Capture** é ideal para sistemas legados, **Listen to Yourself** para ultra-baixa latência, e **Event Sourcing** para domínios com necessidades críticas de auditabilidade. [Confluente +2](https://developer.confluent.io/courses/microservices/the-dual-write-problem/) Cada padrão resolve o mesmo problema fundamental - escrever atomicamente em banco de dados e sistema de mensageria - mas com trade-offs distintos de consistência, performance e complexidade operacional. [Confluente +5](https://developer.confluent.io/courses/microservices/the-dual-write-problem/)

O dual write problem surge quando uma operação precisa atualizar simultaneamente dois sistemas diferentes (banco de dados local e message broker). [Autenticado](https://authzed.com/blog/the-dual-write-problem) Se a aplicação atualizar o banco mas falhar ao enviar o evento, ou vice-versa, o sistema fica em estado inconsistente. [Microservices.io +3](https://microservices.io/patterns/data/transactional-outbox.html) Estes quatro padrões garantem que dados locais e eventos para outros serviços permaneçam sincronizados mesmo quando operações falham parcialmente. [Microservices.io](https://microservices.io/patterns/data/transactional-outbox.html)

## Change Data Capture: transparência total para sistemas legados

O **Change Data Capture (CDC)** resolve dual write eliminando a necessidade de escrever simultaneamente em dois locais. A aplicação escreve apenas no banco de dados, enquanto uma ferramenta especializada monitora o transaction log e captura mudanças automaticamente, propagando-as como eventos para downstream services. [debézio +6](https://debezium.io/)

### Implementação e funcionamento técnico

CDC funciona monitorando o Write-Ahead Log (WAL) do PostgreSQL, binary logs do MySQL, ou oplogs do MongoDB. [AWS +4](https://aws.amazon.com/blogs/database/aws-dms-now-supports-native-cdc-support/) **Debezium**, a ferramenta líder de mercado, conecta-se diretamente ao transaction log e converte mudanças em eventos Kafka: [debézio +4](https://debezium.io/)

json

```json
{
  "name": "orders-connector",
  "config": {
    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
    "database.hostname": "localhost",
    "database.port": "5432",
    "database.user": "debezium",
    "database.dbname": "ecommerce",
    "database.server.name": "orders",
    "table.whitelist": "public.orders,public.order_items"
  }
}
```

O fluxo operacional é direto: aplicação atualiza tabela orders → PostgreSQL grava no WAL → Debezium detecta mudança → evento publicado no Kafka topic → serviços downstream consomem eventos. [Confluente +2](https://developer.confluent.io/courses/microservices/the-dual-write-problem/) **A latência típica é de 50-200ms**, considerada near real-time para a maioria dos casos.

### Vantagens e limitações arquiteturais

**CDC brilha em cenários de migração** onde modificar aplicações existentes é inviável. Não requer mudanças de código, mantém performance da aplicação principal, e **garante que toda mudança no database seja capturada**. [chapéu vermelho +5](https://developers.redhat.com/articles/2021/09/21/distributed-transaction-patterns-microservices-compared) A ordem das operações é preservada, e ferramentas como Debezium oferecem snapshot inicial para sincronizar estado atual. [debézio +3](https://debezium.io/)

As **desvantagens incluem eventos de baixo nível** que refletem estrutura de database ao invés de intenções de negócio. [Upsolver](https://www.upsolver.com/blog/debezium-vs-upsolver)[Estouro de pilha](https://stackoverflow.com/questions/72284628/comparing-cdc-vs-outbox-pattern-for-creating-event-streams) Mudanças técnicas (reorganização de índices, garbage collection) podem gerar ruído. [Lógica Scott](https://blog.scottlogic.com/2025/09/08/solving-data-consistency-in-distributed-systems-with-the-transactional-outbox.html) **Debugging é complexo** pois eventos não mostram contexto da operação original. Diferentes SGBDs requerem conectores específicos, criando dependência de infraestrutura especializada. [chapéu vermelho +2](https://developers.redhat.com/articles/2021/09/21/distributed-transaction-patterns-microservices-compared)

### Tecnologias e stack recomendado

**Debezium + Apache Kafka** formam a combinação mais madura, usado por empresas como Netflix e Uber. [debézio +5](https://debezium.io/) **Confluent Cloud** oferece conectores Debezium pré-configurados para casos cloud-native. Para ambientes AWS, **Database Migration Service (DMS)** oferece CDC gerenciado. [AWS +3](https://aws.amazon.com/blogs/database/aws-dms-now-supports-native-cdc-support/) Alternativas incluem **Airbyte** (600+ conectores), **Striim** (comercial), e **IBM InfoSphere** (enterprise). [Geeks para Geeks](https://www.geeksforgeeks.org/system-design/change-data-capture-cdc/)[Troca de ar](https://airbyte.com/top-etl-tools-for-sources/cdc-tools)

Bancos com melhor suporte CDC: PostgreSQL (WAL nativo), MySQL (binary logs), MongoDB (oplogs), Oracle (redo logs com licenças adicionais). [AWS +4](https://aws.amazon.com/blogs/database/aws-dms-now-supports-native-cdc-support/) **Stack típico**: Aplicação → Database → Debezium → Kafka → Consumer Services.

## Listen to Yourself: velocidade máxima com consistência eventual extrema

**Listen to Yourself** inverte a ordem tradicional de operações: ao invés de escrever no banco e depois enviar evento, **publica o evento primeiro** e responde imediatamente ao cliente. O próprio serviço consome seu evento e atualiza o database assincronamente. [Confluente +3](https://www.confluent.io/blog/dual-write-problem/)

### Fluxo técnico e implementação prática

O padrão segue uma sequência event-first: cliente envia CreateOrder → serviço converte em OrderCreated event → publica no Kafka → responde "comando aceito" → consome próprio evento → atualiza database local. [Médio](https://medium.com/@vivekrajyaguru1993/simplifying-complex-microservice-operations-with-the-listen-to-yourself-pattern-c9965b82dde9)[Confluente](https://developer.confluent.io/courses/microservices/the-listen-to-yourself-pattern/) Em caso de erro no processamento, publica evento compensatório. [Confluente +2](https://www.confluent.io/blog/dual-write-problem/)

Java

```java
@RestController
public class OrderController {
    @PostMapping("/orders")
    public ResponseEntity<String> createOrder(@RequestBody CreateOrderCommand cmd) {
        // 1. Evento PRIMEIRO
        OrderCreatedEvent event = new OrderCreatedEvent(
            cmd.getOrderId(), cmd.getCustomerId(), cmd.getAmount()
        );
        
        // 2. Publica imediatamente
        kafkaTemplate.send("order-events", event);
        
        // 3. Resposta antes de qualquer persistência
        return ResponseEntity.accepted()
            .body("Order creation initiated: " + cmd.getOrderId());
    }
}

@KafkaListener(topics = "order-events")
public void handleOrderCreated(OrderCreatedEvent event) {
    // 4. Próprio serviço atualiza database
    Order order = new Order(event.getOrderId(), event.getCustomerId(), event.getAmount());
    orderRepository.save(order);
}
```

### Performance excepcional com trade-offs significativos

**Listen to Yourself oferece a menor latência possível** - resposta imediata limitada apenas pela velocidade do message broker. Throughput é limitado pela capacidade do Kafka, não pelo database. **Paralelização natural** permite múltiplos workers processando eventos simultaneamente. [Confluente +3](https://www.confluent.io/blog/dual-write-problem/)

O **trade-off crítico é consistência eventual extrema**. Cliente recebe confirmação antes de dados serem persistidos. Não há "read your own writes" - cliente pode buscar ordem criada e não encontrar. [médio +2](https://medium.com/@odedia/listen-to-yourself-design-pattern-for-event-driven-microservices-16f97e3ed066) **Debugging torna-se muito complexo** pois estado fica distribuído entre broker e database com timing variável. [Confluente](https://www.confluent.io/blog/dual-write-problem/)[Confluente](https://developer.confluent.io/courses/microservices/the-listen-to-yourself-pattern/)

### Cenários apropriados e anti-padrões

Ideal para **aplicações tolerantes à inconsistência** como sistemas de recomendação, métricas não-críticas, ou workflows onde velocidade supera precisão imediata. [Confluente](https://www.confluent.io/blog/dual-write-problem/) **Evite para operações financeiras**, criação de contas, ou qualquer caso onde cliente precisa de confirmação imediata de persistência. [Médio](https://medium.com/@vivekrajyaguru1993/simplifying-complex-microservice-operations-with-the-listen-to-yourself-pattern-c9965b82dde9)

**Framework principal é Spring Cloud Stream** com Kafka binder. Alternativas incluem **Akka Streams** para sistemas reativo, **Apache Camel** para integrações complexas. Message brokers: Kafka (padrão), RabbitMQ (menor throughput), Redis Pub/Sub (casos simples).

## Transactional Outbox: o equilíbrio perfeito entre consistência e confiabilidade

**Transactional Outbox** resolve dual write criando uma tabela "outbox" no mesmo banco da aplicação. Uma única transação atualiza dados de negócio E insere evento na outbox. Processo separado (message relay) lê outbox e publica eventos, garantindo atomicidade e eventual delivery. [Microservices.io +7](https://microservices.io/patterns/data/transactional-outbox.html)

### Implementação robusta e testada em produção

O padrão requer schema simples mas efetivo:

sql

```sql
CREATE TABLE outbox_events (
    event_id UUID PRIMARY KEY,
    aggregate_id UUID NOT NULL,
    event_type VARCHAR(255) NOT NULL,
    event_data JSONB NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    processed BOOLEAN DEFAULT FALSE
);
```

A implementação garante atomicidade através de transação única: [Microservices.io +2](https://microservices.io/patterns/data/transactional-outbox.html)

Java

```java
@Service
@Transactional
public class OrderService {
    public void createOrder(CreateOrderCommand command) {
        // 1. Atualiza dados de negócio
        Order order = new Order(command.getOrderId(), command.getCustomerId());
        orderRepository.save(order);
        
        // 2. Evento na outbox (mesma transação)
        OutboxEvent event = new OutboxEvent(
            UUID.randomUUID(),
            order.getId(),
            "OrderCreated",
            JsonUtils.toJson(new OrderCreatedEvent(order))
        );
        outboxRepository.save(event);
        
        // Ambos operações succedem OU ambos falham atomicamente
    }
}
```

**Message relay** processa outbox com polling ou CDC. Polling é mais simples mas menos eficiente. **CDC sobre tabela outbox** oferece latência mínima: [Microservices.io +4](https://microservices.io/patterns/data/transactional-outbox.html)

Java

```java
@Scheduled(fixedDelay = 5000)
public void processOutboxEvents() {
    List<OutboxEvent> events = outboxRepository.findUnprocessedEventsOrderByCreatedAt();
    
    for (OutboxEvent event : events) {
        kafkaTemplate.send("order-events", event.getEventData());
        event.setProcessed(true);
        outboxRepository.save(event);
    }
}
```

### Vantagens arquiteturais decisivas

**Strong consistency local** garante que dados e eventos estejam sempre sincronizados dentro da transação. Cliente pode fazer "read your own writes" imediatamente. **Reliable messaging** com garantia de eventual delivery através de retry mechanisms. [Debézio](https://debezium.io/blog/2020/02/10/event-sourcing-vs-cdc/)[Debézio](https://debezium.io/blog/2019/02/19/reliable-microservices-data-exchange-with-the-outbox-pattern/) **Ordem preservada** usando timestamps na outbox. [microsserviços](https://microservices.io/patterns/data/transactional-outbox.html)[Confluente](https://developer.confluent.io/courses/microservices/the-transactional-outbox-pattern/) **Usa garantias ACID** do database existente. [Microservices.io +2](https://microservices.io/patterns/data/transactional-outbox.html)

**Overhead é mínimo** - benchmarks mostram impacto de 10-15% na performance de writes. **Flexibility** permite controle total sobre formato dos eventos, diferente do CDC que expõe schema de database. [Microservices.io](https://microservices.io/patterns/data/transactional-outbox.html)[Estouro de pilha](https://stackoverflow.com/questions/72284628/comparing-cdc-vs-outbox-pattern-for-creating-event-streams)

### Tecnologias maduras e casos de uso

**Eventuate Tram** é o framework líder para Java, oferecendo implementação completa com Spring Boot integration. [Eventualizar +4](https://eventuate.io/) Para .NET, implementações com **Entity Framework + Azure Service Bus**. Python usa **SQLAlchemy + Celery**. Node.js com **TypeORM + Bull**.

**Soluções nativas em nuvem** : AWS DynamoDB Streams + Lambda, Azure Cosmos DB Change Feed + Service Bus, Google Cloud SQL + Cloud Functions + Pub/Sub.[AWS](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html) Todas mantêm o princípio core: transação local + processamento assíncrono. [AWS](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)[AWS](https://aws.amazon.com/blogs/compute/implementing-the-transactional-outbox-pattern-with-amazon-eventbridge-pipes/)

**Use Transactional Outbox** para sistemas business-critical que precisam de consistência forte local mas eventual consistency distribuída. **Ideal para migração incremental** de monólitos onde cada serviço pode adoptar o padrão independentemente. [Microservices.io](https://microservices.io/patterns/data/transactional-outbox.html)[Nitrobox](https://www.nitrobox.com/outbox-pattern-reliable-message-processing-in-event-driven-architecture/)

## Event Sourcing: auditabilidade total com complexidade arquitetural

**Event Sourcing** elimina dual write armazenando eventos como única fonte de verdade. Estado atual é reconstruído através do replay de eventos imutáveis. Eventos já estão disponíveis para downstream systems, resolvendo naturalmente o problema de dual write. [Microservices.io +5](https://microservices.io/patterns/data/event-sourcing.html)

### Implementação técnica avançada

Event Sourcing requer mudança fundamental no design - **estado não é armazenado, apenas derivado**: [Microsoft Learn +4](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)

Java

```java
public class Order {
    private String orderId;
    private OrderStatus status;
    private List<Event> uncommittedEvents = new ArrayList<>();
    
    // Command handler gera eventos
    public static Order createOrder(CreateOrderCommand command) {
        Order order = new Order();
        OrderCreatedEvent event = new OrderCreatedEvent(
            command.getOrderId(), command.getCustomerId(), command.getAmount()
        );
        order.apply(event);
        order.markEventAsUncommitted(event);
        return order;
    }
    
    // State reconstruction via replay
    public static Order fromEvents(List<Event> events) {
        Order order = new Order();
        events.forEach(event -> order.apply((OrderCreatedEvent) event));
        return order;
    }
}
```

**Event Store** deve garantir ordem e atomicidade: [Microservices.io](https://microservices.io/patterns/data/event-sourcing.html)[microsserviços](https://microservices.io/patterns/data/event-sourcing.html)

Java

```java
public void saveEvents(String aggregateId, List<Event> events, int expectedVersion) {
    // 1. Optimistic locking
    if (getCurrentVersion(aggregateId) != expectedVersion) {
        throw new ConcurrencyException();
    }
    
    // 2. Append eventos atomicamente
    for (Event event : events) {
        EventData eventData = new EventData(
            UUID.randomUUID(), aggregateId, 
            event.getClass().getSimpleName(),
            JsonUtils.serialize(event), Instant.now()
        );
        appendEvent(eventData);
    }
    
    // 3. Publica para message broker
    publishEvents(events);
}
```

### CQRS integration para queries eficientes

O Event Sourcing **requer CQRS** (Command Query Responsibility Segregation) para consultas eficientes.[Microservices.io](https://microservices.io/patterns/data/event-sourcing.html) Projections/read models são atualizadas quando eventos chegam: [Debézio +4](https://debezium.io/blog/2020/02/10/event-sourcing-vs-cdc/)

Java

```java
@EventHandler
public void on(OrderCreatedEvent event) {
    OrderView view = new OrderView();
    view.setOrderId(event.getOrderId());
    view.setCustomerId(event.getCustomerId());
    view.setStatus("CREATED");
    orderViewRepository.save(view);
}
```

### Benefícios únicos e custos operacionais

**Auditabilidade completa** - histórico permanente de todas mudanças com contexto completo. **Time travel** permite reconstruir estado em qualquer ponto temporal. [Microsoft Learn +2](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing) **Modelo de domínio rico** onde eventos representam intenções de negócio, não mudanças técnicas. **Múltiplas projeções** podem ser criadas independentemente para diferentes casos de uso. [Microsoft Learn +2](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)

**Complexidade significativa** requer expertise em DDD (Domain-Driven Design) e CQRS. **Learning curve** é alta - paradigma não familiar para maioria das equipes. [Debézio](https://debezium.io/blog/2020/02/10/event-sourcing-vs-cdc/) **Todas as projeções são eventually consistent**, eliminando strong consistency local. [Microsoft Learn +3](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing) **Versionamento de eventos** é complexo conforme sistema evolui. **Performance de rebuild** pode ser lenta para aggregates com milhares de eventos. [Microsoft Learn](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)[Microservices.io](https://microservices.io/patterns/data/event-sourcing.html)

### Pilha de tecnologia especializada

**Axon Framework** domina ecosistema Java com **Axon Server** como event store dedicado. [Baeldung +2](https://www.baeldung.com/axon-cqrs-event-sourcing) **.NET** usa **Kurrent (anteriormente EventStore)** como solução nativa. **Kurrent Cloud** oferece versão managed em AWS, Azure, GCP. [Loja de eventos](https://www.eventstore.com/event-store-cloud)[Documentação atual](https://docs.kurrent.io/cloud/introduction)

**Implementações alternativas** usam databases convencionais: PostgreSQL com schema simples, MongoDB para flexibilidade, Cassandra para alta escala (usado pela Netflix), [InfoQ](https://www.infoq.com/presentations/netflix-scale-event-sourcing/)DynamoDB para ambientes AWS.[despesa](https://www.infoq.com/presentations/netflix-scale-event-sourcing/) **Apache Kafka** pode ser usado como event store com limitações. [Médio +3](https://medium.com/digitalfrontiers/the-good-the-bad-and-the-ugly-how-to-choose-an-event-store-f1f2a3b70b2d)

**Use Event Sourcing** apenas quando auditabilidade é crítica (sistemas financeiros, healthcare, legal), análise temporal é necessária (fraud detection, analytics), ou domínio tem eventos naturais (workflow management, order processing). [Microsoft Learn +3](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)

## Framework de decisão arquitetural: escolhendo o padrão certo

A escolha entre padrões deve seguir uma árvore de decisão clara baseada em constraints técnicos e organizacionais. [Estouro de pilha](https://stackoverflow.com/questions/72970534/transactional-outbox-pattern-vs-event-sourcing) **Primeiro avalie possibilidade de modificar código** - se não for possível, CDC é única opção. **Segundo, analise tolerância à consistência eventual** - baixa tolerância indica Transactional Outbox.

### Matriz comparativa detalhada

|Critério|CDC|Ouça a si mesmo|Caixa de saída transacional|Sourcing de eventos|
|---|---|---|---|---|
|**Latência**|50-200 ms|<10 ms|100-500 ms|Variável|
|**Taxa de transferência**|Muito Alto|Muito Alto|Alto|Alto|
|**Consistência**|Eventual|Extrema Eventual|Forte → Eventual|Eventual|
|**Depuração**|Difícil|Muito Difícil|Fácil|Médio|
|**Maturidade**|Alto|Baixa|Alto|Média|
|**Curva de Aprendizagem**|Média|Baixa|Baixa|Alto|
|**Trilha de auditoria**|Limitado|Não|Tentar|Completo|
|**Overhead Operacional**|Alto|Baixo|Médio|Alto|

### Recomendações por contexto

**Sistemas legados** → CDC para integração sem modificar código existente [chapéu vermelho](https://developers.redhat.com/articles/2021/09/21/distributed-transaction-patterns-microservices-compared) **Aplicações críticas** → Transactional Outbox para balance ideal entre simplicidade e confiabilidade [Estouro de pilha](https://stackoverflow.com/questions/72970534/transactional-outbox-pattern-vs-event-sourcing)  
**Ultra-baixa latência** → Listen to Yourself apenas se consistência eventual é aceitável **Compliance rigoroso** → Event Sourcing quando audit trail é requisito legal [Lógica Scott](https://blog.scottlogic.com/2025/09/08/solving-data-consistency-in-distributed-systems-with-the-transactional-outbox.html) **Greenfield projects** → Transactional Outbox como padrão seguro, evoluir para Event Sourcing apenas se necessário [Estouro de pilha](https://stackoverflow.com/questions/72970534/transactional-outbox-pattern-vs-event-sourcing)

### Estratégias de migração evolutiva

**Path conservador**: CDC (sem mudança código) → Transactional Outbox (controle sobre eventos) → Event Sourcing (se auditabilidade crítica). **Path agressivo**: Transactional Outbox para MVP → Event Sourcing para domínios específicos.

**Anti-padrão**: nunca migrar diretamente para Event Sourcing sem experiência prévia. [Microservices.io](https://microservices.io/patterns/data/event-sourcing.html) Listen to Yourself não deve ser padrão default - é muito específico para casos de ultra-baixa latência.

## Considerações de performance, complexidade e consistência

**As classificações de desempenho** variam por aspecto: **latência de gravação** (Listen to Yourself > Event Sourcing > CDC > Outbox), **latência de leitura** (Outbox > CDC > Listen to Yourself > Event Sourcing), **throughput** (Event Sourcing > Listen to Yourself > CDC > Outbox). **O Event Sourcing** favorece gravações com operações somente de anexação, mas consultas complexas exigem projeções otimizadas.[Microservices.io +2](https://microservices.io/patterns/data/event-sourcing.html)

**Complexidade operacional** é crítica para sustentabilidade. **Transactional Outbox** oferece melhor balance - padrões estabelecidos, ferramentas maduras, debugging direto. **CDC** requer expertise em ferramentas especializadas mas minimal application impact. **Event Sourcing** demands deep domain expertise e infraestrutura sofisticada. [chapéu vermelho](https://developers.redhat.com/articles/2021/09/21/distributed-transaction-patterns-microservices-compared)

**Trade-offs de consistência** definem user experience. **Strong consistency** (Outbox) permite "read your own writes" mas introduz latência. **Eventual consistency** (CDC, Listen to Yourself, Event Sourcing) melhora performance mas complica client logic. **Choose based on domain requirements**, não preferências técnicas.

## Conclusão: começar simples, evoluir inteligentemente

**Transactional Outbox** emerge como escolha mais equilibrada para maioria dos casos - **combina simplicidade de implementação com garantias sólidas de consistência**. [Debézio](https://debezium.io/blog/2020/02/10/event-sourcing-vs-cdc/) Padrão maduro, ferramentas estabelecidas, debugging direto, e suporte amplo para diferentes linguagens e cloud providers. [Debézio +2](https://debezium.io/blog/2020/02/10/event-sourcing-vs-cdc/)

**CDC** resolve problemas específicos de integração legacy sem modificar aplicações existentes. [chapéu vermelho](https://developers.redhat.com/articles/2021/09/21/distributed-transaction-patterns-microservices-compared) **Listen to Yourself** atende nichos de ultra-baixa latência onde eventual consistency é aceitável. **Event Sourcing** justifica-se apenas quando auditabilidade completa é requisito crítico do domínio. [Debézio](https://debezium.io/blog/2020/02/10/event-sourcing-vs-cdc/)[Lógica Scott](https://blog.scottlogic.com/2025/09/08/solving-data-consistency-in-distributed-systems-with-the-transactional-outbox.html)

**Estratégia recomendada**: implementar **Transactional Outbox para novos microserviços**, usar **CDC para sistemas legados**, considerar **Event Sourcing apenas para bounded contexts específicos** com necessidades claras de audit trail. [Lógica Scott](https://blog.scottlogic.com/2025/09/08/solving-data-consistency-in-distributed-systems-with-the-transactional-outbox.html) Evitar **Listen to Yourself** como padrão - reservar para casos explícitos de performance crítica.

**Lembre-se**: resolver dual write é sobre garantir consistência entre dados locais e eventos distribuídos. [Confluente](https://developer.confluent.io/courses/microservices/the-dual-write-problem/)[Microservices.io](https://microservices.io/patterns/data/transactional-outbox.html) **Escolha o padrão que sua equipe consegue implementar, manter e debuggar efetivamente**, não necessariamente o mais sofisticado. **Simplicidade operacional** supera elegância arquitetural na maioria dos contextos empresariais.