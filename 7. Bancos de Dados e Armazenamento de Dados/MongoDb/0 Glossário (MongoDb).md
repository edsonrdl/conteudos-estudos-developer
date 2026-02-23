# **1. MongoDB – Fundamentos**

## **1. Introdução ao MongoDB**

### 1.1. O que é MongoDB

### 1.2. Características principais

- Banco NoSQL orientado a documentos
    
- Schema flexível
    
- Escalabilidade horizontal
    

### 1.3. Casos de uso comuns

### 1.4. Quando NÃO usar MongoDB

---

## **2. Conceitos Fundamentais**

### 2.1. Documento

- Estrutura BSON
    
- Tipos de dados BSON
    

### 2.2. Coleção

### 2.3. Banco de Dados

### 2.4. Namespace

### 2.5. `_id` e identificadores

---

## **3. Modelagem de Dados no MongoDB**

### 3.1. Modelagem orientada a documentos

### 3.2. Embedded Documents (documentos aninhados)

### 3.3. Referências entre documentos

### 3.4. Normalização vs Desnormalização

### 3.5. Modelagem para leitura vs escrita

### 3.6. Modelagem baseada em casos de uso

---

## **4. Tipos de Dados BSON**

### 4.1. Tipos básicos

- String
    
- Number (`int`, `long`, `double`, `decimal128`)
    
- Boolean
    
- Date
    

### 4.2. Tipos complexos

- Object
    
- Array
    
- ObjectId
    

### 4.3. Tipos especiais

- Binary
    
- UUID
    
- Regex
    
- Timestamp
    

---

## **5. Operações CRUD**

### 5.1. Inserção de Dados

#### 5.1.1. `insertOne`

#### 5.1.2. `insertMany`

### 5.2. Leitura de Dados

#### 5.2.1. `find`

#### 5.2.2. Filtros de consulta

#### 5.2.3. Projeções

### 5.3. Atualização de Dados

#### 5.3.1. `updateOne`

#### 5.3.2. `updateMany`

#### 5.3.3. Operadores de atualização (`$set`, `$inc`, `$push`)

### 5.4. Remoção de Dados

#### 5.4.1. `deleteOne`

#### 5.4.2. `deleteMany`

---

## **6. Consultas Avançadas**

### 6.1. Operadores de Consulta

- `$eq`, `$ne`
    
- `$gt`, `$gte`, `$lt`, `$lte`
    
- `$in`, `$nin`
    

### 6.2. Operadores Lógicos

- `$and`
    
- `$or`
    
- `$not`
    
- `$nor`
    

### 6.3. Consultas em Arrays

- `$elemMatch`
    
- `$all`
    
- `$size`
    

### 6.4. Consultas em Documentos Aninhados

---

## **7. Índices**

### 7.1. O que são índices

### 7.2. Tipos de Índices

#### 7.2.1. Índice simples

#### 7.2.2. Índice composto

#### 7.2.3. Índice único

#### 7.2.4. Índice TTL

#### 7.2.5. Índice Text

#### 7.2.6. Índice Geoespacial

### 7.3. Criação e remoção de índices

### 7.4. Análise de performance (`explain`)

---

## **8. Aggregation Framework**

### 8.1. Conceito de agregação

### 8.2. Pipeline de agregação

### 8.3. Estágios principais

- `$match`
    
- `$project`
    
- `$group`
    
- `$sort`
    
- `$limit`
    
- `$lookup`
    
- `$unwind`
    

### 8.4. Agregações com arrays

### 8.5. Performance em agregações

---

## **9. Transações**

### 9.1. Conceito de transações no MongoDB

### 9.2. Transações multi-documento

### 9.3. Sessões (`sessions`)

### 9.4. Limitações e custos de transações

---

## **10. Controle de Concorrência**

### 10.1. Atomicidade de operações

### 10.2. Write Conflicts

### 10.3. Locking interno do MongoDB

---

## **11. Replicação**

### 11.1. Replica Set

- Primary
    
- Secondary
    
- Arbiter
    

### 11.2. Failover automático

### 11.3. Read Preference

### 11.4. Write Concern

---

## **12. Sharding (Escalabilidade Horizontal)**

### 12.1. Conceito de Sharding

### 12.2. Componentes

- Shard
    
- Config Server
    
- Mongos
    

### 12.3. Shard Key

- Escolha da chave
    
- Problemas comuns
    

### 12.4. Balanceamento de dados

---

## **13. Consistência e Disponibilidade**

### 13.1. Modelo de consistência do MongoDB

### 13.2. Eventual Consistency

### 13.3. Relação com o Teorema CAP

---

## **14. Segurança**

### 14.1. Autenticação

- SCRAM
    
- X.509
    

### 14.2. Autorização

- Roles
    
- Privilégios
    

### 14.3. Criptografia

- Em repouso
    
- Em trânsito
    

### 14.4. Auditoria

---

## **15. Backup e Restore**

### 15.1. `mongodump`

### 15.2. `mongorestore`

### 15.3. Backup consistente em Replica Sets

---

## **16. Performance e Otimização**

### 16.1. Análise de queries

### 16.2. Uso eficiente de índices

### 16.3. Modelagem para performance

### 16.4. Monitoramento de uso de memória e CPU

---

## **17. Logs e Monitoramento**

### 17.1. Logs do MongoDB

### 17.2. Métricas importantes

### 17.3. Ferramentas de monitoramento

---

## **18. MongoDB Atlas (conceitual)**

### 18.1. O que é MongoDB Atlas

### 18.2. Clusters

### 18.3. Backup e monitoramento gerenciado

---

## **19. Versionamento e Evolução de Schema**

### 19.1. Schema flexible vs Schema controlado

### 19.2. Migração de documentos

### 19.3. Estratégias de versionamento

---

## **20. Boas Práticas**

### 20.1. Padrões de nomenclatura

### 20.2. Tamanho de documentos

### 20.3. Anti-patterns comuns

### 20.4. Estratégias de modelagem

---

## **21. Integração com Aplicações (conceitual)**

### 21.1. Drivers oficiais

### 21.2. Conexões e pooling

### 21.3. Separação de responsabilidades

> _Integrações com frameworks específicos ficam fora deste escopo._