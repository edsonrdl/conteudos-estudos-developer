## O que é Pub/Sub?

Pub/Sub (Publish/Subscribe) é um padrão de mensageria assíncrona onde **publicadores** (publishers) enviam mensagens sem saber quem são os **assinantes** (subscribers), e assinantes recebem mensagens sem saber quem são os publicadores. A comunicação é intermediada por um **broker de mensagens** ou **tópico** que gerencia a distribuição.

É fundamentalmente diferente do modelo tradicional ponto-a-ponto, onde um remetente envia mensagem diretamente para um destinatário específico.

## Como Funciona?

### Modelo Tradicional (Ponto-a-Ponto)

```
[Aplicação A] ----mensagem----> [Aplicação B]
```

- A precisa conhecer B
- Acoplamento direto
- Se B está fora, mensagem é perdida

### Modelo Pub/Sub

```
                    ┌─> [Subscriber 1]
[Publisher] → [Tópico] ├─> [Subscriber 2]
                    └─> [Subscriber 3]
```

- Publisher não conhece subscribers
- Subscribers não conhecem publisher
- Tópico gerencia a distribuição
- Desacoplamento total

## Componentes Principais

### Publisher (Publicador)

- **Função**: Envia mensagens para tópicos
- **Características**:
    - Não sabe quantos subscribers existem
    - Não sabe se a mensagem foi processada
    - Apenas "publica e esquece"

### Subscriber (Assinante)

- **Função**: Recebe mensagens de tópicos
- **Características**:
    - Se inscreve em tópicos de interesse
    - Recebe todas as mensagens do tópico (ou filtradas)
    - Pode haver múltiplos subscribers para o mesmo tópico

### Topic/Channel (Tópico/Canal)

- **Função**: Intermediário que gerencia mensagens
- **Características**:
    - Recebe mensagens dos publishers
    - Distribui para todos os subscribers ativos
    - Pode implementar filtros e regras

### Message Broker

- **Função**: Sistema que gerencia todos os tópicos
- **Exemplos**: RabbitMQ, Apache Kafka, Redis, AWS SNS, Google Pub/Sub

## Exemplo Visual Detalhado

[ Imagine um sistema de e-commerce:]

## Características Principais

### 1. **Desacoplamento**

- **Temporal**: Publisher e subscriber não precisam estar ativos simultaneamente
- **Espacial**: Não precisam saber a localização um do outro
- **Sincronização**: Comunicação assíncrona, não bloqueante

### 2. **Escalabilidade**

- Adicionar novos subscribers não afeta publishers
- Pode haver múltiplos publishers para o mesmo tópico
- Distribui carga entre vários consumidores

### 3. **Flexibilidade**

- Subscribers podem filtrar mensagens de interesse
- Fácil adicionar novos tipos de mensagens
- Permite diferentes formatos de mensagem

## Exemplo de Código Simples
# ===================================
# EXEMPLO 1: PUB/SUB SIMPLES EM PYTHON
# ===================================

class SimplePubSub:
    """Implementação básica de Pub/Sub para entender o conceito"""
    
    def __init__(self):
        # Dicionário onde a chave é o tópico e o valor é lista de subscribers
        self.topics = {}
    
    def subscribe(self, topic, callback):
        """Inscreve uma função callback em um tópico"""
        if topic not in self.topics:
            self.topics[topic] = []
        self.topics[topic].append(callback)
        print(f"✓ Subscriber adicionado ao tópico '{topic}'")
    
    def publish(self, topic, message):
        """Publica mensagem para todos os subscribers do tópico"""
        if topic not in self.topics:
            print(f"⚠️  Nenhum subscriber para o tópico '{topic}'")
            return
        
        print(f"\n📢 Publicando no tópico '{topic}': {message}")
        for callback in self.topics[topic]:
            callback(message)
    
    def unsubscribe(self, topic, callback):
        """Remove subscriber de um tópico"""
        if topic in self.topics:
            self.topics[topic].remove(callback)
            print(f"✓ Subscriber removido do tópico '{topic}'")

# Exemplo de uso
def email_handler(message):
    print(f"   📧 Email Service: Enviando email sobre '{message}'")

def log_handler(message):
    print(f"   📝 Logger: Registrando '{message}'")

def analytics_handler(message):
    print(f"   📊 Analytics: Analisando '{message}'")

# Criar instância do pub/sub
pubsub = SimplePubSub()

# Subscribers se inscrevem em tópicos
pubsub.subscribe("user_registration", email_handler)
pubsub.subscribe("user_registration", analytics_handler)
pubsub.subscribe("system_events", log_handler)

# Publishers publicam mensagens
pubsub.publish("user_registration", "Novo usuário: joão@email.com")
pubsub.publish("system_events", "Sistema iniciado às 10:00")
pubsub.publish("user_registration", "Novo usuário: maria@email.com")

# ===================================
# EXEMPLO 2: PUB/SUB COM REDIS
# ===================================

import redis
import json
import threading
import time

class RedisPubSub:
    """Implementação de Pub/Sub usando Redis como broker"""
    
    def __init__(self, host='localhost', port=6379):
        self.redis_client = redis.Redis(host=host, port=port, decode_responses=True)
        self.pubsub = self.redis_client.pubsub()
        self.running = False
        
    def publish(self, channel, message):
        """Publica mensagem em um canal"""
        if isinstance(message, dict):
            message = json.dumps(message)
        
        subscribers = self.redis_client.publish(channel, message)
        print(f"📤 Mensagem publicada no canal '{channel}' para {subscribers} subscribers")
        return subscribers
    
    def subscribe(self, channels, callback):
        """Inscreve-se em canais e processa mensagens"""
        if isinstance(channels, str):
            channels = [channels]
        
        self.pubsub.subscribe(*channels)
        self.running = True
        print(f"📥 Inscrito nos canais: {channels}")
        
        # Thread para escutar mensagens
        def listener():
            for message in self.pubsub.listen():
                if not self.running:
                    break
                    
                if message['type'] == 'message':
                    try:
                        data = json.loads(message['data'])
                    except:
                        data = message['data']
                    
                    callback(message['channel'], data)
        
        thread = threading.Thread(target=listener)
        thread.daemon = True
        thread.start()
        return thread
    
    def unsubscribe(self, channels=None):
        """Cancela inscrição dos canais"""
        self.running = False
        if channels:
            self.pubsub.unsubscribe(channels)
        else:
            self.pubsub.unsubscribe()
        print("🔌 Desconectado dos canais")

# Exemplo de uso com Redis
def order_processor(channel, message):
    print(f"🛒 Processando pedido do canal '{channel}': {message}")

def inventory_updater(channel, message):
    print(f"📦 Atualizando inventário do canal '{channel}': {message}")

# Simular uso (requer Redis rodando)
"""
# Publisher
redis_pub = RedisPubSub()
redis_pub.publish('orders', {'order_id': 123, 'total': 99.90})
redis_pub.publish('inventory', {'product_id': 456, 'quantity': -1})

# Subscriber
redis_sub = RedisPubSub()
redis_sub.subscribe(['orders', 'inventory'], order_processor)

# Manter rodando
time.sleep(10)
redis_sub.unsubscribe()
"""

# ===================================
# EXEMPLO 3: PUB/SUB COM AWS SNS/SQS
# ===================================

import boto3

class AWSPubSub:
    """Implementação de Pub/Sub usando AWS SNS e SQS"""
    
    def __init__(self, region='us-east-1'):
        self.sns_client = boto3.client('sns', region_name=region)
        self.sqs_client = boto3.client('sqs', region_name=region)
        
    def create_topic(self, topic_name):
        """Cria um tópico SNS"""
        response = self.sns_client.create_topic(Name=topic_name)
        topic_arn = response['TopicArn']
        print(f"✓ Tópico criado: {topic_arn}")
        return topic_arn
    
    def create_queue(self, queue_name):
        """Cria uma fila SQS"""
        response = self.sqs_client.create_queue(QueueName=queue_name)
        queue_url = response['QueueUrl']
        print(f"✓ Fila criada: {queue_url}")
        return queue_url
    
    def subscribe_queue_to_topic(self, topic_arn, queue_url):
        """Inscreve uma fila SQS em um tópico SNS"""
        # Obter ARN da fila
        queue_attrs = self.sqs_client.get_queue_attributes(
            QueueUrl=queue_url,
            AttributeNames=['QueueArn']
        )
        queue_arn = queue_attrs['Attributes']['QueueArn']
        
        # Inscrever fila no tópico
        response = self.sns_client.subscribe(
            TopicArn=topic_arn,
            Protocol='sqs',
            Endpoint=queue_arn
        )
        
        print(f"✓ Fila inscrita no tópico")
        return response['SubscriptionArn']
    
    def publish_message(self, topic_arn, message, attributes=None):
        """Publica mensagem no tópico SNS"""
        params = {
            'TopicArn': topic_arn,
            'Message': json.dumps(message) if isinstance(message, dict) else message
        }
        
        if attributes:
            params['MessageAttributes'] = {
                key: {'DataType': 'String', 'StringValue': str(value)}
                for key, value in attributes.items()
            }
        
        response = self.sns_client.publish(**params)
        print(f"📤 Mensagem publicada: {response['MessageId']}")
        return response['MessageId']
    
    def consume_messages(self, queue_url, callback, max_messages=10):
        """Consome mensagens de uma fila SQS"""
        while True:
            response = self.sqs_client.receive_message(
                QueueUrl=queue_url,
                MaxNumberOfMessages=max_messages,
                WaitTimeSeconds=20  # Long polling
            )
            
            messages = response.get('Messages', [])
            
            for message in messages:
                try:
                    # Processar mensagem
                    body = json.loads(message['Body'])
                    callback(body)
                    
                    # Deletar mensagem após processamento
                    self.sqs_client.delete_message(
                        QueueUrl=queue_url,
                        ReceiptHandle=message['ReceiptHandle']
                    )
                    
                except Exception as e:
                    print(f"❌ Erro ao processar mensagem: {e}")

# Exemplo de uso com AWS
"""
aws_pubsub = AWSPubSub()

# Criar infraestrutura
topic_arn = aws_pubsub.create_topic('orders-topic')
queue_url = aws_pubsub.create_queue('orders-processor-queue')
aws_pubsub.subscribe_queue_to_topic(topic_arn, queue_url)

# Publisher
aws_pubsub.publish_message(
    topic_arn,
    {'order_id': 789, 'customer': 'João', 'total': 150.00},
    {'priority': 'high'}
)

# Consumer
def process_order(message):
    print(f"Processing order: {message}")

aws_pubsub.consume_messages(queue_url, process_order)
"""

# ===================================
# EXEMPLO 4: PUB/SUB COM RABBITMQ
# ===================================

"""
# Requer: pip install pika

import pika
import json

class RabbitMQPubSub:
    def __init__(self, host='localhost'):
        self.connection = pika.BlockingConnection(
            pika.ConnectionParameters(host)
        )
        self.channel = self.connection.channel()
    
    def create_exchange(self, exchange_name, exchange_type='fanout'):
        # Exchange é como um tópico no RabbitMQ
        self.channel.exchange_declare(
            exchange=exchange_name,
            exchange_type=exchange_type
        )
        print(f"✓ Exchange '{exchange_name}' criado")
    
    def publish(self, exchange, message, routing_key=''):
        self.channel.basic_publish(
            exchange=exchange,
            routing_key=routing_key,
            body=json.dumps(message) if isinstance(message, dict) else message
        )
        print(f"📤 Mensagem publicada no exchange '{exchange}'")
    
    def subscribe(self, exchange, callback, queue_name='', routing_key=''):
        # Criar fila temporária se não especificada
        if not queue_name:
            result = self.channel.queue_declare(queue='', exclusive=True)
            queue_name = result.method.queue
        else:
            self.channel.queue_declare(queue=queue_name)
        
        # Bind da fila ao exchange
        self.channel.queue_bind(
            exchange=exchange,
            queue=queue_name,
            routing_key=routing_key
        )
        
        # Callback wrapper
        def wrapper(ch, method, properties, body):
            try:
                message = json.loads(body)
            except:
                message = body.decode()
            callback(message)
        
        # Consumir mensagens
        self.channel.basic_consume(
            queue=queue_name,
            on_message_callback=wrapper,
            auto_ack=True
        )
        
        print(f"📥 Inscrito no exchange '{exchange}'")
        
        # Iniciar consumo
        self.channel.start_consuming()
    
    def close(self):
        self.connection.close()
"""

# ===================================
# EXEMPLO 5: PUB/SUB COM PADRÃO OBSERVER
# ===================================

from typing import Dict, List, Callable
from dataclasses import dataclass
from datetime import datetime
from enum import Enum

class EventType(Enum):
    USER_REGISTERED = "user_registered"
    ORDER_PLACED = "order_placed"
    PAYMENT_RECEIVED = "payment_received"
    ITEM_SHIPPED = "item_shipped"

@dataclass
class Event:
    """Classe para representar um evento"""
    type: EventType
    data: Dict
    timestamp: datetime = None
    
    def __post_init__(self):
        if self.timestamp is None:
            self.timestamp = datetime.now()

class EventBus:
    """Event Bus implementando padrão Pub/Sub"""
    
    def __init__(self):
        self._subscribers: Dict[EventType, List[Callable]] = {}
        self._middleware: List[Callable] = []
        
    def subscribe(self, event_type: EventType, handler: Callable):
        """Inscreve um handler para um tipo de evento"""
        if event_type not in self._subscribers:
            self._subscribers[event_type] = []
        
        self._subscribers[event_type].append(handler)
        print(f"✓ {handler.__name__} inscrito em {event_type.value}")
        
    def unsubscribe(self, event_type: EventType, handler: Callable):
        """Remove um handler de um tipo de evento"""
        if event_type in self._subscribers:
            self._subscribers[event_type].remove(handler)
            print(f"✓ {handler.__name__} removido de {event_type.value}")
    
    def publish(self, event: Event):
        """Publica um evento para todos os subscribers"""
        print(f"\n📢 Publicando evento: {event.type.value}")
        print(f"   Timestamp: {event.timestamp}")
        print(f"   Data: {event.data}")
        
        # Aplicar middleware
        for middleware in self._middleware:
            event = middleware(event)
            if event is None:
                print("   ⚠️ Evento filtrado por middleware")
                return
        
        # Notificar subscribers
        if event.type in self._subscribers:
            for handler in self._subscribers[event.type]:
                try:
                    handler(event)
                except Exception as e:
                    print(f"   ❌ Erro no handler {handler.__name__}: {e}")
        else:
            print(f"   ⚠️ Nenhum subscriber para {event.type.value}")
    
    def add_middleware(self, middleware: Callable):
        """Adiciona middleware para processar eventos antes da entrega"""
        self._middleware.append(middleware)
        print(f"✓ Middleware {middleware.__name__} adicionado")

# Exemplo de uso do Event Bus
def main_example():
    print("=" * 60)
    print("DEMONSTRAÇÃO: PUB/SUB COM EVENT BUS")
    print("=" * 60)
    
    # Criar event bus
    event_bus = EventBus()
    
    # Definir handlers (subscribers)
    def send_welcome_email(event: Event):
        print(f"   📧 Email: Enviando boas-vindas para {event.data['email']}")
    
    def update_analytics(event: Event):
        print(f"   📊 Analytics: Registrando {event.type.value}")
    
    def create_user_profile(event: Event):
        print(f"   👤 Profile: Criando perfil para {event.data['username']}")
    
    def process_order(event: Event):
        print(f"   🛒 Order: Processando pedido #{event.data['order_id']}")
    
    def update_inventory(event: Event):
        print(f"   📦 Inventory: Atualizando estoque")
    
    def charge_payment(event: Event):
        print(f"   💳 Payment: Cobrando ${event.data['amount']}")
    
    # Inscrever handlers em eventos
    event_bus.subscribe(EventType.USER_REGISTERED, send_welcome_email)
    event_bus.subscribe(EventType.USER_REGISTERED, create_user_profile)
    event_bus.subscribe(EventType.USER_REGISTERED, update_analytics)
    
    event_bus.subscribe(EventType.ORDER_PLACED, process_order)
    event_bus.subscribe(EventType.ORDER_PLACED, update_inventory)
    event_bus.subscribe(EventType.ORDER_PLACED, update_analytics)
    
    event_bus.subscribe(EventType.PAYMENT_RECEIVED, charge_payment)
    event_bus.subscribe(EventType.PAYMENT_RECEIVED, update_analytics)
    
    # Middleware exemplo: log de todos os eventos
    def logging_middleware(event: Event):
        print(f"   🔍 Middleware: Logando evento {event.type.value}")
        return event  # Retornar None filtraria o evento
    
    event_bus.add_middleware(logging_middleware)
    
    # Publicar eventos
    print("\n" + "=" * 40)
    print("PUBLICANDO EVENTOS")
    print("=" * 40)
    
    # Evento 1: Novo usuário
    event_bus.publish(Event(
        type=EventType.USER_REGISTERED,
        data={
            'user_id': 123,
            'username': 'joao_silva',
            'email': 'joao@email.com'
        }
    ))
    
    # Evento 2: Pedido realizado
    event_bus.publish(Event(
        type=EventType.ORDER_PLACED,
        data={
            'order_id': 456,
            'user_id': 123,
            'items': ['Produto A', 'Produto B'],
            'total': 99.90
        }
    ))
    
    # Evento 3: Pagamento recebido
    event_bus.publish(Event(
        type=EventType.PAYMENT_RECEIVED,
        data={
            'order_id': 456,
            'amount': 99.90,
            'method': 'credit_card'
        }
    ))
    
    print("\n" + "=" * 60)
    print("✅ DEMONSTRAÇÃO CONCLUÍDA")
    print("=" * 60)

if __name__ == "__main__":
    main_example()

## Vantagens do Pub/Sub

### 1. **Desacoplamento Total**

Publishers e subscribers não se conhecem, permitindo evolução independente dos componentes. Facilita manutenção e reduz dependências.

### 2. **Escalabilidade Dinâmica**

Adicionar novos subscribers não impacta publishers. Pode processar milhões de mensagens distribuindo entre múltiplos consumidores.

### 3. **Flexibilidade**

Novos tipos de processamento podem ser adicionados sem modificar código existente. Subscribers podem escolher quais mensagens processar.

### 4. **Resiliência**

Se um subscriber falha, outros continuam funcionando. Mensagens podem ser reprocessadas em caso de erro.

### 5. **Broadcasting Eficiente**

Uma mensagem é automaticamente entregue a todos os interessados. Ideal para notificações e propagação de eventos.

## Desvantagens do Pub/Sub

### 1. **Complexidade de Debugging**

Fluxo de mensagens pode ser difícil de rastrear. Erros podem ocorrer em qualquer subscriber sem afetar outros.

### 2. **Sem Garantia de Entrega**

Em implementações básicas, mensagens podem ser perdidas se não houver subscribers ativos.

### 3. **Ordem de Mensagens**

Ordem de processamento pode não ser garantida em sistemas distribuídos.

### 4. **Overhead de Infraestrutura**

Requer message broker adicional. Adiciona latência comparado a chamadas diretas.

### 5. **Possível Duplicação**

Mensagens podem ser entregues mais de uma vez em caso de falhas de rede.

## Quando Usar Pub/Sub?

### ✅ **USE quando:**

- **Múltiplos consumidores** precisam reagir ao mesmo evento
- **Sistemas precisam escalar** independentemente
- **Broadcasting** de informações é necessário
- **Processamento assíncrono** é aceitável
- **Microsserviços** precisam se comunicar
- **Event-driven architecture** é o objetivo
- **Notificações em tempo real** são necessárias

### ❌ **NÃO USE quando:**

- **Resposta síncrona** é necessária imediatamente
- **Transações ACID** são críticas
- **Ordem estrita** é mandatória
- **Sistema é simples** com poucos componentes
- **Latência ultra-baixa** é requisito
- **Debugging simples** é prioridade

## Comparação: Pub/Sub vs Outras Arquiteturas

### Pub/Sub vs Request/Response

```
Request/Response:
Cliente → Servidor → Resposta
- Síncrono
- Acoplado
- Simples de debugar

Pub/Sub:
Publisher → Broker → Subscribers
- Assíncrono  
- Desacoplado
- Complexo de debugar
```

### Pub/Sub vs Message Queue

```
Message Queue (Fila):
- Uma mensagem → Um consumidor
- Ordem garantida (FIFO)
- Load balancing

Pub/Sub:
- Uma mensagem → Múltiplos consumidores
- Ordem nem sempre garantida
- Broadcasting
```

## Casos de Uso Reais

### 1. **E-commerce**

- Pedido realizado → Email, Estoque, Faturamento, Analytics
- Pagamento confirmado → Shipping, Email, Contabilidade

### 2. **Redes Sociais**

- Post publicado → Notificações, Feed, Cache, Search Index
- Like/Comment → Notificação, Analytics, Trending

### 3. **IoT**

- Sensor data → Storage, Real-time Dashboard, Alerting
- Device status → Monitoring, Logging, Maintenance

### 4. **Microserviços**

- User registration → Profile, Email, Analytics, CRM
- Data change → Cache invalidation, Search update, Audit

### 5. **Gaming**

- Player action → Game state, Leaderboard, Achievements
- Match ended → Stats, Rewards, Matchmaking

## Implementações Populares

### Cloud Services

- **AWS SNS**: Simple Notification Service
- **Google Pub/Sub**: Cloud Pub/Sub
- **Azure Service Bus**: Topics e Subscriptions

### Message Brokers

- **Apache Kafka**: Streaming distribuído
- **RabbitMQ**: AMQP broker
- **Redis Pub/Sub**: In-memory pub/sub

### Frameworks

- **Spring Cloud Stream**: Java/Spring
- **MassTransit**: .NET
- **Celery**: Python

## Melhores Práticas

### 1. **Design de Tópicos**

python

```python
# ❌ Ruim - muito genérico
publish("events", data)

# ✅ Bom - específico e organizado
publish("orders.created.v1", data)
publish("users.registered.v2", data)
```

### 2. **Mensagens Idempotentes**

python

```python
# Incluir ID único para evitar processamento duplicado
message = {
    "id": "uuid-123",  # ID único
    "timestamp": "2024-01-01T10:00:00",
    "data": {...}
}
```

### 3. **Versionamento**

python

```python
# Permitir evolução das mensagens
message = {
    "version": "1.0",
    "type": "user.created",
    "data": {...}
}
```

### 4. **Error Handling**

python

```python
def process_with_retry(message, max_retries=3):
    for attempt in range(max_retries):
        try:
            process_message(message)
            break
        except Exception as e:
            if attempt == max_retries - 1:
                send_to_dlq(message, str(e))
            else:
                time.sleep(2 ** attempt)  # Exponential backoff
```

## Conclusão

Pub/Sub é um padrão arquitetural fundamental para sistemas modernos distribuídos. Ele resolve problemas de acoplamento e escalabilidade, permitindo que sistemas evoluam independentemente enquanto mantêm comunicação eficiente.

**Pontos-chave:**

- ✅ Excelente para desacoplar componentes
- ✅ Permite escalabilidade horizontal
- ✅ Facilita adição de novos recursos
- ⚠️ Adiciona complexidade operacional
- ⚠️ Requer cuidado com ordenação e duplicação

O padrão Pub/Sub é essencial para arquiteturas de microsserviços, sistemas event-driven e aplicações que precisam de comunicação assíncrona eficiente entre múltiplos componentes.