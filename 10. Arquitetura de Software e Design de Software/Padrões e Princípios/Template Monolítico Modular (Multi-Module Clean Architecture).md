# 🏛 Template Monolítico Modular (Multi-Module Clean Architecture)
#arquitetura #clean-architecture #ddd #monolito-modular #maven #design-patterns

> [!info] Visão Geral
> Este é um **template genérico** de arquitetura para um **Monólito Modular** que combina **Clean Architecture** com **Domain-Driven Design (DDD)**. A ideia central é dividir a aplicação em módulos de build fisicamente independentes (módulos Maven, no caso de Java) para que a **regra de dependência da Clean Architecture seja garantida em tempo de compilação** — não apenas por disciplina do time, mas pelo próprio compilador. Este documento usa um exemplo de **loja virtual (e-commerce)** para ilustrar cada camada. Adapte os nomes ao seu domínio.

---

## 1. O que é um Monólito Modular

Um **Monólito Modular** é um meio-termo entre o monólito tradicional e os microsserviços:

```
Monólito Tradicional          Monólito Modular              Microsserviços
─────────────────────         ────────────────────          ──────────────────
Tudo num pacote único    →    Módulos com fronteiras    →    Serviços separados
Sem fronteiras internas       fortes, mas 1 deploy           deploy independente
1 processo, 1 deploy          1 processo, 1 deploy           N processos, N deploys

Problema:                     Vantagem:                      Custo:
acoplamento vira "big         isolamento sem a               complexidade de rede,
ball of mud" com o tempo      complexidade distribuída       observabilidade, DevOps
```

**Por que escolher Monólito Modular:**
- Você quer **fronteiras arquiteturais fortes** (como microsserviços) mas **sem o custo operacional** de sistemas distribuídos (rede, latência, sagas, eventual consistency forçada).
- Um único deploy, um único banco (ou poucos), transações locais simples.
- Se um dia precisar extrair um módulo para um microsserviço, as fronteiras **já estão desenhadas** — a migração é muito mais barata.

---

## 2. A regra inegociável: dependência garantida pelo compilador

O objetivo central desta arquitetura é **impedir fisicamente** que a regra de negócio (o Domínio) importe detalhes de infraestrutura (banco de dados, frameworks web, message brokers, etc.).

```
A regra de dependência da Clean Architecture flui SEMPRE de fora para dentro:

   webapi  →  infrastructure  →  application  →  core (domain)
                                                    ↑
                                          (não conhece ninguém)

O módulo interno NUNCA conhece o externo.
O Domínio (core) não sabe que existe banco, HTTP, Kafka ou Spring.
```

### Por que multi-módulo e não um projeto único?

```
Projeto único (packaging = jar):
  O compilador enxerga TODAS as classes ao mesmo tempo (mesmo classpath).
  → É possível escrever "import jakarta.persistence.Entity" dentro de uma
    regra de negócio e ninguém avisa.
  → O acoplamento indevido aparece meses depois, quando já é caro corrigir.

Multi-módulo (vários jars com dependências declaradas):
  Cada módulo tem seu PRÓPRIO classpath.
  → O módulo "core" só tem no classpath aquilo que ele declara depender
    (ex.: apenas Jackson + JUnit).
  → Qualquer "import" de framework de infraestrutura no core QUEBRA O BUILD
    imediatamente.
  → O compilador vira o guardião da arquitetura, não a boa vontade do dev.
```

> **A frase-chave:** em um monólito modular bem feito, um estagiário que tenta violar a arquitetura **não consegue compilar o projeto**. A fronteira deixa de ser uma convenção e vira uma barreira física.

---

## 3. Os 4 módulos e suas responsabilidades

Usando o exemplo de uma **loja virtual (`shop`)**:

```
┌───────────────────┬────────────────────────────┬─────────────────────────────┐
│ Módulo            │ Responsabilidade           │ Depende de                  │
├───────────────────┼────────────────────────────┼─────────────────────────────┤
│ shop-core         │ Domínio puro: entidades,   │ NADA                        │
│ (domain)          │ regras de negócio, portas  │ (só libs mínimas: Jackson,  │
│                   │ (interfaces/gateways)      │  JUnit p/ teste)            │
├───────────────────┼────────────────────────────┼─────────────────────────────┤
│ shop-application  │ Casos de uso (orquestração │ core                        │
│ (use cases)       │ das regras). Sem framework │                             │
├───────────────────┼────────────────────────────┼─────────────────────────────┤
│ shop-infrastructure│ Adapters: implementa as   │ core + application          │
│ (adapters)        │ portas com tecnologia real │ (JPA, Kafka, Redis, etc.)   │
│                   │ (banco, mensageria, e-mail)│                             │
├───────────────────┼────────────────────────────┼─────────────────────────────┤
│ shop-webapi       │ Entry point: controllers,  │ infrastructure              │
│ (entry point)     │ DTOs, configuração, main() │ (Spring Web, etc.)          │
└───────────────────┴────────────────────────────┴─────────────────────────────┘
```

---

## 4. Estrutura de módulos Maven (visão macro)

```
shop/                                   ← raiz do projeto (aggregator)
│
├── pom.xml                             ← POM PAI: versões, lista de módulos,
│                                          plugins e configurações globais
│
├── shop-core/                          ← Domínio puro (depende de: NADA)
│   └── pom.xml                         ← só Jackson + JUnit no classpath
│
├── shop-application/                   ← Casos de uso (depende de: core)
│   └── pom.xml                         ← core + (opcional) spring-context p/ DI
│
├── shop-infrastructure/                ← Tecnologia (depende de: core + application)
│   └── pom.xml                         ← JPA + PostgreSQL + Kafka + Redis + ...
│
└── shop-webapi/                        ← Entry point (depende de: infrastructure)
    └── pom.xml                         ← spring-boot-web + validation + main()
```

### Como o POM pai amarra tudo

```xml
<!-- shop/pom.xml (POM PAI) -->
<project>
  <groupId>br.com.shop</groupId>
  <artifactId>shop</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>            <!-- pom = agregador, não gera jar -->

  <modules>
    <module>shop-core</module>
    <module>shop-application</module>
    <module>shop-infrastructure</module>
    <module>shop-webapi</module>
  </modules>

  <!-- versões centralizadas para todos os módulos -->
  <dependencyManagement>
    <dependencies>
      <!-- Spring Boot BOM, versões de libs, etc. -->
    </dependencies>
  </dependencyManagement>
</project>
```

```xml
<!-- shop-core/pom.xml — repare no que ele PODE depender -->
<project>
  <parent>
    <groupId>br.com.shop</groupId>
    <artifactId>shop</artifactId>
    <version>1.0.0</version>
  </parent>
  <artifactId>shop-core</artifactId>

  <dependencies>
    <!-- Só o essencial. NENHUM framework de infraestrutura. -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
    </dependency>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

```xml
<!-- shop-application/pom.xml — só conhece o core -->
<dependencies>
  <dependency>
    <groupId>br.com.shop</groupId>
    <artifactId>shop-core</artifactId>   <!-- ← única dependência de negócio -->
  </dependency>
</dependencies>
```

```xml
<!-- shop-infrastructure/pom.xml — implementa as portas com tecnologia -->
<dependencies>
  <dependency><artifactId>shop-core</artifactId>...</dependency>
  <dependency><artifactId>shop-application</artifactId>...</dependency>
  <!-- Aqui SIM entram os frameworks -->
  <dependency><artifactId>spring-boot-starter-data-jpa</artifactId>...</dependency>
  <dependency><artifactId>postgresql</artifactId>...</dependency>
  <dependency><artifactId>spring-kafka</artifactId>...</dependency>
</dependencies>
```

```xml
<!-- shop-webapi/pom.xml — o ponto de entrada, tem o main() -->
<dependencies>
  <dependency><artifactId>shop-infrastructure</artifactId>...</dependency>
  <dependency><artifactId>spring-boot-starter-web</artifactId>...</dependency>
  <dependency><artifactId>spring-boot-starter-validation</artifactId>...</dependency>
</dependencies>
```

---

## 5. Diagrama de fluxo do sistema (exemplo e-commerce)

```
┌─────────────────────────────────────────────────────────────────┐
│  CLIENTE (Browser / App Mobile / Frontend)                       │
│  Usuário finaliza a compra → envia dados do pedido               │
└───────────────────────────────┬─────────────────────────────────┘
                                 │ POST /pedidos
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  shop-webapi (Spring MVC)                                        │
│  PedidoController → valida DTO → chama o Caso de Uso             │
└───────────────────────────────┬─────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  shop-application (Casos de Uso)                                 │
│  CriarPedido · AdicionarItem · AplicarCupom · FinalizarPagamento │
│  (sem framework — testável com JUnit puro)                      │
└───────────────────────────────┬─────────────────────────────────┘
                                 │ chama as portas (interfaces do core)
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  shop-core (Domínio)                                             │
│                                                                  │
│  Pedido (aggregate root) ──────── Carrinho                       │
│       │                              │                           │
│  ItemPedido[]                    Cupom (value object)            │
│       │                                                          │
│  Dinheiro (value object imutável) · StatusPedido (enum)          │
│                                                                  │
│  Portas (interfaces):                                            │
│    IPedidoRepository · IPagamentoGateway                         │
│    IEstoqueGateway · IEventPublisher                             │
│                                                                  │
│  Eventos: PedidoCriado · ItemAdicionado · PagamentoAprovado      │
└───────────────────────────────┬─────────────────────────────────┘
                                 │ é IMPLEMENTADO por
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  shop-infrastructure (Adapters)                                  │
│                                                                  │
│  ┌──────────────────┐   ┌──────────────────┐                    │
│  │  PostgreSQL/JPA  │   │  Gateway Pagamento│                    │
│  │                  │   │  (Stripe/Pagar.me)│                    │
│  │  PedidoJpaEntity │   │                  │                    │
│  │  PedidoRepoImpl  │   │  StripeAdapter   │                    │
│  │  (impl da porta) │   │  (impl da porta) │                    │
│  └──────────────────┘   └──────────────────┘                    │
│  ┌──────────────────┐   ┌──────────────────┐                    │
│  │  Kafka Publisher │   │  Redis (cache)   │                    │
│  │  (eventos)       │   │  EstoqueGateway  │                    │
│  └──────────────────┘   └──────────────────┘                    │
└─────────────────────────────────────────────────────────────────┘
```

**Note a inversão de dependência (a "mágica" da Clean Architecture):** a seta de `application → core` é uma dependência de *chamada* (o caso de uso chama a interface `IPedidoRepository`). A seta de `infrastructure → core` é de *implementação* (o `PedidoRepositoryImpl` implementa `IPedidoRepository`). O `core` define o contrato; a `infrastructure` obedece. O fluxo de controle vai para fora, mas a dependência de código aponta para dentro.

---

## 6. Estrutura de pastas detalhada por módulo

### 6.1 `shop-core` — o Domínio (o coração, não conhece ninguém)

```
shop-core/
└── src/
    ├── main/java/br/com/shop/
    │   └── domain/
    │       │
    │       ├── valueobjects/          ← objetos imutáveis definidos pelo VALOR
    │       │   ├── Dinheiro.java       # record: valor + moeda; nunca float p/ dinheiro
    │       │   ├── Cpf.java            # record validado no construtor
    │       │   ├── Endereco.java       # record: rua, numero, cep, cidade, estado
    │       │   ├── StatusPedido.java   # enum: RASCUNHO, AGUARDANDO_PAGAMENTO,
    │       │   │                       #       PAGO, ENVIADO, ENTREGUE, CANCELADO
    │       │   └── Cupom.java          # value object: codigo + percentualDesconto
    │       │
    │       ├── entities/              ← objetos com IDENTIDADE e ciclo de vida
    │       │   ├── Pedido.java         # AGGREGATE ROOT — ponto de entrada do domínio
    │       │   ├── ItemPedido.java     # entidade filha do aggregate Pedido
    │       │   └── Produto.java        # entidade (ou aggregate próprio)
    │       │
    │       ├── events/                ← eventos de domínio (algo que aconteceu)
    │       │   ├── DomainEvent.java     # base abstrata (eventId + ocorridoEm)
    │       │   ├── PedidoCriadoEvent.java
    │       │   ├── ItemAdicionadoEvent.java
    │       │   ├── PagamentoAprovadoEvent.java
    │       │   └── PedidoCanceladoEvent.java
    │       │
    │       ├── gateways/              ← PORTAS: interfaces que o domínio EXIGE
    │       │   ├── IPedidoRepository.java     # persistência (implementada por JPA)
    │       │   ├── IPagamentoGateway.java     # pagamento (implementada por Stripe)
    │       │   ├── IEstoqueGateway.java       # estoque (implementada por Redis/API)
    │       │   └── IEventPublisher.java       # publicação de eventos (Kafka/Spring)
    │       │
    │       └── exceptions/            ← erros de regra de negócio
    │           ├── DomainException.java        # base de todas
    │           ├── EstoqueInsuficienteException.java
    │           ├── CupomInvalidoException.java
    │           ├── PedidoJaPagoException.java
    │           └── ValorInvalidoException.java
    │
    └── test/java/br/com/shop/
        └── domain/
            ├── PedidoTest.java         # testa regras SEM banco, SEM Spring
            ├── DinheiroTest.java       # ex.: soma/subtração de moedas
            └── CupomTest.java          # ex.: aplicar desconto, validar expiração
```

**Regras deste módulo:**
- **Value Objects** são imutáveis (use `record` em Java 17+). `Dinheiro` nunca é `double` — use `BigDecimal` internamente para evitar erro de ponto flutuante em valores monetários.
- **Aggregate Root** (`Pedido`) é o único ponto de acesso ao aggregate. Ninguém mexe em `ItemPedido` diretamente — sempre através de métodos do `Pedido` (ex.: `pedido.adicionarItem(...)`), garantindo que as invariantes (regras que devem sempre ser verdadeiras) nunca sejam violadas.
- **Gateways** são interfaces (portas). O domínio diz *o que* precisa (`salvar um pedido`), sem saber *como* (Postgres? Mongo? arquivo?).
- **Testes** rodam em milissegundos porque não sobem banco nem contexto de framework.

### 6.2 `shop-application` — os Casos de Uso (orquestração)

```
shop-application/
└── src/main/java/br/com/shop/
    └── application/
        └── usecases/
            ├── CriarPedido/
            │   ├── CriarPedidoInteractor.java   # a lógica de orquestração
            │   ├── CriarPedidoInput.java         # DTO de entrada (dados que chegam)
            │   └── CriarPedidoOutput.java        # DTO de saída (resultado)
            │
            ├── AdicionarItem/
            │   ├── AdicionarItemInteractor.java
            │   └── AdicionarItemInput.java
            │
            ├── AplicarCupom/
            │   └── AplicarCupomInteractor.java
            │
            └── FinalizarPagamento/
                ├── FinalizarPagamentoInteractor.java
                └── FinalizarPagamentoInput.java
```

**O que é um Caso de Uso (Interactor):**

```java
// shop-application — orquestra o domínio SEM saber de tecnologia
public class CriarPedidoInteractor {

    // Depende de INTERFACES (portas) do core, nunca de implementações
    private final IPedidoRepository pedidoRepository;
    private final IEstoqueGateway estoqueGateway;
    private final IEventPublisher eventPublisher;

    public CriarPedidoInteractor(IPedidoRepository pedidoRepository,
                                 IEstoqueGateway estoqueGateway,
                                 IEventPublisher eventPublisher) {
        this.pedidoRepository = pedidoRepository;
        this.estoqueGateway = estoqueGateway;
        this.eventPublisher = eventPublisher;
    }

    public CriarPedidoOutput executar(CriarPedidoInput input) {
        // 1. Reconstrói/valida objetos de domínio a partir do input
        var pedido = Pedido.novo(input.clienteId(), input.enderecoEntrega());

        // 2. Aplica regras de negócio (delegando ao aggregate)
        for (var item : input.itens()) {
            if (!estoqueGateway.temEstoque(item.produtoId(), item.quantidade())) {
                throw new EstoqueInsuficienteException(item.produtoId());
            }
            pedido.adicionarItem(item.produtoId(), item.quantidade(), item.preco());
        }

        // 3. Persiste através da PORTA (não sabe se é Postgres ou Mongo)
        pedidoRepository.salvar(pedido);

        // 4. Publica os eventos gerados pelo domínio
        pedido.eventos().forEach(eventPublisher::publicar);

        // 5. Retorna o output
        return new CriarPedidoOutput(pedido.id(), pedido.total());
    }
}
```

**Por que sem framework aqui?** O caso de uso é a regra de aplicação pura. Ele pode ter uma anotação leve de DI (`@Service` do Spring) se você quiser, mas a lógica não deve depender de nada específico. Isso permite testar o fluxo inteiro com **mocks das portas** e JUnit, sem subir o Spring nem o banco.

### 6.3 `shop-infrastructure` — os Adapters (a tecnologia real)

```
shop-infrastructure/
└── src/main/java/br/com/shop/
    └── infrastructure/
        │
        ├── persistence/              ← tudo relacionado a banco de dados
        │   ├── entities/
        │   │   ├── PedidoJpaEntity.java     # @Entity — modelo do BANCO (≠ domínio!)
        │   │   ├── ItemPedidoJpaEntity.java
        │   │   └── ProdutoJpaEntity.java
        │   ├── repositories/
        │   │   └── PedidoSpringDataRepository.java  # interface Spring Data JPA
        │   ├── mappers/
        │   │   └── PedidoMapper.java         # converte Domínio ↔ JpaEntity
        │   └── adapters/
        │       └── PedidoRepositoryImpl.java # IMPLEMENTA IPedidoRepository (a porta)
        │
        ├── payment/                  ← integração com gateway de pagamento
        │   ├── StripePagamentoAdapter.java   # IMPLEMENTA IPagamentoGateway
        │   └── StripeClient.java             # SDK/HTTP client do Stripe
        │
        ├── stock/                    ← integração com serviço de estoque
        │   └── RedisEstoqueAdapter.java      # IMPLEMENTA IEstoqueGateway
        │
        ├── messaging/               ← publicação de eventos
        │   ├── KafkaEventPublisher.java      # IMPLEMENTA IEventPublisher
        │   └── listeners/
        │       └── PagamentoAprovadoListener.java  # reage a eventos internos
        │
        └── email/                   ← notificações
            └── SmtpEmailAdapter.java         # IMPLEMENTA INotificacaoGateway
```

**O ponto crucial — duas representações do "Pedido":**

```
Pedido (domínio, no shop-core):
  - Objeto rico, com comportamento (métodos adicionarItem, cancelar, etc.)
  - Não sabe que existe banco
  - Protege as invariantes de negócio

PedidoJpaEntity (persistência, no shop-infrastructure):
  - Objeto "burro", só getters/setters e anotações @Entity, @Column, @Id
  - Existe só para o Hibernate mapear tabelas
  - Um MAPPER (PedidoMapper) converte um no outro

Por que separar?
  Se você usasse a MESMA classe para domínio e banco, as anotações de JPA
  (@Entity, @Table) VAZARIAM para o shop-core → violação da arquitetura.
  Ao separar, o core continua puro e o Hibernate fica confinado na infra.
```

```java
// shop-infrastructure — o adapter que implementa a porta
@Repository
public class PedidoRepositoryImpl implements IPedidoRepository {  // ← implementa a porta do core

    private final PedidoSpringDataRepository springRepo;
    private final PedidoMapper mapper;

    @Override
    public void salvar(Pedido pedido) {                 // recebe objeto de DOMÍNIO
        PedidoJpaEntity entity = mapper.paraJpa(pedido); // converte p/ entidade de BANCO
        springRepo.save(entity);                         // Hibernate persiste
    }

    @Override
    public Optional<Pedido> buscarPorId(UUID id) {
        return springRepo.findById(id)
                         .map(mapper::paraDominio);       // converte de volta p/ DOMÍNIO
    }
}
```

### 6.4 `shop-webapi` — o Entry Point (controllers, config, main)

```
shop-webapi/
└── src/main/java/br/com/shop/
    ├── ShopApplication.java              ← @SpringBootApplication + main()
    │
    ├── webapi/
    │   ├── controllers/
    │   │   ├── PedidoController.java      # @RestController — endpoints HTTP
    │   │   └── ProdutoController.java
    │   ├── dtos/
    │   │   ├── CriarPedidoRequest.java    # DTO HTTP de entrada (com @Valid)
    │   │   ├── CriarPedidoResponse.java   # DTO HTTP de saída
    │   │   └── AdicionarItemRequest.java
    │   └── handlers/
    │       └── GlobalExceptionHandler.java # @ControllerAdvice — traduz exceptions
    │
    └── config/
        ├── BeanConfig.java               # monta os Casos de Uso injetando adapters
        ├── JacksonConfig.java            # ObjectMapper (datas, etc.)
        └── OpenApiConfig.java            # Swagger/SpringDoc (documentação)
```

**O controller é fino — só traduz HTTP em chamada de Caso de Uso:**

```java
// shop-webapi
@RestController
@RequestMapping("/pedidos")
public class PedidoController {

    private final CriarPedidoInteractor criarPedido;   // ← o caso de uso

    @PostMapping
    public ResponseEntity<CriarPedidoResponse> criar(
            @Valid @RequestBody CriarPedidoRequest request) {

        // 1. Traduz o DTO HTTP em Input do caso de uso
        var input = new CriarPedidoInput(
            request.clienteId(),
            request.enderecoEntrega(),
            request.itens()
        );

        // 2. Executa o caso de uso
        var output = criarPedido.executar(input);

        // 3. Traduz o Output em DTO HTTP de resposta
        var response = new CriarPedidoResponse(output.pedidoId(), output.total());
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
}
```

**Onde a "cola" acontece — a configuração de beans:**

```java
// shop-webapi/config — o ÚNICO lugar que conhece todos os módulos ao mesmo tempo
@Configuration
public class BeanConfig {

    // Monta o caso de uso injetando as IMPLEMENTAÇÕES (adapters) nas PORTAS
    @Bean
    public CriarPedidoInteractor criarPedidoInteractor(
            IPedidoRepository pedidoRepository,   // Spring injeta PedidoRepositoryImpl
            IEstoqueGateway estoqueGateway,       // Spring injeta RedisEstoqueAdapter
            IEventPublisher eventPublisher) {     // Spring injeta KafkaEventPublisher
        return new CriarPedidoInteractor(pedidoRepository, estoqueGateway, eventPublisher);
    }
}
```

> É aqui — e **somente aqui**, no startup da aplicação — que as camadas se encontram. O `webapi` conhece a `infrastructure` só para dizer ao Spring "quando alguém pedir a porta `IPedidoRepository`, entregue o adapter `PedidoRepositoryImpl`". Isso é **Injeção de Dependência** materializando a inversão de dependência.

---

## 7. Como submódulos de negócio (bounded contexts) se encaixam

Em sistemas maiores, cada **módulo de negócio** (bounded context do DDD) pode replicar essa estrutura de 4 camadas. Exemplo de uma loja com 3 contextos:

```
shop/
├── pom.xml                         ← POM pai raiz
│
├── modulo-catalogo/                ← contexto "Catálogo de Produtos"
│   ├── catalogo-core/
│   ├── catalogo-application/
│   ├── catalogo-infrastructure/
│   └── catalogo-webapi/
│
├── modulo-pedidos/                 ← contexto "Pedidos"
│   ├── pedidos-core/
│   ├── pedidos-application/
│   ├── pedidos-infrastructure/
│   └── pedidos-webapi/
│
├── modulo-pagamentos/              ← contexto "Pagamentos"
│   ├── pagamentos-core/
│   ├── ...
│
└── shop-bootstrap/                 ← junta todos os módulos num único deploy
    └── ShopApplication.java        ← o main() que sobe tudo
```

**Regras entre módulos de negócio:**
- Um módulo **não importa o core de outro** diretamente. A comunicação entre bounded contexts acontece via **eventos de domínio** (ex.: `PedidoPago` publicado por `pedidos` → consumido por `catalogo` para baixar estoque) ou via **portas/interfaces públicas** bem definidas.
- Isso mantém os contextos desacoplados. Se um dia `modulo-pagamentos` virar um microsserviço, só a implementação da comunicação muda (de evento in-process para Kafka/HTTP) — o contrato permanece.

---

## 8. Checklist de conformidade arquitetural

```
✓ shop-core não tem NENHUMA anotação de framework (@Entity, @Service, @Autowired)
✓ shop-core não importa nada de jakarta.*, org.springframework.*, etc.
✓ Toda persistência passa por uma PORTA (interface) definida no core
✓ Objetos de domínio (Pedido) são diferentes das entidades JPA (PedidoJpaEntity)
✓ Casos de uso dependem de interfaces, nunca de implementações concretas
✓ Controllers são finos: traduzem HTTP ↔ caso de uso, sem regra de negócio
✓ Dinheiro/valores monetários usam BigDecimal, nunca double/float
✓ Aggregate roots protegem invariantes — ninguém altera filhos diretamente
✓ A configuração de DI (BeanConfig) é o único ponto que conhece todos os módulos
✓ O build QUEBRA se alguém tentar violar a regra de dependência
```

---

## 9. Trade-offs honestos

```
✅ Vantagens:
  - Arquitetura garantida pelo compilador, não pela disciplina do time
  - Domínio testável em milissegundos (sem banco, sem Spring)
  - Troca de tecnologia isolada (trocar Postgres por Mongo = mexer só na infra)
  - Fronteiras prontas para extração futura em microsserviços
  - Onboarding claro: cada módulo tem uma responsabilidade óbvia

⚠️ Custos:
  - Mais boilerplate: mappers Domínio↔JPA, DTOs em cada camada
  - Curva de aprendizado (devs acostumados a "tudo num projeto" estranham)
  - Build multi-módulo é um pouco mais lento e mais complexo de configurar
  - Over-engineering para projetos pequenos/CRUDs simples

Quando NÃO usar:
  - CRUD simples sem regra de negócio relevante → um monólito em camadas
    (por pacote) já resolve com muito menos cerimônia
  - Protótipo/MVP descartável → a velocidade importa mais que a estrutura
  - Time pequeno sem experiência com Clean Architecture → risco de aplicar errado

Quando VALE muito a pena:
  - Domínio com regras de negócio ricas e que mudam com frequência
  - Sistema que vai crescer e ter vários desenvolvedores/times
  - Necessidade real de proteger o domínio de detalhes tecnológicos voláteis
  - Intenção futura de extrair partes para microsserviços
```

---

## 10. Relação com outros conceitos

Este template é a materialização física de vários princípios:

- **[[10. Arquitetura de Software e Design de Software/Padrões e Princípios/SOLID|SOLID]]** — especialmente o **D** (Dependency Inversion): módulos de alto nível (domínio) não dependem de baixo nível (banco); ambos dependem de abstrações (as portas).
- **Clean Architecture (Uncle Bob)** — os círculos concêntricos com a regra de dependência apontando para dentro.
- **Ports & Adapters (Arquitetura Hexagonal)** — as "portas" (`gateways/`) e os "adapters" (`infrastructure/`) são exatamente os mesmos conceitos.
- **Domain-Driven Design (DDD)** — Aggregate Roots, Value Objects, Domain Events, Bounded Contexts, Ubiquitous Language.

---

## 🧩 Quiz de Fixação

1. Um desenvolvedor júnior, tentando "ganhar tempo", adiciona `@Entity` e `@Table` diretamente na classe `Pedido` dentro do `shop-core` para não ter que criar um mapper. O que acontece quando ele tenta compilar o projeto, e por quê essa é justamente a vantagem do modelo multi-módulo?

2. Explique por que o `CriarPedidoInteractor` recebe `IPedidoRepository` (interface) no construtor em vez de `PedidoRepositoryImpl` (implementação concreta). O que essa escolha permite em termos de teste e de troca de tecnologia?

3. Em um sistema com os módulos `modulo-pedidos` e `modulo-catalogo`, o time precisa que, ao pagar um pedido, o estoque do produto seja reduzido no catálogo. Por que fazer `pedidos-core` importar diretamente `catalogo-core` seria uma violação arquitetural, e qual é a forma correta de comunicar os dois contextos?

**Respostas:**

1) **O build quebra na compilação.** O `shop-core` declara em seu `pom.xml` apenas dependências mínimas (Jackson, JUnit) — o pacote `jakarta.persistence` (onde vivem `@Entity` e `@Table`) **não está no classpath** do módulo core. Ao tentar usar `@Entity`, o compilador não encontra o símbolo e falha com erro de compilação (`cannot find symbol` / `package jakarta.persistence does not exist`). Essa é exatamente a vantagem do modelo multi-módulo: em um projeto único, todas as libs estariam no mesmo classpath e o código compilaria normalmente, deixando a violação passar despercebida até virar um problema caro. No multi-módulo, o compilador vira o guardião — a arquitetura deixa de depender da disciplina do dev e passa a ser fisicamente imposta.

2) O `CriarPedidoInteractor` depende da **abstração** (`IPedidoRepository`), não da implementação — isso é o **Princípio da Inversão de Dependência** (o "D" do SOLID) em ação. Benefícios: **(a) Testabilidade:** nos testes unitários, você injeta um *mock* ou *fake* de `IPedidoRepository` (ex.: um repositório em memória com um `HashMap`) — o caso de uso roda em milissegundos sem subir banco nem Spring. **(b) Troca de tecnologia:** se amanhã a empresa migrar de PostgreSQL para MongoDB, você cria um novo `PedidoMongoRepositoryImpl` que implementa a mesma interface `IPedidoRepository`, troca o bean na configuração, e **nenhuma linha do caso de uso ou do domínio muda**. A dependência aponta para o contrato (a porta), e implementações são detalhes plugáveis.

3) Fazer `pedidos-core` importar `catalogo-core` **acopla dois bounded contexts diretamente**, destruindo o isolamento que justifica a separação em módulos. Se um dia o catálogo virar um microsserviço, esse import in-process se tornaria impossível, e você teria mudanças em cascata. Além disso, cria dependência bidirecional potencial e viola a Ubiquitous Language (cada contexto tem seu próprio modelo — "Produto" no catálogo pode ser diferente de "Produto" nos pedidos). **Forma correta:** comunicação via **eventos de domínio**. O `modulo-pedidos` publica um evento `PagamentoAprovadoEvent` (contendo os IDs dos produtos e quantidades) através da porta `IEventPublisher`. O `modulo-catalogo` tem um listener que **consome** esse evento e executa seu próprio caso de uso `BaixarEstoque`. Os dois contextos se comunicam por um contrato de evento bem definido, permanecem desacoplados, e a migração futura para mensageria distribuída (Kafka/RabbitMQ) muda apenas a implementação do publisher/listener — não o contrato nem a lógica de negócio.
