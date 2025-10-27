![[Pasted image 20251027115450.png]]![[Pasted image 20251027115505.png]]

# Guia Completo de Arquitetura Hexagonal em Java

## 📌 Índice

1. [Introdução à Arquitetura Hexagonal](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#introdu%C3%A7%C3%A3o)
2. [Conceitos Fundamentais](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#conceitos-fundamentais)
3. [Estrutura das Camadas](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#estrutura-das-camadas)
4. [Implementação em Java](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#implementa%C3%A7%C3%A3o-em-java)
5. [Ports e Adapters](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#ports-e-adapters)
6. [Domain-Driven Design (DDD)](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#domain-driven-design)
7. [Dependency Injection](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#dependency-injection)
8. [Testes na Arquitetura Hexagonal](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#testes)
9. [Exemplos Práticos](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#exemplos-pr%C3%A1ticos)
10. [Boas Práticas](https://claude.ai/chat/06288c1e-07e3-45ed-be1b-0503ada0c149#boas-pr%C3%A1ticas)

## 🎯 Introdução

A **Arquitetura Hexagonal**, também conhecida como **Ports and Adapters**, foi criada por Alistair Cockburn em 2005. Seu objetivo principal é isolar a lógica de negócio (domínio) das preocupações técnicas e de infraestrutura.

### Principais Benefícios:

- **Isolamento do domínio**: Lógica de negócio independente de frameworks
- **Testabilidade**: Facilita testes unitários e de integração
- **Flexibilidade**: Troca fácil de tecnologias e frameworks
- **Manutenibilidade**: Código mais organizado e limpo
- **Escalabilidade**: Facilita evolução do sistema

## 🔧 Conceitos Fundamentais

### 1. O Hexágono

O hexágono representa o núcleo da aplicação (domínio). Os seis lados são apenas ilustrativos - você pode ter quantas portas (interfaces) precisar.

### 2. Fluxo de Dependências

```
Exterior → Adapters → Ports → Domain
         ←           ←       ←
```

As dependências sempre apontam para dentro (em direção ao domínio).

### 3. Princípio da Inversão de Dependência

O domínio define interfaces (ports) que são implementadas pelos adapters externos.

## 🏗️ Estrutura das Camadas

### 1. **Core (Domínio)**

#### Entities (Entidades)

```java
// Entidade do Domínio
public class Product {
    private ProductId id;
    private String name;
    private Money price;
    private ProductStatus status;
    
    // Regras de negócio encapsuladas
    public void activate() {
        if (this.price.isLessThanOrEqualTo(Money.ZERO)) {
            throw new InvalidProductException("Product price must be positive");
        }
        this.status = ProductStatus.ACTIVE;
    }
    
    public void applyDiscount(Percentage discount) {
        validateDiscount(discount);
        this.price = this.price.multiply(1 - discount.getValue());
    }
}
```

#### Value Objects

```java
// Value Object imutável
public final class Money {
    private final BigDecimal amount;
    private final Currency currency;
    
    public Money(BigDecimal amount, Currency currency) {
        validateAmount(amount);
        this.amount = amount;
        this.currency = currency;
    }
    
    public Money add(Money other) {
        validateSameCurrency(other);
        return new Money(
            this.amount.add(other.amount), 
            this.currency
        );
    }
    
    // equals, hashCode, toString...
}
```

#### Domain Services

```java
// Serviço de Domínio
public class PricingService {
    public Money calculateFinalPrice(
        Product product, 
        Customer customer, 
        List<Promotion> activePromotions
    ) {
        Money basePrice = product.getPrice();
        
        // Lógica de negócio complexa
        Money discountedPrice = applyCustomerDiscount(basePrice, customer);
        Money finalPrice = applyPromotions(discountedPrice, activePromotions);
        
        return finalPrice;
    }
}
```

#### Application Services

```java
// Serviço de Aplicação (Use Case)
@Service
public class CreateProductUseCase {
    private final ProductRepository productRepository;
    private final EventPublisher eventPublisher;
    
    public CreateProductUseCase(
        ProductRepository productRepository,
        EventPublisher eventPublisher
    ) {
        this.productRepository = productRepository;
        this.eventPublisher = eventPublisher;
    }
    
    @Transactional
    public ProductId execute(CreateProductCommand command) {
        // Validação
        validateCommand(command);
        
        // Criação da entidade
        Product product = Product.create(
            command.getName(),
            command.getPrice(),
            command.getCategory()
        );
        
        // Persistência
        Product savedProduct = productRepository.save(product);
        
        // Publicação de evento
        eventPublisher.publish(
            new ProductCreatedEvent(savedProduct.getId())
        );
        
        return savedProduct.getId();
    }
}
```

### 2. **Ports (Interfaces)**

#### Inbound Ports (Primary/Driving)

```java
// Port de entrada - Define o que a aplicação pode fazer
public interface ProductManagement {
    ProductId createProduct(CreateProductCommand command);
    void updateProduct(UpdateProductCommand command);
    void deleteProduct(ProductId id);
    ProductDTO findProduct(ProductId id);
    List<ProductDTO> searchProducts(SearchCriteria criteria);
}
```

#### Outbound Ports (Secondary/Driven)

```java
// Port de saída - Repository
public interface ProductRepository {
    Product save(Product product);
    Optional<Product> findById(ProductId id);
    List<Product> findByCategory(Category category);
    void delete(ProductId id);
}

// Port de saída - External Service
public interface PaymentGateway {
    PaymentResult processPayment(PaymentRequest request);
    void refund(TransactionId transactionId);
}

// Port de saída - Event Publisher
public interface EventPublisher {
    void publish(DomainEvent event);
}
```

### 3. **Adapters**

#### Primary Adapters (Interface Layer)

**REST Controller Adapter**

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {
    private final ProductManagement productManagement;
    
    @PostMapping
    public ResponseEntity<ProductResponse> createProduct(
        @Valid @RequestBody CreateProductRequest request
    ) {
        CreateProductCommand command = CreateProductCommand.builder()
            .name(request.getName())
            .price(Money.of(request.getPrice(), request.getCurrency()))
            .category(Category.valueOf(request.getCategory()))
            .build();
            
        ProductId productId = productManagement.createProduct(command);
        
        return ResponseEntity.created(
            URI.create("/api/products/" + productId.getValue())
        ).body(new ProductResponse(productId.getValue()));
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<ProductDTO> getProduct(@PathVariable String id) {
        ProductDTO product = productManagement.findProduct(
            new ProductId(id)
        );
        return ResponseEntity.ok(product);
    }
}
```

**GraphQL Adapter**

```java
@Component
public class ProductGraphQLResolver implements GraphQLQueryResolver {
    private final ProductManagement productManagement;
    
    public Product product(String id) {
        return productManagement.findProduct(new ProductId(id));
    }
    
    public List<Product> products(ProductFilter filter) {
        return productManagement.searchProducts(
            mapToSearchCriteria(filter)
        );
    }
}
```

**Message Queue Adapter**

```java
@Component
public class ProductMessageListener {
    private final ProductManagement productManagement;
    
    @RabbitListener(queues = "product.commands")
    public void handleProductCommand(ProductCommandMessage message) {
        switch (message.getType()) {
            case CREATE:
                productManagement.createProduct(
                    mapToCreateCommand(message)
                );
                break;
            case UPDATE:
                productManagement.updateProduct(
                    mapToUpdateCommand(message)
                );
                break;
            // ...
        }
    }
}
```

#### Secondary Adapters (Infrastructure Layer)

**JPA Repository Adapter**

```java
@Repository
public class JpaProductRepositoryAdapter implements ProductRepository {
    private final SpringDataProductRepository jpaRepository;
    private final ProductMapper mapper;
    
    @Override
    public Product save(Product product) {
        ProductEntity entity = mapper.toEntity(product);
        ProductEntity savedEntity = jpaRepository.save(entity);
        return mapper.toDomain(savedEntity);
    }
    
    @Override
    public Optional<Product> findById(ProductId id) {
        return jpaRepository.findById(id.getValue())
            .map(mapper::toDomain);
    }
}

// Entidade JPA
@Entity
@Table(name = "products")
public class ProductEntity {
    @Id
    private String id;
    
    @Column(nullable = false)
    private String name;
    
    @Column(precision = 10, scale = 2)
    private BigDecimal price;
    
    @Enumerated(EnumType.STRING)
    private String status;
    
    // getters, setters...
}
```

**MongoDB Repository Adapter**

```java
@Repository
public class MongoProductRepositoryAdapter implements ProductRepository {
    private final MongoTemplate mongoTemplate;
    
    @Override
    public Product save(Product product) {
        ProductDocument document = ProductDocument.fromDomain(product);
        ProductDocument saved = mongoTemplate.save(document);
        return saved.toDomain();
    }
    
    @Override
    public Optional<Product> findById(ProductId id) {
        Query query = Query.query(
            Criteria.where("_id").is(id.getValue())
        );
        ProductDocument document = mongoTemplate.findOne(
            query, 
            ProductDocument.class
        );
        return Optional.ofNullable(document)
            .map(ProductDocument::toDomain);
    }
}
```

**External Service Adapter**

```java
@Component
public class StripePaymentGatewayAdapter implements PaymentGateway {
    private final StripeClient stripeClient;
    
    @Override
    public PaymentResult processPayment(PaymentRequest request) {
        try {
            ChargeCreateParams params = ChargeCreateParams.builder()
                .setAmount(request.getAmount().getAmountInCents())
                .setCurrency(request.getCurrency().getCode())
                .setSource(request.getToken())
                .build();
                
            Charge charge = stripeClient.charges().create(params);
            
            return PaymentResult.success(
                new TransactionId(charge.getId()),
                Instant.ofEpochSecond(charge.getCreated())
            );
        } catch (StripeException e) {
            return PaymentResult.failure(e.getMessage());
        }
    }
}
```

## 🔌 Ports e Adapters - Padrão Detalhado

### Tipos de Ports

#### 1. **Primary Ports (Driving)**

- Definem os casos de uso da aplicação
- São implementados pelos Application Services
- Exemplos: REST API, CLI, GUI, Scheduled Jobs

#### 2. **Secondary Ports (Driven)**

- Definem as necessidades de infraestrutura
- São implementados pelos adapters de infraestrutura
- Exemplos: Database, Message Queue, External APIs

### Implementação de Ports

```java
// Port primário com múltiplas operações
public interface OrderManagement {
    // Commands (escrita)
    OrderId placeOrder(PlaceOrderCommand command);
    void cancelOrder(CancelOrderCommand command);
    void shipOrder(ShipOrderCommand command);
    
    // Queries (leitura)
    OrderDetails getOrderDetails(OrderId orderId);
    List<OrderSummary> getCustomerOrders(CustomerId customerId);
    OrderStatistics getOrderStatistics(DateRange range);
}

// Implementação do port (Application Service)
@Service
@Transactional
public class OrderManagementService implements OrderManagement {
    private final OrderRepository orderRepository;
    private final CustomerRepository customerRepository;
    private final InventoryService inventoryService;
    private final PaymentGateway paymentGateway;
    private final NotificationService notificationService;
    private final EventBus eventBus;
    
    @Override
    public OrderId placeOrder(PlaceOrderCommand command) {
        // 1. Validação
        Customer customer = customerRepository.findById(command.getCustomerId())
            .orElseThrow(() -> new CustomerNotFoundException(command.getCustomerId()));
            
        // 2. Verificar inventário
        inventoryService.reserveItems(command.getItems());
        
        // 3. Criar pedido
        Order order = Order.place(
            customer,
            command.getItems(),
            command.getShippingAddress()
        );
        
        // 4. Processar pagamento
        PaymentResult paymentResult = paymentGateway.processPayment(
            createPaymentRequest(order)
        );
        
        if (!paymentResult.isSuccessful()) {
            inventoryService.releaseItems(command.getItems());
            throw new PaymentFailedException(paymentResult.getErrorMessage());
        }
        
        // 5. Salvar pedido
        order.confirmPayment(paymentResult.getTransactionId());
        Order savedOrder = orderRepository.save(order);
        
        // 6. Publicar evento
        eventBus.publish(new OrderPlacedEvent(savedOrder));
        
        // 7. Enviar notificação
        notificationService.notifyOrderPlaced(savedOrder);
        
        return savedOrder.getId();
    }
}
```

## 🏛️ Domain-Driven Design (DDD)

### Agregados (Aggregates)

```java
// Aggregate Root
@AggregateRoot
public class Order {
    private OrderId id;
    private CustomerId customerId;
    private List<OrderItem> items;
    private OrderStatus status;
    private Money totalAmount;
    private Address shippingAddress;
    private final List<DomainEvent> events = new ArrayList<>();
    
    // Factory method
    public static Order place(
        Customer customer, 
        List<OrderItem> items,
        Address shippingAddress
    ) {
        Order order = new Order();
        order.id = OrderId.generate();
        order.customerId = customer.getId();
        order.items = new ArrayList<>(items);
        order.shippingAddress = shippingAddress;
        order.status = OrderStatus.PENDING;
        order.totalAmount = calculateTotal(items);
        
        order.validateInvariants();
        order.addEvent(new OrderPlacedEvent(order));
        
        return order;
    }
    
    // Métodos de negócio
    public void cancel(String reason) {
        if (!canBeCancelled()) {
            throw new OrderCannotBeCancelledException(this.id, this.status);
        }
        
        this.status = OrderStatus.CANCELLED;
        addEvent(new OrderCancelledEvent(this.id, reason));
    }
    
    public void ship(ShipmentDetails shipmentDetails) {
        if (this.status != OrderStatus.CONFIRMED) {
            throw new InvalidOrderStateException(
                "Order must be confirmed before shipping"
            );
        }
        
        this.status = OrderStatus.SHIPPED;
        addEvent(new OrderShippedEvent(this.id, shipmentDetails));
    }
    
    // Validação de invariantes
    private void validateInvariants() {
        if (items == null || items.isEmpty()) {
            throw new EmptyOrderException();
        }
        
        if (totalAmount.isLessThanOrEqualTo(Money.ZERO)) {
            throw new InvalidOrderAmountException();
        }
    }
    
    // Gestão de eventos
    protected void addEvent(DomainEvent event) {
        events.add(event);
    }
    
    public List<DomainEvent> pullDomainEvents() {
        List<DomainEvent> currentEvents = new ArrayList<>(events);
        events.clear();
        return currentEvents;
    }
}
```

### Eventos de Domínio

```java
// Base para eventos
public abstract class DomainEvent {
    private final Instant occurredOn;
    private final String eventId;
    
    protected DomainEvent() {
        this.eventId = UUID.randomUUID().toString();
        this.occurredOn = Instant.now();
    }
    
    // getters...
}

// Evento específico
public class OrderPlacedEvent extends DomainEvent {
    private final OrderId orderId;
    private final CustomerId customerId;
    private final Money totalAmount;
    
    public OrderPlacedEvent(Order order) {
        super();
        this.orderId = order.getId();
        this.customerId = order.getCustomerId();
        this.totalAmount = order.getTotalAmount();
    }
}
```

### Specifications Pattern

```java
public interface Specification<T> {
    boolean isSatisfiedBy(T candidate);
    
    default Specification<T> and(Specification<T> other) {
        return candidate -> 
            this.isSatisfiedBy(candidate) && other.isSatisfiedBy(candidate);
    }
    
    default Specification<T> or(Specification<T> other) {
        return candidate -> 
            this.isSatisfiedBy(candidate) || other.isSatisfiedBy(candidate);
    }
    
    default Specification<T> not() {
        return candidate -> !this.isSatisfiedBy(candidate);
    }
}

// Implementação
public class PremiumCustomerSpecification implements Specification<Customer> {
    @Override
    public boolean isSatisfiedBy(Customer customer) {
        return customer.getTotalPurchases().isGreaterThan(Money.of(1000))
            && customer.getRegistrationDate().isBefore(
                LocalDate.now().minusYears(1)
            );
    }
}
```

## 💉 Dependency Injection

### Configuração com Spring

```java
@Configuration
@ComponentScan(basePackages = {
    "com.example.application",
    "com.example.infrastructure"
})
public class ApplicationConfiguration {
    
    // Configuração de Use Cases
    @Bean
    public CreateProductUseCase createProductUseCase(
        ProductRepository productRepository,
        EventPublisher eventPublisher
    ) {
        return new CreateProductUseCase(productRepository, eventPublisher);
    }
    
    // Configuração de Repositories
    @Bean
    @ConditionalOnProperty(name = "database.type", havingValue = "jpa")
    public ProductRepository jpaProductRepository(
        SpringDataProductRepository jpaRepository,
        ProductMapper mapper
    ) {
        return new JpaProductRepositoryAdapter(jpaRepository, mapper);
    }
    
    @Bean
    @ConditionalOnProperty(name = "database.type", havingValue = "mongodb")
    public ProductRepository mongoProductRepository(
        MongoTemplate mongoTemplate
    ) {
        return new MongoProductRepositoryAdapter(mongoTemplate);
    }
    
    // Configuração de Services externos
    @Bean
    @Profile("production")
    public PaymentGateway stripePaymentGateway(
        @Value("${stripe.api.key}") String apiKey
    ) {
        return new StripePaymentGatewayAdapter(apiKey);
    }
    
    @Bean
    @Profile("test")
    public PaymentGateway mockPaymentGateway() {
        return new MockPaymentGateway();
    }
}
```

### Factory Pattern para Injeção

```java
@Component
public class NotificationServiceFactory {
    private final Map<NotificationType, NotificationService> services;
    
    public NotificationServiceFactory(List<NotificationService> notificationServices) {
        this.services = notificationServices.stream()
            .collect(Collectors.toMap(
                NotificationService::getType,
                Function.identity()
            ));
    }
    
    public NotificationService getService(NotificationType type) {
        NotificationService service = services.get(type);
        if (service == null) {
            throw new UnsupportedNotificationTypeException(type);
        }
        return service;
    }
}
```

## 🧪 Testes na Arquitetura Hexagonal

### Testes Unitários do Domínio

```java
class OrderTest {
    
    @Test
    void shouldCreateValidOrder() {
        // Arrange
        Customer customer = CustomerMother.aValidCustomer();
        List<OrderItem> items = Arrays.asList(
            OrderItemMother.aBookItem(),
            OrderItemMother.anElectronicItem()
        );
        Address shippingAddress = AddressMother.aValidAddress();
        
        // Act
        Order order = Order.place(customer, items, shippingAddress);
        
        // Assert
        assertThat(order).isNotNull();
        assertThat(order.getId()).isNotNull();
        assertThat(order.getStatus()).isEqualTo(OrderStatus.PENDING);
        assertThat(order.getTotalAmount()).isEqualTo(
            Money.of(150.00, Currency.USD)
        );
    }
    
    @Test
    void shouldNotCreateOrderWithEmptyItems() {
        // Arrange
        Customer customer = CustomerMother.aValidCustomer();
        List<OrderItem> emptyItems = Collections.emptyList();
        Address shippingAddress = AddressMother.aValidAddress();
        
        // Act & Assert
        assertThatThrownBy(() -> 
            Order.place(customer, emptyItems, shippingAddress)
        )
        .isInstanceOf(EmptyOrderException.class)
        .hasMessage("Order must contain at least one item");
    }
    
    @Test
    void shouldCancelPendingOrder() {
        // Arrange
        Order order = OrderMother.aPendingOrder();
        String reason = "Customer request";
        
        // Act
        order.cancel(reason);
        
        // Assert
        assertThat(order.getStatus()).isEqualTo(OrderStatus.CANCELLED);
        
        List<DomainEvent> events = order.pullDomainEvents();
        assertThat(events).hasSize(1);
        assertThat(events.get(0)).isInstanceOf(OrderCancelledEvent.class);
    }
}
```

### Testes de Integração

```java
@SpringBootTest
@AutoConfigureMockMvc
class ProductControllerIntegrationTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @MockBean
    private ProductRepository productRepository;
    
    @Test
    void shouldCreateProduct() throws Exception {
        // Arrange
        CreateProductRequest request = new CreateProductRequest(
            "Laptop",
            new BigDecimal("999.99"),
            "USD",
            "ELECTRONICS"
        );
        
        Product savedProduct = ProductMother.aLaptop();
        when(productRepository.save(any(Product.class)))
            .thenReturn(savedProduct);
        
        // Act & Assert
        mockMvc.perform(post("/api/products")
                .contentType(MediaType.APPLICATION_JSON)
                .content(toJson(request)))
            .andExpect(status().isCreated())
            .andExpect(header().exists("Location"))
            .andExpect(jsonPath("$.id").value(savedProduct.getId().getValue()));
        
        verify(productRepository).save(any(Product.class));
    }
}
```

### Testes de Arquitetura

```java
@AnalyzeClasses(packages = "com.example")
class ArchitectureTest {
    
    @Test
    void domainShouldNotDependOnInfrastructure() {
        JavaClasses classes = new ClassFileImporter()
            .importPackages("com.example");
        
        ArchRule rule = noClasses()
            .that().resideInAPackage("..domain..")
            .should().dependOnClassesThat()
            .resideInAPackage("..infrastructure..");
        
        rule.check(classes);
    }
    
    @Test
    void portsShouldBeInterfaces() {
        JavaClasses classes = new ClassFileImporter()
            .importPackages("com.example");
        
        ArchRule rule = classes()
            .that().resideInAPackage("..ports..")
            .should().beInterfaces();
        
        rule.check(classes);
    }
    
    @Test
    void adaptersShouldImplementPorts() {
        JavaClasses classes = new ClassFileImporter()
            .importPackages("com.example");
        
        ArchRule rule = classes()
            .that().resideInAPackage("..adapters..")
            .and().areNotInterfaces()
            .should().implement(JavaClass.Predicates.resideInAPackage("..ports.."));
        
        rule.check(classes);
    }
}
```

## 💼 Exemplos Práticos

### Exemplo 1: Sistema de E-commerce

**Estrutura de Pacotes:**

```
com.example.ecommerce/
├── domain/
│   ├── model/
│   │   ├── product/
│   │   │   ├── Product.java
│   │   │   ├── ProductId.java
│   │   │   ├── ProductRepository.java
│   │   │   └── ProductStatus.java
│   │   ├── order/
│   │   │   ├── Order.java
│   │   │   ├── OrderItem.java
│   │   │   ├── OrderRepository.java
│   │   │   └── OrderStatus.java
│   │   └── customer/
│   │       ├── Customer.java
│   │       ├── CustomerId.java
│   │       └── CustomerRepository.java
│   ├── events/
│   │   ├── DomainEvent.java
│   │   ├── OrderPlacedEvent.java
│   │   └── ProductCreatedEvent.java
│   └── services/
│       ├── PricingService.java
│       └── InventoryService.java
├── application/
│   ├── usecases/
│   │   ├── CreateProductUseCase.java
│   │   ├── PlaceOrderUseCase.java
│   │   └── RegisterCustomerUseCase.java
│   └── ports/
│       ├── input/
│       │   ├── ProductManagement.java
│       │   └── OrderManagement.java
│       └── output/
│           ├── PaymentGateway.java
│           ├── EmailService.java
│           └── EventPublisher.java
└── infrastructure/
    ├── adapters/
    │   ├── input/
    │   │   ├── rest/
    │   │   │   ├── ProductController.java
    │   │   │   └── OrderController.java
    │   │   └── messaging/
    │   │       └── OrderEventListener.java
    │   └── output/
    │       ├── persistence/
    │       │   ├── jpa/
    │       │   │   ├── JpaProductRepository.java
    │       │   │   └── JpaOrderRepository.java
    │       │   └── mongodb/
    │       │       └── MongoCustomerRepository.java
    │       └── external/
    │           ├── StripePaymentGateway.java
    │           └── SendGridEmailService.java
    └── configuration/
        ├── ApplicationConfiguration.java
        ├── DatabaseConfiguration.java
        └── SecurityConfiguration.java
```

### Exemplo 2: Fluxo Completo de Criação de Pedido

```java
// 1. Request chega ao Controller (Primary Adapter)
@PostMapping("/orders")
public ResponseEntity<OrderResponse> placeOrder(
    @RequestBody PlaceOrderRequest request,
    @AuthenticationPrincipal UserPrincipal principal
) {
    PlaceOrderCommand command = PlaceOrderCommand.from(request, principal);
    OrderId orderId = orderManagement.placeOrder(command);
    return ResponseEntity.created(/*...*/).body(/*...*/);
}

// 2. Application Service processa o comando
@Service
public class PlaceOrderUseCase implements OrderManagement {
    @Override
    @Transactional
    public OrderId placeOrder(PlaceOrderCommand command) {
        // Buscar cliente (via port)
        Customer customer = customerRepository.findById(command.getCustomerId())
            .orElseThrow(/*...*/);
        
        // Validar e reservar inventário (via port)
        inventoryService.reserveItems(command.getItems());
        
        // Criar ordem (domínio puro)
        Order order = Order.place(customer, command.getItems(), /*...*/);
        
        // Processar pagamento (via port)
        PaymentResult result = paymentGateway.process(/*...*/);
        order.confirmPayment(result);
        
        // Persistir (via port)
        Order savedOrder = orderRepository.save(order);
        
        // Publicar eventos (via port)
        eventPublisher.publishAll(order.pullDomainEvents());
        
        return savedOrder.getId();
    }
}

// 3. Adapters implementam os ports
@Repository
public class JpaOrderRepositoryAdapter implements OrderRepository {
    @Override
    public Order save(Order order) {
        OrderEntity entity = mapper.toEntity(order);
        OrderEntity saved = jpaRepository.save(entity);
        return mapper.toDomain(saved);
    }
}
```

## 📋 Boas Práticas

### 1. **Isolamento do Domínio**

- Nunca use anotações de frameworks no domínio
- Mantenha as entidades livres de dependências externas
- Use tipos primitivos wrapper (Value Objects) em vez de primitivos

### 2. **Ports Bem Definidos**

- Crie interfaces específicas e coesas
- Evite interfaces genéricas demais
- Documente contratos claramente

### 3. **Adapters Simples**

- Adapters devem apenas traduzir, não conter lógica
- Mantenha mapeamentos em classes separadas
- Use bibliotecas como MapStruct para mapeamentos complexos

### 4. **Testes em Camadas**

- Teste o domínio isoladamente (sem mocks)
- Use mocks apenas para ports em testes de application services
- Crie testes de integração para adapters

### 5. **Gestão de Transações**

- Transações pertencem à camada de aplicação
- Use o padrão Unit of Work quando apropriado
- Evite transações distribuídas quando possível

### 6. **Tratamento de Erros**

```java
// Exceções de domínio
public class DomainException extends RuntimeException {
    private final ErrorCode errorCode;
    
    public DomainException(String message, ErrorCode errorCode) {
        super(message);
        this.errorCode = errorCode;
    }
}

// Handler global para APIs REST
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(DomainException.class)
    public ResponseEntity<ErrorResponse> handleDomainException(
        DomainException ex
    ) {
        ErrorResponse error = ErrorResponse.builder()
            .code(ex.getErrorCode())
            .message(ex.getMessage())
            .timestamp(Instant.now())
            .build();
            
        return ResponseEntity.status(mapToHttpStatus(ex.getErrorCode()))
            .body(error);
    }
}
```

### 7. **Configuração e Perfis**

```yaml
# application.yml
spring:
  profiles:
    active: ${ENVIRONMENT:local}

---
spring:
  config:
    activate:
      on-profile: local
  datasource:
    url: jdbc:h2:mem:testdb
    
payment:
  gateway: mock
  
---
spring:
  config:
    activate:
      on-profile: production
  datasource:
    url: ${DATABASE_URL}
    
payment:
  gateway: stripe
  api-key: ${STRIPE_API_KEY}
```

## 🚀 Migração para Arquitetura Hexagonal

### Estratégia de Migração Gradual

1. **Identificar Bounded Contexts**
    
    - Mapear domínios e subdomínios
    - Definir contextos delimitados
2. **Extrair Domínio**
    
    - Mover lógica de negócio para entidades
    - Criar value objects
    - Implementar domain services
3. **Definir Ports**
    
    - Criar interfaces para operações
    - Separar comandos de consultas (CQRS)
4. **Implementar Adapters**
    
    - Criar adapters para infraestrutura existente
    - Implementar novos adapters conforme necessário
5. **Refatorar Incrementalmente**
    
    - Migrar um módulo por vez
    - Manter testes passando durante toda migração

## 📚 Recursos e Ferramentas

### Bibliotecas Úteis

- **Spring Boot**: Framework principal
- **MapStruct**: Mapeamento entre objetos
- **ArchUnit**: Testes de arquitetura
- **Lombok**: Redução de boilerplate
- **Vavr**: Programação funcional em Java
- **AssertJ**: Assertions fluentes para testes

### Padrões Complementares

- **CQRS**: Command Query Responsibility Segregation
- **Event Sourcing**: Armazenamento baseado em eventos
- **Saga Pattern**: Transações distribuídas
- **Repository Pattern**: Abstração de persistência
- **Unit of Work**: Gestão de transações

### Ferramentas de Build

```xml
<!-- Maven -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.tngtech.archunit</groupId>
    <artifactId>archunit-junit5</artifactId>
    <scope>test</scope>
</dependency>
```

```gradle
// Gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    testImplementation 'com.tngtech.archunit:archunit-junit5'
}
```

## 🎯 Conclusão

A Arquitetura Hexagonal oferece uma abordagem robusta para construir aplicações mantíveis, testáveis e adaptáveis. Seus principais benefícios incluem:

✅ **Independência tecnológica**: Troca fácil de frameworks e bibliotecas  
✅ **Testabilidade aprimorada**: Domínio testável isoladamente  
✅ **Clareza de responsabilidades**: Separação clara entre negócio e infraestrutura  
✅ **Evolução facilitada**: Mudanças localizadas e controladas  
✅ **Reusabilidade**: Domínio pode ser usado em diferentes contextos

Ao seguir os princípios e práticas descritos neste guia, você estará apto a implementar uma arquitetura hexagonal robusta e escalável em seus projetos Java.