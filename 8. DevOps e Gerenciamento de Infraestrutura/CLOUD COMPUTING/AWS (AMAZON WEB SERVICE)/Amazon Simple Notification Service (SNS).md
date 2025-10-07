# Amazon SNS - Guia Completo

## O que é Amazon SNS?

Amazon Simple Notification Service (SNS) é um serviço de mensageria pub/sub (publish/subscribe) totalmente gerenciado que permite o envio de mensagens de aplicações para aplicações (A2A) e de aplicações para pessoas (A2P). Ele coordena e gerencia a entrega de mensagens para endpoints ou clientes inscritos, permitindo comunicação assíncrona e desacoplada entre sistemas.

## Conceitos Fundamentais

### Componentes Principais

**Topic (Tópico)**

- Canal de comunicação lógico
- Ponto de acesso para publicadores enviarem mensagens
- Pode ter múltiplos subscribers

**Publisher (Publicador)**

- Aplicação ou serviço que envia mensagens para um tópico
- Pode ser qualquer aplicação com permissões adequadas

**Subscriber (Assinante)**

- Endpoint que recebe mensagens de um tópico
- Tipos: SQS, Lambda, HTTP/HTTPS, Email, SMS, aplicações móveis

**Message (Mensagem)**

- Dados enviados pelo publisher (até 256 KB)
- Pode ter diferentes formatos para diferentes protocolos

**Subscription (Assinatura)**

- Conexão entre um tópico e um endpoint
- Define como e onde as mensagens são entregues

### Tipos de Endpoints

- **SQS**: Filas para processamento assíncrono
- **Lambda**: Funções serverless executadas automaticamente
- **HTTP/HTTPS**: Webhooks para aplicações web
- **Email/Email-JSON**: Notificações por email
- **SMS**: Mensagens de texto para celulares
- **Platform Applications**: Push notifications (iOS, Android, etc.)
- **Kinesis Data Firehose**: Streaming de dados

### Tipos de Tópicos

**Standard Topics**

- Throughput ilimitado
- Entrega "best-effort" (melhor esforço)
- Possível duplicação de mensagens
- Suporta todos os tipos de endpoints

**FIFO Topics**

- Ordem estrita de mensagens
- Deduplicação de mensagens
- Throughput de até 300 mensagens/segundo
- Suporta apenas SQS FIFO como endpoint

## Vantagens do Amazon SNS

### Escalabilidade Massiva

SNS escala automaticamente para bilhões de mensagens por dia sem necessidade de provisionamento. Suporta milhões de subscribers por tópico com alta disponibilidade global.

### Entrega Multi-Protocolo

Um único tópico pode entregar mensagens para múltiplos tipos de endpoints simultaneamente. Permite fan-out patterns eficientes, onde uma mensagem é distribuída para vários destinos.

### Confiabilidade e Durabilidade

Mensagens são armazenadas redundantemente em múltiplas zonas de disponibilidade. Retry automático com políticas configuráveis garante entrega confiável.

### Filtragem de Mensagens

Subscription filter policies permitem que subscribers recebam apenas mensagens relevantes. Reduz processamento desnecessário e custos.

### Integração Nativa AWS

Integração profunda com serviços AWS como CloudWatch, Lambda, SQS. Muitos serviços AWS podem publicar automaticamente para SNS (S3, RDS, EC2, etc.).

### Segurança Robusta

Criptografia em trânsito e em repouso. Controle de acesso granular com IAM. Suporte para VPC endpoints e mensagens assinadas.

### Custo-Efetivo

Modelo pay-per-use sem custos iniciais. Primeiras 1 milhão de requisições gratuitas mensalmente. Preços diferenciados por tipo de entrega.

## Desvantagens e Limitações

### Tamanho de Mensagem

Limite de 256 KB por mensagem (pode ser contornado usando S3 para payloads maiores). Overhead para mensagens grandes comparado a soluções especializadas.

### Sem Garantia de Ordem (Standard)

Standard topics não garantem ordem de entrega. FIFO topics têm throughput limitado e suportam apenas SQS FIFO.

### Persistência Limitada

Mensagens não são persistidas após entrega. Se todos os endpoints falharem, a mensagem pode ser perdida após retries.

### Complexidade de Debugging

Rastreamento de mensagens através de múltiplos subscribers pode ser complexo. Logs distribuídos dificultam troubleshooting.

### Vendor Lock-in

Migração para outra plataforma requer refatoração significativa. APIs proprietárias da AWS.

### Limitações por Região

Alguns recursos podem não estar disponíveis em todas as regiões. Custos adicionais para transferência entre regiões.

### Rate Limits

Limites de taxa para diferentes operações e tipos de entrega. SMS e email têm limites específicos por região.

## Exemplo Prático de Uso

Vou criar um sistema completo de notificações para uma plataforma de e-learning que demonstra diversos recursos do SNS:

import boto3
import json
import logging
from typing import Dict, List, Optional, Any
from datetime import datetime
from enum import Enum
import time
import uuid

# Configuração de logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class NotificationType(Enum):
    """Tipos de notificação do sistema"""
    COURSE_ENROLLMENT = "course_enrollment"
    ASSIGNMENT_DUE = "assignment_due"
    NEW_CONTENT = "new_content"
    GRADE_POSTED = "grade_posted"
    SYSTEM_ALERT = "system_alert"
    MARKETING = "marketing"
    CERTIFICATE_READY = "certificate_ready"

class SNSManager:
    """Gerenciador principal do SNS"""
    
    def __init__(self, region: str = 'us-east-1'):
        """
        Inicializa o gerenciador SNS
        
        Args:
            region: Região AWS
        """
        self.sns_client = boto3.client('sns', region_name=region)
        self.region = region
        self.account_id = boto3.client('sts').get_caller_identity()['Account']
        
    def create_topic(self, topic_name: str, is_fifo: bool = False, 
                    display_name: str = None, tags: Dict = None) -> str:
        """
        Cria um tópico SNS
        
        Args:
            topic_name: Nome do tópico
            is_fifo: Se True, cria tópico FIFO
            display_name: Nome de exibição para SMS/Email
            tags: Tags do tópico
            
        Returns:
            ARN do tópico criado
        """
        try:
            attributes = {}
            
            if is_fifo:
                if not topic_name.endswith('.fifo'):
                    topic_name += '.fifo'
                attributes['FifoTopic'] = 'true'
                attributes['ContentBasedDeduplication'] = 'true'
            
            if display_name:
                attributes['DisplayName'] = display_name
            
            # Criar tópico
            response = self.sns_client.create_topic(
                Name=topic_name,
                Attributes=attributes
            )
            
            topic_arn = response['TopicArn']
            
            # Adicionar tags se fornecidas
            if tags:
                self.sns_client.tag_resource(
                    ResourceArn=topic_arn,
                    Tags=[{'Key': k, 'Value': v} for k, v in tags.items()]
                )
            
            logger.info(f"Tópico criado: {topic_arn}")
            return topic_arn
            
        except Exception as e:
            logger.error(f"Erro ao criar tópico: {str(e)}")
            raise
    
    def set_topic_attributes(self, topic_arn: str, attributes: Dict):
        """Define atributos do tópico"""
        for key, value in attributes.items():
            try:
                self.sns_client.set_topic_attributes(
                    TopicArn=topic_arn,
                    AttributeName=key,
                    AttributeValue=value
                )
                logger.info(f"Atributo {key} definido para {topic_arn}")
            except Exception as e:
                logger.error(f"Erro ao definir atributo {key}: {str(e)}")

class NotificationPublisher:
    """Publicador de notificações"""
    
    def __init__(self, sns_manager: SNSManager):
        self.sns_manager = sns_manager
        self.sns_client = sns_manager.sns_client
    
    def publish_simple(self, topic_arn: str, message: str, subject: str = None) -> str:
        """
        Publica mensagem simples
        
        Args:
            topic_arn: ARN do tópico
            message: Corpo da mensagem
            subject: Assunto (para email)
            
        Returns:
            MessageId da mensagem publicada
        """
        try:
            params = {
                'TopicArn': topic_arn,
                'Message': message
            }
            
            if subject:
                params['Subject'] = subject
            
            response = self.sns_client.publish(**params)
            
            logger.info(f"Mensagem publicada: {response['MessageId']}")
            return response['MessageId']
            
        except Exception as e:
            logger.error(f"Erro ao publicar mensagem: {str(e)}")
            raise
    
    def publish_structured(self, topic_arn: str, message_data: Dict, 
                         notification_type: NotificationType,
                         attributes: Dict = None) -> str:
        """
        Publica mensagem estruturada com diferentes formatos por protocolo
        
        Args:
            topic_arn: ARN do tópico
            message_data: Dados da mensagem
            notification_type: Tipo de notificação
            attributes: Atributos da mensagem para filtragem
            
        Returns:
            MessageId da mensagem publicada
        """
        try:
            # Criar mensagens específicas por protocolo
            message = {
                "default": json.dumps(message_data),
                "email": self._format_email_message(message_data, notification_type),
                "email-json": json.dumps(message_data),
                "sms": self._format_sms_message(message_data, notification_type),
                "lambda": json.dumps(message_data),
                "sqs": json.dumps(message_data),
                "https": json.dumps({
                    "type": notification_type.value,
                    "timestamp": datetime.now().isoformat(),
                    "data": message_data
                })
            }
            
            # Preparar parâmetros
            params = {
                'TopicArn': topic_arn,
                'Message': json.dumps(message),
                'MessageStructure': 'json'
            }
            
            # Adicionar subject para email
            if 'title' in message_data:
                params['Subject'] = message_data['title']
            
            # Adicionar atributos para filtragem
            if attributes:
                params['MessageAttributes'] = {
                    'notification_type': {
                        'DataType': 'String',
                        'StringValue': notification_type.value
                    }
                }
                
                for key, value in attributes.items():
                    params['MessageAttributes'][key] = {
                        'DataType': 'String',
                        'StringValue': str(value)
                    }
            
            response = self.sns_client.publish(**params)
            
            logger.info(f"Mensagem estruturada publicada: {response['MessageId']}")
            return response['MessageId']
            
        except Exception as e:
            logger.error(f"Erro ao publicar mensagem estruturada: {str(e)}")
            raise
    
    def publish_batch(self, topic_arn: str, messages: List[Dict]) -> Dict:
        """
        Publica múltiplas mensagens em lote (apenas para FIFO topics)
        
        Args:
            topic_arn: ARN do tópico FIFO
            messages: Lista de mensagens
            
        Returns:
            Estatísticas do envio
        """
        if not topic_arn.endswith('.fifo'):
            raise ValueError("Batch publishing só é suportado para tópicos FIFO")
        
        successful = 0
        failed = 0
        
        # SNS permite máximo de 10 mensagens por batch
        for i in range(0, len(messages), 10):
            batch = messages[i:i+10]
            entries = []
            
            for idx, msg in enumerate(batch):
                entry = {
                    'Id': str(idx),
                    'Message': json.dumps(msg),
                    'MessageGroupId': msg.get('group_id', 'default')
                }
                
                if 'deduplication_id' in msg:
                    entry['MessageDeduplicationId'] = msg['deduplication_id']
                else:
                    entry['MessageDeduplicationId'] = str(uuid.uuid4())
                
                entries.append(entry)
            
            try:
                response = self.sns_client.publish_batch(
                    TopicArn=topic_arn,
                    PublishBatchRequestEntries=entries
                )
                
                successful += len(response.get('Successful', []))
                failed += len(response.get('Failed', []))
                
                if response.get('Failed'):
                    for failure in response['Failed']:
                        logger.error(f"Falha no batch: {failure}")
                        
            except Exception as e:
                logger.error(f"Erro no envio em lote: {str(e)}")
                failed += len(batch)
        
        return {
            'successful': successful,
            'failed': failed,
            'total': len(messages)
        }
    
    def _format_email_message(self, data: Dict, notification_type: NotificationType) -> str:
        """Formata mensagem para email"""
        templates = {
            NotificationType.COURSE_ENROLLMENT: """
Olá {user_name}!

Você foi matriculado com sucesso no curso: {course_name}

Detalhes do Curso:
- Instrutor: {instructor}
- Duração: {duration}
- Data de Início: {start_date}

Acesse a plataforma para começar seus estudos!

Atenciosamente,
Equipe EduTech
            """,
            NotificationType.ASSIGNMENT_DUE: """
Olá {user_name}!

Lembrete: Você tem uma atividade pendente!

Atividade: {assignment_name}
Curso: {course_name}
Prazo: {due_date}

Não deixe para última hora!

Atenciosamente,
Equipe EduTech
            """,
            NotificationType.CERTIFICATE_READY: """
Parabéns, {user_name}! 🎉

Seu certificado do curso "{course_name}" está pronto!

Você pode fazer o download acessando:
- Meu Perfil > Certificados

Compartilhe sua conquista nas redes sociais!

Atenciosamente,
Equipe EduTech
            """
        }
        
        template = templates.get(
            notification_type,
            "Nova notificação: {message}"
        )
        
        return template.format(**data)
    
    def _format_sms_message(self, data: Dict, notification_type: NotificationType) -> str:
        """Formata mensagem para SMS (máximo 160 caracteres)"""
        templates = {
            NotificationType.COURSE_ENROLLMENT: "EduTech: Matricula confirmada em {course_name}!",
            NotificationType.ASSIGNMENT_DUE: "EduTech: Atividade {assignment_name} vence em {due_date}",
            NotificationType.CERTIFICATE_READY: "EduTech: Seu certificado de {course_name} está pronto!",
            NotificationType.GRADE_POSTED: "EduTech: Nota disponível em {course_name}"
        }
        
        template = templates.get(
            notification_type,
            "EduTech: Nova notificação disponível"
        )
        
        message = template.format(**data)
        return message[:160]  # Truncar para 160 caracteres

class SubscriptionManager:
    """Gerenciador de assinaturas"""
    
    def __init__(self, sns_manager: SNSManager):
        self.sns_manager = sns_manager
        self.sns_client = sns_manager.sns_client
    
    def subscribe_endpoint(self, topic_arn: str, protocol: str, 
                          endpoint: str, attributes: Dict = None) -> str:
        """
        Inscreve um endpoint em um tópico
        
        Args:
            topic_arn: ARN do tópico
            protocol: Protocolo (email, sms, sqs, lambda, https)
            endpoint: Endpoint de destino
            attributes: Atributos da assinatura
            
        Returns:
            ARN da assinatura
        """
        try:
            response = self.sns_client.subscribe(
                TopicArn=topic_arn,
                Protocol=protocol,
                Endpoint=endpoint,
                ReturnSubscriptionArn=True
            )
            
            subscription_arn = response['SubscriptionArn']
            
            # Configurar atributos adicionais
            if attributes:
                for key, value in attributes.items():
                    self.sns_client.set_subscription_attributes(
                        SubscriptionArn=subscription_arn,
                        AttributeName=key,
                        AttributeValue=value
                    )
            
            logger.info(f"Assinatura criada: {subscription_arn}")
            return subscription_arn
            
        except Exception as e:
            logger.error(f"Erro ao criar assinatura: {str(e)}")
            raise
    
    def add_filter_policy(self, subscription_arn: str, filter_policy: Dict):
        """
        Adiciona política de filtro a uma assinatura
        
        Args:
            subscription_arn: ARN da assinatura
            filter_policy: Política de filtro
        """
        try:
            self.sns_client.set_subscription_attributes(
                SubscriptionArn=subscription_arn,
                AttributeName='FilterPolicy',
                AttributeValue=json.dumps(filter_policy)
            )
            logger.info(f"Filtro aplicado: {filter_policy}")
            
        except Exception as e:
            logger.error(f"Erro ao aplicar filtro: {str(e)}")
            raise
    
    def setup_dlq(self, subscription_arn: str, dlq_arn: str, max_receive_count: int = 3):
        """
        Configura Dead Letter Queue para assinatura
        
        Args:
            subscription_arn: ARN da assinatura
            dlq_arn: ARN da DLQ (SQS)
            max_receive_count: Máximo de tentativas
        """
        redrive_policy = {
            "deadLetterTargetArn": dlq_arn,
            "maxReceiveCount": max_receive_count
        }
        
        try:
            self.sns_client.set_subscription_attributes(
                SubscriptionArn=subscription_arn,
                AttributeName='RedrivePolicy',
                AttributeValue=json.dumps(redrive_policy)
            )
            logger.info(f"DLQ configurada para {subscription_arn}")
            
        except Exception as e:
            logger.error(f"Erro ao configurar DLQ: {str(e)}")
            raise
    
    def list_subscriptions(self, topic_arn: str) -> List[Dict]:
        """Lista todas as assinaturas de um tópico"""
        try:
            subscriptions = []
            next_token = None
            
            while True:
                params = {'TopicArn': topic_arn}
                if next_token:
                    params['NextToken'] = next_token
                
                response = self.sns_client.list_subscriptions_by_topic(**params)
                subscriptions.extend(response['Subscriptions'])
                
                next_token = response.get('NextToken')
                if not next_token:
                    break
            
            return subscriptions
            
        except Exception as e:
            logger.error(f"Erro ao listar assinaturas: {str(e)}")
            return []

class NotificationOrchestrator:
    """Orquestrador de notificações multi-canal"""
    
    def __init__(self, region: str = 'us-east-1'):
        self.sns_manager = SNSManager(region)
        self.publisher = NotificationPublisher(self.sns_manager)
        self.subscription_manager = SubscriptionManager(self.sns_manager)
        self.topics = {}
        
    def setup_notification_system(self):
        """Configura sistema completo de notificações"""
        logger.info("Configurando sistema de notificações...")
        
        # Criar tópicos por categoria
        self.topics['academic'] = self.sns_manager.create_topic(
            'edutech-academic-notifications',
            display_name='EduTech Academic',
            tags={'Environment': 'Production', 'Type': 'Academic'}
        )
        
        self.topics['marketing'] = self.sns_manager.create_topic(
            'edutech-marketing-notifications',
            display_name='EduTech News',
            tags={'Environment': 'Production', 'Type': 'Marketing'}
        )
        
        self.topics['urgent'] = self.sns_manager.create_topic(
            'edutech-urgent-notifications.fifo',
            is_fifo=True,
            display_name='EduTech Urgent',
            tags={'Environment': 'Production', 'Type': 'Urgent'}
        )
        
        logger.info(f"Tópicos criados: {list(self.topics.keys())}")
        
    def register_user_preferences(self, user_id: str, preferences: Dict):
        """
        Registra preferências de notificação do usuário
        
        Args:
            user_id: ID do usuário
            preferences: Preferências de notificação
        """
        logger.info(f"Registrando preferências para usuário {user_id}")
        
        # Email para notificações acadêmicas
        if preferences.get('email') and preferences.get('academic_notifications'):
            subscription_arn = self.subscription_manager.subscribe_endpoint(
                topic_arn=self.topics['academic'],
                protocol='email',
                endpoint=preferences['email']
            )
            
            # Filtrar apenas notificações relevantes para o usuário
            filter_policy = {
                "user_id": [user_id],
                "notification_type": [
                    "course_enrollment",
                    "assignment_due",
                    "grade_posted",
                    "certificate_ready"
                ]
            }
            self.subscription_manager.add_filter_policy(subscription_arn, filter_policy)
        
        # SMS para notificações urgentes
        if preferences.get('phone') and preferences.get('urgent_notifications'):
            self.subscription_manager.subscribe_endpoint(
                topic_arn=self.topics['urgent'],
                protocol='sms',
                endpoint=preferences['phone']
            )
        
        # Webhook para integração com sistema externo
        if preferences.get('webhook_url'):
            subscription_arn = self.subscription_manager.subscribe_endpoint(
                topic_arn=self.topics['academic'],
                protocol='https',
                endpoint=preferences['webhook_url']
            )
            
            # Configurar retry policy
            self.sns_client.set_subscription_attributes(
                SubscriptionArn=subscription_arn,
                AttributeName='DeliveryPolicy',
                AttributeValue=json.dumps({
                    "healthyRetryPolicy": {
                        "minDelayTarget": 5,
                        "maxDelayTarget": 60,
                        "numRetries": 5,
                        "backoffFunction": "exponential"
                    }
                })
            )
        
        logger.info(f"Preferências registradas para {user_id}")
    
    def notify_course_enrollment(self, user_data: Dict, course_data: Dict):
        """Notifica sobre matrícula em curso"""
        notification_data = {
            "user_id": user_data['id'],
            "user_name": user_data['name'],
            "course_name": course_data['name'],
            "instructor": course_data['instructor'],
            "duration": course_data['duration'],
            "start_date": course_data['start_date'],
            "title": f"Matrícula Confirmada: {course_data['name']}"
        }
        
        return self.publisher.publish_structured(
            topic_arn=self.topics['academic'],
            message_data=notification_data,
            notification_type=NotificationType.COURSE_ENROLLMENT,
            attributes={
                "user_id": user_data['id'],
                "course_id": course_data['id'],
                "priority": "normal"
            }
        )
    
    def notify_assignment_due(self, assignments: List[Dict]):
        """Notifica sobre atividades próximas do prazo"""
        results = []
        
        for assignment in assignments:
            notification_data = {
                "user_id": assignment['user_id'],
                "user_name": assignment['user_name'],
                "assignment_name": assignment['name'],
                "course_name": assignment['course_name'],
                "due_date": assignment['due_date'],
                "title": f"Lembrete: Atividade {assignment['name']}"
            }
            
            message_id = self.publisher.publish_structured(
                topic_arn=self.topics['academic'],
                message_data=notification_data,
                notification_type=NotificationType.ASSIGNMENT_DUE,
                attributes={
                    "user_id": assignment['user_id'],
                    "urgency": "high" if assignment.get('hours_until_due', 24) < 24 else "normal"
                }
            )
            results.append(message_id)
        
        return results

class NotificationMonitor:
    """Monitor de notificações"""
    
    def __init__(self, sns_manager: SNSManager):
        self.sns_manager = sns_manager
        self.sns_client = sns_manager.sns_client
        self.cloudwatch = boto3.client('cloudwatch', region_name=sns_manager.region)
    
    def get_topic_metrics(self, topic_arn: str, 
                         start_time: datetime, 
                         end_time: datetime) -> Dict:
        """Obtém métricas do tópico"""
        topic_name = topic_arn.split(':')[-1]
        
        metrics = {}
        metric_names = [
            'NumberOfMessagesPublished',
            'NumberOfNotificationsFailed',
            'NumberOfNotificationsDelivered'
        ]
        
        for metric_name in metric_names:
            try:
                response = self.cloudwatch.get_metric_statistics(
                    Namespace='AWS/SNS',
                    MetricName=metric_name,
                    Dimensions=[
                        {'Name': 'TopicName', 'Value': topic_name}
                    ],
                    StartTime=start_time,
                    EndTime=end_time,
                    Period=3600,  # 1 hora
                    Statistics=['Sum']
                )
                
                total = sum([point['Sum'] for point in response['Datapoints']])
                metrics[metric_name] = total
                
            except Exception as e:
                logger.error(f"Erro ao obter métrica {metric_name}: {str(e)}")
                metrics[metric_name] = 0
        
        # Calcular taxa de sucesso
        delivered = metrics.get('NumberOfNotificationsDelivered', 0)
        failed = metrics.get('NumberOfNotificationsFailed', 0)
        
        if delivered + failed > 0:
            metrics['SuccessRate'] = (delivered / (delivered + failed)) * 100
        else:
            metrics['SuccessRate'] = 100
        
        return metrics
    
    def get_subscription_status(self, subscription_arn: str) -> Dict:
        """Obtém status de uma assinatura"""
        try:
            response = self.sns_client.get_subscription_attributes(
                SubscriptionArn=subscription_arn
            )
            
            attributes = response['Attributes']
            
            return {
                'endpoint': attributes.get('Endpoint'),
                'protocol': attributes.get('Protocol'),
                'pending_confirmation': attributes.get('PendingConfirmation', 'false') == 'true',
                'enabled': attributes.get('Enabled', 'true') == 'true',
                'filter_policy': json.loads(attributes.get('FilterPolicy', '{}')),
                'delivery_policy': json.loads(attributes.get('DeliveryPolicy', '{}')),
                'effective_delivery_policy': json.loads(
                    attributes.get('EffectiveDeliveryPolicy', '{}')
                )
            }
            
        except Exception as e:
            logger.error(f"Erro ao obter status da assinatura: {str(e)}")
            return {}

# ==================== EXEMPLO DE USO COMPLETO ====================

def exemplo_uso_completo():
    """Demonstração completa do sistema de notificações"""
    
    print("=" * 70)
    print("SISTEMA DE NOTIFICAÇÕES MULTI-CANAL COM SNS")
    print("=" * 70)
    
    # 1. Inicializar orquestrador
    print("\n1. Inicializando sistema de notificações...")
    orchestrator = NotificationOrchestrator(region='us-east-1')
    
    # 2. Configurar tópicos
    print("\n2. Configurando tópicos SNS...")
    orchestrator.setup_notification_system()
    
    # 3. Registrar usuários de exemplo
    print("\n3. Registrando preferências de usuários...")
    
    usuarios = [
        {
            'id': 'USER-001',
            'preferences': {
                'email': 'aluno1@example.com',
                'phone': '+5511999999999',
                'academic_notifications': True,
                'urgent_notifications': True,
                'marketing_notifications': False,
                'webhook_url': 'https://api.exemplo.com/webhooks/sns'
            }
        },
        {
            'id': 'USER-002',
            'preferences': {
                'email': 'aluno2@example.com',
                'academic_notifications': True,
                'urgent_notifications': False,
                'marketing_notifications': True
            }
        }
    ]
    
    for usuario in usuarios:
        orchestrator.register_user_preferences(
            usuario['id'],
            usuario['preferences']
        )
    
    # 4. Enviar notificação de matrícula
    print("\n4. Enviando notificação de matrícula...")
    
    user_data = {
        'id': 'USER-001',
        'name': 'João Silva',
        'email': 'joao.silva@example.com'
    }
    
    course_data = {
        'id': 'COURSE-101',
        'name': 'Python Avançado para Data Science',
        'instructor': 'Dra. Maria Santos',
        'duration': '8 semanas',
        'start_date': '2025-02-01'
    }
    
    message_id = orchestrator.notify_course_enrollment(user_data, course_data)
    print(f"✓ Notificação de matrícula enviada: {message_id}")
    
    # 5. Enviar lembretes de atividades
    print("\n5. Enviando lembretes de atividades...")
    
    assignments = [
        {
            'user_id': 'USER-001',
            'user_name': 'João Silva',
            'name': 'Projeto Final - Análise de Dados',
            'course_name': 'Python Avançado',
            'due_date': '2025-01-15 23:59',
            'hours_until_due': 12
        },
        {
            'user_id': 'USER-002',
            'user_name': 'Maria Costa',
            'name': 'Quiz Módulo 3',
            'course_name': 'Machine Learning Básico',
            'due_date': '2025-01-20 18:00',
            'hours_until_due': 48
        }
    ]
    
    results = orchestrator.notify_assignment_due(assignments)
    print(f"✓ {len(results)} lembretes enviados")
    
    # 6. Publicação em lote (para tópico FIFO)
    print("\n6. Enviando notificações urgentes em lote...")
    
    urgent_messages = [
        {
            'message': 'Manutenção programada do sistema às 22h',
            'group_id': 'system-maintenance',
            'deduplication_id': f'maintenance-{datetime.now().date()}'
        },
        {
            'message': 'Nova política de segurança implementada',
            'group_id': 'security-updates',
            'deduplication_id': f'security-{datetime.now().date()}'
        }
    ]
    
    if orchestrator.topics.get('urgent'):
        stats = orchestrator.publisher.publish_batch(
            orchestrator.topics['urgent'],
            urgent_messages
        )
        print(f"✓ Lote enviado: {stats['successful']} sucesso, {stats['failed']} falhas")
    
    # 7. Monitorar métricas
    print("\n7. Monitorando métricas...")
    
    monitor = NotificationMonitor(orchestrator.sns_manager)
    
    # Simular período de análise
    end_time = datetime.now()
    start_time = datetime(2025, 1, 1)
    
    for topic_name, topic_arn in orchestrator.topics.items():
        metrics = monitor.get_topic_metrics(topic_arn, start_time, end_time)
        
        print(f"\n📊 Métricas do tópico {topic_name}:")
        print(f"  - Mensagens publicadas: {metrics.get('NumberOfMessagesPublished', 0)}")
        print(f"  - Notificações entregues: {metrics.get('NumberOfNotificationsDelivered', 0)}")
        print(f"  - Notificações falhadas: {metrics.get('NumberOfNotificationsFailed', 0)}")
        print(f"  - Taxa de sucesso: {metrics.get('SuccessRate', 100):.1f}%")
    
    # 8. Listar assinaturas
    print("\n8. Listando assinaturas ativas...")
    
    for topic_name, topic_arn in orchestrator.topics.items():
        subscriptions = orchestrator.subscription_manager.list_subscriptions(topic_arn)
        print(f"\n📋 Tópico {topic_name}: {len(subscriptions)} assinaturas")
        
        for sub in subscriptions[:3]:  # Mostrar apenas primeiras 3
            print(f"  - {sub['Protocol']}: {sub['Endpoint'][:30]}...")
    
    # 9. Exemplo de fan-out pattern
    print("\n9. Demonstrando fan-out pattern...")
    
    # Criar tópico para fan-out
    fanout_topic = orchestrator.sns_manager.create_topic(
        'edutech-fanout-demo',
        display_name='Fan-out Demo'
    )
    
    # Criar múltiplas filas SQS (simulado)
    print("  - Configurando múltiplos endpoints...")
    endpoints = [
        ('sqs', 'arn:aws:sqs:us-east-1:123456789:queue-analytics'),
        ('lambda', 'arn:aws:lambda:us-east-1:123456789:function:process-notification'),
        ('https', 'https://api.partner.com/notifications')
    ]
    
    print("  - Uma mensagem seria distribuída para:")
    for protocol, endpoint in endpoints:
        print(f"    • {protocol}: {endpoint}")
    
    # 10. Cleanup exemplo
    print("\n10. Limpeza (comentado para segurança)...")
    print("  # Para limpar os recursos criados:")
    print("  # for topic_arn in orchestrator.topics.values():")
    print("  #     orchestrator.sns_client.delete_topic(TopicArn=topic_arn)")
    
    print("\n" + "=" * 70)
    print("DEMONSTRAÇÃO CONCLUÍDA")
    print("=" * 70)
    
    print("\n📝 Resumo do Sistema Implementado:")
    print("  ✓ Notificações multi-canal (Email, SMS, Webhook)")
    print("  ✓ Filtragem por atributos de mensagem")
    print("  ✓ Mensagens estruturadas por protocolo")
    print("  ✓ Suporte para tópicos FIFO")
    print("  ✓ Monitoramento com CloudWatch")
    print("  ✓ Fan-out pattern para processamento paralelo")
    print("  ✓ Políticas de retry configuráveis")

# Script de teste de integração SNS + SQS
def teste_integracao_sns_sqs():
    """Demonstra integração SNS + SQS"""
    
    print("\n" + "=" * 70)
    print("TESTE DE INTEGRAÇÃO SNS + SQS")
    print("=" * 70)
    
    try:
        # Criar clientes
        sns = boto3.client('sns', region_name='us-east-1')
        sqs = boto3.client('sqs', region_name='us-east-1')
        
        # 1. Criar tópico SNS
        print("\n1. Criando tópico SNS...")
        topic_response = sns.create_topic(Name='test-sns-sqs-integration')
        topic_arn = topic_response['TopicArn']
        print(f"✓ Tópico criado: {topic_arn}")
        
        # 2. Criar fila SQS
        print("\n2. Criando fila SQS...")
        queue_response = sqs.create_queue(
            QueueName='test-sns-sqs-queue',
            Attributes={
                'VisibilityTimeout': '60',
                'MessageRetentionPeriod': '86400'
            }
        )
        queue_url = queue_response['QueueUrl']
        
        # Obter ARN da fila
        queue_attrs = sqs.get_queue_attributes(
            QueueUrl=queue_url,
            AttributeNames=['QueueArn']
        )
        queue_arn = queue_attrs['Attributes']['QueueArn']
        print(f"✓ Fila criada: {queue_arn}")
        
        # 3. Configurar política de acesso SQS
        print("\n3. Configurando permissões...")
        policy = {
            "Version": "2012-10-17",
            "Statement": [{
                "Effect": "Allow",
                "Principal": {"Service": "sns.amazonaws.com"},
                "Action": "sqs:SendMessage",
                "Resource": queue_arn,
                "Condition": {
                    "ArnEquals": {
                        "aws:SourceArn": topic_arn
                    }
                }
            }]
        }
        
        sqs.set_queue_attributes(
            QueueUrl=queue_url,
            Attributes={
                'Policy': json.dumps(policy)
            }
        )
        print("✓ Política de acesso configurada")
        
        # 4. Inscrever fila no tópico
        print("\n4. Inscrevendo fila no tópico...")
        subscription = sns.subscribe(
            TopicArn=topic_arn,
            Protocol='sqs',
            Endpoint=queue_arn
        )
        print(f"✓ Assinatura criada: {subscription['SubscriptionArn']}")
        
        # 5. Publicar mensagem teste
        print("\n5. Publicando mensagem teste...")
        test_message = {
            'event': 'test_integration',
            'timestamp': datetime.now().isoformat(),
            'data': {
                'id': 'TEST-001',
                'message': 'Teste de integração SNS + SQS'
            }
        }
        
        sns.publish(
            TopicArn=topic_arn,
            Message=json.dumps(test_message),
            Subject='Teste SNS-SQS'
        )
        print("✓ Mensagem publicada no SNS")
        
        # 6. Consumir da fila SQS
        print("\n6. Consumindo mensagem da fila SQS...")
        time.sleep(2)  # Aguardar propagação
        
        messages = sqs.receive_message(
            QueueUrl=queue_url,
            MaxNumberOfMessages=1,
            WaitTimeSeconds=5
        )
        
        if 'Messages' in messages:
            msg = messages['Messages'][0]
            body = json.loads(msg['Body'])
            print("✓ Mensagem recebida do SQS:")
            print(f"  MessageId: {body.get('MessageId')}")
            print(f"  Subject: {body.get('Subject')}")
            print(f"  Message: {body.get('Message')}")
            
            # Deletar mensagem
            sqs.delete_message(
                QueueUrl=queue_url,
                ReceiptHandle=msg['ReceiptHandle']
            )
        
        print("\n✅ Integração SNS + SQS funcionando corretamente!")
        
        # Cleanup
        print("\n7. Limpando recursos de teste...")
        sns.delete_topic(TopicArn=topic_arn)
        sqs.delete_queue(QueueUrl=queue_url)
        print("✓ Recursos removidos")
        
    except Exception as e:
        print(f"❌ Erro no teste: {str(e)}")

# Executar demonstração
if __name__ == "__main__":
    print("\nEscolha uma demonstração:")
    print("1. Sistema completo de notificações")
    print("2. Teste de integração SNS + SQS")
    
    choice = input("\nOpção (1 ou 2): ").strip()
    
    try:
        if choice == '1':
            exemplo_uso_completo()
        elif choice == '2':
            teste_integracao_sns_sqs()
        else:
            print("Opção inválida")
    except Exception as e:
        logger.error(f"Erro na execução: {str(e)}")
        print("\nRequisitos:")
        print("• boto3 instalado (pip install boto3)")
        print("• Credenciais AWS configuradas")
        print("• Permissões SNS e SQS")

## Casos de Uso Comuns

### Notificações de Aplicação

- **Alertas de sistema**: Notificar equipes sobre falhas, erros ou eventos críticos
- **Confirmações de transação**: Enviar confirmações por múltiplos canais simultaneamente
- **Atualizações de status**: Informar mudanças de estado em processos de negócio

### Processamento Assíncrono

- **Fan-out pattern**: Distribuir uma mensagem para múltiplos sistemas de processamento
- **Workflow orchestration**: Coordenar etapas de processamento entre serviços
- **Event broadcasting**: Propagar eventos entre microsserviços

### Comunicação com Usuários

- **Marketing campaigns**: Envio de promoções e newsletters
- **Alertas de segurança**: Notificações de login, mudanças de senha
- **Lembretes**: Agendamentos, vencimentos, prazos

### Integração de Sistemas

- **Webhooks**: Notificar sistemas externos sobre eventos
- **Data pipeline triggers**: Iniciar processamento de dados
- **Cross-region replication**: Replicar eventos entre regiões AWS

### DevOps e Monitoramento

- **CI/CD notifications**: Alertas de build, deploy e testes
- **Infrastructure alerts**: Notificações do CloudWatch sobre recursos
- **Audit trail**: Registrar e distribuir eventos de auditoria

## Padrões de Arquitetura com SNS

### Fan-out Pattern

SNS distribui uma mensagem para múltiplos destinos simultaneamente. Ideal para processar a mesma informação de diferentes formas - por exemplo, um pedido que precisa ser processado pelo financeiro (SQS), análise em tempo real (Kinesis) e notificação ao cliente (Email).

### Message Filtering

Subscribers recebem apenas mensagens relevantes através de filter policies. Reduz processamento desnecessário e custos, permitindo que cada consumidor receba apenas o que precisa.

### Cross-Region Fanout

SNS pode replicar mensagens entre regiões para disaster recovery ou distribuição geográfica. Útil para aplicações globais que precisam de baixa latência regional.

### Decoupling Microservices

SNS atua como barramento de eventos entre serviços, permitindo que evoluam independentemente. Serviços publicam eventos sem conhecer os consumidores.

### Hybrid Cloud Integration

SNS pode enviar notificações para endpoints on-premises via HTTPS. Facilita integração entre AWS e datacenters privados.

## Melhores Práticas

### Design de Tópicos

Organize tópicos por domínio de negócio ou tipo de evento, não por destino. Use convenção de nomenclatura consistente como `environment-domain-event-type`. Evite criar tópicos muito genéricos que recebem todos os tipos de mensagem. Considere usar tags para organização e billing.

### Segurança

Implemente princípio de menor privilégio com IAM policies específicas. Use criptografia KMS para dados sensíveis. Configure VPC endpoints para tráfego privado. Implemente message signing para verificar autenticidade. Audite acesso com CloudTrail.

### Confiabilidade

Configure retry policies apropriadas para cada tipo de endpoint. Implemente Dead Letter Queues para mensagens falhadas. Monitore delivery status e configure alertas. Use message deduplication em tópicos FIFO. Implemente idempotência nos consumers.

### Performance

Use batching quando possível para reduzir chamadas de API. Configure subscription filter policies para reduzir processamento. Ajuste message attributes apenas com dados necessários. Use message compression para payloads grandes. Distribua carga entre múltiplos tópicos se necessário.

### Custos

Monitore usage patterns para otimizar custos. Consolide tópicos similares quando apropriado. Use filter policies para evitar processamento desnecessário. Configure retention apropriada em destinos como SQS. Considere Reserved Capacity para alto volume.

### Monitoramento

Configure métricas customizadas no CloudWatch. Implemente distributed tracing com X-Ray. Mantenha logs estruturados de publicação e consumo. Configure dashboards para visualização em tempo real. Estabeleça SLOs e monitore aderência.

## Comparação com Alternativas

### SNS vs SQS

- **SNS**: Push-based, pub/sub, múltiplos destinatários, sem persistência
- **SQS**: Pull-based, queue, um consumidor por mensagem, persistência até 14 dias
- **Uso conjunto**: SNS + SQS é padrão comum para fan-out com garantia de entrega

### SNS vs EventBridge

- **SNS**: Simples, focado em notificações, filtering básico
- **EventBridge**: Event routing avançado, schema registry, replay de eventos
- **Escolha**: SNS para notificações simples, EventBridge para event-driven architectures complexas

### SNS vs Kinesis

- **SNS**: Mensagens individuais, múltiplos tipos de destino, simples
- **Kinesis**: Streaming contínuo, ordem garantida, replay de dados
- **Escolha**: SNS para notificações, Kinesis para análise de streaming

### SNS vs Apache Kafka

- **SNS**: Totalmente gerenciado, integração AWS nativa, setup simples
- **Kafka**: Mais controle, recursos avançados, requer gerenciamento
- **Escolha**: SNS para simplicidade, Kafka para requisitos complexos de streaming

### SNS vs Google Pub/Sub

- **SNS**: Integração profunda com AWS, múltiplos protocolos
- **Pub/Sub**: Similar funcionalidade, melhor integração GCP
- **Escolha**: Baseada no cloud provider principal

## Limitações e Considerações

### Limites de Serviço

- 100,000 tópicos por região (soft limit)
- 12,500,000 subscriptions por tópico
- 256 KB máximo por mensagem
- 10 mensagens por batch publish
- Rate limits variam por endpoint type

### Considerações de Design

- Sem replay de mensagens nativo (use EventBridge se necessário)
- Ordem não garantida em standard topics
- FIFO topics limitados a SQS FIFO queues
- Sem message routing complexo (considere EventBridge)
- Delivery status limitado a certos protocolos

## Troubleshooting Comum

### Mensagens Não Entregues

- Verificar IAM permissions do tópico e destino
- Confirmar subscription confirmada (não pending)
- Revisar filter policies
- Verificar CloudWatch logs para erros
- Testar endpoint diretamente

### Alta Latência

- Verificar retry configuration
- Avaliar endpoint performance
- Considerar regional proximity
- Verificar throttling limits
- Otimizar message size

### Custos Inesperados

- Auditar número de mensagens publicadas
- Verificar failed deliveries causando retries
- Revisar SMS/email volumes
- Avaliar cross-region transfers
- Verificar message attributes desnecessários

## Conclusão

Amazon SNS é uma solução poderosa e versátil para implementar comunicação assíncrona e notificações em arquiteturas modernas. Sua capacidade de entregar mensagens para múltiplos destinos simultaneamente, combinada com a simplicidade operacional de um serviço totalmente gerenciado, o torna ideal para uma ampla gama de casos de uso.

As principais vantagens incluem a escalabilidade automática, integração profunda com o ecossistema AWS, suporte multi-protocolo e modelo de custos flexível. É particularmente forte em cenários de fan-out, notificações de usuário e integração de microsserviços.

As limitações em tamanho de mensagem e complexidade de roteamento devem ser consideradas, mas para a maioria dos casos de uso de pub/sub, SNS oferece um excelente equilíbrio entre funcionalidade e simplicidade. A combinação com outros serviços AWS como SQS, Lambda e EventBridge permite arquiteturas event-driven robustas e escaláveis.

O exemplo fornecido demonstra um sistema completo de notificações multi-canal que pode ser adaptado para diversos cenários empresariais, desde e-commerce até plataformas educacionais, incluindo filtragem avançada, múltiplos protocolos e monitoramento completo.