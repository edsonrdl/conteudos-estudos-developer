# Amazon SQS - Guia Completo

## O que é Amazon SQS?

Amazon Simple Queue Service (SQS) é um serviço de filas de mensagens totalmente gerenciado da AWS que permite desacoplar e escalar microsserviços, sistemas distribuídos e aplicações serverless. Ele funciona como um buffer entre componentes de aplicação, permitindo que eles se comuniquem de forma assíncrona.

## Conceitos Fundamentais

### Tipos de Filas

**Standard Queue (Padrão)**

- Throughput ilimitado
- Entrega "at-least-once" (pelo menos uma vez)
- Ordenação "best-effort" (melhor esforço)
- Possibilidade de mensagens duplicadas

**FIFO Queue (First-In-First-Out)**

- Throughput de até 3.000 mensagens/segundo com batching
- Entrega exatamente uma vez (exactly-once)
- Ordem estritamente preservada
- Sem duplicação de mensagens

### Componentes Principais

- **Producer**: Aplicação que envia mensagens para a fila
- **Consumer**: Aplicação que recebe e processa mensagens
- **Message**: Dados transmitidos entre aplicações (até 256 KB)
- **Visibility Timeout**: Período em que uma mensagem fica invisível após ser lida
- **Dead Letter Queue (DLQ)**: Fila para mensagens que falharam múltiplas vezes

## Vantagens do Amazon SQS

### Escalabilidade e Disponibilidade

O SQS escala automaticamente para lidar com qualquer volume de mensagens, sem necessidade de provisionamento prévio. Oferece alta disponibilidade com redundância automática em múltiplas zonas de disponibilidade.

### Gerenciamento Simplificado

Como serviço totalmente gerenciado, elimina a complexidade operacional de manter infraestrutura de mensageria. Não requer patches, atualizações ou manutenção de servidores.

### Desacoplamento de Componentes

Permite que diferentes partes da aplicação operem independentemente, aumentando a resiliência do sistema. Se um componente falhar, as mensagens permanecem na fila até serem processadas.

### Custo-Efetivo

Modelo de pagamento por uso (pay-per-request), sem custos iniciais ou compromissos mínimos. Primeiras 1 milhão de requisições mensais gratuitas no free tier.

### Segurança Robusta

Criptografia em trânsito (TLS) e em repouso (SSE) disponível. Integração com IAM para controle granular de acesso. Suporte para VPC endpoints para comunicação privada.

### Integração com Ecossistema AWS

Integração nativa com Lambda, SNS, EC2, ECS, e outros serviços. Métricas automáticas no CloudWatch para monitoramento.

## Desvantagens e Limitações

### Tamanho de Mensagem Limitado

Limite de 256 KB por mensagem (pode usar S3 para mensagens maiores com SQS Extended Client Library). Overhead adicional para mensagens grandes.

### Latência Variável

Standard queues podem ter latência variável na entrega. Não adequado para comunicação em tempo real ou baixíssima latência.

### Complexidade de Ordenação

Standard queues não garantem ordem estrita. FIFO queues têm throughput limitado comparado às standard.

### Retenção de Mensagens

Máximo de 14 dias de retenção de mensagens. Mensagens são perdidas após o período de retenção.

### Custos em Alto Volume

Pode se tornar caro com bilhões de mensagens mensais. Custos adicionais por transferência de dados entre regiões.

### Vendor Lock-in

Migração para outra solução requer refatoração significativa. Dependência da disponibilidade e políticas da AWS.

## Exemplo Prático de Uso

Vou criar um exemplo completo de um sistema de processamento de pedidos de e-commerce usando SQS:


import boto3
import json
import time
from datetime import datetime
from typing import Dict, List, Optional
import logging

# Configuração de logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class OrderQueueManager:
    """Gerenciador de filas SQS para processamento de pedidos"""
    
    def __init__(self, queue_name: str, region: str = 'us-east-1'):
        """
        Inicializa o gerenciador de filas
        
        Args:
            queue_name: Nome da fila SQS
            region: Região AWS
        """
        self.sqs_client = boto3.client('sqs', region_name=region)
        self.queue_name = queue_name
        self.queue_url = self._get_or_create_queue()
        
    def _get_or_create_queue(self) -> str:
        """Obtém URL da fila ou cria uma nova se não existir"""
        try:
            response = self.sqs_client.get_queue_url(QueueName=self.queue_name)
            logger.info(f"Fila {self.queue_name} encontrada")
            return response['QueueUrl']
        except self.sqs_client.exceptions.QueueDoesNotExist:
            # Criar fila FIFO para garantir ordem de processamento
            response = self.sqs_client.create_queue(
                QueueName=self.queue_name,
                Attributes={
                    'DelaySeconds': '0',
                    'MessageRetentionPeriod': '345600',  # 4 dias
                    'VisibilityTimeout': '60',  # 1 minuto
                    'ReceiveMessageWaitTimeSeconds': '20',  # Long polling
                    'FifoQueue': 'true' if self.queue_name.endswith('.fifo') else 'false',
                    'ContentBasedDeduplication': 'true' if self.queue_name.endswith('.fifo') else 'false'
                }
            )
            logger.info(f"Fila {self.queue_name} criada com sucesso")
            return response['QueueUrl']

class OrderProducer:
    """Produtor de mensagens de pedidos"""
    
    def __init__(self, queue_manager: OrderQueueManager):
        self.queue_manager = queue_manager
        self.sqs_client = queue_manager.sqs_client
        
    def send_order(self, order: Dict) -> bool:
        """
        Envia pedido para a fila
        
        Args:
            order: Dicionário com dados do pedido
            
        Returns:
            True se enviado com sucesso
        """
        try:
            # Adicionar timestamp e ID único
            order['timestamp'] = datetime.now().isoformat()
            order['processing_attempts'] = 0
            
            # Preparar mensagem
            message_body = json.dumps(order)
            
            # Enviar para SQS
            params = {
                'QueueUrl': self.queue_manager.queue_url,
                'MessageBody': message_body,
                'MessageAttributes': {
                    'order_type': {
                        'StringValue': order.get('type', 'standard'),
                        'DataType': 'String'
                    },
                    'priority': {
                        'StringValue': order.get('priority', 'normal'),
                        'DataType': 'String'
                    }
                }
            }
            
            # Se for fila FIFO, adicionar group ID e deduplication ID
            if self.queue_manager.queue_name.endswith('.fifo'):
                params['MessageGroupId'] = f"order-{order.get('customer_id', 'default')}"
                params['MessageDeduplicationId'] = f"order-{order['order_id']}-{order['timestamp']}"
            
            response = self.sqs_client.send_message(**params)
            
            logger.info(f"Pedido {order['order_id']} enviado. MessageId: {response['MessageId']}")
            return True
            
        except Exception as e:
            logger.error(f"Erro ao enviar pedido {order.get('order_id')}: {str(e)}")
            return False
    
    def send_batch_orders(self, orders: List[Dict]) -> Dict:
        """
        Envia múltiplos pedidos em lote (até 10 por vez)
        
        Args:
            orders: Lista de pedidos
            
        Returns:
            Estatísticas do envio
        """
        successful = 0
        failed = 0
        
        # SQS permite máximo de 10 mensagens por batch
        for i in range(0, len(orders), 10):
            batch = orders[i:i+10]
            entries = []
            
            for idx, order in enumerate(batch):
                order['timestamp'] = datetime.now().isoformat()
                entry = {
                    'Id': str(idx),
                    'MessageBody': json.dumps(order),
                    'MessageAttributes': {
                        'order_type': {
                            'StringValue': order.get('type', 'standard'),
                            'DataType': 'String'
                        }
                    }
                }
                
                if self.queue_manager.queue_name.endswith('.fifo'):
                    entry['MessageGroupId'] = f"order-{order.get('customer_id', 'default')}"
                    entry['MessageDeduplicationId'] = f"order-{order['order_id']}-{datetime.now().timestamp()}"
                
                entries.append(entry)
            
            try:
                response = self.sqs_client.send_message_batch(
                    QueueUrl=self.queue_manager.queue_url,
                    Entries=entries
                )
                
                successful += len(response.get('Successful', []))
                failed += len(response.get('Failed', []))
                
                if response.get('Failed'):
                    for failure in response['Failed']:
                        logger.error(f"Falha no envio: {failure}")
                        
            except Exception as e:
                logger.error(f"Erro no envio em lote: {str(e)}")
                failed += len(batch)
        
        return {
            'successful': successful,
            'failed': failed,
            'total': len(orders)
        }

class OrderConsumer:
    """Consumidor de mensagens de pedidos"""
    
    def __init__(self, queue_manager: OrderQueueManager, dlq_manager: Optional[OrderQueueManager] = None):
        self.queue_manager = queue_manager
        self.dlq_manager = dlq_manager
        self.sqs_client = queue_manager.sqs_client
        self.max_retries = 3
        
    def process_orders(self, max_messages: int = 10, wait_time: int = 20):
        """
        Processa pedidos da fila
        
        Args:
            max_messages: Número máximo de mensagens por vez (1-10)
            wait_time: Tempo de espera para long polling (segundos)
        """
        try:
            response = self.sqs_client.receive_message(
                QueueUrl=self.queue_manager.queue_url,
                AttributeNames=['All'],
                MessageAttributeNames=['All'],
                MaxNumberOfMessages=min(max_messages, 10),
                WaitTimeSeconds=wait_time,
                VisibilityTimeout=60
            )
            
            messages = response.get('Messages', [])
            
            if not messages:
                logger.info("Nenhuma mensagem na fila")
                return
            
            logger.info(f"Processando {len(messages)} mensagens")
            
            for message in messages:
                self._process_single_order(message)
                
        except Exception as e:
            logger.error(f"Erro ao receber mensagens: {str(e)}")
    
    def _process_single_order(self, message: Dict):
        """Processa um único pedido"""
        try:
            # Extrair dados do pedido
            order = json.loads(message['Body'])
            receipt_handle = message['ReceiptHandle']
            
            logger.info(f"Processando pedido {order['order_id']}")
            
            # Simular processamento do pedido
            success = self._simulate_order_processing(order)
            
            if success:
                # Deletar mensagem da fila após sucesso
                self.sqs_client.delete_message(
                    QueueUrl=self.queue_manager.queue_url,
                    ReceiptHandle=receipt_handle
                )
                logger.info(f"Pedido {order['order_id']} processado com sucesso")
            else:
                # Incrementar contador de tentativas
                order['processing_attempts'] = order.get('processing_attempts', 0) + 1
                
                if order['processing_attempts'] >= self.max_retries:
                    # Enviar para DLQ se exceder tentativas
                    if self.dlq_manager:
                        self._send_to_dlq(order, message)
                    # Deletar da fila principal
                    self.sqs_client.delete_message(
                        QueueUrl=self.queue_manager.queue_url,
                        ReceiptHandle=receipt_handle
                    )
                    logger.warning(f"Pedido {order['order_id']} enviado para DLQ após {self.max_retries} tentativas")
                else:
                    # Deixar mensagem voltar para a fila após visibility timeout
                    logger.info(f"Pedido {order['order_id']} será reprocessado (tentativa {order['processing_attempts']})")
                    
        except json.JSONDecodeError as e:
            logger.error(f"Erro ao decodificar mensagem: {str(e)}")
            # Deletar mensagem malformada
            self.sqs_client.delete_message(
                QueueUrl=self.queue_manager.queue_url,
                ReceiptHandle=message['ReceiptHandle']
            )
        except Exception as e:
            logger.error(f"Erro ao processar mensagem: {str(e)}")
    
    def _simulate_order_processing(self, order: Dict) -> bool:
        """
        Simula processamento do pedido
        
        Returns:
            True se processado com sucesso
        """
        # Simular diferentes cenários de processamento
        import random
        
        # 80% de chance de sucesso
        if random.random() < 0.8:
            # Simular tempo de processamento
            time.sleep(0.5)
            
            # Aqui você faria o processamento real:
            # - Validar pedido
            # - Verificar estoque
            # - Processar pagamento
            # - Atualizar banco de dados
            # - Enviar email de confirmação
            # - Etc.
            
            logger.info(f"✓ Pedido {order['order_id']} - Total: ${order.get('total', 0)}")
            return True
        else:
            logger.warning(f"✗ Falha simulada no processamento do pedido {order['order_id']}")
            return False
    
    def _send_to_dlq(self, order: Dict, original_message: Dict):
        """Envia pedido falhado para Dead Letter Queue"""
        if not self.dlq_manager:
            return
            
        try:
            self.dlq_manager.sqs_client.send_message(
                QueueUrl=self.dlq_manager.queue_url,
                MessageBody=json.dumps(order),
                MessageAttributes={
                    'original_queue': {
                        'StringValue': self.queue_manager.queue_name,
                        'DataType': 'String'
                    },
                    'failure_reason': {
                        'StringValue': 'max_retries_exceeded',
                        'DataType': 'String'
                    },
                    'original_receive_count': {
                        'StringValue': original_message.get('Attributes', {}).get('ApproximateReceiveCount', '0'),
                        'DataType': 'String'
                    }
                }
            )
        except Exception as e:
            logger.error(f"Erro ao enviar para DLQ: {str(e)}")

class QueueMonitor:
    """Monitor de filas SQS"""
    
    def __init__(self, queue_manager: OrderQueueManager):
        self.queue_manager = queue_manager
        self.sqs_client = queue_manager.sqs_client
    
    def get_queue_stats(self) -> Dict:
        """Obtém estatísticas da fila"""
        try:
            response = self.sqs_client.get_queue_attributes(
                QueueUrl=self.queue_manager.queue_url,
                AttributeNames=['All']
            )
            
            attributes = response['Attributes']
            
            stats = {
                'queue_name': self.queue_manager.queue_name,
                'messages_available': int(attributes.get('ApproximateNumberOfMessages', 0)),
                'messages_in_flight': int(attributes.get('ApproximateNumberOfMessagesNotVisible', 0)),
                'messages_delayed': int(attributes.get('ApproximateNumberOfMessagesDelayed', 0)),
                'oldest_message_age': attributes.get('ApproximateAgeOfOldestMessage', 'N/A'),
                'created_timestamp': attributes.get('CreatedTimestamp', 'N/A'),
                'visibility_timeout': attributes.get('VisibilityTimeout', 'N/A'),
                'message_retention_period': attributes.get('MessageRetentionPeriod', 'N/A'),
                'is_fifo': attributes.get('FifoQueue', 'false') == 'true'
            }
            
            return stats
            
        except Exception as e:
            logger.error(f"Erro ao obter estatísticas: {str(e)}")
            return {}
    
    def purge_queue(self) -> bool:
        """Limpa todas as mensagens da fila (use com cuidado!)"""
        try:
            response = input(f"⚠️  Tem certeza que deseja limpar TODAS as mensagens da fila {self.queue_manager.queue_name}? (sim/não): ")
            if response.lower() != 'sim':
                logger.info("Operação cancelada")
                return False
                
            self.sqs_client.purge_queue(QueueUrl=self.queue_manager.queue_url)
            logger.info(f"Fila {self.queue_manager.queue_name} limpa com sucesso")
            return True
            
        except Exception as e:
            logger.error(f"Erro ao limpar fila: {str(e)}")
            return False

# ==================== EXEMPLO DE USO ====================

def exemplo_uso_completo():
    """Demonstração completa do sistema de processamento de pedidos"""
    
    print("=" * 60)
    print("SISTEMA DE PROCESSAMENTO DE PEDIDOS COM SQS")
    print("=" * 60)
    
    # 1. Configurar filas
    print("\n1. Configurando filas...")
    
    # Fila principal (usar .fifo para garantir ordem)
    main_queue = OrderQueueManager("pedidos-ecommerce.fifo", region='us-east-1')
    
    # Dead Letter Queue para mensagens falhadas
    dlq_queue = OrderQueueManager("pedidos-ecommerce-dlq.fifo", region='us-east-1')
    
    # 2. Criar produtor
    print("\n2. Inicializando produtor de pedidos...")
    producer = OrderProducer(main_queue)
    
    # 3. Enviar pedidos de exemplo
    print("\n3. Enviando pedidos para a fila...")
    
    # Pedidos individuais
    pedidos_exemplo = [
        {
            'order_id': 'ORD-001',
            'customer_id': 'CUST-123',
            'items': [
                {'product_id': 'PROD-A', 'quantity': 2, 'price': 29.99},
                {'product_id': 'PROD-B', 'quantity': 1, 'price': 49.99}
            ],
            'total': 109.97,
            'type': 'standard',
            'priority': 'normal'
        },
        {
            'order_id': 'ORD-002',
            'customer_id': 'CUST-456',
            'items': [
                {'product_id': 'PROD-C', 'quantity': 3, 'price': 15.99}
            ],
            'total': 47.97,
            'type': 'express',
            'priority': 'high'
        },
        {
            'order_id': 'ORD-003',
            'customer_id': 'CUST-789',
            'items': [
                {'product_id': 'PROD-D', 'quantity': 1, 'price': 199.99}
            ],
            'total': 199.99,
            'type': 'standard',
            'priority': 'normal'
        }
    ]
    
    # Enviar pedidos
    for pedido in pedidos_exemplo:
        producer.send_order(pedido)
    
    # Envio em lote
    print("\n4. Enviando lote de pedidos...")
    pedidos_lote = [
        {
            'order_id': f'ORD-BATCH-{i:03d}',
            'customer_id': f'CUST-{100+i}',
            'items': [{'product_id': 'PROD-X', 'quantity': 1, 'price': 9.99}],
            'total': 9.99,
            'type': 'standard'
        }
        for i in range(5)
    ]
    
    stats = producer.send_batch_orders(pedidos_lote)
    print(f"Lote enviado: {stats['successful']} sucesso, {stats['failed']} falhas")
    
    # 4. Monitorar fila
    print("\n5. Estatísticas da fila:")
    monitor = QueueMonitor(main_queue)
    stats = monitor.get_queue_stats()
    for key, value in stats.items():
        print(f"  {key}: {value}")
    
    # 5. Consumir mensagens
    print("\n6. Processando pedidos da fila...")
    consumer = OrderConsumer(main_queue, dlq_queue)
    
    # Processar mensagens em loop
    print("\nIniciando processamento contínuo (Ctrl+C para parar)...")
    try:
        while True:
            consumer.process_orders(max_messages=5, wait_time=5)
            
            # Mostrar estatísticas atualizadas
            stats = monitor.get_queue_stats()
            print(f"\n📊 Fila: {stats['messages_available']} mensagens disponíveis, "
                  f"{stats['messages_in_flight']} em processamento")
            
            # Pequena pausa entre ciclos
            time.sleep(2)
            
    except KeyboardInterrupt:
        print("\n\n✓ Processamento interrompido pelo usuário")
    
    # 6. Verificar DLQ
    print("\n7. Verificando Dead Letter Queue...")
    dlq_monitor = QueueMonitor(dlq_queue)
    dlq_stats = dlq_monitor.get_queue_stats()
    print(f"Mensagens na DLQ: {dlq_stats.get('messages_available', 0)}")
    
    print("\n" + "=" * 60)
    print("DEMONSTRAÇÃO CONCLUÍDA")
    print("=" * 60)

# Executar exemplo se o script for rodado diretamente
if __name__ == "__main__":
    # Nota: Para executar este código, você precisa:
    # 1. Instalar boto3: pip install boto3
    # 2. Configurar credenciais AWS (AWS CLI, variáveis de ambiente, ou IAM role)
    # 3. Ter permissões SQS apropriadas
    
    try:
        exemplo_uso_completo()
    except Exception as e:
        logger.error(f"Erro na execução: {str(e)}")
        print("\nCertifique-se de que:")
        print("1. boto3 está instalado (pip install boto3)")
        print("2. Credenciais AWS estão configuradas")
        print("3. Você tem permissões para criar/acessar filas SQS")`

## Casos de Uso Comuns

### E-commerce e Varejo

- **Processamento de pedidos**: Desacoplar recebimento de pedidos do processamento
- **Atualização de inventário**: Sincronizar estoques entre sistemas
- **Notificações**: Envio assíncrono de emails e SMS

### Processamento de Mídia

- **Transcodificação de vídeo**: Fila de vídeos para conversão
- **Redimensionamento de imagens**: Processamento em lote de uploads
- **Geração de thumbnails**: Criação assíncrona de miniaturas

### Aplicações Financeiras

- **Processamento de transações**: Buffer para picos de transações
- **Conciliação bancária**: Processamento batch de arquivos
- **Análise de fraude**: Pipeline assíncrono de verificação

### IoT e Telemetria

- **Ingestão de dados de sensores**: Buffer para milhões de eventos
- **Processamento de logs**: Agregação e análise assíncrona
- **Alertas e monitoramento**: Detecção de anomalias em pipeline

## Melhores Práticas

### Design de Mensagens

Mantenha mensagens pequenas e autocontidas, incluindo apenas IDs e referências quando possível. Use S3 para payloads grandes, armazenando apenas a referência na mensagem SQS. Implemente versionamento de mensagens para compatibilidade.

### Tratamento de Erros

Configure Dead Letter Queues para mensagens problemáticas. Implemente retry com backoff exponencial. Monitore e alerte sobre mensagens na DLQ. Registre falhas para análise posterior.

### Performance e Otimização

Use long polling para reduzir custos e latência. Processe mensagens em batch quando possível. Ajuste visibility timeout baseado no tempo de processamento real. Implemente auto-scaling de consumers baseado no tamanho da fila.

### Segurança

Criptografe mensagens sensíveis end-to-end além da criptografia do SQS. Use IAM roles com menor privilégio necessário. Implemente validação de mensagens contra schemas. Monitore acesso não autorizado às filas.

### Monitoramento

Configure alarmes CloudWatch para métricas críticas como idade das mensagens e tamanho da fila. Implemente tracing distribuído para rastrear mensagens através do sistema. Mantenha logs detalhados de processamento. Realize health checks regulares dos consumers.

## Comparação com Alternativas

### SQS vs Kinesis

- **SQS**: Melhor para processamento individual de mensagens, sem ordem garantida (standard), pay-per-request
- **Kinesis**: Melhor para streaming de dados em tempo real, ordem garantida, cobrança por shard-hour

### SQS vs SNS

- **SQS**: Pull model, persistência de mensagens, um consumer por mensagem
- **SNS**: Push model, sem persistência, múltiplos subscribers simultâneos

### SQS vs RabbitMQ/ActiveMQ

- **SQS**: Totalmente gerenciado, sem manutenção, escalabilidade automática
- **RabbitMQ/ActiveMQ**: Mais controle, recursos avançados de roteamento, requer gerenciamento

## Conclusão

Amazon SQS é uma escolha excelente para sistemas que precisam de desacoplamento, escalabilidade e confiabilidade sem a complexidade de gerenciar infraestrutura de mensageria. Suas principais forças estão na simplicidade operacional, integração com AWS e modelo de custos flexível.

É ideal para cargas de trabalho assíncronas, processamento em batch, e como buffer entre componentes com diferentes taxas de processamento. As limitações de tamanho de mensagem e latência devem ser consideradas, mas para a maioria dos casos de uso corporativos, SQS oferece um excelente equilíbrio entre funcionalidade e simplicidade.

O exemplo fornecido demonstra um sistema completo de processamento de pedidos que pode ser adaptado para diversos cenários reais, incluindo tratamento de erros, monitoramento e processamento em lote.