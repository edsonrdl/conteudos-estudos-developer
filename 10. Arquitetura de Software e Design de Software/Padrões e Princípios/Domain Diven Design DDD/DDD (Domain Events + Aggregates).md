## 1. Contexto rápido: o que é DDD aqui?

Dentro de DDD, a gente tenta:

- Modelar o **domínio** (regras de negócio) de forma explícita.
- Centralizar regra de negócio em **Entidades / Value Objects / Aggregates**.
- Evitar que a aplicação vire só “CRUD com banco”.

Dois conceitos muito importantes nesse modelo:

- **Aggregate (Agregado)**
- **Domain Event (Evento de Domínio)**

Eles combinam MUITO bem com arquitetura event-driven.

---

## 2. O que é um Aggregate?

Um **Aggregate** é um “pacote” de objetos do domínio que:

- São **consistentes juntos** (compartilham invariantes).
- Têm uma **raiz** (Aggregate Root) que é a **única porta de entrada**.

Exemplo clássico:

- `Pedido` (Aggregate Root)
    - `ItemDoPedido` (entidade interna)
    - `EnderecoEntrega` (Value Object)

Regras como:

- “O total do pedido = soma dos itens”
- “Não pode ter item com quantidade <= 0”
- “Não pode adicionar item se pedido está CANCELADO”

➡️ Essas regras vivem **dentro do aggregate**, não no controller, nem no service de aplicação.

---

## 3. O que é um Domain Event?

**Domain Event** = “algo importante que _aconteceu_ no domínio”.

Características:

- Nome no **passado**: `PedidoCriado`, `PagamentoConfirmado`, `EstoqueRebaixado`.
- Imutável.
- Representa **fato de negócio**, não detalhe técnico.
- Geralmente carregam:
    - IDs relevantes
    - Dados mínimos necessários para quem vai reagir

Exemplo:

```java
public class PedidoCriadoEvent {
    private final String pedidoId;
    private final double valorTotal;

    public PedidoCriadoEvent(String pedidoId, double valorTotal) {
        this.pedidoId = pedidoId;
        this.valorTotal = valorTotal;
    }

    public String getPedidoId() { return pedidoId; }
    public double getValorTotal() { return valorTotal; }
}

```

---

## 4. Como Aggregates e Domain Events se conectam?

Fluxo típico:

1. Um método de negócio no **aggregate** muda o estado:
    - `pedido.confirmarPagamento()`
2. Se algo relevante aconteceu:
    - o aggregate **“levanta” um Domain Event**
3. Camada de aplicação/repositório:
    - persiste o aggregate
    - publica os Domain Events para o “mundo” (Spring, Kafka, Rabbit, etc)

Ideia importante:

> Quem sabe que o evento aconteceu é o domínio (o aggregate),
> 
> mas **quem publica para fora é a aplicação/infraestrutura**.

---

## 5. Implementação prática em Java (estilo “cleanzinho”)

### 5.1. Interface base de DomainEvent

```java
import java.time.Instant;

public interface DomainEvent {
    Instant occurredOn();
}

```

### 5.2. Evento de domínio concreto

```java
import java.time.Instant;

public class PedidoCriadoEvent implements DomainEvent {

    private final String pedidoId;
    private final double valorTotal;
    private final Instant occurredOn;

    public PedidoCriadoEvent(String pedidoId, double valorTotal) {
        this.pedidoId = pedidoId;
        this.valorTotal = valorTotal;
        this.occurredOn = Instant.now();
    }

    public String getPedidoId() { return pedidoId; }
    public double getValorTotal() { return valorTotal; }

    @Override
    public Instant occurredOn() {
        return occurredOn;
    }
}

```

---

### 5.3. Classe base para Aggregate Root

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public abstract class AggregateRoot {

    private final List<DomainEvent> domainEvents = new ArrayList<>();

    protected void registerEvent(DomainEvent event) {
        domainEvents.add(event);
    }

    public List<DomainEvent> getDomainEvents() {
        return Collections.unmodifiableList(domainEvents);
    }

    public void clearDomainEvents() {
        domainEvents.clear();
    }
}

```

---

### 5.4. Aggregate: `Pedido`

```java
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class Pedido extends AggregateRoot {

    private final String id;
    private final List<ItemPedido> itens = new ArrayList<>();
    private StatusPedido status;
    private double valorTotal;

    private Pedido() {
        this.id = UUID.randomUUID().toString();
        this.status = StatusPedido.CRIADO;
    }

    public static Pedido criarNovo(List<ItemPedido> itens) {
        Pedido pedido = new Pedido();
        itens.forEach(pedido::adicionarItemInterno);
        pedido.recalcularTotal();

        // REGISTRA O EVENTO DE DOMÍNIO
        pedido.registerEvent(
                new PedidoCriadoEvent(pedido.id, pedido.valorTotal)
        );

        return pedido;
    }

    private void adicionarItemInterno(ItemPedido item) {
        if (item.getQuantidade() <= 0) {
            throw new IllegalArgumentException("Quantidade inválida");
        }
        itens.add(item);
    }

    private void recalcularTotal() {
        this.valorTotal = itens.stream()
                .mapToDouble(ItemPedido::getSubtotal)
                .sum();
    }

    public String getId() { return id; }
    public double getValorTotal() { return valorTotal; }
    public StatusPedido getStatus() { return status; }

    public void confirmarPagamento() {
        if (status != StatusPedido.CRIADO) {
            throw new IllegalStateException("Só pode confirmar pagamento de pedido criado");
        }
        this.status = StatusPedido.PAGO;

        registerEvent(new PagamentoConfirmadoEvent(id, valorTotal));
    }
}

```

Classes auxiliares simples:

```java
public class ItemPedido {
    private final String produtoId;
    private final int quantidade;
    private final double precoUnitario;

    public ItemPedido(String produtoId, int quantidade, double precoUnitario) {
        this.produtoId = produtoId;
        this.quantidade = quantidade;
        this.precoUnitario = precoUnitario;
    }

    public int getQuantidade() { return quantidade; }

    public double getSubtotal() {
        return quantidade * precoUnitario;
    }
}

enum StatusPedido {
    CRIADO, PAGO, CANCELADO
}

```

---

### 5.5. DomainEventPublisher (abstração)

```java
public interface DomainEventPublisher {
    void publish(DomainEvent event);
    default void publishAll(Iterable<DomainEvent> events) {
        for (DomainEvent e : events) {
            publish(e);
        }
    }
}

```

---

### 5.6. Implementação usando Spring `ApplicationEventPublisher`

```java
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Component;

@Component
public class SpringDomainEventPublisher implements DomainEventPublisher {

    private final ApplicationEventPublisher springPublisher;

    public SpringDomainEventPublisher(ApplicationEventPublisher springPublisher) {
        this.springPublisher = springPublisher;
    }

    @Override
    public void publish(DomainEvent event) {
        springPublisher.publishEvent(event);
    }
}

```

Agora, todo `DomainEvent` vira um evento do Spring automaticamente.

---

### 5.7. Application Service usando Aggregate + Events

```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@Service
public class PedidoApplicationService {

    private final PedidoRepository pedidoRepository;
    private final DomainEventPublisher eventPublisher;

    public PedidoApplicationService(PedidoRepository pedidoRepository,
                                    DomainEventPublisher eventPublisher) {
        this.pedidoRepository = pedidoRepository;
        this.eventPublisher = eventPublisher;
    }

    @Transactional
    public String criarPedido(List<ItemPedido> itens) {
        Pedido pedido = Pedido.criarNovo(itens);

        pedidoRepository.save(pedido);

        eventPublisher.publishAll(pedido.getDomainEvents());
        pedido.clearDomainEvents();

        return pedido.getId();
    }

    @Transactional
    public void confirmarPagamento(String pedidoId) {
        Pedido pedido = pedidoRepository.findById(pedidoId)
                .orElseThrow();

        pedido.confirmarPagamento();

        pedidoRepository.save(pedido);

        eventPublisher.publishAll(pedido.getDomainEvents());
        pedido.clearDomainEvents();
    }
}

```

---

### 5.8. Reagindo a Domain Events com `@EventListener`

```java
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class PedidoEventHandler {

    @EventListener
    public void onPedidoCriado(PedidoCriadoEvent event) {
        System.out.println("📧 Enviar e-mail: pedido criado " + event.getPedidoId());
    }

    @EventListener
    public void onPagamentoConfirmado(PagamentoConfirmadoEvent event) {
        System.out.println("✅ Atualizar financeiro: pagamento confirmado " + event.getPedidoId());
    }
}

```

---

## 6. Onde entra RabbitMQ / Kafka nisso?

Esse mesmo `PedidoEventHandler` poderia:

- Em vez de só dar `System.out.println`, publicar em:
    - RabbitMQ
    - Kafka
    - Webhook
    - Outro microserviço

Ou você pode ter um outro `DomainEventPublisher` que:

- Persistir no banco (Outbox Pattern)
- Ou mandar direto pro Kafka/Rabbit

Exemplo de publisher para Kafka (ideia):

```java
@Component
public class KafkaDomainEventPublisher implements DomainEventPublisher {

    private final KafkaTemplate<String, String> kafka;

    public KafkaDomainEventPublisher(KafkaTemplate<String, String> kafka) {
        this.kafka = kafka;
    }

    @Override
    public void publish(DomainEvent event) {
        if (event instanceof PedidoCriadoEvent e) {
            kafka.send("pedido-criado", e.getPedidoId(), /* serializa JSON */ "...json...");
        }
        // etc...
    }
}

```

---

## 7. Resumindo (estilo “cola mental”)

- **Aggregate**:
    - raiz que guarda consistência e regra de negócio
    - controla acesso às entidades internas
- **Domain Event**:
    - fato importante do domínio
    - gerado pelo aggregate
    - publicado pela aplicação/infra
- **Fluxo recomendado**:
    1. Service de aplicação chama método do aggregate
    2. Aggregate muda estado + registra Domain Event
    3. Service salva aggregate no repositório
    4. Service publica os Domain Events (Spring, Kafka, Rabbit, etc)
    5. Listeners reagem (e-mail, log, integrações, etc)