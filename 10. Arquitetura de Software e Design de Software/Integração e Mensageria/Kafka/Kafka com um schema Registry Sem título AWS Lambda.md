1. Um **produtor Python** envia dados de eventos estruturados (por exemplo, um clique do usuário) para um tópico do Kafka.
    
2. Os dados são serializados usando um **esquema Avro** gerenciado por um **Registro de Esquemas** . Isso garante a qualidade dos dados e estabelece um contrato entre produtores e consumidores.
    
3. Uma **função do AWS Lambda** é acionada por novas mensagens no tópico do Kafka.
    
4. A função Lambda desserializa a mensagem Avro usando o esquema do registro e então a processa (no nosso caso, ela simplesmente registrará os dados decodificados).
    

### Conceitos Básicos

- **Apache Kafka:** Uma plataforma de streaming de eventos distribuída usada para feeds de dados de alta taxa de transferência em tempo real. Usaremos o Amazon MSK (Managed Streaming for Kafka) como nosso broker Kafka.
    
- **Registro de Esquemas:** Um repositório centralizado para seus esquemas de dados. Ele garante que os dados produzidos sejam compatíveis com os consumidos, evitando problemas de qualidade dos dados. Usaremos o Registro de Esquemas Confluent.
    
- **Apache Avro:** um formato compacto de serialização de dados binários que se baseia em esquemas. É altamente eficiente e suporta evolução de esquemas (por exemplo, adicionar um novo campo sem interromper os consumidores).
    
- **AWS Lambda:** um serviço de computação sem servidor e orientado a eventos que permite executar código sem provisionar ou gerenciar servidores. É um consumidor perfeito para um tópico do Kafka.
    

### Diagrama de Arquitetura

```
+----------------+      +-------------------------+      +-----------------+      +---------------------+      +---------------+
| Python         |----->|   Amazon MSK (Kafka)    |----->|  AWS Lambda     |----->| AWS CloudWatch      |
| Producer       |      |       (Avro Data)       |      |  (Consumer)     |      |      (Logs)         |
+----------------+      +-------------------------+      +-----------------+      +---------------------+
       |                            ^
       | (Register/Fetch Schema)    | (Fetch Schema)
       v                            |
+--------------------------+
|  Confluent Schema        |
|  Registry                |
+--------------------------+
```

---

### Pré-requisitos

1. **Conta AWS:** com permissões para criar clusters MSK, funções Lambda, funções IAM e recursos VPC.
    
2. **Conta Confluent Cloud:** usaremos o nível gratuito do Confluent Cloud para hospedar nosso Schema Registry, que é a maneira mais fácil de começar.
    
3. **Python 3.8+:** instalado na sua máquina local.
    
4. **AWS CLI:** Configurado com suas credenciais.
    
5. **Uma VPC na sua conta AWS:** o Amazon MSK requer uma Nuvem Privada Virtual (VPC). Você pode usar sua VPC padrão para este tutorial. Anote o ID, as sub-redes e o grupo de segurança.
    

---

### Etapa 1: configurar o Kafka e o Schema Registry

Neste tutorial, usaremos o Amazon MSK para Kafka e o Confluent Cloud para o Schema Registry.

#### A. Configurar Amazon MSK (Kafka Cluster)

1. Navegue até o serviço **Amazon MSK** no Console da AWS.
    
2. Clique em **Criar cluster** .
    
3. Selecione **Criação personalizada** .
    
4. **Nome do cluster:** `my-kafka-cluster`
    
5. **Tipo de corretor:** use um tipo de instância pequena como `kafka.t3.small`a deste tutorial.
    
6. **Rede:** Selecione sua VPC e pelo menos duas sub-redes em diferentes Zonas de Disponibilidade. Isso é crucial para alta disponibilidade.
    
7. **Grupo de Segurança:** Crie um novo grupo de segurança para o cluster MSK. Edite suas regras de entrada para permitir **todo** o tráfego TCP de dentro do próprio grupo de segurança (selecione o ID do grupo como origem). Isso permite que os brokers se comuniquem. Posteriormente, você adicionará uma regra para sua função Lambda.
    
8. **Controle de acesso:** Para simplificar, selecione a autenticação **SASL/SCRAM** . Crie um novo segredo no AWS Secrets Manager para armazenar o nome de usuário e a senha. Associe esse segredo ao cluster.
    
9. Deixe as outras configurações como padrão e clique em **Criar cluster** . O provisionamento pode levar de 15 a 25 minutos.
    

Quando o cluster estiver ativo, clique nele e, em seguida, em **Exibir informações do cliente** . Copie a string **dos servidores Bootstrap** para SASL/SCRAM. Você precisará dela mais tarde.

#### B. Configurar o Registro de Esquema do Confluent Cloud

1. Inscreva-se gratuitamente[Nuvem Confluente](https://www.confluent.io/get-started/)conta.
    
2. Crie um novo ambiente de cluster "Básico".
    
3. No seu ambiente, no menu à esquerda, vá para **Schema Registry** .
    
4. Habilite o Registro de Esquema, caso ainda não esteja habilitado.
    
5. No lado direito, em "Credenciais da API", clique em **Criar chave** . Dê um nome a ela e salve a **Chave** e **o Segredo** . Essas são as suas credenciais da API do Schema Registry.
    
6. Você também precisará do URL **do ponto de extremidade da API** para o registro, que é exibido na mesma página.
    

---

### Etapa 2: Definir o Esquema Avro

Um esquema Avro é definido usando JSON. Vamos criar um esquema simples para um evento de clique do usuário.

Crie um arquivo chamado `user_click.avsc`:

JSON

```
{
  "namespace": "com.mycompany.events",
  "type": "record",
  "name": "UserClick",
  "fields": [
    { "name": "user_id", "type": "string" },
    { "name": "url", "type": "string" },
    { "name": "timestamp", "type": "long", "logicalType": "timestamp-millis" }
  ]
}
```

Este esquema define um `UserClick`evento com três campos. O esquema será registrado automaticamente pelo nosso produtor na primeira vez que for executado.

---

### Etapa 3: Crie o produtor Python Kafka

Este script será executado na sua máquina local para enviar mensagens codificadas em Avro para o seu tópico MSK.

Primeiro, instale as bibliotecas Python necessárias:

Bash

```
pip install confluent-kafka[avro]
```

Agora, crie um script Python chamado `producer.py`:

Python

```
import time
import uuid
from confluent_kafka import avro
from confluent_kafka.avro import AvroProducer

# --- Configuration ---
# MSK Kafka Configuration
KAFKA_BOOTSTRAP_SERVERS = "YOUR_MSK_BOOTSTRAP_SERVERS_SASL_SCRAM" # From MSK client info
KAFKA_TOPIC = "user-clicks-avro"
KAFKA_SASL_USERNAME = "YOUR_SASL_USERNAME_FROM_SECRETS_MANAGER"
KAFKA_SASL_PASSWORD = "YOUR_SASL_PASSWORD_FROM_SECRETS_MANAGER"

# Confluent Schema Registry Configuration
SCHEMA_REGISTRY_URL = "YOUR_SCHEMA_REGISTRY_API_ENDPOINT" # From Confluent Cloud
SCHEMA_REGISTRY_API_KEY = "YOUR_SCHEMA_REGISTRY_KEY"
SCHEMA_REGISTRY_API_SECRET = "YOUR_SCHEMA_REGISTRY_SECRET"

# --- Avro Schema ---
value_schema_str = """
{
  "namespace": "com.mycompany.events",
  "type": "record",
  "name": "UserClick",
  "fields": [
    { "name": "user_id", "type": "string" },
    { "name": "url", "type": "string" },
    { "name": "timestamp", "type": "long", "logicalType": "timestamp-millis" }
  ]
}
"""
value_schema = avro.loads(value_schema_str)

# --- Producer Logic ---
def delivery_report(err, msg):
    """ Called once for each message produced to indicate delivery result. """
    if err is not None:
        print(f"Message delivery failed: {err}")
    else:
        print(f"Message delivered to {msg.topic()} [{msg.partition()}]")

def main():
    producer_config = {
        'bootstrap.servers': KAFKA_BOOTSTRAP_SERVERS,
        'security.protocol': 'SASL_SSL',
        'sasl.mechanisms': 'SCRAM-SHA-512',
        'sasl.username': KAFKA_SASL_USERNAME,
        'sasl.password': KAFKA_SASL_PASSWORD,
        'schema.registry.url': SCHEMA_REGISTRY_URL,
        'schema.registry.basic.auth.user.info': f'{SCHEMA_REGISTRY_API_KEY}:{SCHEMA_REGISTRY_API_SECRET}'
    }

    avro_producer = AvroProducer(producer_config, default_value_schema=value_schema)

    try:
        for i in range(5):
            user_id = str(uuid.uuid4())
            click_event = {
                "user_id": user_id,
                "url": f"https://example.com/page/{i}",
                "timestamp": int(time.time() * 1000)
            }
            
            print(f"Producing message: {click_event}")
            avro_producer.produce(
                topic=KAFKA_TOPIC,
                value=click_event,
                callback=delivery_report
            )
            time.sleep(1)
    except Exception as e:
        print(f"An error occurred: {e}")
    finally:
        # Wait for any outstanding messages to be delivered and delivery reports to be received.
        avro_producer.flush()

if __name__ == '__main__':
    main()
```

**Antes de correr:**

- Preencha todos os `YOUR_*`valores de espaço reservado.
    
- Certifique-se de ter criado o tópico `user-clicks-avro`no seu cluster MSK. Você pode fazer isso por meio das ferramentas da CLI do Kafka.
    

Execute o produtor: `python producer.py`. Ele deve começar a enviar mensagens.

---

### Etapa 4: criar e configurar o consumidor do AWS Lambda

#### A. Crie a função IAM

1. Acesse o serviço **IAM** no Console da AWS.
    
2. Vá para **Funções** e clique em **Criar função** .
    
3. Selecione **o serviço AWS** e escolha **Lambda** como caso de uso.
    
4. Adicione as seguintes políticas gerenciadas pela AWS:
    
    - `AWSLambdaMSKExecutionRole`: Permite que o Lambda leia tópicos do MSK e gerencie grupos de consumidores.
        
    - `AWSLambdaVPCAccessExecutionRole`: Permite que o Lambda se conecte a recursos dentro da sua VPC (que é onde o MSK fica).
        
5. Dê um nome à função (por exemplo, `LambdaMSKAvroConsumerRole`) e crie-a.
    

#### B. Crie a função Lambda e o pacote de implantação

A função Lambda precisa da `confluent-kafka[avro]`biblioteca para desserializar as mensagens. Como esta não é uma biblioteca interna, precisamos criar um pacote de implantação.

1. Crie uma pasta de projeto na sua máquina local:
    
    Bash
    
    ```
    mkdir lambda_consumer
    cd lambda_consumer
    ```
    
2. Instale as bibliotecas nesta pasta:
    
    Bash
    
    ```
    pip install confluent-kafka[avro] -t .
    ```
    
3. Crie o arquivo manipulador `lambda_function.py`no `lambda_consumer`diretório:
    
    Python
    
    ```
    import base64
    import os
    import io
    from confluent_kafka.avro import AvroDeserializer
    from confluent_kafka.avro.serializer import SerializerError
    
    # --- Configuration (loaded from environment variables) ---
    SCHEMA_REGISTRY_URL = os.environ['SCHEMA_REGISTRY_URL']
    SCHEMA_REGISTRY_API_KEY = os.environ['SCHEMA_REGISTRY_API_KEY']
    SCHEMA_REGISTRY_API_SECRET = os.environ['SCHEMA_REGISTRY_API_SECRET']
    
    # --- Initialize Deserializer ---
    # This part should be outside the handler to be reused across invocations
    schema_registry_conf = {
        'url': SCHEMA_REGISTRY_URL,
        'basic.auth.user.info': f'{SCHEMA_REGISTRY_API_KEY}:{SCHEMA_REGISTRY_API_SECRET}'
    }
    avro_deserializer = AvroDeserializer(schema_registry_conf=schema_registry_conf)
    
    
    def lambda_handler(event, context):
        print(f"Received {len(event['records'])} records.")
    
        # event['records'] is a dictionary where keys are 'topic-partition'
        # and values are lists of message records.
        for topic_partition, records in event['records'].items():
            print(f"Processing records from {topic_partition}")
            for record in records:
                # The message value from MSK is Base64 encoded.
                message_bytes = base64.b64decode(record['value'])
    
                try:
                    # The first byte is a "magic byte", followed by 4 bytes for schema ID.
                    # The AvroDeserializer handles this automatically.
                    deserialized_message = avro_deserializer(message_bytes, None) # context is not used
    
                    print(f"Successfully deserialized message: {deserialized_message}")
    
                    # --- YOUR BUSINESS LOGIC GOES HERE ---
                    # e.g., save to DynamoDB, send to SQS, call another API, etc.
    
                except SerializerError as e:
                    print(f"Error deserializing message: {e}")
                except Exception as e:
                    print(f"An unexpected error occurred: {e}")
    
        return {
            'statusCode': 200,
            'body': 'Successfully processed records.'
        }
    ```
    
4. Compacte o conteúdo do `lambda_consumer`diretório (não o diretório em si).
    
    Bash
    
    ```
    zip -r deployment_package.zip .
    ```
    

#### C. Implantar e configurar o Lambda

1. Acesse o serviço **Lambda** no Console da AWS.
    
2. Clique em **Criar função** .
    
3. Selecione **Autor do zero** .
    
4. **Nome da função:** `KafkaAvroProcessor`
    
5. **Tempo de execução:** Python 3.9
    
6. **Arquitetura:** x86_64
    
7. **Permissões:** escolha **Usar uma função existente** e selecione a que `LambdaMSKAvroConsumerRole`você criou.
    
8. Clique em **Criar função** .
    
9. **Código de upload:**
    
    - Em **Fonte do código** , clique em **Carregar de** e selecione **o arquivo .zip** .
        
    - Faça upload do que `deployment_package.zip`você criou.
        
10. **Configurar VPC:**
    
    - Vá para a aba **Configuração** e selecione **VPC** .
        
    - Clique em **Editar** .
        
    - Selecione a mesma VPC do seu cluster MSK.
        
    - Selecione as mesmas sub-redes.
        
    - Selecione um grupo de segurança que possa acessar o cluster MSK. Uma boa prática é criar um novo grupo de segurança para o Lambda e, em seguida, modificar as regras de entrada do grupo de segurança MSK para permitir todo o tráfego TCP do grupo de segurança do Lambda.
        
11. **Definir variáveis ​​de ambiente:**
    
    - Vá para a aba **Configuração** e selecione **Variáveis ​​de ambiente** .
        
    - Adicione os seguintes pares de chave-valor com suas credenciais do Confluent Cloud:
        
        - `SCHEMA_REGISTRY_URL`:`YOUR_SCHEMA_REGISTRY_API_ENDPOINT`
            
        - `SCHEMA_REGISTRY_API_KEY`:`YOUR_SCHEMA_REGISTRY_KEY`
            
        - `SCHEMA_REGISTRY_API_SECRET`:`YOUR_SCHEMA_REGISTRY_SECRET`
            
12. **Adicione o gatilho Kafka:**
    
    - Na visão geral da função, clique em **Adicionar gatilho** .
        
    - Selecione **MSK** .
        
    - **Cluster MSK:** Escolha seu `my-kafka-cluster`.
        
    - **Tamanho do lote:** 100 (o padrão é adequado para testes).
        
    - **Nome do tópico:** `user-clicks-avro`
        
    - **Autenticação:** habilite SASL/SCRAM-512 e selecione o mesmo segredo que você criou para MSK.
        
    - Clique em **Adicionar** . O gatilho iniciará no estado "Criando" e deverá ficar "Habilitado" após alguns minutos.
        

---

### Etapa 5: Teste o pipeline de ponta a ponta

1. Execute seu `producer.py`script novamente na sua máquina local.
    
    Bash
    
    ```
    python producer.py
    ```
    
2. Navegue até sua `KafkaAvroProcessor`função Lambda no console da AWS.
    
3. Vá para a aba **Monitor** e clique em **Exibir logs do CloudWatch** .
    
4. Você deverá ver entradas de log mostrando o lote de registros recebidos e, mais importante, os objetos de mensagem desserializados com sucesso, impressos no formato JSON.
    

**Exemplo de saída de log do CloudWatch:**

```
INFO Received 5 records.
INFO Processing records from user-clicks-avro-0
INFO Successfully deserialized message: {'user_id': '...', 'url': '...', 'timestamp': 167...}
INFO Successfully deserialized message: {'user_id': '...', 'url': '...', 'timestamp': 167...}
...
```

Parabéns! Você construiu com sucesso um pipeline de processamento de dados sem servidor com Kafka, um Schema Registry e AWS Lambda, garantindo que todos os seus dados de streaming estejam bem estruturados e validados.