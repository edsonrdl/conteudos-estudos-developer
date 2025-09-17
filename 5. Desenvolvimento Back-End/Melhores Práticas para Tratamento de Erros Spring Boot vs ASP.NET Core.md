# Melhores Práticas para Tratamento de Erros: Spring Boot vs ASP.NET Core

Aplicações modernas exigem estratégias sofisticadas de tratamento de erros que vão além do tradicional try-catch. Esta análise compara as abordagens de Spring Boot e ASP.NET Core, explorando padrões funcionais modernos e implementações práticas para sistemas distribuídos.

## Try-catch versus padrões funcionais de erro

### Limitações do try-catch tradicional

O modelo tradicional de exceções apresenta problemas significativos em aplicações modernas. **Exceções são 750x mais lentas** que verificações de valor de retorno no CLR do Windows, criando gargalos de performance em cenários de alta falha. Em sistemas distribuídos, where failures are expected rather than exceptional, this overhead becomes prohibitive.

**Performance comparativo por taxa de falha:**

- 0% falhas: Try-catch 37% mais lento (overhead base)
- 10% falhas: Padrões funcionais 323% mais rápidos
- 50% falhas: Padrões funcionais 1175% mais rápidos

### Result Pattern e Either monad

O **Result Pattern** encapsula resultados de operações em tipos que representam explicitamente sucesso ou falha, eliminando dependência de exceções para fluxo de controle.

```csharp
// Result Pattern em C#
public sealed record Error(string Code, string Description);

public class Result<T>
{
    public bool IsSuccess { get; }
    public bool IsFailure => !IsSuccess;
    public T Value { get; }
    public Error Error { get; }

    public static Result<T> Success(T value) => new(true, value, Error.None);
    public static Result<T> Failure(Error error) => new(false, default, error);
}

// Uso prático
public async Task<Result<User>> GetUserAsync(Guid id)
{
    var validation = ValidateId(id);
    if (validation.IsFailure) return Result<User>.Failure(validation.Error);
    
    var user = await _repository.GetByIdAsync(id);
    return user != null 
        ? Result<User>.Success(user)
        : Result<User>.Failure(UserErrors.NotFound(id));
}
```

O **Either monad** oferece capacidades mais sofisticadas, permitindo **acumulação de erros** e composição funcional elegante através de operações bind e map.

## Tratamento de erros em Spring Boot

### @ControllerAdvice e @ExceptionHandler modernos

Spring Boot 3.x introduziu suporte nativo para **RFC 9457 Problem Details**, substituindo o RFC 7807 com melhor padronização.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ProblemDetail> handleResourceNotFound(
        ResourceNotFoundException ex, WebRequest request) {
        
        ProblemDetail problemDetail = ProblemDetail.forStatusAndDetail(
            HttpStatus.NOT_FOUND, ex.getMessage());
        problemDetail.setTitle("Resource Not Found");
        problemDetail.setInstance(URI.create(request.getDescription(false)));
        problemDetail.setProperty("errorCode", ex.getErrorCode());
        problemDetail.setProperty("timestamp", Instant.now());
        
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(problemDetail);
    }
    
    @ExceptionHandler(ValidationException.class)
    public ResponseEntity<ValidationProblemDetails> handleValidation(
        ValidationException ex) {
        
        ValidationProblemDetails details = new ValidationProblemDetails();
        ex.getErrors().forEach(error -> 
            details.getErrors().put(error.getField(), 
                List.of(error.getMessage())));
        
        return ResponseEntity.badRequest().body(details);
    }
}
```

### Padrões de ResponseEntity customizados

Para APIs REST consistentes, implemente um builder pattern para respostas de erro estruturadas:

```java
public class ErrorResponseBuilder {
    public static ResponseEntity<ApiErrorResponse> build(
        HttpStatus status, String message, String path) {
        
        ApiErrorResponse error = ApiErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(status.value())
            .error(status.getReasonPhrase())
            .message(message)
            .path(path)
            .traceId(getCurrentTraceId())
            .build();
            
        return ResponseEntity.status(status)
            .header("X-Error-Source", "application")
            .body(error);
    }
}
```

### Logging e observabilidade com Spring Boot

Configure logging estruturado com correlação de trace para melhor observabilidade:

```java
@RestControllerAdvice
public class ObservableExceptionHandler {
    private static final Logger logger = LoggerFactory.getLogger(ObservableExceptionHandler.class);
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleException(
        Exception ex, HttpServletRequest request) {
        
        String traceId = TraceContext.current().traceId();
        
        // Logging estruturado com MDC
        MDC.put("errorType", ex.getClass().getSimpleName());
        MDC.put("httpMethod", request.getMethod());
        MDC.put("requestUri", request.getRequestURI());
        
        logger.error("Unhandled exception occurred", ex);
        MDC.clear();
        
        return createErrorResponse(ex, traceId);
    }
}
```

## Tratamento de erros em ASP.NET Core

### IExceptionHandler interface (novo no .NET 8)

ASP.NET Core 8 introduziu a interface **IExceptionHandler** como a abordagem moderna preferida para tratamento global de exceções:

```csharp
public class GlobalExceptionHandler : IExceptionHandler
{
    private readonly ILogger<GlobalExceptionHandler> _logger;

    public async ValueTask<bool> TryHandleAsync(
        HttpContext httpContext,
        Exception exception,
        CancellationToken cancellationToken)
    {
        _logger.LogError(exception, "Unhandled exception occurred");

        var statusCode = exception switch
        {
            ArgumentException => StatusCodes.Status400BadRequest,
            KeyNotFoundException => StatusCodes.Status404NotFound,
            UnauthorizedAccessException => StatusCodes.Status401Unauthorized,
            _ => StatusCodes.Status500InternalServerError
        };

        var problemDetails = new ProblemDetails
        {
            Status = statusCode,
            Title = GetTitle(exception),
            Detail = exception.Message,
            Type = exception.GetType().Name,
            Instance = httpContext.Request.Path
        };

        httpContext.Response.StatusCode = statusCode;
        await httpContext.Response.WriteAsJsonAsync(problemDetails, cancellationToken);
        
        return true; // Exception handled
    }
}

// Registro no Program.cs
builder.Services.AddExceptionHandler<GlobalExceptionHandler>();
builder.Services.AddProblemDetails();
app.UseExceptionHandler();
```

### Problem Details (RFC 7807/9457)

Configure Problem Details personalizados para respostas consistentes:

```csharp
builder.Services.AddProblemDetails(options =>
{
    options.CustomizeProblemDetails = context =>
    {
        context.ProblemDetails.Instance = 
            $"{context.HttpContext.Request.Method} {context.HttpContext.Request.Path}";
        
        context.ProblemDetails.Extensions.TryAdd("requestId", 
            context.HttpContext.TraceIdentifier);
        
        var activity = context.HttpContext.Features.Get<IHttpActivityFeature>()?.Activity;
        context.ProblemDetails.Extensions.TryAdd("traceId", activity?.Id);
        
        context.ProblemDetails.Extensions.TryAdd("timestamp", DateTimeOffset.UtcNow);
    };
});
```

### Logging e observabilidade com ASP.NET Core

Integre OpenTelemetry para observabilidade completa:

```csharp
builder.Services.AddOpenTelemetry()
    .WithTracing(tracing =>
    {
        tracing
            .AddAspNetCoreInstrumentation(options =>
            {
                options.RecordException = true;
                options.Filter = context => !context.Request.Path.StartsWithSegments("/health");
            })
            .AddHttpClientInstrumentation()
            .AddEntityFrameworkCoreInstrumentation()
            .AddOtlpExporter();
    })
    .WithMetrics(metrics =>
    {
        metrics
            .AddAspNetCoreInstrumentation()
            .AddRuntimeInstrumentation()
            .AddPrometheusExporter();
    });
```

## Padrões arquiteturais modernos para microserviços

### Circuit Breaker pattern

Para sistemas distribuídos, implemente circuit breakers para resiliência:

```java
// Resilience4j em Spring Boot
@Component
public class ExternalServiceClient {
    
    @CircuitBreaker(name = "payment-service", fallbackMethod = "fallbackPayment")
    @Retry(name = "payment-service")
    @TimeLimiter(name = "payment-service")
    public CompletableFuture<PaymentResult> processPayment(PaymentRequest request) {
        return restTemplate.postForObject("/payment", request, PaymentResult.class);
    }
    
    public CompletableFuture<PaymentResult> fallbackPayment(PaymentRequest request, Exception ex) {
        return CompletableFuture.completedFuture(
            PaymentResult.failure("Payment service unavailable"));
    }
}
```

### Domain-driven design e error handling

Estruture erros por camadas arquiteturais:

```csharp
// Domain Errors
public static class OrderErrors
{
    public static Error InsufficientInventory(int requested, int available) =>
        new("Orders.InsufficientInventory", 
            $"Requested {requested} items, only {available} available");
            
    public static Error CustomerNotFound(Guid customerId) =>
        new("Orders.CustomerNotFound", 
            $"Customer {customerId} not found");
}

// Application Errors
public static class ApplicationErrors
{
    public static Error ValidationFailed(string field, string message) =>
        new("Application.ValidationFailed", $"{field}: {message}");
        
    public static Error ConcurrencyConflict() =>
        new("Application.ConcurrencyConflict", 
            "Resource was modified by another process");
}
```

### Railway-oriented programming

Implemente pipelines de processamento com tratamento elegante de falhas:

```fsharp
let processOrder request =
    Success request
    >>= validateInput
    >>= authenticate  
    >>= authorize
    >>= executeBusinessLogic
    >>= formatResponse
```

## Comparação prática entre plataformas

### Performance e overhead

**Benchmarks de throughput:**

- ASP.NET Core: ~50,000 requests/segundo, 30ms latência média
- Spring Boot: ~35,000 requests/segundo, 45ms latência média

**ASP.NET Core demonstra 43% maior throughput** e significativamente melhor eficiência de memória (~100MB vs >1GB típico do Spring Boot).

### Experiência de desenvolvimento

**Spring Boot** oferece maior flexibilidade e diversidade de ecossistema, ideal para organizações com heritage Java e preferência por open-source.

**ASP.NET Core** proporciona superior produtividade de desenvolvedor com excelente integração ao Visual Studio e abordagens modernas de tratamento de erro.

## Ferramentas e bibliotecas modernas

### Java/Spring Boot ecosystem

- **Vavr**: Try, Either e Option monads para programação funcional
- **Resilience4j**: Circuit breakers, retry, rate limiting
- **Micrometer**: Métricas e observabilidade
- **TestContainers**: Testes de integração com infraestrutura real

### .NET/ASP.NET Core ecosystem

- **LanguageExt**: Programação funcional completa para C#
- **FluentResults**: Result pattern simplificado
- **Polly**: Políticas de resiliência e fault-handling
- **Application Insights**: Monitoramento nativo Azure

## Exemplos de implementação por cenário

### E-commerce: Processamento de pedidos resiliente

```csharp
public async Task<Result<OrderResult>> ProcessOrderAsync(OrderRequest request)
{
    var validation = await ValidateOrder(request);
    if (validation.IsFailure) return Result<OrderResult>.Failure(validation.Error);
    
    var inventory = await ReserveInventory(request.Items);
    if (inventory.IsFailure) return Result<OrderResult>.Failure(inventory.Error);
    
    var payment = await ProcessPayment(request.Payment);
    if (payment.IsFailure) 
    {
        await ReleaseInventory(request.Items); // Compensação
        return Result<OrderResult>.Failure(payment.Error);
    }
    
    return Result<OrderResult>.Success(new OrderResult(request.OrderId));
}
```

### Banking: Tratamento de erros com compliance

```java
@Service
public class TransferService {
    
    @Transactional
    public Result<TransferResult> transfer(TransferRequest request) {
        return validateTransfer(request)
            .flatMap(this::checkFraudRules)
            .flatMap(this::verifyAccountBalances)
            .flatMap(this::executeTransfer)
            .onSuccess(result -> auditLog.logTransfer(result))
            .onFailure(error -> auditLog.logFailedTransfer(request, error));
    }
}
```

## Recomendações finais por cenário

### Use Spring Boot quando:

- Equipe tem forte expertise em Java
- Independência de plataforma é crítica
- Diversidade de ecossistema é necessária
- Integração com sistemas legacy Java

### Use ASP.NET Core quando:

- Performance é prioridade máxima
- Alinhamento com ecossistema Microsoft/Azure
- Timeline de desenvolvimento rápido
- Equipe tem expertise .NET

Ambas as plataformas são enterprise-ready com capacidades robustas de tratamento de erro. ASP.NET Core demonstra vantagens técnicas em performance e abordagens modernas, enquanto Spring Boot oferece maior flexibilidade e diversidade de ecossistema. A decisão deve alinhar com skills da equipe, infraestrutura existente e estratégia tecnológica de longo prazo.