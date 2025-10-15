## O que é Event-Driven Communication?

Event-Driven Communication é um padrão arquitetural onde componentes do sistema se comunicam através de **eventos** em vez de chamadas diretas. Quando algo importante acontece (um evento), o sistema publica esse evento e outros componentes interessados podem reagir a ele, sem que o publicador precise conhecer os consumidores.

## Conceitos Fundamentais

- **Event (Evento)**: Representa algo que aconteceu no sistema (ex: "PedidoCriado", "PagamentoAprovado")
- **Publisher (Publicador)**: Quem emite/publica o evento
- **Listener/Subscriber (Ouvinte)**: Quem recebe e reage ao evento
- **Event Bus**: Canal de comunicação entre publicadores e ouvintes

---

## ✅ Vantagens

1. **Desacoplamento**: Componentes não precisam conhecer uns aos outros
2. **Escalabilidade**: Fácil adicionar novos ouvintes sem modificar código existente
3. **Manutenibilidade**: Mudanças isoladas em cada componente
4. **Reatividade**: Sistema responde a mudanças em tempo real
5. **Auditoria**: Histórico de eventos facilita rastreamento
6. **Resiliência**: Falhas em um ouvinte não afetam outros
7. **Testabilidade**: Componentes podem ser testados isoladamente

## ❌ Desvantagens

1. **Complexidade**: Fluxo de execução mais difícil de rastrear
2. **Debugging**: Harder to debug (async nature)
3. **Consistência Eventual**: Dados podem ficar temporariamente inconsistentes
4. **Overhead**: Pode ser excessivo para aplicações simples
5. **Ordenação**: Garantir ordem de processamento pode ser desafiador
6. **Monitoramento**: Requer ferramentas específicas para observabilidade
7. **Duplicação**: Eventos podem ser processados múltiplas vezes (idempotência necessária)

---

## 📝 Exemplo Simples

java

```java
// 1. Definir o Evento
public class PedidoCriadoEvent {
    private final Long pedidoId;
    private final String cliente;
    private final BigDecimal valor;
    private final LocalDateTime dataCriacao;

    public PedidoCriadoEvent(Long pedidoId, String cliente, BigDecimal valor) {
        this.pedidoId = pedidoId;
        this.cliente = cliente;
        this.valor = valor;
        this.dataCriacao = LocalDateTime.now();
    }

    // Getters
    public Long getPedidoId() { return pedidoId; }
    public String getCliente() { return cliente; }
    public BigDecimal getValor() { return valor; }
    public LocalDateTime getDataCriacao() { return dataCriacao; }
}

// 2. Publicar o Evento
@Service
public class PedidoService {
    
    @Autowired
    private ApplicationEventPublisher eventPublisher;
    
    @Autowired
    private PedidoRepository pedidoRepository;
    
    public Pedido criarPedido(PedidoDTO pedidoDTO) {
        // Salvar pedido
        Pedido pedido = new Pedido();
        pedido.setCliente(pedidoDTO.getCliente());
        pedido.setValor(pedidoDTO.getValor());
        pedido = pedidoRepository.save(pedido);
        
        // Publicar evento
        PedidoCriadoEvent evento = new PedidoCriadoEvent(
            pedido.getId(),
            pedido.getCliente(),
            pedido.getValor()
        );
        eventPublisher.publishEvent(evento);
        
        return pedido;
    }
}

// 3. Ouvir o Evento
@Component
public class EmailListener {
    
    @Async
    @EventListener
    public void enviarEmailConfirmacao(PedidoCriadoEvent evento) {
        System.out.println("Enviando email para: " + evento.getCliente());
        System.out.println("Pedido #" + evento.getPedidoId() + " confirmado!");
        // Lógica de envio de email
    }
}

@Component
public class NotificacaoListener {
    
    @Async
    @EventListener
    public void enviarNotificacao(PedidoCriadoEvent evento) {
        System.out.println("Enviando notificação push para: " + evento.getCliente());
        // Lógica de notificação
    }
}

@Component
public class EstoqueListener {
    
    @Async
    @EventListener
    @Transactional
    public void atualizarEstoque(PedidoCriadoEvent evento) {
        System.out.println("Atualizando estoque para pedido: " + evento.getPedidoId());
        // Lógica de atualização de estoque
    }
}

// 4. Configuração para processamento assíncrono
@Configuration
@EnableAsync
public class AsyncConfig {
    
    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("event-");
        executor.initialize();
        return executor;
    }
}
```

---

## 🏭 Exemplo Real de Produção (Nível Especialista)

java

```java
// ============================================
// 1. EVENTO BASE COM METADADOS
// ============================================
@Getter
public abstract class DomainEvent {
    private final String eventId;
    private final String eventType;
    private final LocalDateTime occurredOn;
    private final String aggregateId;
    private final Integer version;
    
    protected DomainEvent(String aggregateId) {
        this.eventId = UUID.randomUUID().toString();
        this.eventType = this.getClass().getSimpleName();
        this.occurredOn = LocalDateTime.now();
        this.aggregateId = aggregateId;
        this.version = 1;
    }
}

// ============================================
// 2. EVENTO DE DOMÍNIO ESPECÍFICO
// ============================================
@Getter
public class PedidoFinalizadoEvent extends DomainEvent {
    private final Long pedidoId;
    private final String clienteId;
    private final BigDecimal valorTotal;
    private final List<ItemPedido> itens;
    private final String metodoPagamento;
    private final EnderecoEntrega endereco;
    
    public PedidoFinalizadoEvent(Pedido pedido) {
        super(pedido.getId().toString());
        this.pedidoId = pedido.getId();
        this.clienteId = pedido.getClienteId();
        this.valorTotal = pedido.getValorTotal();
        this.itens = new ArrayList<>(pedido.getItens());
        this.metodoPagamento = pedido.getMetodoPagamento();
        this.endereco = pedido.getEnderecoEntrega();
    }
}

// ============================================
// 3. EVENT STORE PARA PERSISTÊNCIA
// ============================================
@Entity
@Table(name = "event_store")
@Getter @Setter
public class StoredEvent {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String eventId;
    private String eventType;
    private String aggregateId;
    private LocalDateTime occurredOn;
    
    @Column(columnDefinition = "TEXT")
    private String payload;
    
    private boolean processed;
    private LocalDateTime processedAt;
    private Integer retryCount;
}

@Repository
public interface EventStoreRepository extends JpaRepository<StoredEvent, Long> {
    List<StoredEvent> findByProcessedFalseOrderByOccurredOnAsc();
    List<StoredEvent> findByAggregateIdOrderByOccurredOnAsc(String aggregateId);
}

// ============================================
// 4. SERVIÇO DE PUBLICAÇÃO COM TRANSAÇÃO
// ============================================
@Service
@Slf4j
public class EventPublisherService {
    
    private final ApplicationEventPublisher eventPublisher;
    private final EventStoreRepository eventStoreRepository;
    private final ObjectMapper objectMapper;
    
    @Autowired
    public EventPublisherService(
            ApplicationEventPublisher eventPublisher,
            EventStoreRepository eventStoreRepository,
            ObjectMapper objectMapper) {
        this.eventPublisher = eventPublisher;
        this.eventStoreRepository = eventStoreRepository;
        this.objectMapper = objectMapper;
    }
    
    @Transactional
    public void publish(DomainEvent event) {
        try {
            // 1. Persistir evento primeiro (Transactional Outbox Pattern)
            StoredEvent storedEvent = new StoredEvent();
            storedEvent.setEventId(event.getEventId());
            storedEvent.setEventType(event.getEventType());
            storedEvent.setAggregateId(event.getAggregateId());
            storedEvent.setOccurredOn(event.getOccurredOn());
            storedEvent.setPayload(objectMapper.writeValueAsString(event));
            storedEvent.setProcessed(false);
            storedEvent.setRetryCount(0);
            
            eventStoreRepository.save(storedEvent);
            
            // 2. Publicar evento no mesmo contexto transacional
            eventPublisher.publishEvent(event);
            
            log.info("Evento publicado e persistido: {}", event.getEventType());
            
        } catch (Exception e) {
            log.error("Erro ao publicar evento: {}", event.getEventType(), e);
            throw new EventPublicationException("Falha ao publicar evento", e);
        }
    }
}

// ============================================
// 5. LISTENER COM TRATAMENTO DE ERROS
// ============================================
@Component
@Slf4j
public class PedidoEventHandlers {
    
    private final EmailService emailService;
    private final NotificacaoService notificacaoService;
    private final EstoqueService estoqueService;
    private final EventStoreRepository eventStoreRepository;
    private final MetricsService metricsService;
    
    @Autowired
    public PedidoEventHandlers(
            EmailService emailService,
            NotificacaoService notificacaoService,
            EstoqueService estoqueService,
            EventStoreRepository eventStoreRepository,
            MetricsService metricsService) {
        this.emailService = emailService;
        this.notificacaoService = notificacaoService;
        this.estoqueService = estoqueService;
        this.eventStoreRepository = eventStoreRepository;
        this.metricsService = metricsService;
    }
    
    @Async
    @EventListener
    @Retryable(
        value = {TemporaryException.class},
        maxAttempts = 3,
        backoff = @Backoff(delay = 2000, multiplier = 2)
    )
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void handlePedidoFinalizado(PedidoFinalizadoEvent evento) {
        String eventId = evento.getEventId();
        Stopwatch stopwatch = Stopwatch.createStarted();
        
        try {
            log.info("Processando evento: {} - Pedido: {}", 
                    eventId, evento.getPedidoId());
            
            // Enviar email
            emailService.enviarConfirmacaoPedido(
                evento.getClienteId(),
                evento.getPedidoId(),
                evento.getValorTotal()
            );
            
            // Enviar notificação
            notificacaoService.notificarPedidoFinalizado(
                evento.getClienteId(),
                evento.getPedidoId()
            );
            
            // Atualizar estoque
            estoqueService.reservarItens(evento.getItens());
            
            // Marcar como processado
            markAsProcessed(eventId);
            
            // Métricas
            metricsService.recordEventProcessed(
                evento.getEventType(),
                stopwatch.elapsed(TimeUnit.MILLISECONDS)
            );
            
            log.info("Evento processado com sucesso: {}", eventId);
            
        } catch (Exception e) {
            log.error("Erro ao processar evento: {}", eventId, e);
            metricsService.recordEventFailed(evento.getEventType());
            throw new EventProcessingException("Falha no processamento", e);
        }
    }
    
    @Recover
    public void recover(EventProcessingException e, PedidoFinalizadoEvent evento) {
        log.error("Falha permanente ao processar evento após retries: {}", 
                evento.getEventId());
        // Enviar para Dead Letter Queue ou alertar equipe
        sendToDeadLetterQueue(evento, e);
    }
    
    private void markAsProcessed(String eventId) {
        eventStoreRepository.findByEventId(eventId).ifPresent(stored -> {
            stored.setProcessed(true);
            stored.setProcessedAt(LocalDateTime.now());
            eventStoreRepository.save(stored);
        });
    }
    
    private void sendToDeadLetterQueue(DomainEvent event, Exception e) {
        // Implementar lógica de DLQ
        log.error("Evento enviado para DLQ: {}", event.getEventId());
    }
}

// ============================================
// 6. SERVIÇO PRINCIPAL COM ORCHESTRATION
// ============================================
@Service
@Slf4j
public class PedidoService {
    
    private final PedidoRepository pedidoRepository;
    private final EventPublisherService eventPublisher;
    private final PagamentoService pagamentoService;
    
    @Autowired
    public PedidoService(
            PedidoRepository pedidoRepository,
            EventPublisherService eventPublisher,
            PagamentoService pagamentoService) {
        this.pedidoRepository = pedidoRepository;
        this.eventPublisher = eventPublisher;
        this.pagamentoService = pagamentoService;
    }
    
    @Transactional
    public Pedido finalizarPedido(Long pedidoId) {
        // 1. Buscar e validar pedido
        Pedido pedido = pedidoRepository.findById(pedidoId)
            .orElseThrow(() -> new PedidoNotFoundException(pedidoId));
        
        if (pedido.getStatus() != StatusPedido.PENDENTE) {
            throw new InvalidPedidoStatusException("Pedido não está pendente");
        }
        
        // 2. Processar pagamento
        ResultadoPagamento pagamento = pagamentoService.processar(pedido);
        
        if (!pagamento.isAprovado()) {
            throw new PagamentoRecusadoException("Pagamento não aprovado");
        }
        
        // 3. Atualizar status
        pedido.setStatus(StatusPedido.CONFIRMADO);
        pedido.setDataConfirmacao(LocalDateTime.now());
        pedido = pedidoRepository.save(pedido);
        
        // 4. Publicar evento (dentro da mesma transação)
        PedidoFinalizadoEvent evento = new PedidoFinalizadoEvent(pedido);
        eventPublisher.publish(evento);
        
        log.info("Pedido finalizado: {}", pedidoId);
        return pedido;
    }
}

// ============================================
// 7. PROCESSADOR DE EVENTOS PENDENTES (Outbox Pattern)
// ============================================
@Component
@Slf4j
public class PendingEventProcessor {
    
    private final EventStoreRepository eventStoreRepository;
    private final ApplicationEventPublisher eventPublisher;
    private final ObjectMapper objectMapper;
    
    @Autowired
    public PendingEventProcessor(
            EventStoreRepository eventStoreRepository,
            ApplicationEventPublisher eventPublisher,
            ObjectMapper objectMapper) {
        this.eventStoreRepository = eventStoreRepository;
        this.eventPublisher = eventPublisher;
        this.objectMapper = objectMapper;
    }
    
    @Scheduled(fixedDelay = 30000) // A cada 30 segundos
    @Transactional
    public void processarEventosPendentes() {
        List<StoredEvent> eventosPendentes = 
            eventStoreRepository.findByProcessedFalseOrderByOccurredOnAsc();
        
        if (eventosPendentes.isEmpty()) {
            return;
        }
        
        log.info("Processando {} eventos pendentes", eventosPendentes.size());
        
        for (StoredEvent stored : eventosPendentes) {
            try {
                // Recriar o evento do payload
                Class<?> eventClass = Class.forName(
                    "com.example.events." + stored.getEventType()
                );
                Object event = objectMapper.readValue(
                    stored.getPayload(), 
                    eventClass
                );
                
                // Republicar
                eventPublisher.publishEvent(event);
                
                // Atualizar retry count
                stored.setRetryCount(stored.getRetryCount() + 1);
                eventStoreRepository.save(stored);
                
            } catch (Exception e) {
                log.error("Erro ao reprocessar evento: {}", 
                        stored.getEventId(), e);
                
                // Após 5 tentativas, marcar como falha permanente
                if (stored.getRetryCount() >= 5) {
                    stored.setProcessed(true); // Evitar loop infinito
                    eventStoreRepository.save(stored);
                    // Alertar equipe
                }
            }
        }
    }
}

// ============================================
// 8. CONFIGURAÇÃO AVANÇADA
// ============================================
@Configuration
@EnableAsync
@EnableRetry
@EnableScheduling
public class EventDrivenConfig {
    
    @Bean
    public Executor eventExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);
        executor.setMaxPoolSize(50);
        executor.setQueueCapacity(500);
        executor.setThreadNamePrefix("event-handler-");
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        executor.setWaitForTasksToCompleteOnShutdown(true);
        executor.setAwaitTerminationSeconds(60);
        executor.initialize();
        return executor;
    }
    
    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        mapper.registerModule(new JavaTimeModule());
        mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        return mapper;
    }
}

// ============================================
// 9. EXCEPTION CUSTOMIZADAS
// ============================================
public class EventPublicationException extends RuntimeException {
    public EventPublicationException(String message, Throwable cause) {
        super(message, cause);
    }
}

public class EventProcessingException extends RuntimeException {
    public EventProcessingException(String message, Throwable cause) {
        super(message, cause);
    }
}

public class TemporaryException extends RuntimeException {
    public TemporaryException(String message) {
        super(message);
    }
}
```

---

## 🎯 Boas Práticas de Produção

1. **Transactional Outbox Pattern**: Persistir eventos na mesma transação do domínio
2. **Idempotência**: Listeners devem processar eventos múltiplas vezes sem efeitos colaterais
3. **Dead Letter Queue**: Para eventos que falharam após múltiplas tentativas
4. **Versionamento**: Eventos devem ter versão para evolução
5. **Monitoramento**: Métricas de eventos publicados/processados/falhados
6. **Retry Strategy**: Estratégia de retry com backoff exponencial
7. **Event Sourcing**: Considere guardar histórico completo de eventos
8. **Ordenação Garantida**: Use filas ordenadas quando necessário

## 📊 Quando Usar Event-Driven?

✅ **Use quando:**

- Sistema precisa escalar horizontalmente
- Múltiplos módulos precisam reagir a mudanças
- Processos assíncronos são aceitáveis
- Auditoria e rastreamento são importantes
- Microserviços precisam se comunicar

❌ **Evite quando:**

- Sistema é muito simples
- Consistência imediata é crítica
- Equipe não tem experiência
- Debugging complexo é inaceitável