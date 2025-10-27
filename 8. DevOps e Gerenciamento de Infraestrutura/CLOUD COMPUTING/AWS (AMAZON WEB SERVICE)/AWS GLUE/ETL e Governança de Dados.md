
# AWS Glue: Guia Completo de ETL e Governança de Dados



---

## Visão Geral

O **AWS Glue** é um serviço de ETL (Extract, Transform, Load) totalmente gerenciado que facilita a preparação e carregamento de dados para análise. É uma plataforma serverless que automatiza grande parte do trabalho pesado envolvido na descoberta, catalogação e transformação de dados.

### Características Principais

- **Serverless**: Sem infraestrutura para gerenciar
- **Pay-as-you-go**: Paga apenas pelo tempo de execução
- **Escalável**: Processa de gigabytes a petabytes
- **Integrado**: Conecta-se nativamente com serviços AWS

### Quando Usar AWS Glue

- Construção de data lakes e data warehouses
- Unificação de dados de múltiplas fontes
- Preparação de dados para Machine Learning
- Implementação de governança de dados corporativa
- Migração e modernização de pipelines ETL

---

## Componentes Principais

### 1. **Glue Data Catalog**

Repositório centralizado de metadados que armazena informações sobre estrutura, schema e localização dos dados.

**Funcionalidades:**

- Armazenamento de metadados de tabelas e databases
- Versionamento automático de schemas
- Compatível com Apache Hive Metastore
- Integração com Amazon Athena, EMR e Redshift Spectrum

### 2. **Glue Crawlers**

Processos automatizados que descobrem e catalogam dados automaticamente.

**Características:**

- Inferência automática de schema
- Detecção de partições
- Classificação de formatos de arquivo
- Agendamento flexível

### 3. **Glue Jobs**

Processos ETL que executam transformações em dados.

**Tipos de Jobs:**

- **Spark ETL Jobs**: Para processamento em larga escala
- **Python Shell Jobs**: Para tarefas menores e scripts Python
- **Streaming Jobs**: Para processamento em tempo real

### 4. **Glue Studio**

Interface visual drag-and-drop para criar, executar e monitorar jobs ETL.

**Benefícios:**

- Desenvolvimento sem código
- Visualização do fluxo de dados
- Debugging visual
- Geração automática de código

### 5. **Glue DataBrew**

Ferramenta visual de preparação de dados para usuários não-técnicos.

**Capacidades:**

- 250+ transformações pré-construídas
- Profiling automático de dados
- Receitas reutilizáveis
- Visualizações interativas

---

## AWS Glue como Plataforma ETL

### Arquitetura ETL

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Extract   │ --> │  Transform  │ --> │    Load     │
└─────────────┘     └─────────────┘     └─────────────┘
      ↓                    ↓                    ↓
  [Fontes]            [Glue Jobs]          [Destinos]
  - S3                - PySpark            - Redshift
  - RDS               - Python             - S3 Data Lake
  - DynamoDB          - Scala              - RDS
  - On-premises                           - ElasticSearch
```

### Processo ETL Detalhado

#### 1. **Extração (Extract)**

```python
# Conectando a uma fonte de dados
datasource = glueContext.create_dynamic_frame.from_catalog(
    database = "vendas_db",
    table_name = "transacoes_raw",
    transformation_ctx = "datasource"
)
```

#### 2. **Transformação (Transform)**

```python
# Aplicando transformações
from awsglue.transforms import *

# Filtrar dados
filtered = Filter.apply(
    frame = datasource,
    f = lambda x: x["valor"] > 100
)

# Mapear campos
mapped = ApplyMapping.apply(
    frame = filtered,
    mappings = [
        ("id", "long", "transaction_id", "long"),
        ("data", "string", "transaction_date", "date"),
        ("valor", "double", "amount", "decimal")
    ]
)

# Resolver escolhas de tipo
resolved = ResolveChoice.apply(
    frame = mapped,
    choice = "cast:decimal"
)
```

#### 3. **Carga (Load)**

```python
# Carregar dados transformados
datasink = glueContext.write_dynamic_frame.from_catalog(
    frame = resolved,
    database = "vendas_analytics",
    table_name = "transacoes_processadas",
    transformation_ctx = "datasink"
)
```

### Processamento em Tempo Real

O Glue também suporta ETL streaming para dados em tempo real:

```python
# Configurar streaming source
streaming_frame = glueContext.create_data_frame.from_catalog(
    database = "streaming_db",
    table_name = "kinesis_stream",
    transformation_ctx = "streaming_source",
    additional_options = {
        "startingPosition": "latest",
        "maxFetchTimeInMs": 60000
    }
)

# Processar micro-batches
def process_batch(data_frame, batch_id):
    # Transformações
    processed = transform_data(data_frame)
    # Salvar resultados
    processed.write.mode("append").parquet("s3://bucket/streaming/")

# Iniciar streaming
query = streaming_frame.writeStream \
    .foreachBatch(process_batch) \
    .outputMode("append") \
    .start()
```

---

## Governança de Dados

### Papel do Glue na Governança Corporativa

O AWS Glue é fundamental para estabelecer uma governança de dados robusta, fornecendo:

#### 1. **Catalogação Centralizada**

- **Metadados unificados**: Fonte única de verdade para schemas
- **Descoberta automática**: Crawlers identificam novos dados
- **Documentação**: Descrições e tags para contexto de negócio

#### 2. **Linhagem de Dados (Data Lineage)**

```python
# Rastreamento de transformações
lineage_info = {
    "source": "s3://raw-data/sales/",
    "transformations": [
        {"step": 1, "operation": "filter", "criteria": "valor > 100"},
        {"step": 2, "operation": "aggregate", "groupby": "categoria"},
        {"step": 3, "operation": "join", "with": "dim_produtos"}
    ],
    "destination": "s3://processed-data/sales-analytics/",
    "job_run_id": context.get_job_run_id()
}
```

#### 3. **Classificação e Descoberta de Dados Sensíveis**

**Configuração de Classificadores Customizados:**

```json
{
  "Name": "PII_Classifier",
  "Predicate": {
    "Matches": [
      {"ColumnName": "cpf", "Pattern": "\\d{3}\\.\\d{3}\\.\\d{3}-\\d{2}"},
      {"ColumnName": "email", "Pattern": "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$"}
    ]
  },
  "Classification": "Sensitive-PII"
}
```

#### 4. **Integração com Lake Formation**

O Glue Data Catalog integra-se perfeitamente com o AWS Lake Formation para governança avançada:

```python
# Definir permissões granulares
lake_formation_permissions = {
    "Principal": "arn:aws:iam::123456789012:role/DataAnalyst",
    "Resource": {
        "Table": {
            "DatabaseName": "vendas_db",
            "Name": "transacoes"
        }
    },
    "Permissions": ["SELECT"],
    "PermissionsWithGrantOption": []
}
```

### Conformidade e Auditoria

#### Tracking de Acessos com CloudTrail

```json
{
  "eventTime": "2024-01-15T10:30:00Z",
  "eventName": "GetTable",
  "userIdentity": {
    "principalId": "AIDAI23HXD2O5EXAMPLE",
    "userName": "data_engineer"
  },
  "requestParameters": {
    "databaseName": "vendas_db",
    "name": "transacoes_sensíveis"
  }
}
```

---

## Data Quality

### Implementação de Regras de Qualidade

#### 1. **Tipos de Regras Disponíveis**

```python
# Definição de regras de qualidade
quality_rules = """
Rules = [
    # Completude
    IsComplete "customer_id",
    Completeness "email" > 0.95,
    
    # Unicidade
    IsUnique "transaction_id",
    Uniqueness "order_number" = 1.0,
    
    # Validação de formato
    ColumnValues "email" matches Email(),
    ColumnValues "phone" matches Pattern("[0-9]{10,11}"),
    
    # Validação de intervalo
    ColumnValues "age" between 0 and 120,
    ColumnValues "transaction_date" > CurrentDate() - 30,
    
    # Integridade referencial
    ReferentialIntegrity "product_id" references "dim_products.id",
    
    # Consistência
    ColumnCorrelation "total" = "quantity * unit_price",
    
    # Estatísticas
    Mean "response_time" < 2000,
    StandardDeviation "price" < 100
]
"""
```

#### 2. **Estratégias de Implementação**

##### A. Validação Preventiva (Durante ETL)

```python
def validate_and_process(glueContext, source_frame):
    # Avaliar qualidade
    dq_results = glueContext.evaluate_data_quality(
        frame=source_frame,
        ruleset=quality_rules,
        publishing_options={
            "dataQualityEvaluationContext": "MyETLJob",
            "enableDataQualityCloudWatchMetrics": True,
            "enableDataQualityResultsPublishing": True
        }
    )
    
    # Separar dados bons e ruins
    good_records = dq_results.ruleOutcomes['Passed']
    bad_records = dq_results.ruleOutcomes['Failed']
    
    # Processar dados bons
    if good_records.count() > 0:
        process_good_data(good_records)
    
    # Tratar dados ruins
    if bad_records.count() > 0:
        handle_bad_data(bad_records)
    
    return dq_results.score
```

##### B. Pipeline com Staging e Validação

```python
class DataQualityPipeline:
    def __init__(self, glue_context):
        self.glue_context = glue_context
        
    def run_pipeline(self, source_path, target_db):
        # 1. Carregar para staging
        staging_data = self.load_to_staging(source_path)
        
        # 2. Validar qualidade
        quality_score = self.validate_data_quality(staging_data)
        
        # 3. Decisão baseada em threshold
        if quality_score >= 0.95:
            # Promover para produção
            self.promote_to_production(staging_data, target_db)
            self.log_success(quality_score)
        elif quality_score >= 0.80:
            # Aceitar com alertas
            self.promote_with_warnings(staging_data, target_db)
            self.send_quality_alert(quality_score)
        else:
            # Rejeitar e investigar
            self.quarantine_data(staging_data)
            self.trigger_investigation(quality_score)
    
    def validate_data_quality(self, data):
        rules = self.get_quality_rules()
        results = self.glue_context.evaluate_data_quality(data, rules)
        
        # Publicar métricas no CloudWatch
        self.publish_metrics(results)
        
        return results.score
```

#### 3. **Monitoramento Contínuo**

```python
# Job agendado para monitorar qualidade
def continuous_quality_monitoring():
    # Configurar regras por tabela
    table_rules = {
        "customers": ["IsComplete 'email'", "IsUnique 'customer_id'"],
        "transactions": ["ColumnValues 'amount' > 0", "IsComplete 'date'"],
        "products": ["IsUnique 'sku'", "ColumnValues 'price' > 0"]
    }
    
    for table, rules in table_rules.items():
        # Avaliar cada tabela
        results = evaluate_table_quality(table, rules)
        
        # Criar dashboard metrics
        cloudwatch.put_metric_data(
            Namespace='DataQuality',
            MetricData=[
                {
                    'MetricName': f'{table}_quality_score',
                    'Value': results.score,
                    'Timestamp': datetime.now()
                }
            ]
        )
        
        # Alertar se abaixo do threshold
        if results.score < 0.90:
            sns.publish(
                TopicArn='arn:aws:sns:region:account:data-quality-alerts',
                Subject=f'Alerta: Qualidade de {table} abaixo do esperado',
                Message=f'Score: {results.score}\nRegras falhadas: {results.failed_rules}'
            )
```

---

## Arquiteturas e Padrões

### 1. **Arquitetura Data Lake com Governança**

```
┌──────────────────────────────────────────────────────┐
│                   Fontes de Dados                     │
├────────────┬────────────┬────────────┬──────────────┤
│    RDS     │  DynamoDB  │   Kinesis  │  On-Premises │
└────────────┴────────────┴────────────┴──────────────┘
                            ↓
┌──────────────────────────────────────────────────────┐
│                  AWS Glue Crawlers                    │
│              (Descoberta e Catalogação)               │
└──────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────┐
│                 AWS Glue Data Catalog                 │
│           (Metadados Centralizados + Tags)            │
└──────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────┐
│                    AWS Glue Jobs                      │
│                         ↓                             │
│    ┌─────────────────────────────────────────┐       │
│    │  1. Raw Zone (Bronze)                   │       │
│    │     - Dados brutos                      │       │
│    │     - Sem transformação                 │       │
│    └─────────────────────────────────────────┘       │
│                         ↓                             │
│    ┌─────────────────────────────────────────┐       │
│    │  2. Refined Zone (Silver)               │       │
│    │     - Dados limpos                      │       │
│    │     - Validação de qualidade            │       │
│    │     - Deduplicação                      │       │
│    └─────────────────────────────────────────┘       │
│                         ↓                             │
│    ┌─────────────────────────────────────────┐       │
│    │  3. Curated Zone (Gold)                 │       │
│    │     - Dados agregados                   │       │
│    │     - Business logic aplicada           │       │
│    │     - Pronto para consumo               │       │
│    └─────────────────────────────────────────┘       │
└──────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────┐
│                      Consumo                          │
├────────────┬────────────┬────────────┬──────────────┤
│   Athena   │  Redshift  │ QuickSight │  SageMaker   │
└────────────┴────────────┴────────────┴──────────────┘
```

### 2. **Pipeline de Dados com Validação Multi-Stage**

```python
class MultiStageETLPipeline:
    """Pipeline ETL com múltiplas camadas de validação"""
    
    def __init__(self, glue_context, job_context):
        self.glue = glue_context
        self.job = job_context
        self.metrics = {}
        
    def execute(self):
        try:
            # Stage 1: Ingestão
            raw_data = self.ingest_raw_data()
            self.metrics['raw_records'] = raw_data.count()
            
            # Stage 2: Validação inicial
            validated_data = self.initial_validation(raw_data)
            self.metrics['validated_records'] = validated_data.count()
            
            # Stage 3: Transformação
            transformed_data = self.transform_data(validated_data)
            
            # Stage 4: Enriquecimento
            enriched_data = self.enrich_data(transformed_data)
            
            # Stage 5: Validação final
            final_data = self.final_validation(enriched_data)
            self.metrics['final_records'] = final_data.count()
            
            # Stage 6: Carga
            self.load_data(final_data)
            
            # Publicar métricas
            self.publish_metrics()
            
        except Exception as e:
            self.handle_failure(e)
            raise
    
    def initial_validation(self, data):
        """Validações básicas de schema e tipos"""
        rules = """
        Rules = [
            SchemaCompliance "expected_schema.json",
            ColumnDataType "id" = "INTEGER",
            ColumnExists "customer_id"
        ]
        """
        
        result = self.glue.evaluate_data_quality(data, rules)
        
        if result.score < 0.8:
            raise DataQualityException(f"Initial validation failed: {result.score}")
        
        return result.passed_records
    
    def transform_data(self, data):
        """Aplicar transformações de negócio"""
        # Exemplo de transformação complexa
        transformed = data.toDF()
        
        # Adicionar colunas calculadas
        transformed = transformed.withColumn(
            "total_value",
            col("quantity") * col("unit_price")
        )
        
        # Normalizar dados
        transformed = transformed.withColumn(
            "normalized_date",
            to_timestamp(col("date"), "yyyy-MM-dd")
        )
        
        return DynamicFrame.fromDF(transformed, self.glue, "transformed")
    
    def enrich_data(self, data):
        """Enriquecer com dados de dimensão"""
        # Join com tabelas de dimensão
        dim_customer = self.glue.create_dynamic_frame.from_catalog(
            database="dimensions",
            table_name="dim_customers"
        )
        
        enriched = Join.apply(
            frame1=data,
            frame2=dim_customer,
            keys1=["customer_id"],
            keys2=["id"],
            transformation_ctx="enrich"
        )
        
        return enriched
    
    def final_validation(self, data):
        """Validações de negócio complexas"""
        business_rules = """
        Rules = [
            ColumnValues "total_value" > 0,
            CustomSQL "SELECT COUNT(*) FROM dataset WHERE status='INVALID'" = 0,
            ReferentialIntegrity "product_id" references "products.id",
            ColumnCorrelation "discount" <= "total_value" * 0.5
        ]
        """
        
        result = self.glue.evaluate_data_quality(data, business_rules)
        self.metrics['quality_score'] = result.score
        
        return result.passed_records
```

---

## Melhores Práticas

### 1. **Organização do Data Catalog**

```python
# Nomenclatura padronizada
naming_convention = {
    "databases": "{environment}_{domain}_{layer}",
    "tables": "{source}_{entity}_{version}",
    "examples": {
        "database": "prod_sales_raw",
        "table": "sap_transactions_v2"
    }
}

# Tags para classificação
tagging_strategy = {
    "Environment": ["dev", "staging", "prod"],
    "DataClassification": ["public", "internal", "confidential", "restricted"],
    "Owner": "team-name",
    "CostCenter": "cc-12345",
    "Project": "project-name",
    "Compliance": ["LGPD", "GDPR", "SOC2"]
}
```

### 2. **Otimização de Performance**

```python
# Configurações otimizadas para Glue Jobs
job_configuration = {
    # Escolher o tipo correto de worker
    "WorkerType": "G.2X",  # Para jobs com muita memória
    "NumberOfWorkers": 10,  # Baseado no volume de dados
    
    # Configurações Spark
    "DefaultArguments": {
        # Particionamento otimizado
        "--spark.sql.adaptive.enabled": "true",
        "--spark.sql.adaptive.coalescePartitions.enabled": "true",
        
        # Gerenciamento de memória
        "--spark.executor.memory": "10g",
        "--spark.executor.memoryOverhead": "2g",
        "--spark.driver.memory": "10g",
        
        # Compressão
        "--spark.sql.parquet.compression.codec": "snappy",
        
        # Paralelismo
        "--spark.default.parallelism": "200",
        "--spark.sql.shuffle.partitions": "200"
    }
}

# Uso de pushdown predicates
def optimize_reading(glue_context):
    # Usar push_down_predicate para filtrar na origem
    datasource = glue_context.create_dynamic_frame.from_catalog(
        database="sales_db",
        table_name="large_table",
        push_down_predicate="year=2024 and month=1"
    )
    return datasource
```

### 3. **Gestão de Custos**

```python
# Estratégias para reduzir custos
cost_optimization = {
    "job_bookmarks": {
        "enabled": True,
        "description": "Processa apenas dados novos"
    },
    
    "auto_scaling": {
        "enabled": True,
        "min_workers": 2,
        "max_workers": 10
    },
    
    "job_timeout": 120,  # Minutos
    
    "development": {
        "use_notebooks": True,
        "interactive_sessions": True,
        "sample_data": "1%"  # Desenvolver com amostra
    }
}
```

### 4. **Tratamento de Erros**

```python
class RobustETLJob:
    """Job ETL com tratamento robusto de erros"""
    
    def __init__(self):
        self.max_retries = 3
        self.backoff_base = 2
        
    def run_with_retry(self, func, *args, **kwargs):
        """Implementa retry com backoff exponencial"""
        for attempt in range(self.max_retries):
            try:
                return func(*args, **kwargs)
            except Exception as e:
                if attempt == self.max_retries - 1:
                    # Última tentativa falhou
                    self.log_error(e)
                    self.send_to_dlq(args[0])  # Enviar para Dead Letter Queue
                    raise
                
                # Calcular tempo de espera
                wait_time = self.backoff_base ** attempt
                time.sleep(wait_time)
                self.log_retry(attempt, e)
    
    def process_with_error_handling(self, frame):
        """Processa frame com isolamento de erros"""
        # Separar registros bons e ruins
        try:
            # Tentar processar todos
            processed = self.transform_all(frame)
            return processed
        except Exception:
            # Processar registro por registro se falhar
            good_records = []
            bad_records = []
            
            for record in frame.toDF().collect():
                try:
                    processed_record = self.transform_single(record)
                    good_records.append(processed_record)
                except Exception as e:
                    bad_records.append({
                        "record": record,
                        "error": str(e),
                        "timestamp": datetime.now()
                    })
            
            # Salvar registros ruins para análise
            self.save_bad_records(bad_records)
            
            return self.glue.createDataFrame(good_records)
```

### 5. **Segurança e Conformidade**

```python
# Configurações de segurança
security_config = {
    "Encryption": {
        "S3Encryption": {
            "S3EncryptionMode": "SSE-KMS",
            "KmsKeyArn": "arn:aws:kms:region:account:key/key-id"
        },
        "CloudWatchEncryption": {
            "CloudWatchEncryptionMode": "SSE-KMS",
            "KmsKeyArn": "arn:aws:kms:region:account:key/key-id"
        },
        "JobBookmarksEncryption": {
            "JobBookmarksEncryptionMode": "CSE-KMS",
            "KmsKeyArn": "arn:aws:kms:region:account:key/key-id"
        }
    },
    
    "SecurityConfiguration": "glue-security-config",
    
    # VPC para isolamento de rede
    "Connections": ["vpc-connection"],
    
    # IAM Role com princípio do menor privilégio
    "Role": "arn:aws:iam::account:role/GlueJobRole"
}

# Mascaramento de dados sensíveis
def mask_sensitive_data(frame):
    """Mascara dados PII antes de processar"""
    from pyspark.sql.functions import regexp_replace, sha2
    
    df = frame.toDF()
    
    # Mascarar CPF
    df = df.withColumn(
        "cpf",
        regexp_replace("cpf", r"(\d{3})(\d{3})(\d{3})(\d{2})", "***.$2.***-**")
    )
    
    # Hash de email
    df = df.withColumn(
        "email_hash",
        sha2("email", 256)
    ).drop("email")
    
    return DynamicFrame.fromDF(df, glue_context, "masked")
```

---

## Casos de Uso

### Caso 1: **E-commerce - Pipeline de Análise de Vendas**

```python
# Pipeline completo para análise de vendas
class EcommerceSalesPipeline:
    def __init__(self, glue_context):
        self.glue = glue_context
        
    def daily_sales_analysis(self):
        # 1. Coletar dados de múltiplas fontes
        orders = self.get_orders()
        products = self.get_products()
        customers = self.get_customers()
        
        # 2. Juntar e enriquecer
        sales_data = self.enrich_sales_data(orders, products, customers)
        
        # 3. Calcular métricas
        metrics = self.calculate_metrics(sales_data)
        
        # 4. Criar agregações para dashboard
        daily_summary = self.create_daily_summary(metrics)
        weekly_trends = self.create_weekly_trends(metrics)
        product_performance = self.analyze_product_performance(metrics)
        
        # 5. Salvar para consumo
        self.save_to_warehouse(daily_summary, "daily_sales_summary")
        self.save_to_warehouse(weekly_trends, "weekly_sales_trends")
        self.save_to_warehouse(product_performance, "product_performance")
        
    def calculate_metrics(self, sales_data):
        df = sales_data.toDF()
        
        # Métricas de vendas
        df = df.withColumn("revenue", col("quantity") * col("unit_price"))
        df = df.withColumn("profit", col("revenue") - col("cost"))
        df = df.withColumn("margin", (col("profit") / col("revenue")) * 100)
        
        # Segmentação de clientes
        df = df.withColumn(
            "customer_segment",
            when(col("total_purchases") > 10000, "Premium")
            .when(col("total_purchases") > 5000, "Gold")
            .when(col("total_purchases") > 1000, "Silver")
            .otherwise("Bronze")
        )
        
        return DynamicFrame.fromDF(df, self.glue, "metrics")
```

### Caso 2: **Banco - Detecção de Fraude em Tempo Real**

```python
# Pipeline de detecção de fraude
class FraudDetectionPipeline:
    def __init__(self, glue_context):
        self.glue = glue_context
        
    def setup_streaming_job(self):
        # Configurar fonte Kinesis
        kinesis_options = {
            "streamARN": "arn:aws:kinesis:region:account:stream/transactions",
            "startingPosition": "latest",
            "inferSchema": "true",
            "classification": "json"
        }
        
        # Criar streaming frame
        streaming_frame = self.glue.create_data_frame.from_options(
            connection_type="kinesis",
            connection_options=kinesis_options
        )
        
        # Processar micro-batches
        query = streaming_frame.writeStream \
            .foreachBatch(lambda df, epoch_id: self.detect_fraud(df, epoch_id)) \
            .outputMode("append") \
            .trigger(processingTime='10 seconds') \
            .start()
        
        return query
    
    def detect_fraud(self, transaction_batch, batch_id):
        # Regras de detecção
        suspicious = transaction_batch.filter(
            (col("amount") > 10000) |
            (col("country") != col("card_country")) |
            (col("transactions_last_hour") > 5)
        )
        
        if suspicious.count() > 0:
            # Enviar alerta
            self.send_fraud_alert(suspicious)
            
            # Salvar para investigação
            suspicious.write \
                .mode("append") \
                .parquet(f"s3://fraud-detection/suspicious/{batch_id}/")
```

### Caso 3: **Healthcare - Pipeline de Dados Clínicos com LGPD**

```python
class HealthcareCompliancePipeline:
    """Pipeline com conformidade LGPD/HIPAA"""
    
    def process_clinical_data(self, patient_data):
        # 1. Verificar consentimento
        consented_data = self.verify_consent(patient_data)
        
        # 2. Anonimizar dados sensíveis
        anonymized = self.anonymize_pii(consented_data)
        
        # 3. Aplicar regras de qualidade médica
        validated = self.validate_clinical_rules(anonymized)
        
        # 4. Segregar por nível de acesso
        public_data = self.extract_public_metrics(validated)
        research_data = self.prepare_research_data(validated)
        clinical_data = self.prepare_clinical_data(validated)
        
        # 5. Salvar com encriptação apropriada
        self.save_with_encryption(public_data, "public")
        self.save_with_encryption(research_data, "research")
        self.save_with_encryption(clinical_data, "clinical")
    
    def anonymize_pii(self, data):
        df = data.toDF()
        
        # Técnicas de anonimização
        df = df.withColumn("patient_id", sha2("patient_id", 256))
        df = df.withColumn("birth_date", 
            concat(year("birth_date"), lit("-01-01")))
        df = df.drop("name", "cpf", "address", "phone")
        
        # Generalização de dados
        df = df.withColumn("age_group",
            when(col("age") < 18, "0-17")
            .when(col("age") < 30, "18-29")
            .when(col("age") < 50, "30-49")
            .when(col("age") < 70, "50-69")
            .otherwise("70+")
        ).drop("age")
        
        return DynamicFrame.fromDF(df, self.glue, "anonymized")
```

---

## Conclusão

O AWS Glue é uma plataforma poderosa e completa para ETL e governança de dados que oferece:

✅ **Capacidades ETL Completas**: Do desenvolvimento visual ao código customizado

✅ **Governança Robusta**: Catalogação, linhagem e qualidade de dados

✅ **Escalabilidade**: Processa de gigabytes a petabytes sem gerenciar infraestrutura

✅ **Integração**: Conecta-se nativamente com todo ecossistema AWS

✅ **Flexibilidade**: Suporta batch e streaming, SQL e programação

### Quando Escolher AWS Glue

**Ideal para:**

- Organizações já investidas no ecossistema AWS
- Projetos que precisam de governança forte
- Pipelines ETL complexos com múltiplas fontes
- Requisitos de conformidade (LGPD, GDPR, etc.)
- Times que preferem serverless

**Considerar alternativas se:**

- Precisa de processamento em tempo real de ultra baixa latência
- Orçamento muito limitado para volumes pequenos
- Requisitos de processamento muito específicos não cobertos pelo Spark

### Recursos Adicionais

- [Documentação Oficial AWS Glue](https://docs.aws.amazon.com/glue/)
- [Melhores Práticas AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/best-practices.html)
- [Workshops AWS Glue](https://catalog.us-east-1.prod.workshops.aws/workshops/)
