### **1. POR FUNCIONALIDADE (mais comum)**

```
Tópico: "pedidos-criados"
├── Group "pagamento" → Processa cobrança
├── Group "estoque" → Atualiza disponibilidade  
├── Group "email" → Envia confirmação
├── Group "logistica" → Agenda entrega
└── Group "analytics" → Atualiza dashboards
```

### **2. POR AMBIENTE**

```
Tópico: "user-events"
├── Group "prod-analytics" → Produção
├── Group "staging-analytics" → Homologação
├── Group "dev-analytics" → Desenvolvimento
└── Group "qa-testing" → Testes automatizados
```

### **3. POR PRIORIDADE/SLA**

```
Tópico: "transactions"
├── Group "critical-processing" → SLA: 100ms
├── Group "normal-processing" → SLA: 1s
├── Group "batch-processing" → SLA: 5min
└── Group "reporting" → SLA: 1h
```

### **4. POR REGIÃO GEOGRÁFICA**

```
Tópico: "global-orders"
├── Group "us-east-processors" → Virginia datacenter
├── Group "us-west-processors" → California datacenter
├── Group "europe-processors" → Frankfurt datacenter
└── Group "asia-processors" → Singapore datacenter
```

### **5. POR VERSÃO DE SOFTWARE**

```
Tópico: "api-events"
├── Group "app-v1" → Aplicação legada
├── Group "app-v2" → Nova versão
└── Group "canary-v3" → Teste de nova feature
```

## 🏢 **CENÁRIOS REAIS POR EMPRESA:**

### **🛒 MERCADO LIVRE:**

yaml

```yaml
# Pedido criado dispara múltiplos processos
Topic: "order-created"
Groups:
  - "payment-processor": 
      services: [credit-card, pix, boleto]
      replicas: 10
      sla: "< 2s"
  
  - "inventory-updater":
      services: [stock-reduction, availability]  
      replicas: 5
      sla: "< 5s"
      
  - "seller-notification":
      services: [email, sms, push]
      replicas: 3
      sla: "< 30s"
      
  - "fraud-detection":
      services: [ml-analysis, blacklist-check]
      replicas: 2  
      sla: "< 10s"
      
  - "recommendation-engine":
      services: [user-behavior, product-affinity]
      replicas: 1
      sla: "< 5min"
```

### **🏦 NUBANK:**

yaml

```yaml
# Transação PIX processada
Topic: "pix-transaction"
Groups:
  - "fraud-realtime":
      priority: "critical"
      replicas: 20
      sla: "< 50ms"
      
  - "balance-update":
      priority: "critical" 
      replicas: 15
      sla: "< 100ms"
      
  - "notification-push":
      priority: "high"
      replicas: 5
      sla: "< 1s"
      
  - "compliance-audit":
      priority: "medium"
      replicas: 2
      sla: "< 30s"
      
  - "analytics-realtime":
      priority: "low"
      replicas: 1
      sla: "< 5min"
```

### **🎬 NETFLIX:**

yaml

```yaml
# Usuário assiste filme
Topic: "video-watched"
Groups:
  - "recommendation-ml":
      purpose: "Update user preferences"
      replicas: 50
      
  - "billing-tracker":
      purpose: "Track usage for billing"
      replicas: 10
      
  - "content-analytics":
      purpose: "Content performance metrics"
      replicas: 5
      
  - "ab-testing":
      purpose: "Feature experiment data"
      replicas: 3
      
  - "data-warehouse":
      purpose: "Long-term storage for BI"
      replicas: 1
```

### **🚗 UBER:**

yaml

```yaml
# Corrida finalizada
Topic: "trip-completed"
Groups:
  - "payment-processing":
      regions: ["us-east", "us-west", "europe", "latam"]
      replicas: 100
      
  - "driver-rating":
      purpose: "Update driver scores"
      replicas: 20
      
  - "surge-pricing":
      purpose: "Real-time pricing adjustment"  
      replicas: 30
      
  - "eta-optimization":
      purpose: "Improve route predictions"
      replicas: 10
      
  - "city-planning":
      purpose: "Traffic pattern analysis"
      replicas: 2
```

## 🔄 **PADRÕES AVANÇADOS:**

### **A. PIPELINE DE PROCESSAMENTO:**

yaml

```yaml
# Dados fluem entre tópicos
Topic: "raw-events" 
└── Group: "enricher" → Topic: "enriched-events"
    └── Group: "aggregator" → Topic: "aggregated-events"
        └── Group: "alerting" → Topic: "alerts"
```

### **B. FAN-OUT + FAN-IN:**

yaml

```yaml
# Um evento dispara múltiplos processamentos que convergem
Topic: "user-signup"
├── Group: "email-verification" → Topic: "verified-users"
├── Group: "profile-creation" → Topic: "verified-users"  
└── Group: "welcome-campaign" → Topic: "verified-users"

Topic: "verified-users"
└── Group: "onboarding-complete" → Topic: "active-users"
```

### **C. CIRCUIT BREAKER:**

yaml

```yaml
# Grupos backup para tolerância a falhas
Topic: "critical-transactions"
├── Group: "primary-processor" (healthy)
└── Group: "backup-processor" (standby, ativa se primary falhar)
```

### **D. MULTI-TENANT:**

yaml

```yaml
# Diferentes clientes, mesmo código
Topic: "tenant-events"
├── Group: "tenant-123-processor"
├── Group: "tenant-456-processor"
└── Group: "tenant-789-processor"
```

## ⚙️ **CONFIGURAÇÕES TÍPICAS:**

### **ALTA PERFORMANCE:**

yaml

```yaml
group: "realtime-trading"
properties:
  session.timeout.ms: 10000
  heartbeat.interval.ms: 3000
  max.poll.records: 1
  enable.auto.commit: false
  fetch.min.bytes: 1
```

### **BATCH PROCESSING:**

yaml

```yaml
group: "daily-reports" 
properties:
  session.timeout.ms: 300000
  max.poll.records: 10000
  fetch.min.bytes: 1048576
  enable.auto.commit: true
```

### **DESENVOLVIMENTO:**

yaml

```yaml
group: "dev-testing"
properties:
  auto.offset.reset: earliest
  enable.auto.commit: true
  session.timeout.ms: 30000
```

## 📊 **MONITORAMENTO POR GRUPO:**

yaml

```yaml
# Métricas importantes por consumer group
metrics:
  - consumer_lag: "Mensagens não processadas"
  - throughput: "Mensagens/segundo"
  - error_rate: "% de falhas"  
  - processing_time: "Tempo médio de processamento"
  - rebalance_frequency: "Frequência de redistribuição"
```

**A escolha do padrão depende da arquitetura, SLA e necessidades específicas do negócio!**
## ❌ **ANTI-PATTERN: Tópico por endpoint**

```
❌ RUIM:
├── topico-criar-usuario
├── topico-atualizar-usuario  
├── topico-deletar-usuario
├── topico-criar-produto
├── topico-atualizar-produto
├── topico-deletar-produto
└── ... (centenas de tópicos!)
```

