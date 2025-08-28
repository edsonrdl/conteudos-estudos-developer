# Escopos de Beans no Spring Framework: Guia Especialista Completo

O gerenciamento de escopos de beans representa um dos aspectos mais fundamentais e críticos do Spring Framework, determinando diretamente o ciclo de vida, performance, segurança e arquitetura de aplicações empresariais. Este guia aborda desde conceitos fundamentais até cenários avançados de uso, fornecendo insights práticos para implementações robustas e escaláveis.

## Fundamentos dos escopos e características técnicas

O Spring Framework suporta **seis escopos principais** de beans, cada um com características específicas de ciclo de vida e gerenciamento de memória. Os escopos Singleton e Prototype estão disponíveis em qualquer ApplicationContext, enquanto Request, Session, Application e WebSocket requerem contexto web-aware.

### Singleton: o padrão otimizado para performance

O escopo **Singleton** representa o comportamento padrão do Spring, criando uma única instância compartilhada por container IoC. Diferentemente do padrão Singleton clássico do GoF, a singularidade aqui é definida por container, não por ClassLoader.

```java
@Component
@Scope("singleton") // ou omitir para usar o padrão
public class UserService {
    
    private final UserRepository repository;
    private final ValidationService validator;
    
    public UserService(UserRepository repository, ValidationService validator) {
        this.repository = repository;
        this.validator = validator;
    }
    
    public User createUser(UserRequest request) {
        // Bean stateless - thread-safe por design
        validator.validate(request);
        return repository.save(new User(request.getName(), request.getEmail()));
    }
}
```

**Características de memória e performance**: A instância única é armazenada no DefaultSingletonBeanRegistry usando mapas concorrentes, permanecendo na memória heap com referências fortes mantidas pelo container até o shutdown do ApplicationContext. Oferece a maior eficiência de memória e performance, com zero overhead de garbage collection após inicialização.

**Thread safety**: Beans singleton **não são inerentemente thread-safe**. Múltiplas threads acessam a mesma instância simultaneamente, exigindo design stateless ou gerenciamento cuidadoso de estado compartilhado.

### Prototype: novas instâncias para cada requisição

O escopo **Prototype** cria uma nova instância a cada chamada getBean() ou injeção de dependência, oferecendo isolamento completo entre usos.

```java
@Component
@Scope("prototype")
public class ReportGenerator {
    
    private String reportId = UUID.randomUUID().toString();
    private List<String> reportData = new ArrayList<>();
    private LocalDateTime createdAt = LocalDateTime.now();
    
    public void addData(String data) {
        reportData.add(data);
    }
    
    public Report generateReport() {
        return new Report(reportId, new ArrayList<>(reportData), createdAt);
    }
    
    // IMPORTANTE: Spring NÃO chama @PreDestroy em beans prototype
}
```

**Limitação crítica do prototype**: Quando injetado em beans singleton, apenas uma instância é criada no momento da injeção. Para resolver isso, use `@Lookup`, `proxyMode = ScopedProxyMode.TARGET_CLASS` ou injete o ApplicationContext.

```java
@Service
public class SingletonService {
    
    // Solução com @Lookup
    @Lookup
    protected ReportGenerator createReportGenerator() {
        return null; // Implementação será fornecida pelo Spring via CGLIB
    }
    
    public void processReports() {
        ReportGenerator generator = createReportGenerator(); // Nova instância
        generator.addData("Sample data");
        Report report = generator.generateReport();
    }
}
```

### Request scope: isolamento por requisição HTTP

O escopo **Request** cria uma nova instância para cada requisição HTTP, ideal para dados específicos do contexto de requisição.

```java
@Component
@RequestScope // Equivale a @Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestTracker {
    
    private final String requestId = UUID.randomUUID().toString();
    private final LocalDateTime startTime = LocalDateTime.now();
    private final Map<String, Object> requestAttributes = new HashMap<>();
    
    @PostConstruct
    public void init() {
        log.info("Iniciando processamento da requisição: {}", requestId);
    }
    
    @PreDestroy
    public void cleanup() {
        Duration duration = Duration.between(startTime, LocalDateTime.now());
        log.info("Requisição {} concluída em {}ms", requestId, duration.toMillis());
    }
    
    public void addAttribute(String key, Object value) {
        requestAttributes.put(key, value);
    }
}
```

### Session scope: estado persistente do usuário

O escopo **Session** mantém uma instância por sessão HTTP, perfeito para dados específicos do usuário que persistem através de múltiplas requisições.

```java
@Component
@SessionScope
public class UserSession implements Serializable {
    
    private static final long serialVersionUID = 1L;
    
    private String sessionId;
    private String userId;
    private UserPreferences preferences = new UserPreferences();
    private ShoppingCart cart = new ShoppingCart();
    private LocalDateTime loginTime;
    
    @PostConstruct
    public void init() {
        this.sessionId = UUID.randomUUID().toString();
        this.loginTime = LocalDateTime.now();
        log.info("Nova sessão criada: {}", sessionId);
    }
    
    // Thread safety: Sincronizar acesso a estado mutável
    public synchronized void addToCart(Product product) {
        cart.addProduct(product);
    }
    
    @PreDestroy
    public void cleanup() {
        log.info("Sessão {} finalizada após {}", 
            sessionId, Duration.between(loginTime, LocalDateTime.now()));
    }
}
```

### Application e WebSocket: casos especializados

**Application scope** cria uma instância por ServletContext, diferindo do singleton por ser específico da aplicação web e exposto como atributo ServletContext.

**WebSocket scope** gerencia uma instância por sessão WebSocket, ideal para aplicações real-time com estado por conexão.

```java
@Component
@ApplicationScope
public class GlobalConfiguration {
    
    private final Properties globalSettings = new Properties();
    private final AtomicLong requestCounter = new AtomicLong(0);
    
    @PostConstruct
    public void loadConfiguration() {
        // Carrega configurações globais da aplicação
        globalSettings.load(getClass().getResourceAsStream("/app.properties"));
    }
    
    public void incrementRequestCount() {
        requestCounter.incrementAndGet();
    }
}

@Component
@Scope(scopeName = "websocket", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class WebSocketSessionData {
    
    private String connectionId;
    private Set<String> subscribedChannels = new ConcurrentHashMap<>().keySet();
    
    @PostConstruct
    public void onConnect() {
        connectionId = UUID.randomUUID().toString();
        log.info("WebSocket session initiated: {}", connectionId);
    }
    
    @PreDestroy
    public void onDisconnect() {
        subscribedChannels.clear();
        log.info("WebSocket session terminated: {}", connectionId);
    }
}
```

## Configuração avançada e padrões de integração

### Anotações vs XML vs Java Config: escolha estratégica

A **configuração por anotações** representa a abordagem moderna recomendada, oferecendo melhor legibilidade e integração com IDEs. Para aplicações Spring Boot, use anotações especializadas como `@RequestScope`, `@SessionScope` e `@ApplicationScope`.

```java
// Configuração Java moderna
@Configuration
@EnableWebMvc
public class ScopeConfiguration {
    
    @Bean
    @Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
    public AuditContext auditContext() {
        return new AuditContext();
    }
    
    @Bean
    @SessionScope
    public UserDashboard userDashboard() {
        return new UserDashboard();
    }
    
    @Bean
    @Scope("prototype")
    public EmailNotification emailNotification() {
        return new EmailNotification();
    }
}
```

**XML ainda é relevante** para sistemas legados e configurações complexas que requerem namespaces específicos:

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:aop="http://www.springframework.org/schema/aop">
    
    <bean id="requestBean" class="com.example.RequestScopedService" 
          scope="request">
        <aop:scoped-proxy/>
    </bean>
    
    <bean id="sessionBean" class="com.example.SessionScopedService" 
          scope="session">
        <aop:scoped-proxy/>
    </bean>
</beans>
```

### Spring Boot: auto-configuração inteligente

O Spring Boot configura automaticamente os escopos web quando `spring-boot-starter-web` está presente, eliminando a necessidade de configuração manual de listeners ou filtros.

```properties
# application.yml - configurações de sessão
server.servlet.session.timeout=30m
server.servlet.session.cookie.http-only=true
server.servlet.session.cookie.secure=true
server.servlet.session.cookie.max-age=1800

# Tipo de aplicação
spring.main.web-application-type=servlet # ou reactive, none
```

### Integração com diferentes tipos de aplicação

**Aplicações web** se beneficiam de todos os escopos, especialmente Request e Session para gerenciamento de estado do usuário:

```java
@RestController
@RequestMapping("/api/orders")
public class OrderController {
    
    private final OrderService orderService; // Singleton
    private final AuditLogger auditLogger;   // Request-scoped
    private final UserSession userSession;   // Session-scoped
    
    @PostMapping
    public ResponseEntity<Order> createOrder(@RequestBody OrderRequest request) {
        auditLogger.log("Criando pedido para usuário: " + userSession.getUserId());
        
        Order order = orderService.createOrder(request, userSession.getUserId());
        userSession.addRecentOrder(order.getId());
        
        return ResponseEntity.ok(order);
    }
}
```

**Aplicações batch** utilizam principalmente Singleton e JobScope/StepScope específicos do Spring Batch:

```java
@Configuration
@EnableBatchProcessing
public class BatchConfiguration {
    
    @Bean
    @JobScope
    public FlatFileItemReader<Customer> reader(
            @Value("#{jobParameters['inputFile']}") String inputFile) {
        return new FlatFileItemReaderBuilder<Customer>()
            .name("customerReader")
            .resource(new FileSystemResource(inputFile))
            .delimited()
            .names(new String[]{"firstName", "lastName", "email"})
            .targetType(Customer.class)
            .build();
    }
    
    @Bean
    @StepScope
    public ItemProcessor<Customer, ProcessedCustomer> processor() {
        return customer -> {
            // Processa cada item com nova instância por step
            return new ProcessedCustomer(customer);
        };
    }
}
```

**Aplicações standalone** devem excluir configurações web e usar apenas Singleton e Prototype:

```java
@SpringBootApplication(exclude = {
    WebMvcAutoConfiguration.class,
    DispatcherServletAutoConfiguration.class
})
public class StandaloneApp {
    
    public static void main(String[] args) {
        ConfigurableApplicationContext context = 
            new SpringApplicationBuilder(StandaloneApp.class)
                .web(WebApplicationType.NONE)
                .run(args);
        
        ProcessingService service = context.getBean(ProcessingService.class);
        service.executeProcessing();
        context.close();
    }
}
```

## Performance, segurança e considerações de produção

### Características de performance por escopo

A escolha do escopo impacta diretamente na performance e uso de memória da aplicação:

|Escopo|Uso de Memória|Performance|Impacto GC|Casos de Uso|
|---|---|---|---|---|
|Singleton|Baixo|Alto|Baixo|Serviços stateless, repositórios|
|Prototype|Alto|Baixo|Alto|Objetos stateful de curta duração|
|Request|Médio|Médio|Médio|Contexto de requisição HTTP|
|Session|Médio-Alto|Médio|Baixo-Médio|Estado do usuário|
|Application|Baixo|Alto|Baixo|Configurações globais|

### Gerenciamento de memória e prevenção de vazamentos

**Patterns críticos de vazamento de memória**:

```java
// ANTI-PATTERN: Coleção crescente em singleton
@Service
public class CacheService {
    private final Map<String, Object> cache = new HashMap<>(); // PERIGO!
    
    // Solução: usar WeakHashMap ou implementar estratégia de limpeza
    private final Map<String, WeakReference<Object>> cache = 
        new ConcurrentHashMap<>();
}

// PATTERN correto: Limpeza automática
@Component
@RequestScope
public class RequestDataHolder {
    private final Map<String, Object> data = new HashMap<>();
    
    @PreDestroy
    public void cleanup() {
        data.clear();
        log.debug("Request data cleared for scope cleanup");
    }
}
```

### Estratégias de otimização de performance

**Configuração JVM otimizada para aplicações Spring**:

```bash
JAVA_OPTS="-server -Xmx2g -Xms2g 
           -XX:+UseG1GC 
           -XX:MaxGCPauseMillis=200 
           -XX:+HeapDumpOnOutOfMemoryError 
           -XX:HeapDumpPath=/var/logs/heapdumps/"
```

**Monitoramento com Spring Boot Actuator**:

```java
@Component
public class ScopeMetrics {
    
    private final MeterRegistry meterRegistry;
    private final Counter prototypeCreations;
    private final Timer requestProcessingTime;
    
    public ScopeMetrics(MeterRegistry meterRegistry) {
        this.meterRegistry = meterRegistry;
        this.prototypeCreations = Counter.builder("prototype.creations")
            .description("Number of prototype beans created")
            .register(meterRegistry);
        this.requestProcessingTime = Timer.builder("request.processing.time")
            .description("Request processing duration")
            .register(meterRegistry);
    }
}
```

### Considerações de segurança para escopos web

**Session scope security patterns**:

```java
@Component
@SessionScope
public class SecureUserSession implements Serializable {
    
    private String encryptedUserId;
    private Set<String> permissions;
    private LocalDateTime lastActivity;
    
    // Validação automática de sessão
    @PostConstruct
    public void validateSession() {
        if (isSessionExpired()) {
            invalidateSession();
        }
    }
    
    public void updateActivity() {
        this.lastActivity = LocalDateTime.now();
    }
    
    private boolean isSessionExpired() {
        return lastActivity != null && 
               Duration.between(lastActivity, LocalDateTime.now())
                       .toMinutes() > 30;
    }
}
```

**Request scope para audit e compliance**:

```java
@Component
@RequestScope
public class ComplianceAuditContext {
    
    private final String requestId = UUID.randomUUID().toString();
    private final List<AuditEvent> auditTrail = new ArrayList<>();
    private final String userAgent;
    private final String clientIP;
    
    public ComplianceAuditContext(HttpServletRequest request) {
        this.userAgent = request.getHeader("User-Agent");
        this.clientIP = getClientIP(request);
    }
    
    public void recordAction(String action, Object data) {
        auditTrail.add(new AuditEvent(action, data, LocalDateTime.now()));
    }
    
    @PreDestroy
    public void persistAuditTrail() {
        // Persiste trilha de auditoria no final da requisição
        auditService.persistTrail(requestId, auditTrail);
    }
}
```

## Cenários avançados e resolução de problemas

### Problemas comuns e suas soluções

**Dependências circulares com escopos diferentes**:

```java
// Problema: Dependência circular com AOP
@Service
@Transactional
public class UserService {
    @Autowired private OrderService orderService; // Circular!
}

@Service  
@Transactional
public class OrderService {
    @Autowired private UserService userService; // Circular!
}

// Solução: @Lazy quebra o ciclo
@Service
@Transactional
public class UserService {
    @Lazy @Autowired private OrderService orderService;
}
```

**Acesso a beans web-scoped fora do contexto web**:

```java
// Configuração para testes
@TestConfiguration
public class TestScopeConfig {
    
    @Bean
    public CustomScopeConfigurer scopeConfigurer() {
        CustomScopeConfigurer configurer = new CustomScopeConfigurer();
        // Substitui session scope por thread scope em testes
        configurer.addScope("session", new SimpleThreadScope());
        return configurer;
    }
}
```

### Implementação de escopos customizados

Para cenários específicos, implemente escopos personalizados:

```java
public class TenantScope implements Scope {
    
    private final Map<String, Object> scopedObjects = 
        Collections.synchronizedMap(new HashMap<>());
    
    @Override
    public Object get(String name, ObjectFactory<?> objectFactory) {
        String tenantId = TenantContext.getCurrentTenant();
        String key = tenantId + "_" + name;
        
        return scopedObjects.computeIfAbsent(key, k -> {
            log.debug("Creating scoped object {} for tenant {}", name, tenantId);
            return objectFactory.getObject();
        });
    }
    
    @Override
    public Object remove(String name) {
        String tenantId = TenantContext.getCurrentTenant();
        String key = tenantId + "_" + name;
        return scopedObjects.remove(key);
    }
    
    @Override
    public void registerDestructionCallback(String name, Runnable callback) {
        // Implementar callbacks de destruição se necessário
    }
    
    @Override
    public String getConversationId() {
        return TenantContext.getCurrentTenant();
    }
}

// Registrar o escopo customizado
@Configuration
public class CustomScopeConfiguration {
    
    @Bean
    public static CustomScopeConfigurer tenantScopeConfigurer() {
        CustomScopeConfigurer configurer = new CustomScopeConfigurer();
        configurer.addScope("tenant", new TenantScope());
        return configurer;
    }
}
```

### Estratégias de teste para beans scoped

**Testes de integração para escopos web**:

```java
@SpringJUnitWebConfig
@TestMethodOrder(OrderAnnotation.class)
class WebScopeIntegrationTest {
    
    @Autowired private TestRestTemplate restTemplate;
    
    @Test
    @Order(1)
    void shouldCreateNewSessionScopedBean() {
        ResponseEntity<SessionInfo> response = restTemplate.getForEntity(
            "/api/session/info", SessionInfo.class);
        
        assertThat(response.getBody().getSessionId()).isNotNull();
    }
    
    @Test
    @Order(2)
    void shouldPreserveSessionAcrossRequests() {
        // Usa HttpEntity para manter cookies de sessão
        HttpHeaders headers = new HttpHeaders();
        headers.add("Cookie", extractSessionCookie());
        HttpEntity<String> entity = new HttpEntity<>(headers);
        
        ResponseEntity<SessionInfo> response = restTemplate.exchange(
            "/api/session/info", HttpMethod.GET, entity, SessionInfo.class);
        
        // Validar que é a mesma sessão
        assertThat(response.getBody().getSessionId())
            .isEqualTo(previousSessionId);
    }
}
```

## Melhores práticas arquiteturais

### Diretrizes para seleção de escopo

**Decision tree para seleção de escopo**:

1. **O componente mantém estado?**
    
    - Não → Singleton
    - Sim → Continue
2. **O estado é compartilhado entre usuários?**
    
    - Sim → Application ou Singleton (com thread-safety)
    - Não → Continue
3. **O estado persiste entre requisições?**
    
    - Sim → Session
    - Não → Continue
4. **O estado é específico da requisição?**
    
    - Sim → Request
    - Não → Prototype

### Padrões arquiteturais recomendados

**Layered architecture com scopes apropriados**:

```java
// Camada de apresentação - Request scope para contexto
@RestController
public class ProductController {
    private final ProductService service;        // Singleton
    private final RequestContext context;        // Request
    private final UserSession userSession;       // Session
}

// Camada de serviço - Singleton stateless
@Service
@Transactional
public class ProductService {
    private final ProductRepository repository;  // Singleton
    private final NotificationService notifications; // Singleton
}

// Camada de dados - Singleton 
@Repository
public class ProductRepository {
    private final EntityManager entityManager;   // Singleton proxy
}

// Objetos de dados - Prototype quando stateful
@Component
@Scope("prototype")
public class ProductSearchCriteria {
    private String category;
    private PriceRange priceRange;
    private List<String> tags = new ArrayList<>();
}
```

### Considerações para microsserviços

Em arquiteturas de microsserviços, **prefira singletons stateless** para facilitar a escalabilidade horizontal:

```java
@Service
public class CustomerService {
    
    private final CustomerRepository repository;
    private final EventPublisher eventPublisher;
    
    // Método stateless - thread-safe por design
    public Customer updateCustomer(Long customerId, CustomerUpdate update) {
        Customer customer = repository.findById(customerId)
            .orElseThrow(() -> new CustomerNotFoundException(customerId));
        
        customer.applyUpdate(update);
        Customer saved = repository.save(customer);
        
        eventPublisher.publishEvent(new CustomerUpdatedEvent(saved));
        return saved;
    }
}
```

## Conclusão e recomendações estratégicas

O domínio dos escopos de beans no Spring Framework é essencial para construir aplicações robustas, performáticas e seguras. **As decisões de escopo impactam diretamente a arquitetura, performance e maintainability** de sistemas empresariais.

**Princípios fundamentais para aplicação**:

1. **Prefira singleton stateless** como padrão para máxima eficiência
2. **Use prototype com parcimônia** devido ao overhead de criação
3. **Implemente monitoring** de criação de beans e uso de memória
4. **Aplique security hardening** para escopos web-aware
5. **Teste cenários de concorrência** específicos para cada escopo
6. **Documente decisões não-convencionais** de escopo na arquitetura
7. **Monitore e profile continuamente** em ambientes produtivos

A seleção cuidadosa e implementação consciente dos escopos de beans possibilita a construção de aplicações Spring que escalam eficientemente, mantêm alta performance e atendem aos rigorosos requisitos de segurança e compliance empresarial.