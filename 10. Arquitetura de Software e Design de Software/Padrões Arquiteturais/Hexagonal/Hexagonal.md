![[img-hexagonal-1.png]]![[img-hexagonal-2.png]]

# 🔷 Guia Completo das Camadas da Arquitetura Hexagonal

## 📊 Visão Geral da Arquitetura

```
┌─────────────────────────────────────────────────────────────┐
│                     CAMADA EXTERNA                           │
│  (Interface Adapters / Infrastructure)                       │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                 CAMADA DE APLICAÇÃO                  │    │
│  │              (Application Services)                  │    │
│  │                                                     │    │
│  │  ┌─────────────────────────────────────────────┐   │    │
│  │  │           CAMADA DE DOMÍNIO                 │   │    │
│  │  │         (Core / Business Logic)             │   │    │
│  │  │                                             │   │    │
│  │  └─────────────────────────────────────────────┘   │    │
│  │                                                     │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
└─────────────────────────────────────────────────────────────┘

Fluxo: Outside → Adapters → Ports → Application → Domain
```

---

# 🎯 1. CAMADA DE DOMÍNIO (CORE)

## O que é?

O **coração da aplicação** onde vive toda a lógica de negócio. É completamente independente de frameworks, bancos de dados ou qualquer tecnologia externa.

## Componentes Principais

### 1.1 **Entities (Entidades)**

**O que são:** Objetos com identidade única que representam conceitos centrais do negócio.

**Características:**

- Possuem ID único
- Mantêm estado ao longo do tempo
- Encapsulam regras de negócio
- São mutáveis

**Quando usar:**

- Quando o objeto precisa ser rastreado ao longo do tempo
- Quando a identidade importa mais que os atributos
- Para representar conceitos principais do domínio

**Exemplo Prático:**

```java
// ENTIDADE - Representa um conceito central do negócio
public class Cliente {
    private ClienteId id;          // Identidade única
    private String nome;
    private Email email;           // Value Object
    private CPF cpf;               // Value Object
    private StatusCliente status;
    private LocalDate dataCadastro;
    
    // Regra de negócio encapsulada
    public void ativar() {
        if (this.email == null || !this.email.isValido()) {
            throw new ClienteInvalidoException("Email é obrigatório para ativar cliente");
        }
        this.status = StatusCliente.ATIVO;
    }
    
    // Comportamento de negócio
    public boolean podeRealizarCompra() {
        return this.status == StatusCliente.ATIVO 
            && !temPendenciaFinanceira();
    }
    
    public void atualizarEmail(Email novoEmail) {
        // Validação de negócio
        if (novoEmail == null || !novoEmail.isValido()) {
            throw new EmailInvalidoException();
        }
        this.email = novoEmail;
    }
}
```

### 1.2 **Value Objects**

**O que são:** Objetos imutáveis sem identidade, definidos apenas por seus atributos.

**Características:**

- Imutáveis
- Sem identidade própria
- Comparados por valor
- Auto-validáveis

**Quando usar:**

- Para representar conceitos que são medidos/descritos
- Quando não precisa rastrear mudanças
- Para garantir consistência de valores

**Exemplo Prático:**

```java
// VALUE OBJECT - Imutável e auto-validável
public final class Dinheiro {
    private final BigDecimal valor;
    private final Moeda moeda;
    
    public Dinheiro(BigDecimal valor, Moeda moeda) {
        // Auto-validação
        if (valor == null || valor.compareTo(BigDecimal.ZERO) < 0) {
            throw new ValorInvalidoException("Valor não pode ser negativo");
        }
        this.valor = valor;
        this.moeda = Objects.requireNonNull(moeda);
    }
    
    // Imutabilidade - retorna novo objeto
    public Dinheiro somar(Dinheiro outro) {
        validarMesmaMoeda(outro);
        return new Dinheiro(
            this.valor.add(outro.valor), 
            this.moeda
        );
    }
    
    public Dinheiro aplicarDesconto(Porcentagem desconto) {
        BigDecimal valorDesconto = valor.multiply(desconto.toDecimal());
        return new Dinheiro(valor.subtract(valorDesconto), moeda);
    }
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Dinheiro)) return false;
        Dinheiro dinheiro = (Dinheiro) o;
        return valor.compareTo(dinheiro.valor) == 0 
            && moeda.equals(dinheiro.moeda);
    }
}
```

### 1.3 **Domain Services**

**O que são:** Classes que executam operações de negócio que não pertencem naturalmente a uma entidade.

**Quando usar:**

- Operação envolve múltiplas entidades
- Lógica não se encaixa em nenhuma entidade
- Cálculos complexos de negócio

**Exemplo Prático:**

```java
// DOMAIN SERVICE - Lógica que não pertence a uma entidade específica
public class ServicoCalculoFrete {
    
    public Dinheiro calcularFrete(
        Pedido pedido, 
        EnderecoEntrega destino,
        TipoEntrega tipoEntrega
    ) {
        // Lógica complexa envolvendo múltiplas entidades
        Dinheiro valorBase = calcularValorBasePorDistancia(
            pedido.getEnderecoOrigem(), 
            destino
        );
        
        Dinheiro valorPeso = calcularAdicionalPorPeso(
            pedido.getPesoTotal()
        );
        
        Dinheiro valorUrgencia = calcularAdicionalUrgencia(
            tipoEntrega
        );
        
        return valorBase
            .somar(valorPeso)
            .somar(valorUrgencia);
    }
    
    private Dinheiro calcularValorBasePorDistancia(
        Endereco origem, 
        Endereco destino
    ) {
        double distanciaKm = GeoCalculator.distancia(origem, destino);
        
        if (distanciaKm <= 50) {
            return new Dinheiro(new BigDecimal("15.00"), Moeda.BRL);
        } else if (distanciaKm <= 200) {
            return new Dinheiro(new BigDecimal("25.00"), Moeda.BRL);
        } else {
            return new Dinheiro(new BigDecimal("40.00"), Moeda.BRL);
        }
    }
}
```

### 1.4 **Aggregates (Agregados)**

**O que são:** Cluster de entidades e value objects tratados como uma unidade.

**Características:**

- Possui uma raiz agregada (Aggregate Root)
- Garante consistência interna
- Define limites transacionais

**Quando usar:**

- Para garantir invariantes de negócio
- Agrupar objetos que mudam juntos
- Definir limites de consistência

**Exemplo Prático:**

```java
// AGGREGATE ROOT - Ponto de entrada do agregado
@AggregateRoot
public class Pedido {
    private PedidoId id;
    private ClienteId clienteId;
    private List<ItemPedido> itens;      // Entidades internas
    private StatusPedido status;
    private Dinheiro valorTotal;
    private EnderecoEntrega enderecoEntrega;
    private LocalDateTime dataCriacao;
    
    // Toda modificação passa pelo Aggregate Root
    public void adicionarItem(Produto produto, int quantidade) {
        // Validação de invariantes
        if (status != StatusPedido.RASCUNHO) {
            throw new PedidoNaoEditavelException();
        }
        
        // Regra de negócio
        if (quantidade <= 0) {
            throw new QuantidadeInvalidaException();
        }
        
        ItemPedido novoItem = new ItemPedido(produto, quantidade);
        
        // Verifica se item já existe
        Optional<ItemPedido> itemExistente = itens.stream()
            .filter(i -> i.getProdutoId().equals(produto.getId()))
            .findFirst();
            
        if (itemExistente.isPresent()) {
            itemExistente.get().aumentarQuantidade(quantidade);
        } else {
            itens.add(novoItem);
        }
        
        // Recalcula invariantes
        recalcularValorTotal();
    }
    
    public void finalizar() {
        // Garante consistência do agregado
        if (itens.isEmpty()) {
            throw new PedidoVazioException();
        }
        
        if (valorTotal.ehMenorQue(VALOR_MINIMO_PEDIDO)) {
            throw new ValorMinimoPedidoException();
        }
        
        this.status = StatusPedido.FINALIZADO;
    }
    
    // Métodos privados mantêm invariantes
    private void recalcularValorTotal() {
        this.valorTotal = itens.stream()
            .map(ItemPedido::getValorTotal)
            .reduce(Dinheiro.ZERO, Dinheiro::somar);
    }
}
```

### 1.5 **Domain Events**

**O que são:** Eventos que representam algo importante que aconteceu no domínio.

**Quando usar:**

- Comunicar mudanças entre agregados
- Implementar eventual consistency
- Criar audit log

**Exemplo Prático:**

```java
// DOMAIN EVENT
public class PedidoFinalizadoEvent extends EventoDominio {
    private final PedidoId pedidoId;
    private final ClienteId clienteId;
    private final Dinheiro valorTotal;
    private final Instant ocorridoEm;
    
    public PedidoFinalizadoEvent(Pedido pedido) {
        this.pedidoId = pedido.getId();
        this.clienteId = pedido.getClienteId();
        this.valorTotal = pedido.getValorTotal();
        this.ocorridoEm = Instant.now();
    }
}

// Uso no agregado
public class Pedido {
    private List<EventoDominio> eventos = new ArrayList<>();
    
    public void finalizar() {
        // ... lógica de finalização ...
        this.status = StatusPedido.FINALIZADO;
        
        // Registra evento
        eventos.add(new PedidoFinalizadoEvent(this));
    }
    
    public List<EventoDominio> obterEventosPendentes() {
        List<EventoDominio> eventosPendentes = new ArrayList<>(eventos);
        eventos.clear();
        return eventosPendentes;
    }
}
```

### 1.6 **Specifications**

**O que são:** Padrão para encapsular regras de negócio reutilizáveis.

**Quando usar:**

- Regras de negócio complexas
- Validações reutilizáveis
- Composição de regras

**Exemplo Prático:**

```java
// SPECIFICATION PATTERN
public interface Specification<T> {
    boolean isSatisfiedBy(T candidate);
    
    default Specification<T> and(Specification<T> other) {
        return candidate -> this.isSatisfiedBy(candidate) 
            && other.isSatisfiedBy(candidate);
    }
}

public class ClienteAtivoSpecification implements Specification<Cliente> {
    @Override
    public boolean isSatisfiedBy(Cliente cliente) {
        return cliente.getStatus() == StatusCliente.ATIVO;
    }
}

public class ClientePremiumSpecification implements Specification<Cliente> {
    @Override
    public boolean isSatisfiedBy(Cliente cliente) {
        return cliente.getTotalCompras().ehMaiorQue(
            new Dinheiro(10000, Moeda.BRL)
        );
    }
}

// Uso combinado
Specification<Cliente> clienteElegivelDesconto = 
    new ClienteAtivoSpecification()
        .and(new ClientePremiumSpecification());
```

---

# 🔄 2. CAMADA DE APLICAÇÃO (APPLICATION)

## O que é?

Orquestra o fluxo da aplicação, coordena o domínio e delega trabalho para a infraestrutura. **Não contém lógica de negócio**.

## Componentes Principais

### 2.1 **Application Services / Use Cases**

**O que são:** Orquestradores que implementam casos de uso específicos.

**Responsabilidades:**

- Coordenar fluxo de trabalho
- Gerenciar transações
- Chamar serviços de domínio
- Publicar eventos
- Fazer log e auditoria

**Quando usar:**

- Para cada caso de uso do sistema
- Operações que envolvem múltiplos agregados
- Coordenação com infraestrutura

**Exemplo Prático:**

```java
// APPLICATION SERVICE - Orquestra um caso de uso
@Service
@Transactional
public class FinalizarPedidoUseCase {
    private final PedidoRepository pedidoRepository;
    private final ClienteRepository clienteRepository;
    private final ServicoCalculoFrete servicoFrete;
    private final GatewayPagamento gatewayPagamento;
    private final ServicoNotificacao servicoNotificacao;
    private final PublicadorEventos publicadorEventos;
    
    public PedidoId executar(FinalizarPedidoCommand comando) {
        // 1. Busca agregados necessários
        Pedido pedido = pedidoRepository.buscarPorId(comando.getPedidoId())
            .orElseThrow(() -> new PedidoNaoEncontradoException());
            
        Cliente cliente = clienteRepository.buscarPorId(pedido.getClienteId())
            .orElseThrow(() -> new ClienteNaoEncontradoException());
        
        // 2. Valida pré-condições (delega ao domínio)
        if (!cliente.podeRealizarCompra()) {
            throw new ClienteImpedidoCompraException();
        }
        
        // 3. Calcula frete (usa domain service)
        Dinheiro valorFrete = servicoFrete.calcularFrete(
            pedido, 
            comando.getEnderecoEntrega(),
            comando.getTipoEntrega()
        );
        
        // 4. Aplica frete ao pedido (lógica no domínio)
        pedido.aplicarFrete(valorFrete);
        
        // 5. Processa pagamento (infraestrutura)
        ResultadoPagamento resultado = gatewayPagamento.processar(
            new SolicitacaoPagamento(
                pedido.getValorTotal(),
                comando.getDadosPagamento()
            )
        );
        
        if (!resultado.foiAprovado()) {
            throw new PagamentoRecusadoException(resultado.getMotivo());
        }
        
        // 6. Finaliza pedido (domínio)
        pedido.finalizar();
        pedido.registrarPagamento(resultado.getTransacaoId());
        
        // 7. Persiste mudanças
        Pedido pedidoSalvo = pedidoRepository.salvar(pedido);
        
        // 8. Publica eventos
        publicadorEventos.publicarTodos(
            pedido.obterEventosPendentes()
        );
        
        // 9. Envia notificações
        servicoNotificacao.notificarPedidoFinalizado(
            cliente.getEmail(), 
            pedidoSalvo
        );
        
        return pedidoSalvo.getId();
    }
}
```

### 2.2 **Commands e Queries (CQRS)**

**O que são:** DTOs que representam intenções de mudança (Commands) ou consulta (Queries).

**Commands - Para modificações:**

```java
// COMMAND - Intenção de mudança
public class CriarProdutoCommand {
    @NotNull
    private String nome;
    
    @NotNull
    @Positive
    private BigDecimal preco;
    
    @NotNull
    private String categoria;
    
    private String descricao;
    
    // Validação adicional
    public void validar() {
        if (nome.length() < 3) {
            throw new ComandoInvalidoException("Nome deve ter no mínimo 3 caracteres");
        }
    }
}

// Handler do Command
@Service
public class CriarProdutoCommandHandler {
    @Transactional
    public ProdutoId handle(CriarProdutoCommand comando) {
        comando.validar();
        
        // Cria entidade de domínio
        Produto produto = new Produto(
            comando.getNome(),
            new Dinheiro(comando.getPreco(), Moeda.BRL),
            Categoria.valueOf(comando.getCategoria())
        );
        
        // Persiste
        Produto salvo = produtoRepository.salvar(produto);
        
        return salvo.getId();
    }
}
```

**Queries - Para consultas:**

```java
// QUERY - Intenção de consulta
public class BuscarProdutosPorCategoriaQuery {
    private final String categoria;
    private final int pagina;
    private final int tamanhoPagina;
    
    // getters, validação...
}

// Handler da Query
@Service
public class BuscarProdutosPorCategoriaQueryHandler {
    
    public PaginaResultado<ProdutoDTO> handle(BuscarProdutosPorCategoriaQuery query) {
        // Busca otimizada para leitura
        return produtoReadRepository.buscarPorCategoria(
            query.getCategoria(),
            PageRequest.of(query.getPagina(), query.getTamanhoPagina())
        );
    }
}
```

### 2.3 **DTOs (Data Transfer Objects)**

**O que são:** Objetos para transferir dados entre camadas.

**Quando usar:**

- Comunicação com mundo externo
- Otimização de consultas
- Versionamento de API

```java
// DTO para resposta
public class ProdutoDTO {
    private String id;
    private String nome;
    private BigDecimal preco;
    private String moeda;
    private String categoria;
    private String status;
    
    // Método factory para converter do domínio
    public static ProdutoDTO fromDomain(Produto produto) {
        ProdutoDTO dto = new ProdutoDTO();
        dto.id = produto.getId().getValue();
        dto.nome = produto.getNome();
        dto.preco = produto.getPreco().getValor();
        dto.moeda = produto.getPreco().getMoeda().getCodigo();
        dto.categoria = produto.getCategoria().name();
        dto.status = produto.getStatus().name();
        return dto;
    }
}
```

---

# 🔌 3. PORTS (PORTAS)

## O que são?

Interfaces que definem contratos entre as camadas. São os "contratos" que permitem a inversão de dependência.

## Tipos de Ports

### 3.1 **Primary/Driving Ports (Portas de Entrada)**

**O que são:** Interfaces que definem o que a aplicação pode fazer.

**Implementadas por:** Application Services

**Usadas por:** Adapters de entrada (Controllers, CLI, etc.)

```java
// PRIMARY PORT - Define capacidades da aplicação
public interface GestãoProdutos {
    // Comandos
    ProdutoId criarProduto(CriarProdutoCommand comando);
    void atualizarProduto(AtualizarProdutoCommand comando);
    void excluirProduto(ProdutoId id);
    
    // Consultas
    ProdutoDetalhes buscarProduto(ProdutoId id);
    ListaPaginada<ProdutoResumo> listarProdutos(FiltrosProduto filtros);
    EstatisticasProdutos obterEstatisticas();
}

// Implementação (Application Service)
@Service
public class GestãoProdutosService implements GestãoProdutos {
    // Implementa todos os métodos definidos no port
}
```

### 3.2 **Secondary/Driven Ports (Portas de Saída)**

**O que são:** Interfaces que definem o que a aplicação precisa do mundo externo.

**Implementadas por:** Adapters de infraestrutura

**Usadas por:** Application Services e Domain Services

```java
// SECONDARY PORT - Define necessidades da aplicação
public interface ProdutoRepository {
    Produto salvar(Produto produto);
    Optional<Produto> buscarPorId(ProdutoId id);
    List<Produto> buscarPorCategoria(Categoria categoria);
    void excluir(ProdutoId id);
    boolean existe(ProdutoId id);
}

// SECONDARY PORT - Serviço externo
public interface ServicoEmail {
    void enviar(Email destinatario, String assunto, String corpo);
    void enviarComAnexo(Email destinatario, String assunto, String corpo, Anexo anexo);
}

// SECONDARY PORT - Sistema de filas
public interface PublicadorEventos {
    void publicar(EventoDominio evento);
    void publicarTodos(List<EventoDominio> eventos);
}
```

---

# 🔧 4. ADAPTERS (ADAPTADORES)

## O que são?

Implementações concretas dos Ports. Fazem a ponte entre o mundo externo e a aplicação.

## Tipos de Adapters

### 4.1 **Input/Primary Adapters (Interface)**

**Responsabilidade:** Receber requisições externas e convertê-las para a aplicação.

#### REST Controller Adapter

```java
@RestController
@RequestMapping("/api/v1/produtos")
public class ProdutoRestController {
    private final GestãoProdutos gestãoProdutos;
    
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public ProdutoResponse criarProduto(@Valid @RequestBody CriarProdutoRequest request) {
        // Converte request HTTP para Command
        CriarProdutoCommand comando = CriarProdutoCommand.builder()
            .nome(request.getNome())
            .preco(request.getPreco())
            .categoria(request.getCategoria())
            .build();
        
        // Chama o port
        ProdutoId produtoId = gestãoProdutos.criarProduto(comando);
        
        // Converte resultado para response HTTP
        return new ProdutoResponse(produtoId.getValue(), "Produto criado com sucesso");
    }
    
    @GetMapping("/{id}")
    public ProdutoDetalhesResponse buscarProduto(@PathVariable String id) {
        ProdutoDetalhes detalhes = gestãoProdutos.buscarProduto(new ProdutoId(id));
        return ProdutoDetalhesResponse.from(detalhes);
    }
}
```

#### GraphQL Adapter

```java
@Component
public class ProdutoGraphQLResolver implements GraphQLQueryResolver {
    private final GestãoProdutos gestãoProdutos;
    
    public ProdutoType produto(String id) {
        ProdutoDetalhes detalhes = gestãoProdutos.buscarProduto(new ProdutoId(id));
        return GraphQLMapper.toGraphQL(detalhes);
    }
    
    @DgsQuery
    public List<ProdutoType> produtos(
        @InputArgument String categoria,
        @InputArgument Integer limite
    ) {
        FiltrosProduto filtros = new FiltrosProduto(categoria, limite);
        return gestãoProdutos.listarProdutos(filtros)
            .stream()
            .map(GraphQLMapper::toGraphQL)
            .collect(Collectors.toList());
    }
}
```

#### CLI Adapter

```java
@Component
public class ProdutoCLIAdapter {
    private final GestãoProdutos gestãoProdutos;
    
    @ShellMethod("Criar novo produto")
    public String criarProduto(
        @ShellOption String nome,
        @ShellOption BigDecimal preco,
        @ShellOption String categoria
    ) {
        CriarProdutoCommand comando = new CriarProdutoCommand(nome, preco, categoria);
        ProdutoId id = gestãoProdutos.criarProduto(comando);
        return "Produto criado com ID: " + id.getValue();
    }
}
```

#### Message Queue Listener Adapter

```java
@Component
public class ProdutoMessageListener {
    private final GestãoProdutos gestãoProdutos;
    
    @RabbitListener(queues = "produtos.comandos")
    public void processarComando(ComandoProdutoMessage message) {
        switch (message.getTipo()) {
            case CRIAR:
                gestãoProdutos.criarProduto(converterParaCriarCommand(message));
                break;
            case ATUALIZAR:
                gestãoProdutos.atualizarProduto(converterParaAtualizarCommand(message));
                break;
            case EXCLUIR:
                gestãoProdutos.excluirProduto(new ProdutoId(message.getProdutoId()));
                break;
        }
    }
    
    @KafkaListener(topics = "produtos.eventos", groupId = "produto-service")
    public void processarEvento(String eventoJson) {
        // Processa eventos de outros serviços
    }
}
```

### 4.2 **Output/Secondary Adapters (Infrastructure)**

**Responsabilidade:** Implementar as necessidades de infraestrutura da aplicação.

#### Database Adapter (JPA)

```java
@Repository
public class ProdutoJPAAdapter implements ProdutoRepository {
    private final ProdutoJPARepository jpaRepository;
    private final ProdutoEntityMapper mapper;
    
    @Override
    public Produto salvar(Produto produto) {
        // Converte domínio para entidade JPA
        ProdutoEntity entity = mapper.toEntity(produto);
        
        // Salva no banco
        ProdutoEntity saved = jpaRepository.save(entity);
        
        // Converte de volta para domínio
        return mapper.toDomain(saved);
    }
    
    @Override
    public Optional<Produto> buscarPorId(ProdutoId id) {
        return jpaRepository.findById(id.getValue())
            .map(mapper::toDomain);
    }
}

// Entidade JPA
@Entity
@Table(name = "produtos")
public class ProdutoEntity {
    @Id
    private String id;
    
    @Column(nullable = false)
    private String nome;
    
    @Column(precision = 10, scale = 2)
    private BigDecimal preco;
    
    @Column(length = 3)
    private String moeda;
}
```

#### NoSQL Adapter (MongoDB)

```java
@Repository
public class ProdutoMongoAdapter implements ProdutoRepository {
    private final MongoTemplate mongoTemplate;
    
    @Override
    public Produto salvar(Produto produto) {
        ProdutoDocument document = ProdutoDocument.from(produto);
        ProdutoDocument saved = mongoTemplate.save(document);
        return saved.toDomain();
    }
    
    @Override
    public Optional<Produto> buscarPorId(ProdutoId id) {
        Query query = new Query(Criteria.where("_id").is(id.getValue()));
        ProdutoDocument document = mongoTemplate.findOne(query, ProdutoDocument.class);
        return Optional.ofNullable(document).map(ProdutoDocument::toDomain);
    }
}
```

#### External Service Adapter

```java
@Component
public class SendGridEmailAdapter implements ServicoEmail {
    private final SendGrid sendGrid;
    
    @Override
    public void enviar(Email destinatario, String assunto, String corpo) {
        Mail mail = new Mail(
            new com.sendgrid.helpers.mail.objects.Email("noreply@empresa.com"),
            assunto,
            new com.sendgrid.helpers.mail.objects.Email(destinatario.getValor()),
            new Content("text/html", corpo)
        );
        
        try {
            Request request = new Request();
            request.setMethod(Method.POST);
            request.setEndpoint("mail/send");
            request.setBody(mail.build());
            
            Response response = sendGrid.api(request);
            
            if (response.getStatusCode() >= 400) {
                throw new FalhaEnvioEmailException(
                    "Erro ao enviar email: " + response.getBody()
                );
            }
        } catch (IOException e) {
            throw new FalhaEnvioEmailException("Erro de comunicação com SendGrid", e);
        }
    }
}
```

#### Cache Adapter

```java
@Component
public class RedisCacheAdapter implements CacheProduto {
    private final RedisTemplate<String, String> redisTemplate;
    private final ObjectMapper objectMapper;
    
    @Override
    public void salvar(ProdutoId id, Produto produto, Duration ttl) {
        try {
            String json = objectMapper.writeValueAsString(produto);
            redisTemplate.opsForValue().set(
                chaveCache(id),
                json,
                ttl
            );
        } catch (JsonProcessingException e) {
            log.error("Erro ao serializar produto para cache", e);
        }
    }
    
    @Override
    public Optional<Produto> buscar(ProdutoId id) {
        String json = redisTemplate.opsForValue().get(chaveCache(id));
        if (json == null) {
            return Optional.empty();
        }
        
        try {
            Produto produto = objectMapper.readValue(json, Produto.class);
            return Optional.of(produto);
        } catch (JsonProcessingException e) {
            log.error("Erro ao deserializar produto do cache", e);
            return Optional.empty();
        }
    }
    
    private String chaveCache(ProdutoId id) {
        return "produto:" + id.getValue();
    }
}
```

#### Message Publisher Adapter

```java
@Component
public class RabbitMQEventPublisher implements PublicadorEventos {
    private final RabbitTemplate rabbitTemplate;
    private final ObjectMapper objectMapper;
    
    @Override
    public void publicar(EventoDominio evento) {
        try {
            String eventoJson = objectMapper.writeValueAsString(evento);
            
            rabbitTemplate.convertAndSend(
                "eventos-dominio",  // exchange
                evento.getClass().getSimpleName(), // routing key
                eventoJson
            );
            
            log.info("Evento publicado: {}", evento.getClass().getSimpleName());
        } catch (JsonProcessingException e) {
            throw new FalhaPublicacaoEventoException(
                "Erro ao serializar evento", e
            );
        }
    }
}
```

---

# 📋 QUANDO USAR CADA CAMADA

## Guia de Decisão por Cenário

### Cenário 1: **Nova Funcionalidade de Negócio**

```
1. Começa no DOMÍNIO
   → Criar/modificar Entidades e Value Objects
   → Implementar regras no Aggregate
   → Criar Domain Events se necessário

2. Define o PORT primário
   → Interface do caso de uso

3. Implementa APPLICATION SERVICE
   → Orquestra a lógica

4. Cria ADAPTER de entrada
   → REST Controller ou GraphQL Resolver

5. Se precisar persistência/serviços externos
   → Define PORT secundário
   → Implementa ADAPTER de saída
```

### Cenário 2: **Mudança de Tecnologia**

```
Exemplo: Trocar de PostgreSQL para MongoDB

1. Mantém PORT (Repository interface) ✓
2. Cria novo ADAPTER (MongoAdapter) ✓
3. Domain e Application NÃO MUDAM ✓
```

### Cenário 3: **Nova Interface de Entrada**

```
Exemplo: Adicionar GraphQL além de REST

1. Domain NÃO MUDA ✓
2. Application NÃO MUDA ✓  
3. Ports NÃO MUDAM ✓
4. Adiciona novo ADAPTER (GraphQLResolver) ✓
```

### Cenário 4: **Regra de Negócio Complexa**

```
1. Implementa no DOMÍNIO
   → Domain Service se envolver múltiplas entidades
   → Método na Entidade se for específico dela
   → Specification se for regra reutilizável

2. Application Service apenas chama
```

## Fluxo de Requisição Completo

```
1. REQUEST chega no sistema
   ↓
2. PRIMARY ADAPTER recebe (ex: REST Controller)
   ↓
3. Converte para Command/Query
   ↓
4. Chama PRIMARY PORT
   ↓
5. APPLICATION SERVICE processa
   ↓
6. Chama lógica do DOMÍNIO
   ↓
7. DOMÍNIO executa regras de negócio
   ↓
8. APPLICATION chama SECONDARY PORTS (se necessário)
   ↓
9. SECONDARY ADAPTERS executam (DB, APIs, etc.)
   ↓
10. Resultado volta pelo caminho inverso
   ↓
11. PRIMARY ADAPTER converte para Response
   ↓
12. RESPONSE retorna ao cliente
```

---

# ⚡ DECISÕES RÁPIDAS

## Onde Colocar Cada Tipo de Código

|Tipo de Código|Camada|Exemplo|
|---|---|---|
|Validação de formato|Adapter de Entrada|Email válido, CPF formato|
|Validação de negócio|Domínio|Cliente pode comprar|
|Cálculos de negócio|Domínio|Calcular desconto|
|Orquestração|Application|Processar pedido completo|
|Conversão de dados|Adapters|JSON → Command|
|Acesso a banco|Adapter de Saída|JPA Repository|
|Chamada API externa|Adapter de Saída|REST Client|
|Autenticação|Adapter de Entrada|JWT validation|
|Autorização|Application|User can access|
|Transação|Application|@Transactional|
|Cache|Adapter de Saída|Redis Adapter|
|Log técnico|Adapters|HTTP logs|
|Log de auditoria|Application|Ação do usuário|
|Eventos de negócio|Domínio|PedidoCriado|
|Publicação eventos|Adapter de Saída|RabbitMQ|

## Sinais de Problemas na Arquitetura

### ❌ ERRADO

- Entidades com anotações JPA
- Domain Services chamando APIs
- Controllers com lógica de negócio
- Application Services com cálculos
- Adapters decidindo fluxo

### ✅ CORRETO

- Entidades puras (POJOs)
- Domain Services com lógica pura
- Controllers só convertem dados
- Application Services só orquestram
- Adapters só traduzem

---

# 🎯 RESUMO EXECUTIVO

## Princípio Fundamental

**"O Domínio não conhece ninguém, todos conhecem o Domínio"**

## Responsabilidades Principais

|Camada|Responsabilidade|Não Deve|
|---|---|---|
|**Domain**|Regras de negócio|Conhecer infraestrutura|
|**Application**|Orquestração|Ter lógica de negócio|
|**Ports**|Contratos|Ter implementação|
|**Adapters**|Tradução/Integração|Ter lógica de negócio|

## Benefícios por Camada

### Domain

✅ Testável sem mocks  
✅ Reutilizável  
✅ Expressa o negócio

### Application

✅ Casos de uso claros  
✅ Transações controladas  
✅ Fluxo documentado

### Ports

✅ Contratos explícitos  
✅ Inversão de dependência  
✅ Flexibilidade

### Adapters

✅ Substituíveis  
✅ Tecnologia isolada  
✅ Evolução independente

## Quando Vale a Pena

### USE Arquitetura Hexagonal quando:

- ✅ Domínio complexo
- ✅ Múltiplas interfaces (REST, GraphQL, CLI)
- ✅ Longevidade esperada (anos)
- ✅ Equipe média/grande
- ✅ Necessidade de testes robustos

### NÃO USE quando:

- ❌ CRUD simples
- ❌ Protótipo/MVP
- ❌ Equipe inexperiente
- ❌ Prazo muito curto
- ❌ Domínio trivial