
## Estratégias de Testes

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