
Este guia explica como configurar e usar o Apache Kafka para mensageria usando Docker, incluindo exemplos práticos em Python.

## 📋 Sumário

- [O que é Kafka?](https://claude.ai/chat/3dcbca0c-9f48-49b1-9497-8a14e9cc32f3#o-que-%C3%A9-kafka)
- [Conceitos Fundamentais](https://claude.ai/chat/3dcbca0c-9f48-49b1-9497-8a14e9cc32f3#conceitos-fundamentais)
- [Pré-requisitos](https://claude.ai/chat/3dcbca0c-9f48-49b1-9497-8a14e9cc32f3#pr%C3%A9-requisitos)
- [Configuração do Ambiente](https://claude.ai/chat/3dcbca0c-9f48-49b1-9497-8a14e9cc32f3#configura%C3%A7%C3%A3o-do-ambiente)
- [Usando os Exemplos](https://claude.ai/chat/3dcbca0c-9f48-49b1-9497-8a14e9cc32f3#usando-os-exemplos)
- [Comandos Úteis](https://claude.ai/chat/3dcbca0c-9f48-49b1-9497-8a14e9cc32f3#comandos-%C3%BAteis)
- [Monitoramento](https://claude.ai/chat/3dcbca0c-9f48-49b1-9497-8a14e9cc32f3#monitoramento)
- [Troubleshooting](https://claude.ai/chat/3dcbca0c-9f48-49b1-9497-8a14e9cc32f3#troubleshooting)
- [Próximos Passos](https://claude.ai/chat/3dcbca0c-9f48-49b1-9497-8a14e9cc32f3#pr%C3%B3ximos-passos)

## O que é Kafka?

Apache Kafka é uma plataforma de streaming distribuída que funciona como um **sistema de mensageria pub/sub** (publish/subscribe). É amplamente usado para:

- **Mensageria entre serviços**: Comunicação assíncrona entre microserviços
- **Streaming de dados em tempo real**: Processamento de grandes volumes de dados
- **Log de eventos**: Armazenamento durável de eventos de aplicação
- **Integração de sistemas**: Conectar diferentes aplicações e banco de dados

### Por que usar Kafka?

- ✅ **Alta performance**: Pode processar milhões de mensagens por segundo
- ✅ **Durabilidade**: Mensagens são persistidas em disco
- ✅ **Escalabilidade**: Fácil de escalar horizontalmente
- ✅ **Tolerância a falhas**: Replicação automática dos dados
- ✅ **Tempo real**: Latência muito baixa

## Conceitos Fundamentais

### 🏗️ **Broker**

- É um servidor Kafka individual
- Responsável por armazenar e servir as mensagens
- Em produção, você tem múltiplos brokers formando um cluster

### 📂 **Topic (Tópico)**

- É como uma "pasta" ou "canal" onde as mensagens são organizadas
- Exemplo: `user-events`, `payment-notifications`, `system-logs`
- As mensagens são categorizadas por tópico

### 📊 **Partition (Partição)**

- Cada tópico é dividido em partições para permitir paralelismo
- Cada partição é uma sequência ordenada de mensagens
- Permite que múltiplos consumers processem o mesmo tópico simultaneamente

### 📤 **Producer (Produtor)**

- Aplicação que **envia** mensagens para tópicos
- Decide em qual partição a mensagem vai ser armazenada
- Exemplo: microserviço de checkout enviando eventos de compra

### 📥 **Consumer (Consumidor)**

- Aplicação que **lê** mensagens de tópicos
- Pode processar mensagens de uma ou múltiplas partições
- Exemplo: serviço de email lendo eventos para enviar notificações

### 👥 **Consumer Group (Grupo de Consumidores)**

- Grupo de consumers que trabalham juntos para processar um tópico
- Kafka distribui as partições entre os consumers do grupo
- Se um consumer falha, outro assume suas partições

### 🎯 **Offset**

- É a posição de uma mensagem dentro de uma partição
- Como um "bookmark" que indica qual foi a última mensagem lida
- Kafka salva automaticamente o offset para cada consumer group

### 🔑 **Key (Chave)**

- Identificador opcional de uma mensagem
- Mensagens com a mesma chave vão sempre para a mesma partição
- Útil para manter ordem de eventos relacionados

### 🐘 **Zookeeper**

- Sistema usado para coordenar o cluster Kafka
- Gerencia metadados dos brokers, tópicos e partições
- **Nota**: Versões mais novas do Kafka estão removendo dependência do Zookeeper

## Pré-requisitos

- Docker e Docker Compose instalados
- Python 3.7+ (para os exemplos)
- Pelo menos 4GB de RAM disponível

## Configuração do Ambiente

### 1. Clone e Configure

```bash
# Clone este repositório
git clone <seu-repositorio>
cd kafka-docker-setup

# Suba o ambiente
docker-compose up -d

# Verifique se está rodando
docker-compose ps
```

### 2. Serviços Disponíveis

Após executar `docker-compose up -d`, você terá:

|Serviço|Porta|Descrição|
|---|---|---|
|**Zookeeper**|2181|Coordenação do cluster|
|**Kafka Broker**|9092|Broker principal|
|**Kafka UI**|8080|Interface web para monitoramento|

### 3. Verifique se está funcionando

Acesse http://localhost:8080 - você deve ver a interface do Kafka UI.

## Usando os Exemplos

### Instalação das dependências

```bash
pip install kafka-python
```

### Producer (Enviando Mensagens)

```bash
# Execute o producer
python kafka_producer.py
```

O producer vai:

- Conectar no Kafka
- Enviar mensagens de exemplo para o tópico `user-events`
- Mostrar confirmações de entrega

### Consumer (Recebendo Mensagens)

```bash
# Execute o consumer (em outro terminal)
python kafka_consumer.py
```

O consumer vai:

- Conectar no Kafka
- Ler mensagens do tópico `user-events`
- Processar cada mensagem recebida

### Testando o Fluxo Completo

1. **Terminal 1**: Execute o consumer
2. **Terminal 2**: Execute o producer
3. **Browser**: Acesse http://localhost:8080 para monitorar

Você verá as mensagens sendo enviadas pelo producer e recebidas pelo consumer em tempo real!

## Comandos Úteis

### Gerenciamento de Tópicos

```bash
# Entrar no container do Kafka
docker exec -it kafka bash

# Criar um tópico
kafka-topics --create \
  --topic meu-topico \
  --bootstrap-server localhost:9092 \
  --partitions 3 \
  --replication-factor 1

# Listar tópicos
kafka-topics --list --bootstrap-server localhost:9092

# Descrever um tópico
kafka-topics --describe --topic user-events --bootstrap-server localhost:9092

# Deletar um tópico
kafka-topics --delete --topic meu-topico --bootstrap-server localhost:9092
```

### Testando via Terminal

```bash
# Producer via terminal (digite mensagens)
kafka-console-producer --topic user-events --bootstrap-server localhost:9092

# Consumer via terminal (recebe mensagens)
kafka-console-consumer --topic user-events --from-beginning --bootstrap-server localhost:9092

# Consumer específico de um grupo
kafka-console-consumer \
  --topic user-events \
  --group meu-grupo \
  --bootstrap-server localhost:9092
```

### Monitoramento

```bash
# Ver grupos de consumers
kafka-consumer-groups --list --bootstrap-server localhost:9092

# Detalhes de um grupo específico
kafka-consumer-groups --describe --group my-consumer-group --bootstrap-server localhost:9092

# Logs do Kafka
docker-compose logs -f kafka

# Estatísticas de performance
docker exec -it kafka kafka-run-class kafka.tools.JmxTool --object-name kafka.server:type=BrokerTopicMetrics,name=MessagesInPerSec
```

## Monitoramento

### Kafka UI (Interface Web)

Acesse http://localhost:8080 para:

- **Topics**: Ver todos os tópicos, partições e mensagens
- **Consumers**: Monitorar grupos de consumers e lag
- **Brokers**: Status dos servidores Kafka
- **Messages**: Visualizar mensagens em tempo real

### Métricas Importantes

- **Lag**: Diferença entre última mensagem produzida e última consumida
- **Throughput**: Mensagens por segundo (produzidas/consumidas)
- **Partitions**: Distribuição de carga entre partições
- **Replication**: Status da replicação entre brokers

## Troubleshooting

### Problemas Comuns

#### ❌ "Connection refused" ao conectar

```bash
# Verifique se os containers estão rodando
docker-compose ps

# Verifique os logs
docker-compose logs kafka

# Reinicie os serviços
docker-compose restart
```

#### ❌ Consumer não recebe mensagens

```bash
# Verifique se o tópico existe
docker exec -it kafka kafka-topics --list --bootstrap-server localhost:9092

# Verifique o offset do consumer group
kafka-consumer-groups --describe --group SEU_GRUPO --bootstrap-server localhost:9092
```

#### ❌ Mensagens não são entregues

- Verifique se o producer está usando o servidor correto (`localhost:9092`)
- Confirme que o tópico foi criado automaticamente ou crie manualmente
- Verifique logs do broker para erros

### Limpeza do Ambiente

```bash
# Para todos os serviços
docker-compose down

# Remove volumes (CUIDADO: apaga todas as mensagens)
docker-compose down -v

# Remove imagens
docker-compose down --rmi all
```

## Próximos Passos

### Para Aprender Mais

1. **Kafka Streams**: Para processamento de streams
2. **Schema Registry**: Para evolução de schemas
3. **Kafka Connect**: Para integração com bancos de dados
4. **KSQL**: Para consultas SQL em streams

### Configurações de Produção

- **Múltiplos Brokers**: Para alta disponibilidade
- **Replicação**: `replication-factor` > 1
- **Monitoramento**: Prometheus + Grafana
- **Segurança**: SSL/SASL para autenticação
- **Backup**: Estratégias de backup e recovery

### Integrações Úteis

- **Spring Boot**: Spring Kafka para aplicações Java
- **Node.js**: KafkaJS para aplicações JavaScript
- **.NET**: Confluent.Kafka para aplicações .NET
- **Go**: Sarama para aplicações Go

---

## 📚 Recursos Adicionais

- [Documentação Oficial do Kafka](https://kafka.apache.org/documentation/)
- [Confluent Platform](https://www.confluent.io/)
- [Kafka Streams Documentation](https://kafka.apache.org/documentation/streams/)

---

**Dica**: Comece pequeno com um tópico e alguns producers/consumers, depois gradualmente adicione complexidade conforme sua necessidade!