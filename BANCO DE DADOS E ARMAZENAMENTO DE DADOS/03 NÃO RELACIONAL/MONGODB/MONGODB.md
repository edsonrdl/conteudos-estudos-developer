### 1. **Inserção de Documentos (DML - Data Manipulation Language)**

#### Usando Mongo Shell ou Drivers (Node.js, Python, etc.):

- **Inserir um Documento:**
    
    javascript
    
    Copiar
    
    `db.collection.insertOne({ name: "João", age: 30, city: "São Paulo" });`
    
- **Inserir Múltiplos Documentos:**
    
    javascript
    
    Copiar
    
    `db.collection.insertMany([   { name: "Maria", age: 25 },   { name: "Carlos", age: 28, city: "Rio de Janeiro" } ]);`
    

#### No MongoDB Compass:

- **Inserir um Documento:**
    
    1. Selecione a coleção desejada.
        
    2. Clique em **Add Data** > **Insert Document**.
        
    3. Insira o documento JSON, por exemplo:
        
        json
        
        Copiar
        
        `{ "name": "João", "age": 30, "city": "São Paulo" }`
        
- **Inserir Múltiplos Documentos:** Você pode inserir documentos adicionais um por um clicando em "Insert Document" novamente.
    

---

### 2. **Consulta de Documentos (DQL - Data Query Language)**

#### Usando Mongo Shell ou Drivers:

- **Consulta Simples (Todos os Documentos):**
    
    javascript
    
    Copiar
    
    `db.collection.find();`
    
- **Consulta com Filtro:**
    
    javascript
    
    Copiar
    
    `db.collection.find({ age: { $gt: 25 } });`
    
- **Consulta de um Documento Específico:**
    
    javascript
    
    Copiar
    
    `db.collection.findOne({ name: "João" });`
    
- **Projeção (Selecionar Campos Específicos):**
    
    javascript
    
    Copiar
    
    `db.collection.find({ city: "São Paulo" }, { name: 1, age: 1 });`
    

#### No MongoDB Compass:

- **Consulta Simples (Todos os Documentos):** Na barra de pesquisa da coleção, insira `{}` para buscar todos os documentos.
    
- **Consulta com Filtro:** Insira a query no campo de filtro, como:
    
    json
    
    Copiar
    
    `{ "age": { "$gt": 25 } }`
    
- **Projeção (Selecionar Campos):** Você pode especificar campos para retorno na seção de projeção, como:
    
    json
    
    Copiar
    
    `{ "name": 1, "age": 1 }`
    

---

### 3. **Atualização de Documentos (DML - Data Manipulation Language)**

#### Usando Mongo Shell ou Drivers:

- **Atualizar um Documento (Primeiro Encontrado):**
    
    javascript
    
    Copiar
    
    `db.collection.updateOne({ name: "João" }, { $set: { age: 31 } });`
    
- **Atualizar Múltiplos Documentos:**
    
    javascript
    
    Copiar
    
    `db.collection.updateMany({ city: "São Paulo" }, { $set: { city: "Santos" } });`
    
- **Incremento de Valor:**
    
    javascript
    
    Copiar
    
    `db.collection.updateOne({ name: "Carlos" }, { $inc: { age: 1 } });`
    

#### No MongoDB Compass:

- **Atualizar um Documento:**
    
    1. Selecione o documento que deseja editar.
        
    2. Clique em **Edit** e modifique os valores diretamente.
        
- **Operações de Atualização mais Complexas:** No editor de consultas, você pode usar operadores como `$set` e `$inc` para realizar atualizações mais específicas.
    

---

### 4. **Remoção de Documentos (DML - Data Manipulation Language)**

#### Usando Mongo Shell ou Drivers:

- **Remover um Documento:**
    
    javascript
    
    Copiar
    
    `db.collection.deleteOne({ name: "João" });`
    
- **Remover Múltiplos Documentos:**
    
    javascript
    
    Copiar
    
    `db.collection.deleteMany({ age: { $lt: 30 } });`
    

#### No MongoDB Compass:

- **Remover um Documento:** Selecione o documento e clique em **Delete Document**.
    
- **Remover Documentos com Filtro:** Você pode aplicar um filtro na barra de consulta para selecionar documentos específicos a serem removidos.
    

---

### 5. **Definição de Coleções e Índices (DDL - Data Definition Language)**

#### Usando Mongo Shell ou Drivers:

- **Criar uma Nova Coleção:**
    
    javascript
    
    Copiar
    
    `db.createCollection("novaColecao");`
    
- **Remover uma Coleção:**
    
    javascript
    
    Copiar
    
    `db.novaColecao.drop();`
    
- **Criar um Índice:**
    
    javascript
    
    Copiar
    
    `db.collection.createIndex({ name: 1 });`
    

#### No MongoDB Compass:

- **Criar uma Coleção:**
    
    1. Selecione o banco de dados.
        
    2. Clique em **Create Collection**.
        
- **Remover uma Coleção:** Clique com o botão direito na coleção e selecione **Drop Collection**.
    
- **Criar Índices:** Selecione a coleção e vá para a guia **Indexes**, depois clique em **Create Index**.
    

---

### 6. **Operações de Agregação (DQL - Data Query Language)**

#### Usando Mongo Shell ou Drivers:

- **Pipeline de Agregação:**
    
    javascript
    
    Copiar
    
    `db.collection.aggregate([   { $match: { age: { $gte: 30 } } },   { $group: { _id: "$city", total: { $sum: 1 } } } ]);`
    

#### No MongoDB Compass:

- **Operações de Agregação:**
    
    1. Selecione a coleção.
        
    2. Vá para a guia **Aggregation**.
        
    3. Use a interface visual para montar e executar o pipeline de agregação.
        

---

### 7. **Transações (DTL - Data Transaction Language)**

#### Usando Mongo Shell ou Drivers:

- **Uso de Transações:**
    
    javascript
    
    Copiar
    
    `const session = db.getMongo().startSession(); session.startTransaction(); try {   db.collection.insertOne({ name: "Ana" }, { session });   db.collection.updateOne({ name: "Carlos" }, { $set: { age: 35 } }, { session });   session.commitTransaction(); } catch (error) {   session.abortTransaction(); } finally {   session.endSession(); }`
    

#### No MongoDB Compass:

- As transações são principalmente gerenciadas via shell ou drivers programáticos, pois o Compass oferece suporte limitado para transações.
    

---

### 8. **Operações em Massa (Bulk Operations)**

#### Usando Mongo Shell ou Drivers:

- **Operações em Massa:**
    
    javascript
    
    Copiar
    
    `const bulk = db.collection.initializeUnorderedBulkOp(); bulk.insert({ name: "Pedro", age: 29 }); bulk.find({ name: "Maria" }).updateOne({ $set: { city: "Recife" } }); bulk.execute();`
    

#### No MongoDB Compass:

- As operações em massa não são diretamente suportadas no Compass, mas podem ser realizadas via shell ou usando scripts externos.
    

---

### Resumo das Categorias e Operações:

- **DML (Manipulação de Dados):** `insertOne`, `insertMany`, `updateOne`, `updateMany`, `deleteOne`, `deleteMany`.
    
- **DDL (Definição de Dados):** `createCollection`, `drop`, `createIndex`.
    
- **DQL (Consulta de Dados):** `find`, `findOne`, `aggregate`, `countDocuments`.
    
- **DTL (Transações):** `startSession`, `startTransaction`, `commitTransaction`, `abortTransaction`.