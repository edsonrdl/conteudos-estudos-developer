### 📊 Cenário Inicial (baixo volume):

```
Tópico: pedidos (5 partições já criadas)
├── Partition 0: [2 mensagens]
├── Partition 1: [2 mensagens]  
├── Partition 2: [2 mensagens]
├── Partition 3: [2 mensagens]
└── Partition 4: [2 mensagens]

Kubernetes: 1 Pod (1 consumer) → lê todas as 5 partições
```

### 🚀 Cenário Alto Volume:

```
Mesmo tópico (5 partições)
├── Partition 0: [20 mensagens]
├── Partition 1: [20 mensagens]
├── Partition 2: [20 mensagens]  
├── Partition 3: [20 mensagens]
└── Partition 4: [20 mensagens]

Kubernetes: 5 Pods (5 consumers)
├── Pod 1 → Partition 0 (20 msgs)
├── Pod 2 → Partition 1 (20 msgs)
├── Pod 3 → Partition 2 (20 msgs)
├── Pod 4 → Partition 3 (20 msgs)
└── Pod 5 → Partition 4 (20 msgs)
```

## 🎯 Estratégia de Planejamento:

### 1. **Crie partições pensando no PICO, não no volume atual**

yaml

```yaml
# Exemplo: Sistema que hoje tem 10 msg/min, mas na Black Friday pode ter 1000 msg/min
apiVersion: v1
kind: ConfigMap
metadata:
  name: kafka-config
data:
  topic: "pedidos"
  partitions: "20"  # Pensando no pico futuro
  current-pods: "2"  # Hoje
  max-pods: "20"     # Black Friday
```

### 2. **HPA (Horizontal Pod Autoscaler) baseado em lag**

yaml

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: kafka-consumer-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: pedidos-consumer
  minReplicas: 2
  maxReplicas: 20  # Limitado pelo número de partições
  metrics:
  - type: External
    external:
      metric:
        name: kafka_consumer_lag
      target:
        type: Value
        value: "100"  # Se lag > 100 msgs, escala
```

### 3. **Cálculo Prático de Partições**

```
Fórmula:
Partições = (Pico de Msgs/min ÷ Msgs processadas por consumer/min) × Fator de Segurança

Exemplo:
- Pico: 10.000 msgs/min
- 1 Consumer processa: 500 msgs/min  
- Fator segurança: 1.5x

Partições = (10.000 ÷ 500) × 1.5 = 30 partições
```

## ⚠️ Armadilhas Importantes:

### ❌ Erro Comum:

```
"Vou criar 2 partições hoje e depois aumento"
```

**Problema**: Kafka não permite aumentar partições facilmente, e a distribuição de chaves muda!

### ✅ Abordagem Correta:

```
"Vou criar 20 partições hoje, usar 2 consumers, e escalar conforme necessidade"
```

## 🏗️ Arquitetura Kubernetes Recomendada:

yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pedidos-consumer
spec:
  replicas: 2  # Inicia baixo
  template:
    spec:
      containers:
      - name: consumer
        image: meu-consumer:v1
        env:
        - name: KAFKA_GROUP_ID
          value: "pedidos-group"
        - name: KAFKA_TOPIC  
          value: "pedidos"
        - name: KAFKA_PARTITIONS
          value: "20"  # Mesmo que só use 2 consumers inicialmente
        resources:
          requests:
            memory: "256Mi"
            cpu: "200m"
          limits:
            memory: "512Mi" 
            cpu: "500m"
```

## 📈 Monitoramento:

```
Métricas para acompanhar:
├── Consumer Lag por partição
├── Throughput por consumer  
├── CPU/Memory por pod
└── Tempo de processamento por mensagem
```

**Sua estratégia está perfeita**: criar as partições antecipadamente pensando na carga máxima, e usar Kubernetes para escalar os consumers horizontalmente conforme a demanda!