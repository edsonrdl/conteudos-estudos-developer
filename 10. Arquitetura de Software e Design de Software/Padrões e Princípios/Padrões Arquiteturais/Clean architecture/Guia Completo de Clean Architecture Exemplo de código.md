
## Fundamentos da Clean Architecture

A Clean Architecture, criada por Robert C. Martin (Uncle Bob), representa uma evolução natural dos padrões arquiteturais modernos. **Esta arquitetura promove separação rigorosa de responsabilidades através de camadas concêntricas, onde dependências sempre apontam para dentro**, garantindo que regras de negócio permaneçam independentes de frameworks, interfaces e tecnologias externas.

### Os quatro círculos da Clean Architecture

**Entities (núcleo)**: Encapsulam regras de negócio corporativas mais estáveis e gerais. São objetos que contêm dados críticos e métodos que operam sobre esses dados, representando conceitos fundamentais do domínio que raramente mudam quando algo externo muda.

**Use Cases**: Contêm regras de negócio específicas da aplicação, orquestrando fluxos de dados entre entidades e direcionando-as a usar suas regras corporativas. Esta camada isola a aplicação de mudanças em UI, banco de dados ou outros elementos externos.

**Interface Adapters**: Convertem dados entre formatos convenientes para use cases/entidades e formatos apropriados para agências externas como web, banco de dados ou APIs. Toda arquitetura MVC de uma GUI pertence aqui.

**Frameworks & Drivers**: A camada mais externa, composta por frameworks e ferramentas que se comunicam com o círculo seguinte. Aqui ficam detalhes como web servers, bancos de dados e interfaces externas.

## Padrões Avançados de Implementação

### CQRS (Command Query Responsibility Segregation)

O CQRS separa operações de leitura e escrita em modelos distintos, **permitindo otimização independente e escalabilidade diferenciada**. Commands alteram estado sem retornar dados, enquanto Queries retornam dados sem modificar estado.

**Implementação prática em C#:**

```csharp
// Command Handler
public class CreateOrderCommandHandler : IRequestHandler<CreateOrderCommand, Guid>
{
    private readonly IOrderRepository _repository;
    private readonly IEventPublisher _eventPublisher;
    
    public async Task<Guid> Handle(CreateOrderCommand command, CancellationToken cancellationToken)
    {
        var order = Order.Create(command.CustomerId, command.Items);
        await _repository.AddAsync(order);
        
        // Publicar eventos de domínio
        await _eventPublisher.PublishAsync(order.DomainEvents);
        return order.Id;
    }
}

// Query Handler separado
public class GetOrderQueryHandler : IRequestHandler<GetOrderQuery, OrderDto>
{
    private readonly IOrderReadRepository _readRepository;
    
    public async Task<OrderDto> Handle(GetOrderQuery query, CancellationToken cancellationToken)
    {
        return await _readRepository.GetOrderProjectionAsync(query.OrderId);
    }
}
```

### Event Sourcing

Event Sourcing captura todas mudanças de estado como sequência de eventos imutáveis. **Estado atual é derivado pela reprodução de eventos**, proporcionando auditoria completa, capacidade de reconstrução temporal e debugging avançado.

**Estrutura de implementação:**

```java
public class OrderAggregate {
    private UUID id;
    private List<OrderItem> items = new ArrayList<>();
    private OrderStatus status;
    private List<DomainEvent> changes = new ArrayList<>();
    
    public void createOrder(UUID customerId, List<OrderItem> items) {
        OrderCreatedEvent event = new OrderCreatedEvent(UUID.randomUUID(), customerId, items);
        applyEvent(event);
        changes.add(event);
    }
    
    private void applyEvent(OrderCreatedEvent event) {
        this.id = event.getOrderId();
        this.items = event.getItems();
        this.status = OrderStatus.CREATED;
    }
    
    // Reconstruir estado a partir de eventos históricos
    public static OrderAggregate fromEvents(List<DomainEvent> events) {
        OrderAggregate aggregate = new OrderAggregate();
        events.forEach(aggregate::applyEvent);
        return aggregate;
    }
}
```

### Domain Events

Domain Events representam algo significativo que aconteceu no domínio. **São imutáveis, nomeados no passado e carregam contexto relevante** para outras partes do sistema reagirem apropriadamente.

**Implementação com MediatR:**

```csharp
public class Order : AggregateRoot
{
    public void CompletePayment(decimal amount)
    {
        if (Status != OrderStatus.PaymentPending)
            throw new InvalidOperationException("Pagamento não pode ser processado");
            
        Status = OrderStatus.Paid;
        PaidAmount = amount;
        
        // Adicionar evento de domínio
        AddDomainEvent(new PaymentCompletedEvent(Id, amount, DateTime.UtcNow));
    }
}

public class PaymentCompletedEventHandler : INotificationHandler<PaymentCompletedEvent>
{
    private readonly IEmailService _emailService;
    private readonly IInventoryService _inventoryService;
    
    public async Task Handle(PaymentCompletedEvent notification, CancellationToken cancellationToken)
    {
        // Enviar confirmação por email
        await _emailService.SendPaymentConfirmationAsync(notification.OrderId);
        
        // Atualizar estoque
        await _inventoryService.ReserveItemsAsync(notification.OrderId);
    }
}
```

### Saga Pattern

O Saga Pattern gerencia transações distribuídas como sequência de transações locais, cada uma com operação compensatória correspondente. **Essencial para manter consistência em microservices sem usar 2PC**.

**Implementação com Orchestração:**

```csharp
public class OrderProcessingSaga
{
    public async Task<bool> ProcessOrderAsync(ProcessOrderCommand command)
    {
        var sagaId = Guid.NewGuid();
        var context = new SagaContext(sagaId);
        
        try 
        {
            // Step 1: Validar pedido
            await ExecuteStepAsync(context, new ValidateOrderStep(command.OrderId));
            
            // Step 2: Processar pagamento
            await ExecuteStepAsync(context, new ProcessPaymentStep(command.PaymentInfo));
            
            // Step 3: Reservar estoque  
            await ExecuteStepAsync(context, new ReserveInventoryStep(command.Items));
            
            // Step 4: Confirmar pedido
            await ExecuteStepAsync(context, new ConfirmOrderStep(command.OrderId));
            
            return true;
        }
        catch (SagaExecutionException ex)
        {
            // Executar compensações na ordem reversa
            await CompensateAsync(context, ex.FailedStepIndex);
            return false;
        }
    }
}
```

## Implementações Práticas por Linguagem

### Java/Spring Boot

**Estrutura recomendada:**

```
src/main/java/
├── domain/
│   ├── entities/     # Entidades de domínio
│   ├── valueobjects/ # Value Objects
│   └── services/     # Domain Services
├── application/
│   ├── usecases/     # Casos de uso
│   ├── ports/        # Interfaces (ports)
│   └── services/     # Application Services
├── infrastructure/
│   ├── adapters/     # Implementações dos ports
│   ├── persistence/  # Repositórios e JPA
│   └── config/       # Configurações Spring
└── interfaces/
    └── web/         # Controllers REST
```

**Exemplo de Use Case com Spring:**

```java
@UseCase
@Transactional
public class TransferMoneyUseCase {
    private final AccountRepository accountRepository;
    private final TransactionRepository transactionRepository;
    private final DomainEventPublisher eventPublisher;
    
    public TransferResult execute(TransferMoneyCommand command) {
        Account sourceAccount = accountRepository.findById(command.getSourceAccountId())
            .orElseThrow(() -> new AccountNotFoundException(command.getSourceAccountId()));
            
        Account targetAccount = accountRepository.findById(command.getTargetAccountId())
            .orElseThrow(() -> new AccountNotFoundException(command.getTargetAccountId()));
        
        // Lógica de domínio
        Transaction transaction = sourceAccount.transferTo(targetAccount, command.getAmount());
        
        // Persistir mudanças
        accountRepository.save(sourceAccount);
        accountRepository.save(targetAccount);
        transactionRepository.save(transaction);
        
        // Publicar eventos
        eventPublisher.publish(transaction.getDomainEvents());
        
        return TransferResult.success(transaction.getId());
    }
}
```

### Python/FastAPI

**Estrutura modular framework-agnóstica:**

```python
# domain/entities.py
from dataclasses import dataclass, field
from typing import List, Optional
from decimal import Decimal

@dataclass
class Account:
    id: AccountId
    balance: Money
    customer_id: CustomerId
    _domain_events: List[DomainEvent] = field(default_factory=list, init=False)
    
    def withdraw(self, amount: Money) -> None:
        if self.balance < amount:
            raise InsufficientFundsError(f"Saldo insuficiente: {self.balance}")
        
        self.balance -= amount
        self._domain_events.append(
            MoneyWithdrawnEvent(account_id=self.id, amount=amount)
        )
    
    def deposit(self, amount: Money) -> None:
        self.balance += amount
        self._domain_events.append(
            MoneyDepositedEvent(account_id=self.id, amount=amount)
        )

# application/use_cases.py
from typing import Protocol

class AccountRepository(Protocol):
    async def get_by_id(self, account_id: AccountId) -> Account:
        ...
    async def save(self, account: Account) -> None:
        ...

class TransferMoneyInteractor:
    def __init__(
        self,
        account_repo: AccountRepository,
        event_publisher: EventPublisher
    ):
        self._account_repo = account_repo
        self._event_publisher = event_publisher
    
    async def execute(self, command: TransferMoneyCommand) -> TransferResult:
        source_account = await self._account_repo.get_by_id(command.source_account_id)
        target_account = await self._account_repo.get_by_id(command.target_account_id)
        
        # Executar transferência
        source_account.withdraw(command.amount)
        target_account.deposit(command.amount)
        
        # Salvar alterações
        await self._account_repo.save(source_account)
        await self._account_repo.save(target_account)
        
        # Publicar eventos
        all_events = source_account._domain_events + target_account._domain_events
        await self._event_publisher.publish_many(all_events)
        
        return TransferResult.success(source_account.id)
```

### TypeScript/NestJS

**Implementação com decorators e DI:**

```typescript
// domain/entities/User.ts
export class User {
  constructor(
    public readonly id: UserId,
    public email: Email,
    private _isActive: boolean = true
  ) {}
  
  deactivate(): DomainEvent[] {
    if (!this._isActive) {
      throw new UserAlreadyInactiveError();
    }
    
    this._isActive = false;
    return [new UserDeactivatedEvent(this.id, new Date())];
  }
  
  get isActive(): boolean {
    return this._isActive;
  }
}

// application/use-cases/DeactivateUser.usecase.ts
@Injectable()
export class DeactivateUserUseCase {
  constructor(
    @Inject('USER_REPOSITORY')
    private readonly userRepository: UserRepository,
    private readonly eventBus: EventBus
  ) {}
  
  async execute(command: DeactivateUserCommand): Promise<void> {
    const user = await this.userRepository.findById(command.userId);
    if (!user) {
      throw new UserNotFoundError(command.userId);
    }
    
    const events = user.deactivate();
    await this.userRepository.save(user);
    
    // Publicar eventos de domínio
    for (const event of events) {
      this.eventBus.publish(event);
    }
  }
}

// infrastructure/controllers/User.controller.ts
@Controller('users')
export class UserController {
  constructor(private readonly deactivateUserUseCase: DeactivateUserUseCase) {}
  
  @Delete(':id/deactivate')
  @HttpCode(HttpStatus.NO_CONTENT)
  async deactivateUser(@Param('id') userId: string): Promise<void> {
    const command = new DeactivateUserCommand(new UserId(userId));
    await this.deactivateUserUseCase.execute(command);
  }
}
```

## Estratégias de Testes

### Testes de Unidade por Camada

**Domain Layer** - Testes puros, sem dependências:

```csharp
[Test]
public void Account_Withdraw_ShouldThrowException_WhenInsufficientFunds()
{
    // Arrange
    var account = new Account(AccountId.New(), Money.Of(100));
    var withdrawAmount = Money.Of(150);
    
    // Act & Assert
    Assert.Throws<InsufficientFundsException>(() => 
        account.Withdraw(withdrawAmount)
    );
}
```

**Application Layer** - Testes com mocks:

```csharp
[Test] 
public async Task TransferMoney_ShouldSucceed_WhenAccountsExistAndHaveFunds()
{
    // Arrange
    var sourceAccount = new Account(sourceAccountId, Money.Of(500));
    var targetAccount = new Account(targetAccountId, Money.Of(100));
    
    _accountRepository.GetByIdAsync(sourceAccountId).Returns(sourceAccount);
    _accountRepository.GetByIdAsync(targetAccountId).Returns(targetAccount);
    
    var command = new TransferMoneyCommand(sourceAccountId, targetAccountId, Money.Of(200));
    
    // Act
    var result = await _transferMoneyHandler.Handle(command, CancellationToken.None);
    
    // Assert
    result.IsSuccess.Should().BeTrue();
    await _accountRepository.Received(2).SaveAsync(Arg.Any<Account>());
}
```

### Architecture Tests

**Validação de dependências com ArchUnit:**

```csharp
[Test]
public void Domain_ShouldNotDependOn_ApplicationOrInfrastructure()
{
    var result = Types.InCurrentDomain()
        .That()
        .ResideInNamespace("MyApp.Domain")
        .ShouldNot()
        .HaveDependencyOnAny("MyApp.Application", "MyApp.Infrastructure")
        .GetResult();
    
    Assert.IsTrue(result.IsSuccessful, 
        $"Domain layer has forbidden dependencies: {string.Join(", ", result.FailingTypes)}");
}
```

### Padrões de Teste

**Repository Pattern Testing:**

```java
@DataJpaTest
class AccountRepositoryTest {
    @Autowired
    private TestEntityManager entityManager;
    
    @Autowired 
    private AccountRepository accountRepository;
    
    @Test
    void findByCustomerId_ShouldReturnAccount_WhenExists() {
        // Given
        Account account = new Account(CustomerId.of("123"), Money.of(BigDecimal.valueOf(1000)));
        entityManager.persistAndFlush(account);
        
        // When
        Optional<Account> found = accountRepository.findByCustomerId(CustomerId.of("123"));
        
        // Then
        assertThat(found).isPresent();
        assertThat(found.get().getBalance()).isEqualTo(Money.of(BigDecimal.valueOf(1000)));
    }
}
```

**Unit of Work Pattern:**

```csharp
public class UnitOfWork : IUnitOfWork
{
    private readonly DbContext _context;
    private readonly Dictionary<Type, object> _repositories = new();
    
    public IRepository<T> Repository<T>() where T : class
    {
        if (_repositories.ContainsKey(typeof(T)))
            return (IRepository<T>)_repositories[typeof(T)];
            
        var repository = new Repository<T>(_context);
        _repositories.Add(typeof(T), repository);
        return repository;
    }
    
    public async Task<int> SaveChangesAsync()
    {
        return await _context.SaveChangesAsync();
    }
}
```

## Clean Architecture em Microservices

### Estrutura por Microserviço

Cada microserviço mantém sua própria implementação de Clean Architecture, **permitindo tecnologias diferentes e autonomia de desenvolvimento**:

```
/microservice-order/
├── Domain/           # Entities específicas do domínio Order
├── Application/      # Use cases do contexto Order  
├── Infrastructure/   # Adapters para este serviço
└── API/             # Interface REST/GraphQL

/microservice-payment/
├── Domain/          # Entities de Payment
├── Application/     # Use cases de pagamento
├── Infrastructure/  # Integração com gateways
└── API/            # API de pagamento
```

### Comunicação Entre Serviços

**Event-Driven Architecture:**

```csharp
// Publicação de Integration Events
public class OrderProcessedIntegrationEvent : IntegrationEvent
{
    public Guid OrderId { get; }
    public decimal TotalAmount { get; }
    public List<OrderItem> Items { get; }
    
    public OrderProcessedIntegrationEvent(Guid orderId, decimal totalAmount, List<OrderItem> items)
    {
        OrderId = orderId;
        TotalAmount = totalAmount;
        Items = items;
    }
}

// Handler em outro microserviço
[EventHandler]
public class UpdateInventoryOnOrderProcessedHandler : IIntegrationEventHandler<OrderProcessedIntegrationEvent>
{
    private readonly IInventoryService _inventoryService;
    
    public async Task Handle(OrderProcessedIntegrationEvent @event)
    {
        foreach (var item in @event.Items)
        {
            await _inventoryService.ReserveItemAsync(item.ProductId, item.Quantity);
        }
    }
}
```

### Estratégias de Deployment

**Monorepo vs Multirepo:**

**Monorepo - Vantagens:**

- Refactoring cross-service simplificado
- Dependency management centralizado
- CI/CD pipeline único
- Atomic commits entre serviços

**Multirepo - Vantagens:**

- Autonomia completa das equipes
- Deployment cycles independentes
- Controle de acesso granular
- Tecnologias específicas por serviço

## Performance e Otimizações

### Estratégias de Caching Multi-Level

```python
class CacheStrategy:
    def __init__(self):
        self.l1_cache = InMemoryCache()      # Cache local
        self.l2_cache = RedisCache()         # Cache distribuído
        self.l3_cache = CDNCache()           # Cache de borda
    
    async def get_data(self, key: str) -> Optional[Data]:
        # L1: Cache em memória
        if data := await self.l1_cache.get(key):
            return data
            
        # L2: Cache distribuído
        if data := await self.l2_cache.get(key):
            await self.l1_cache.set(key, data, ttl=300)
            return data
            
        # L3: Busca na fonte com cache de borda
        data = await self.data_source.get(key)
        if data:
            await self.l3_cache.set(key, data, ttl=3600)
            await self.l2_cache.set(key, data, ttl=1800)
            await self.l1_cache.set(key, data, ttl=300)
        
        return data
```

### Database Sharding e Read Replicas

```yaml
# Estratégia de database por microserviço
services:
  order-service:
    database:
      write: order-db-master
      read: order-db-replica-1, order-db-replica-2
      sharding: customer_id % 4
  
  inventory-service:
    database:
      write: inventory-db-master  
      read: inventory-db-replica-1
      sharding: product_category
```

## Estratégias de Migração

### Strangler Fig Pattern

**Implementação gradual para substituir sistemas legacy:**

```java
@Component
public class PaymentServiceProxy {
    private final LegacyPaymentService legacyService;
    private final ModernPaymentService modernService;
    private final FeatureToggle featureToggle;
    
    public PaymentResult processPayment(PaymentRequest request) {
        // Determinar qual implementação usar
        if (featureToggle.isModernPaymentEnabled(request.getCustomerId())) {
            return modernService.processPayment(request);
        } else {
            // Anti-corruption layer para sistema legacy
            LegacyPaymentData legacyData = PaymentDataTranslator.toLegacy(request);
            LegacyPaymentResponse response = legacyService.processPayment(legacyData);
            return PaymentDataTranslator.toModern(response);
        }
    }
}
```

### Migration em Fases

**Fase 1 - Assessment (2-4 semanas):**

- Mapear arquitetura atual e dependências
- Identificar bounded contexts
- Definir estratégia de dados

**Fase 2 - Infrastructure (2-3 semanas):**

- Setup de novo ambiente
- Implementar Anti-corruption layers
- Configurar pipelines CI/CD

**Fase 3 - Core Domain (4-8 semanas):**

- Migrar entities e value objects
- Implementar domain services
- Testes de domínio

**Fase 4 - Application Layer (3-6 semanas):**

- Migrar use cases
- Implementar command/query handlers
- Testes de aplicação

## DevOps e CI/CD

### Pipeline Arquiteturalmente Consciente

```yaml
# GitHub Actions com validação arquitetural
name: Clean Architecture CI/CD
on:
  pull_request:
    branches: [main]

jobs:
  architecture-validation:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Run Architecture Tests
        run: |
          dotnet test ArchitectureTests/ --logger trx --results-directory TestResults/
          
      - name: SonarQube Analysis
        uses: sonarqube-quality-gate-action@v1.3.0
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          
  build-and-test:
    needs: architecture-validation
    runs-on: ubuntu-latest
    steps:
      - name: Unit Tests
        run: dotnet test --collect:"XPlat Code Coverage"
        
      - name: Integration Tests  
        run: docker-compose -f docker-compose.test.yml up --abort-on-container-exit
        
  deploy:
    needs: build-and-test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - name: Blue-Green Deployment
        run: |
          # Deploy para ambiente blue
          kubectl apply -f k8s/blue/
          # Validar health checks
          # Switch traffic para blue
          kubectl patch service app-service -p '{"spec":{"selector":{"version":"blue"}}}'
```

### Security Patterns

**Implementação de autenticação em microservices:**

```csharp
// JWT com claims específicos
public class JwtSecurityService : ISecurityService
{
    public ClaimsPrincipal ValidateToken(string token)
    {
        var tokenHandler = new JwtSecurityTokenHandler();
        var key = Encoding.ASCII.GetBytes(_secretKey);
        
        var principal = tokenHandler.ValidateToken(token, new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(key),
            ValidateIssuer = true,
            ValidIssuer = _issuer,
            ValidateAudience = true,
            ValidAudience = _audience,
            ClockSkew = TimeSpan.Zero
        }, out SecurityToken validatedToken);
        
        return principal;
    }
}

// Authorization baseada em políticas
[Authorize(Policy = "CanProcessOrders")]
public class OrderController : ControllerBase
{
    [HttpPost]
    public async Task<IActionResult> CreateOrder(CreateOrderCommand command)
    {
        // Contexto de segurança automaticamente validado
        var userId = User.FindFirst("user_id")?.Value;
        command.UserId = userId;
        
        var result = await _mediator.Send(command);
        return Ok(result);
    }
}
```

## Comparação com Outras Arquiteturas

### Clean vs Hexagonal vs Onion

|Aspecto|Clean Architecture|Hexagonal Architecture|Onion Architecture|
|---|---|---|---|
|**Foco Principal**|Use Cases como primeira classe|Ports & Adapters|Domain Model central|
|**Estrutura**|4 camadas prescritas|Flexível (core + adapters)|Camadas concêntricas DDD|
|**Complexidade**|Alta (mais boilerplate)|Média (flexibilidade)|Média-Alta (DDD)|
|**Quando Usar**|Sistemas complexos, equipes grandes|Muitas integrações, microservices|Domain-rich applications|

**Clean Architecture** é ideal quando você precisa de **estrutura prescrita e casos de uso bem definidos**. Hexagonal funciona melhor para **sistemas com muitas integrações externas**. Onion Architecture é perfeita para **aplicações ricas em domínio com DDD**.

### Anti-Padrões Comuns

**Over-Engineering em Projetos Simples:**

```java
// ANTI-PADRÃO: Clean Architecture para CRUD simples
public class GetUserByIdUseCase {
    public UserResponse execute(GetUserByIdRequest request) {
        User user = userRepository.findById(request.getId());
        return UserResponseMapper.toResponse(user);
    }
}

// MELHOR: Controller direto para operações simples
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return userRepository.findById(id);
}
```

**Anemic Domain Models:**

```csharp
// ANTI-PADRÃO: Entidade sem comportamento
public class Order 
{
    public Guid Id { get; set; }
    public decimal Total { get; set; }
    public OrderStatus Status { get; set; }
    // Apenas propriedades
}

// CORREÇÃO: Entidade rica com comportamento
public class Order
{
    public Guid Id { get; private set; }
    public Money Total { get; private set; }
    public OrderStatus Status { get; private set; }
    
    public void AddItem(OrderItem item)
    {
        if (Status != OrderStatus.Draft)
            throw new InvalidOperationException("Não é possível modificar pedido processado");
            
        _items.Add(item);
        Total = CalculateTotal();
        AddDomainEvent(new ItemAddedToOrderEvent(Id, item));
    }
}
```

## Ferramentas e Frameworks

### Templates Recomendados por Linguagem

**.NET:**

- **Jason Taylor Clean Architecture**: `dotnet new install Clean.Architecture.Solution.Template`
- **Ardalis Clean Architecture**: `dotnet new install Ardalis.CleanArchitecture.Template`

**Python/FastAPI:**

- **fastapi-clean-example**: Framework-agnostic com CQRS
- **Clean FastAPI template**: Estrutura modular e testável

**TypeScript/NestJS:**

- **NestJS CLI**: `nest new project-name`
- **Clean Architecture NestJS boilerplate**: TypeScript + MongoDB

**Java/Spring Boot:**

- **Spring Boot Clean Architecture archetype**
- **Maven templates** com estrutura predefinida

### Ferramentas de Qualidade

**SonarQube** - Análise estática multi-linguagem:

- Detecção de code smells e bugs
- Métricas de complexidade
- Validação de regras arquiteturais customizáveis
- Integração CI/CD nativa

**ArchUnit** - Testes de arquitetura automatizados:

```java
@ArchTest
static final ArchRule layered_architecture = layeredArchitecture()
    .layer("Controllers").definedBy("..controller..")
    .layer("UseCases").definedBy("..usecase..")
    .layer("Domain").definedBy("..domain..")
    
    .whereLayer("Controllers").mayNotBeAccessedByAnyLayer()
    .whereLayer("UseCases").mayOnlyBeAccessedByLayers("Controllers")
    .whereLayer("Domain").mayOnlyBeAccessedByLayers("UseCases");
```

## Métricas de Qualidade Arquitetural

### Métricas Fundamentais

**Complexidade Ciclomática**: Mede caminhos independentes no código

- 1-10: Baixo risco, fácil manutenção
- 11-20: Complexidade moderada
- 21+: Alto risco, refatoração necessária

**Acoplamento (Coupling)**: Mede dependências entre módulos

- **Efferent Coupling (Ce)**: Dependências que saem do módulo
- **Afferent Coupling (Ca)**: Dependências que entram no módulo
- **Instability (I)**: Ce/(Ca+Ce) - módulos estáveis têm I próximo de 0

**Cobertura de Testes**: Percentual de código testado

- Meta recomendada: 80-90% para código crítico
- Foque em **branch coverage** além de line coverage

### Implementação de Observabilidade

**Logging Estruturado com Contexto:**

```csharp
public class OrderService 
{
    private readonly ILogger<OrderService> _logger;
    
    public async Task<Result> ProcessOrderAsync(ProcessOrderCommand command)
    {
        using var activity = _activitySource.StartActivity("ProcessOrder");
        activity?.SetTag("orderId", command.OrderId.ToString());
        activity?.SetTag("customerId", command.CustomerId.ToString());
        
        _logger.LogInformation("Iniciando processamento do pedido {OrderId} para cliente {CustomerId}", 
            command.OrderId, command.CustomerId);
            
        try 
        {
            var result = await _orderProcessor.ProcessAsync(command);
            
            _logger.LogInformation("Pedido {OrderId} processado com sucesso em {ElapsedMs}ms", 
                command.OrderId, activity?.Duration.TotalMilliseconds);
                
            return result;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Erro ao processar pedido {OrderId}: {ErrorMessage}", 
                command.OrderId, ex.Message);
            throw;
        }
    }
}
```

**Métricas de Negócio com Prometheus:**

```csharp
public class OrderMetrics
{
    private static readonly Counter OrdersProcessed = Metrics
        .CreateCounter("orders_processed_total", "Total de pedidos processados")
        .WithTag("status");
        
    private static readonly Histogram OrderProcessingDuration = Metrics
        .CreateHistogram("order_processing_duration_seconds", "Tempo de processamento de pedidos");
        
    public void RecordOrderProcessed(OrderStatus status, double durationSeconds)
    {
        OrdersProcessed.WithTag("status", status.ToString()).Inc();
        OrderProcessingDuration.Observe(durationSeconds);
    }
}
```

## Casos de Uso Complexos do Mundo Real

### E-commerce com Clean Architecture

**Estrutura completa de um pedido online:**

```csharp
// Domain Layer - Aggregate Root
public class Order : AggregateRoot<OrderId>
{
    private readonly List<OrderItem> _items = new();
    public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();
    public CustomerId CustomerId { get; private set; }
    public OrderStatus Status { get; private set; }
    public Money Total { get; private set; }
    
    public static Order Create(CustomerId customerId, ShippingAddress address)
    {
        var order = new Order 
        {
            Id = OrderId.New(),
            CustomerId = customerId,
            Status = OrderStatus.Draft,
            Total = Money.Zero()
        };
        
        order.AddDomainEvent(new OrderCreatedEvent(order.Id, customerId));
        return order;
    }
    
    public void AddItem(ProductId productId, Money unitPrice, Quantity quantity)
    {
        if (Status != OrderStatus.Draft)
            throw new InvalidOperationException("Não é possível modificar pedido em processamento");
            
        var existingItem = _items.FirstOrDefault(x => x.ProductId == productId);
        if (existingItem != null)
        {
            existingItem.ChangeQuantity(existingItem.Quantity + quantity);
        }
        else
        {
            _items.Add(new OrderItem(productId, unitPrice, quantity));
        }
        
        RecalculateTotal();
        AddDomainEvent(new ItemAddedToOrderEvent(Id, productId, quantity));
    }
    
    public Result ProcessPayment(PaymentMethod paymentMethod)
    {
        if (Status != OrderStatus.Draft)
            return Result.Failure("Pedido já está em processamento");
            
        if (!Items.Any())
            return Result.Failure("Pedido não possui itens");
            
        Status = OrderStatus.PaymentPending;
        AddDomainEvent(new PaymentRequestedEvent(Id, Total, paymentMethod));
        
        return Result.Success();
    }
}

// Application Layer - Use Case orquestrando o fluxo
[UseCase]
public class PlaceOrderUseCase
{
    private readonly IOrderRepository _orderRepository;
    private readonly IInventoryService _inventoryService;
    private readonly IPaymentGateway _paymentGateway;
    private readonly IDomainEventPublisher _eventPublisher;
    
    public async Task<Result<OrderId>> ExecuteAsync(PlaceOrderCommand command)
    {
        // Validar disponibilidade no estoque
        var availabilityCheck = await _inventoryService.CheckAvailabilityAsync(command.Items);
        if (!availabilityCheck.IsSuccess)
            return Result.Failure<OrderId>(availabilityCheck.Error);
        
        // Criar pedido
        var order = Order.Create(command.CustomerId, command.ShippingAddress);
        
        // Adicionar itens
        foreach (var item in command.Items)
        {
            order.AddItem(item.ProductId, item.UnitPrice, item.Quantity);
        }
        
        // Processar pagamento
        var paymentResult = order.ProcessPayment(command.PaymentMethod);
        if (!paymentResult.IsSuccess)
            return Result.Failure<OrderId>(paymentResult.Error);
        
        // Persistir pedido
        await _orderRepository.AddAsync(order);
        await _orderRepository.UnitOfWork.SaveChangesAsync();
        
        // Publicar eventos de domínio
        await _eventPublisher.PublishAsync(order.DomainEvents);
        
        return Result.Success(order.Id);
    }
}
```

### Sistema Bancário com Event Sourcing

```java
// Event Store implementation
public class AccountEventStore {
    private final EventRepository eventRepository;
    
    public void saveEvents(UUID aggregateId, List<DomainEvent> events, int expectedVersion) {
        // Validar versão para concorrência otimista
        int currentVersion = getVersion(aggregateId);
        if (currentVersion != expectedVersion) {
            throw new ConcurrencyException("Versão esperada não confere com atual");
        }
        
        // Persistir eventos atomicamente
        for (int i = 0; i < events.size(); i++) {
            EventData eventData = new EventData(
                aggregateId,
                events.get(i).getClass().getSimpleName(),
                JsonSerializer.serialize(events.get(i)),
                currentVersion + i + 1
            );
            eventRepository.save(eventData);
        }
    }
    
    public Account loadAggregate(UUID aggregateId) {
        List<EventData> eventHistory = eventRepository.findByAggregateId(aggregateId);
        List<DomainEvent> events = eventHistory.stream()
            .map(this::deserializeEvent)
            .collect(Collectors.toList());
            
        return Account.fromHistory(aggregateId, events);
    }
}

// Projeção para queries otimizadas
@EventHandler
public class AccountProjectionHandler {
    private final AccountProjectionRepository projectionRepository;
    
    @Handle
    public void on(MoneyDepositedEvent event) {
        AccountProjection projection = projectionRepository.findById(event.getAccountId())
            .orElse(new AccountProjection(event.getAccountId()));
            
        projection.setBalance(projection.getBalance().add(event.getAmount()));
        projection.setLastUpdated(event.getTimestamp());
        
        projectionRepository.save(projection);
    }
}
```

Este guia completo de Clean Architecture oferece uma base sólida para implementação em projetos brasileiros, **combinando teoria sólida com exemplos práticos**, estratégias de migração comprovadas e ferramentas modernas. A arquitetura limpa, quando bem implementada, resulta em sistemas mais testáveis, manuteníveis e adaptáveis às mudanças de requisitos de negócio.