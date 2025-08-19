### **Kafka Cluster (3 Brokers em servidores diferentes):**

```
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   BROKER 1      │  │   BROKER 2      │  │   BROKER 3      │
│ (10.0.1.10)     │  │ (10.0.1.11)     │  │ (10.0.1.12)     │
│                 │  │                 │  │                 │
│ - Partition 0   │  │ - Partition 1   │  │ - Partition 2   │
│   (leader)      │  │   (leader)      │  │   (leader)      │
│ - Partition 1   │  │ - Partition 2   │  │ - Partition 0   │
│   (replica)     │  │   (replica)     │  │   (replica)     │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

## 🎯 **CENÁRIO REAL: E-commerce da Magazine Luiza**

### **2 PRODUCERS em ação:**

#### 🛒 **Producer 1: API de Pedidos Web**

java

```java
// Servidor: web-api-01.magazineluiza.com
@RestController
public class PedidosController {
    
    @Autowired
    private KafkaTemplate<String, Object> kafkaTemplate;
    
    @PostMapping("/pedidos")
    public ResponseEntity criarPedido(@RequestBody PedidoRequest request) {
        
        // Lógica de negócio...
        Pedido pedido = processarPedido(request);
        
        // DECISÃO: Qual tópico usar?
        String topico = "pedidos-web";
        String chave = pedido.getClienteId(); // Para garantir ordem por cliente
        
        // Envia para Kafka
        kafkaTemplate.send(topico, chave, pedido);
        
        return ResponseEntity.ok("Pedido criado: " + pedido.getId());
    }
}
```

#### 📱 **Producer 2: API de Pedidos Mobile**

java

```java
// Servidor: mobile-api-01.magazineluiza.com  
@RestController
public class PedidosMobileController {
    
    @Autowired
    private KafkaTemplate<String, Object> kafkaTemplate;
    
    @PostMapping("/mobile/pedidos")
    public ResponseEntity criarPedidoMobile(@RequestBody PedidoMobileRequest request) {
        
        // Lógica específica mobile (geolocalização, push notification, etc.)
        PedidoMobile pedido = processarPedidoMobile(request);
        
        // DECISÃO: Tópico específico para mobile
        String topico = "pedidos-mobile";
        String chave = pedido.getClienteId();
        
        // Envia para Kafka
        kafkaTemplate.send(topico, chave, pedido);
        
        return ResponseEntity.ok("Pedido mobile criado: " + pedido.getId());
    }
}
```

## 📊 **DISTRIBUIÇÃO NAS PARTIÇÕES:**

### **Tópico: pedidos-web (3 partições)**

```
Cliente "joao123" faz pedido → hash("joao123") % 3 = 1
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   BROKER 1      │  │   BROKER 2      │  │   BROKER 3      │
│                 │  │                 │  │                 │
│ Partition 0     │  │ Partition 1     │  │ Partition 2     │
│ [outras msgs]   │  │ [PED-WEB-001]   │  │ [outras msgs]   │
│                 │  │ ↑ CHEGOU AQUI   │  │                 │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

### **Tópico: pedidos-mobile (3 partições)**

```
Cliente "maria456" faz pedido → hash("maria456") % 3 = 0
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   BROKER 1      │  │   BROKER 2      │  │   BROKER 3      │
│                 │  │                 │  │                 │
│ Partition 0     │  │ Partition 1     │  │ Partition 2     │
│ [PED-MOB-001]   │  │ [outras msgs]   │  │ [outras msgs]   │
│ ↑ CHEGOU AQUI   │  │                 │  │                 │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

## 🔄 **CONSUMERS PROCESSANDO:**

### **Microserviços consumindo de AMBOS os tópicos:**

#### 💳 **Pagamento Service (processa web + mobile):**

java

```java
// Deployment: payment-service (3 pods no Kubernetes)
@Component
public class PagamentoConsumer {
    
    @KafkaListener(
        topics = {"pedidos-web", "pedidos-mobile"}, 
        groupId = "pagamento-group"
    )
    public void processarPagamento(Object pedido, 
                                  @Header(KafkaHeaders.RECEIVED_TOPIC) String topico) {
        
        if ("pedidos-web".equals(topico)) {
            Pedido pedidoWeb = (Pedido) pedido;
            cobrarCartao(pedidoWeb);
            log.info("Pagamento WEB processado: " + pedidoWeb.getId());
            
        } else if ("pedidos-mobile".equals(topico)) {
            PedidoMobile pedidoMobile = (PedidoMobile) pedido;
            cobrarCartao(pedidoMobile);
            enviarPushNotification(pedidoMobile); // Específico mobile
            log.info("Pagamento MOBILE processado: " + pedidoMobile.getId());
        }
    }
}
```

#### 📦 **Estoque Service (processa web + mobile):**

java

```java
// Deployment: inventory-service (2 pods no Kubernetes)
@Component  
public class EstoqueConsumer {
    
    @KafkaListener(
        topics = {"pedidos-web", "pedidos-mobile"},
        groupId = "estoque-group"
    )
    public void atualizarEstoque(Object pedido,
                               @Header(KafkaHeaders.RECEIVED_TOPIC) String topico) {
        
        List<Produto> produtos = extrairProdutos(pedido);
        estoqueService.reduzir(produtos);
        
        log.info("Estoque atualizado para pedido de: " + topico);
    }
}
```

#### 📧 **Email Service (só processa web):**

java

```java
// Deployment: email-service (1 pod no Kubernetes)
@Component
public class EmailConsumer {
    
    @KafkaListener(
        topics = {"pedidos-web"},  // SÓ WEB
        groupId = "email-group"
    )
    public void enviarEmail(Pedido pedido) {
        emailService.enviarConfirmacao(pedido);
        log.info("Email enviado para pedido web: " + pedido.getId());
    }
}
```

#### 📱 **Push Notification Service (só processa mobile):**

java

```java
// Deployment: push-service (1 pod no Kubernetes)
@Component
public class PushConsumer {
    
    @KafkaListener(
        topics = {"pedidos-mobile"}, // SÓ MOBILE
        groupId = "push-group"
    )
    public void enviarPush(PedidoMobile pedido) {
        pushService.enviarNotificacao(pedido);
        log.info("Push enviado para pedido mobile: " + pedido.getId());
    }
}
```

## ⚡ **FLUXO COMPLETO:**

### **1. Cliente Web faz pedido:**

```
API Web → Broker 2 (Partition 1) → 
├── Pagamento Service (3 pods dividem trabalho)
├── Estoque Service (2 pods dividem trabalho)  
└── Email Service (1 pod processa)
```

### **2. Cliente Mobile faz pedido:**

```
API Mobile → Broker 1 (Partition 0) →
├── Pagamento Service (3 pods dividem trabalho)
├── Estoque Service (2 pods dividem trabalho)
└── Push Service (1 pod processa)
```

## 🎯 **DECISÃO DE ROTEAMENTO:**

### **Por que brokers diferentes?**

- **Tolerância a falhas**: Se Broker 2 cair, Broker 1 assume a liderança
- **Performance**: Distribui carga entre servidores
- **Escalabilidade**: Cada broker pode estar em datacenter diferente

### **Por que tópicos diferentes?**

- **Lógica de negócio**: Web vs Mobile têm fluxos diferentes
- **Performance**: Particionar por canal de vendas
- **Monitoramento**: Métricas separadas por canal
## ❌ **EQUÍVOCO COMUM:**

```
ERRADO: Mensagem é copiada para todas as partições
┌──────────────────────────────────────────┐
│ Tópico: pedidos                          │
├──────────────────────────────────────────┤
│ Partition 0: [PED-123] ← NÃO!           │
│ Partition 1: [PED-123] ← NÃO!           │
│ Partition 2: [PED-123] ← NÃO!           │
└──────────────────────────────────────────┘
```

## ✅ **REALIDADE:**

```
CORRETO: Mensagem vai para UMA partição específica
┌──────────────────────────────────────────┐
│ Tópico: pedidos                          │
├──────────────────────────────────────────┤
│ Partition 0: [outras mensagens]         │
│ Partition 1: [PED-123] ← SÓ AQUI!       │
│ Partition 2: [outras mensagens]         │
└──────────────────────────────────────────┘
```

## 🎯 **COMO A PARTIÇÃO É ESCOLHIDA:**

### **1. Com chave (mais comum):**

java

```java
kafkaProducer.send("pedidos", "cliente_123", mensagem);
                            ↑
                        chave da mensagem

Algoritmo: hash("cliente_123") % 3 = 1
Resultado: Vai para Partition 1
```

### **2. Sem chave (round-robin):**

java

```java
kafkaProducer.send("pedidos", null, mensagem);
                            ↑
                        sem chave

Algoritmo: Round-robin
Mensagem 1 → Partition 0
Mensagem 2 → Partition 1  
Mensagem 3 → Partition 2
Mensagem 4 → Partition 0 (volta ao início)
```

### **3. Particionador customizado:**

java

```java
kafkaProducer.send("pedidos", chave, mensagem);

// Particionador personalizado por região
if (pedido.getRegiao().equals("SUDESTE")) return 0;
if (pedido.getRegiao().equals("NORDESTE")) return 1;
if (pedido.getRegiao().equals("SUL")) return 2;
```

## 🔄 **O QUE REALMENTE ACONTECE:**

### **Cenário: 3 mensagens chegam:**

```
Producer envia:
├── PED-001 (cliente: "joao") → hash % 3 = 1 → Partition 1
├── PED-002 (cliente: "maria") → hash % 3 = 0 → Partition 0  
└── PED-003 (cliente: "carlos") → hash % 3 = 2 → Partition 2

Resultado no tópico:
┌──────────────────────────────────────────┐
│ Tópico: pedidos                          │
├──────────────────────────────────────────┤
│ Partition 0: [PED-002]                   │
│ Partition 1: [PED-001]                   │
│ Partition 2: [PED-003]                   │
└──────────────────────────────────────────┘
```

## 💾 **CONFUSÃO COM REPLICAÇÃO:**

### **Replicação acontece no BROKER level, não mensagem level:**

```
CLUSTER KAFKA (replication-factor = 2):
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   BROKER 1      │  │   BROKER 2      │  │   BROKER 3      │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ Partition 0     │  │ Partition 1     │  │ Partition 2     │
│ [PED-002]       │  │ [PED-001]       │  │ [PED-003]       │
│ (LEADER)        │  │ (LEADER)        │  │ (LEADER)        │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ Partition 2     │  │ Partition 0     │  │ Partition 1     │
│ [PED-003]       │  │ [PED-002]       │  │ [PED-001]       │
│ (REPLICA)       │  │ (REPLICA)       │  │ (REPLICA)       │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

**Cada partição é replicada, mas cada MENSAGEM específica ainda está em uma partição específica!**

## 📝 **RESUMO:**

1. **1 mensagem = 1 partição específica**
2. **Partições são replicadas entre brokers (tolerância a falhas)**
3. **Consumers leem de TODAS as partições do tópico**
4. **Não há redundância de mensagem entre partições**

**A "redundância" é da infraestrutura (brokers), não do conteúdo (mensagens)!**