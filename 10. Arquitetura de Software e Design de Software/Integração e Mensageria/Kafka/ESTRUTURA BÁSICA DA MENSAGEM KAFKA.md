### **Componentes de uma mensagem:**

```
┌─────────────────────────────────────────┐
│              KAFKA MESSAGE              │
├─────────────────────────────────────────┤
│ Key: "user-123"                         │ ← Determina partição
│ Value: { JSON payload }                 │ ← Dados da mensagem
│ Headers: { metadata }                   │ ← Informações extras
│ Timestamp: 2024-08-16T10:30:00Z        │ ← Quando foi criada
│ Partition: 1                            │ ← Onde foi armazenada
│ Offset: 12345                           │ ← Posição na partição
└─────────────────────────────────────────┘
```

## 🎯 **FORMATOS DE VALUE (payload):**

### **1. JSON (mais comum):**

json

```json
{
  "eventId": "evt-123e4567-e89b-12d3-a456-426614174000",
  "eventType": "USER_CREATED",
  "aggregateId": "user-456",
  "aggregateType": "USER",
  "timestamp": "2024-08-16T10:30:00Z",
  "source": "user-service",
  "version": "1.0",
  "data": {
    "userId": "user-456",
    "email": "joao@email.com",
    "nome": "João Silva",
    "status": "ACTIVE"
  },
  "metadata": {
    "correlationId": "req-789",
    "causationId": "evt-previous-123",
    "userId": "admin-123"
  }
}
```

### **2. Avro (schema evolution):**

json

```json
// Schema Avro
{
  "type": "record",
  "name": "UserEvent",
  "fields": [
    {"name": "eventId", "type": "string"},
    {"name": "eventType", "type": "string"},
    {"name": "userId", "type": "string"},
    {"name": "timestamp", "type": "long"},
    {"name": "data", "type": {
      "type": "record",
      "name": "UserData",
      "fields": [
        {"name": "email", "type": "string"},
        {"name": "nome", "type": "string"}
      ]
    }}
  ]
}
```

### **3. Protobuf (performance):**

protobuf

```protobuf
syntax = "proto3";

message UserEvent {
  string event_id = 1;
  string event_type = 2;
  string user_id = 3;
  int64 timestamp = 4;
  UserData data = 5;
}

message UserData {
  string email = 1;
  string nome = 2;
  string status = 3;
}
```

## 🏗️ **EXEMPLOS PRÁTICOS POR DOMÍNIO:**

### **📦 E-commerce - Pedido Criado:**

json

```json
{
  "eventId": "order-001",
  "eventType": "ORDER_CREATED",
  "aggregateId": "order-12345",
  "timestamp": "2024-08-16T10:30:00Z",
  "source": "checkout-service",
  "data": {
    "orderId": "order-12345",
    "customerId": "customer-456",
    "items": [
      {
        "productId": "prod-789",
        "quantity": 2,
        "price": 50.00
      }
    ],
    "total": 100.00,
    "paymentMethod": "CREDIT_CARD",
    "shippingAddress": {
      "street": "Rua das Flores, 123",
      "city": "São Paulo",
      "state": "SP",
      "zipCode": "01234-567"
    }
  },
  "metadata": {
    "correlationId": "session-abc123",
    "userAgent": "mobile-app-v2.1",
    "channel": "MOBILE"
  }
}
```

### **💳 Banking - Transação PIX:**

json

```json
{
  "eventId": "pix-001",
  "eventType": "PIX_TRANSFER_COMPLETED",
  "aggregateId": "txn-987654",
  "timestamp": "2024-08-16T10:30:15Z",
  "source": "payment-engine",
  "data": {
    "transactionId": "txn-987654",
    "fromAccount": "12345-6",
    "toAccount": "78901-2",
    "amount": 250.00,
    "currency": "BRL",
    "pixKey": "joao@email.com",
    "description": "Pagamento aluguel"
  },
  "metadata": {
    "correlationId": "pix-req-456",
    "authenticationMethod": "BIOMETRIC",
    "location": "São Paulo, SP"
  }
}
```

### **📱 User Analytics - Evento de Clique:**

json

```json
{
  "eventId": "click-001",
  "eventType": "BUTTON_CLICKED", 
  "aggregateId": "user-session-789",
  "timestamp": "2024-08-16T10:30:45Z",
  "source": "mobile-app",
  "data": {
    "userId": "user-123",
    "sessionId": "session-789",
    "buttonId": "add-to-cart",
    "productId": "prod-456",
    "page": "/product-details",
    "coordinates": {
      "x": 150,
      "y": 300
    }
  },
  "metadata": {
    "deviceType": "MOBILE",
    "osVersion": "iOS 17.1",
    "appVersion": "3.2.1"
  }
}
```

## 🔧 **HEADERS TÍPICOS:**

java

```java
// Headers Kafka (key-value pairs)
Headers headers = new RecordHeaders();
headers.add("eventType", "ORDER_CREATED".getBytes());
headers.add("source", "checkout-service".getBytes());
headers.add("version", "v1".getBytes());
headers.add("contentType", "application/json".getBytes());
headers.add("correlationId", "req-123".getBytes());

// No consumer:
@KafkaListener(topics = "orders")
public void process(@Payload OrderEvent event, 
                   @Header("eventType") String eventType,
                   @Header("correlationId") String correlationId) {
    // Processa baseado no header
}
```

## 🎨 **KEY STRATEGIES:**

### **1. Por ID da entidade:**

java

```java
// Garante ordem por usuário
kafkaTemplate.send("user-events", user.getId(), userEvent);
```

### **2. Por particionamento customizado:**

java

```java
// Por região geográfica
String key = user.getRegion() + "-" + user.getId();
kafkaTemplate.send("user-events", key, userEvent);
```

### **3. Sem key (round-robin):**

java

```java
// Distribui uniformemente
kafkaTemplate.send("analytics-events", null, analyticsEvent);
```

## 📊 **ESTRUTURAS ESPECIAIS:**

### **Dead Letter Queue (DLQ):**

json

```json
{
  "originalTopic": "orders",
  "originalPartition": 1,
  "originalOffset": 12345,
  "errorReason": "PAYMENT_SERVICE_TIMEOUT",
  "errorTimestamp": "2024-08-16T10:35:00Z",
  "retryCount": 3,
  "originalMessage": {
    // Mensagem original que falhou
  }
}
```

### **Saga Pattern (Event Sourcing):**

json

```json
{
  "sagaId": "order-saga-123",
  "eventType": "PAYMENT_PROCESSED",
  "step": 2,
  "totalSteps": 4,
  "compensationEvents": [
    "CANCEL_INVENTORY_RESERVATION",
    "REFUND_PAYMENT"
  ],
  "data": {
    // Dados do evento
  }
}
```

## ⚡ **BOAS PRÁTICAS:**

### **✅ DO:**

- Use `eventId` único para idempotência
- Inclua `timestamp` para debugging
- Use `correlationId` para rastreamento
- Mantenha `data` específico do domínio
- Versione seus schemas

### **❌ DON'T:**

- Não coloque dados sensíveis (senhas, cartões)
- Não faça payloads gigantes (>1MB)
- Não mude estrutura sem versionamento
- Não use campos genéricos demais