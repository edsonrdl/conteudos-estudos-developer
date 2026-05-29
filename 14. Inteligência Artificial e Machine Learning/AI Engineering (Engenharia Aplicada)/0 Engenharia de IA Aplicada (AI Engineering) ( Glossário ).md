# 📚 Guia de Estudos: Engenharia de IA Aplicada (AI Engineering) — 2026

#ia #agentes #rag #llmops #arquitetura #finetuning #embedding #cache #retrieval

> [!info] Visão Geral A Engenharia de IA Aplicada (AI Engineering) não foca em treinar modelos do zero (matemática pura), mas sim na orquestração, integração e otimização de sistemas distribuídos que consomem Modelos de Linguagem (LLMs). Este guia mapeia desde a dominação do contexto (Prompting e RAG) até a construção de fluxos agênticos autônomos e a infraestrutura necessária para monitorar essas decisões em produção (LLMOps). **Atualizado em 2026 com os tópicos mais modernos do ecossistema.**

---

## 🧭 Domínio 0: Guia de Orientação — "O Que Faz Cada Coisa?"

> Este domínio foi adicionado para eliminar a confusão mais comum: **"Qual ferramenta uso para quê?"**. Leia isto antes de qualquer outro domínio.

### 0.1 O Mapa Mental do Sistema RAG

Um sistema de IA em produção pode ser dividido em **7 responsabilidades distintas**. Cada categoria de ferramenta resolve exatamente UMA dessas responsabilidades:

```
[DADOS BRUTOS] → Extração → Chunking → Embedding → [BANCO VETORIAL]
                                                           ↓
[USUÁRIO] → Pergunta → Framework → LLM ← Retrieval ← [BUSCA]
                                    ↓
                              [RESPOSTA]
                                    ↓
                             [AVALIAÇÃO / OBSERVABILIDADE]
```

---

### 0.2 As 7 Categorias e Suas Diferenças

#### 🤖 Categoria 1: LLMs (Os Cérebros)

**O que fazem:** Recebem texto → pensam → devolvem texto. São o "motor" de raciocínio.

**Diferença entre eles:**

|Modelo|Ponto Forte|Quando Usar|
|---|---|---|
|GPT-4o / o3|Raciocínio geral, integração OpenAI|Tarefas complexas, coding|
|Claude (Anthropic)|Contexto longo (200K), seguir instruções|Documentos grandes, análise|
|Gemini 1.5/2.0 Pro|Contexto massivo (1M tokens), multimodal|Vídeos, áudio, docs enormes|
|Llama 4 / DeepSeek|Open-source, roda localmente|Privacidade, custo zero de API|
|Mistral / Qwen 3|Eficiência, multilíngue|Aplicações europeias/asiáticas|
|Phi-4 / Gemma 3|Modelos pequenos de alta qualidade|Edge, mobile, IoT|
|Cohere|Especializado em RAG e busca corporativa|Sistemas RAG enterprise|

**Regra de ouro:** LLMs **não sabem** o que aconteceu depois do treinamento deles. Por isso precisam do RAG.

---

#### 🔗 Categoria 2: Frameworks de Orquestração (O Sistema Nervoso)

**O que fazem:** Conectam o LLM com bancos de dados, APIs, ferramentas e outros agentes. São o "esqueleto" que faz tudo conversar.

**Diferença entre eles:**

|Framework|Nível|Para quê|
|---|---|---|
|LangChain|Iniciante/Intermediário|Prototipagem rápida, muitas integrações prontas|
|LlamaIndex|Intermediário|Foco específico em RAG e indexação de documentos|
|Haystack|Intermediário/Avançado|Pipelines de busca corporativa, muito customizável|
|Txtai|Avançado|Tudo-em-um: embedding + busca + LLM em biblioteca única|
|LangGraph|Avançado/Produção|Agentes complexos com estado, loops, paralelismo|
|CrewAI / AutoGen|Avançado|Múltiplos agentes colaborando com papéis definidos|

**Regra de ouro:** Comece com LangChain ou LlamaIndex. Vá para LangGraph quando precisar de controle fino em produção.

---

#### 🗄️ Categoria 3: Bancos Vetoriais (A Memória de Longo Prazo)

**O que fazem:** Guardam embeddings (vetores numéricos) e permitem busca por similaridade semântica ("o que é mais parecido com isso?").

**Diferença entre eles:**

|Banco|Ponto Forte|Quando Usar|
|---|---|---|
|Qdrant|Performance, filtros avançados, open-source|Produção geral, preferência atual do mercado|
|Pinecone|Totalmente gerenciado (serverless)|Quando não quer gerenciar infraestrutura|
|Weaviate|Busca híbrida nativa, GraphQL|Projetos com busca complexa|
|Milvus|Escala massiva (bilhões de vetores)|Big data, grandes empresas|
|Chroma|Simplicidade, desenvolvimento local|Prototipagem, projetos pequenos|
|PGVector|Já usa PostgreSQL? Adicione vetores|Infraestrutura existente em Postgres|
|Cassandra / OpenSearch|Uso misto com dados já existentes|Integração com stack legada|

**Regra de ouro:** Para aprender use Chroma (local). Para produção use Qdrant ou Pinecone.

---

#### 🕷️ Categoria 4: Extração de Dados (Os Coletores)

**O que fazem:** Pegam dados brutos do mundo (sites, PDFs, documentos) e os transformam em texto limpo para o RAG consumir.

**Diferença entre eles:**

|Ferramenta|Especialidade|Quando Usar|
|---|---|---|
|Crawl4AI|Web scraping otimizado para IA, respeita robots.txt|Coletar sites em escala para RAG|
|FireCrawl|API de web crawling gerenciada, converte em Markdown|Quando não quer gerenciar o crawler|
|Scrape GraphAI|Extração estruturada com LLM + grafo de dados|Dados complexos de páginas dinâmicas|
|MegaParser|Parser de documentos (PDF, DOCX, PPTX) de alta fidelidade|Documentos empresariais variados|
|Docling (IBM)|Parsing avançado de PDFs com preservação de layout|PDFs científicos, relatórios com tabelas|
|LlamaParse|Parser de PDFs via API, integrado com LlamaIndex|Quando já usa LlamaIndex|
|Extract Thinker|Extração estruturada de documentos com LLM|Formulários, contratos, NFs|

**Regra de ouro:** O dado de entrada define a qualidade do RAG inteiro. "Garbage in, garbage out." Escolha o parser certo para seu tipo de documento.

---

#### 🚀 Categoria 5: Acesso a LLMs Open-Source (As Plataformas)

**O que fazem:** Hospedam e servem modelos open-source para você consumir via API, sem precisar de GPU própria.

**Diferença entre eles:**

|Plataforma|Diferencial|Quando Usar|
|---|---|---|
|Hugging Face|Hub de modelos, datasets, espaços de demo|Explorar e baixar qualquer modelo|
|Ollama|Roda modelos localmente em 1 comando|Desenvolvimento local, privacidade total|
|Groq|Velocidade extrema (LPU), baixíssima latência|Quando latência é crítica (<100ms)|
|Together AI|Variedade de modelos open-source via API|Alternativa barata à OpenAI|

**Regra de ouro:** Desenvolvimento → Ollama (local, grátis). Produção com velocidade → Groq. Produção com variedade → Together AI.

---

#### 📐 Categoria 6: Text Embeddings (O Tradutor para Vetores)

**O que fazem:** Convertem texto em vetores numéricos (listas de números) que representam o _significado_ do texto. Dois textos com significado parecido terão vetores próximos.

**Diferença entre eles:**

|Modelo|Diferencial|Quando Usar|
|---|---|---|
|OpenAI text-embedding-3-large|Alta qualidade, pago|Produção quando já usa OpenAI|
|Voyage AI (voyage-3)|Melhor qualidade geral, pago|RAG de alta precisão|
|SBERT (sentence-transformers)|Open-source, roda local, vários modelos|Projetos com restrição de dados|
|Nomic embed-text|Open-source, contexto de 8K tokens|Documentos longos, gratuito|
|Google (text-embedding)|Integrado ao Gemini/Vertex AI|Stack Google Cloud|
|Cohere embed|Multilíngue, busca semântica|Documentos em múltiplos idiomas|

**Regra de ouro:** Embedding é onde qualidade importa mais que custo. Um embedding ruim quebra o RAG inteiro.

---

#### ✅ Categoria 7: Avaliação (O Controle de Qualidade)

**O que fazem:** Medem se o sistema de IA está respondendo bem, de forma automatizada e sistemática.

**Diferença entre eles:**

|Ferramenta|Especialidade|Quando Usar|
|---|---|---|
|Ragas|Métricas específicas de RAG (faithfulness, recall)|Avaliar pipeline RAG completo|
|DeepEval|Avaliação geral de LLMs com LLM-as-Judge|Testes de qualidade de outputs|
|Giskard|Detecção de viés, alucinações e vulnerabilidades|Auditoria de segurança e compliance|
|inspect_ai|Avaliação de capacidades e segurança|Red teaming, benchmarks de segurança|
|PromptFoo|Testes de regressão de prompts no CI/CD|Garantir que mudanças não quebram qualidade|
|LangSmith|Observabilidade + avaliação integrada ao LangChain|Stack LangChain completo|

**Regra de ouro:** Sem avaliação sistemática, você não sabe se o sistema melhorou ou piorou ao mudar algo.

---

### 0.3 Como as Categorias se Conectam na Prática

```
Exemplo: "Construir um chatbot que responde perguntas sobre contratos da empresa"

1. EXTRAÇÃO    → Docling lê os PDFs dos contratos
2. EMBEDDING   → Voyage AI converte os textos em vetores
3. BANCO VET.  → Qdrant armazena os vetores
4. FRAMEWORK   → LlamaIndex orquestra o pipeline RAG
5. LLM         → Claude responde com base nos contratos recuperados
6. ACESSO OSS  → Ollama para testes locais antes de subir para Claude
7. AVALIAÇÃO   → Ragas mede se as respostas são fiéis aos contratos
```

---

## 1 Domínio: Fundamentos de LLM e Unidades de Controle

### 1.1 Contexto, Tokens e Inferência

- **1.1.1. Gestão de Context Window**
    
    - 1.1.1.1. **Tokenização:** Como o modelo quebra palavras (BPE - Byte Pair Encoding) e como isso afeta o custo financeiro da API (input/output tokens).
    - 1.1.1.2. **Sliding Window e Truncation:** Estratégias para lidar com históricos de conversas que ultrapassam o limite do modelo sem perder o fio da meada.
    - 1.1.1.3. **Contextos Grandes (Long Context):** Modelos modernos como Gemini 1.5/2.0 Pro (1M tokens), Claude 3.x (200K tokens) e GPT-4o suportam janelas de contexto massivas. O desafio mudou: não é mais "como caber o documento" mas sim **"Lost in the Middle"** — modelos tendem a ignorar informações no centro de contextos longos. Estratégias de posicionamento (colocar instruções críticas no início e no final) e técnicas de compressão como **LLMLingua** (compressão de prompt sem perda semântica) tornam-se essenciais.
    - 1.1.1.4. **Prompt Caching (Anthropic/OpenAI):** Tanto a Anthropic quanto a OpenAI oferecem cache nativo de prefixos de prompt em nível de API. Prefixos estáticos (system prompts, documentação base) são cacheados no servidor, reduzindo custo e latência em até 90% em chamadas repetidas.
- **1.1.2. Tuning de Parâmetros de API**
    
    - 1.1.2.1. **Temperature & Top-P:** Controle matemático da criatividade vs. determinismo. Para agentes de software e geradores de código, a _Temperature_ deve ser sempre próxima de `0.0` para evitar alucinações de sintaxe.
    - 1.1.2.2. **Semantic Caching:** Uso de ferramentas como Redis/RedisVL para fazer cache de respostas baseadas em similaridade semântica (se dois usuários fazem a mesma pergunta com palavras diferentes, o sistema não paga o processamento do LLM novamente).
    - 1.1.2.3. **Estratégias de Cache Avançadas:** Cache em múltiplas camadas — (1) Cache Exato (hash de string idêntica), (2) Cache Semântico (similaridade de embedding com threshold configurável, ex: cosine > 0.95), (3) Cache de Prefixo de API (nativo da plataforma). Bibliotecas como **GPTCache** abstraem essas três camadas em pipeline único.
- **1.1.3. Modelos de Raciocínio (Thinking/Reasoning Models)**
    
    - 1.1.3.1. **O Paradigma Shift:** Modelos como OpenAI o1/o3, Claude 3.7 Sonnet (Extended Thinking) e Gemini 2.0 Flash Thinking introduziram um modo de inferência onde o modelo realiza uma "cadeia de pensamento interna" (scratchpad privado) antes de responder. Isso aumenta drasticamente a precisão em tarefas de lógica, matemática e código.
    - 1.1.3.2. **Tradeoffs:** O custo de tokens de raciocínio é alto (pode ser 5-10x o custo de uma resposta normal). A estratégia de engenharia é usar modelos de raciocínio apenas para tarefas que exigem planejamento complexo, e modelos menores para tarefas rotineiras (ver LLM Routing no domínio 7).
    - 1.1.3.3. **Budget Tokens:** APIs modernas permitem configurar um orçamento máximo de tokens de raciocínio interno, balanceando qualidade vs custo.

### 1.2 Engenharia de Prompts Avançada

- **1.2.1. Técnicas de Raciocínio (Reasoning)**
    
    - 1.2.1.1. **Few-shot Prompting:** Fornecer exemplos de input/output no próprio prompt para padronizar a resposta.
    - 1.2.1.2. **Chain-of-Thought (CoT):** Forçar o modelo a gerar o passo a passo lógico ("Pense passo a passo") antes de dar a resposta final, reduzindo erros matemáticos e lógicos.
    - 1.2.1.3. **Self-Consistency:** Pedir ao modelo para gerar 3 a 5 respostas de raciocínio em paralelo e eleger a mais consistente (votação majoritária).
    - 1.2.1.4. **Tree of Thought (ToT):** Extensão do CoT que explora múltiplos caminhos de raciocínio em estrutura de árvore, permitindo backtracking. Útil para problemas de otimização e puzzles lógicos.
    - 1.2.1.5. **Meta-Prompting:** Usar um LLM para gerar e otimizar o próprio prompt que será enviado a outro LLM, substituindo a engenharia manual de prompts por um processo automatizado.
- **1.2.2. Structured Outputs (O Pilar da Integração)**
    
    - 1.2.2.1. **Mecânica:** A API do LLM não deve retornar "texto", mas sim objetos JSON estritos.
    - 1.2.2.2. **Validação:** Uso de bibliotecas de schema (Pydantic no Python ou Zod no TypeScript) para garantir que o LLM devolva as chaves exatas que o seu Back-end precisa processar.
    - 1.2.2.3. **Constrained Decoding:** Bibliotecas como **Outlines** e **Guidance** forçam a geração token-a-token a seguir uma gramática formal ou JSON Schema, tornando os Structured Outputs determinísticos (sem fallback para parsear texto).

---

## 2 Domínio: Retrieval-Augmented Generation (RAG) Avançado

### 2.1 Ingestão e Processamento (Pipeline de Dados)

- **2.1.1. Chunking Strategies — O Segredo do RAG**
    
    - 2.1.1.1. **Fixed-size vs Recursive Chunking:** Dividir PDFs/Textos por número de caracteres ou respeitando os parágrafos e headers do documento.
    - 2.1.1.2. **Semantic Chunking:** Usar IA para agrupar o texto com base no significado das frases, e não apenas no tamanho, evitando cortar conceitos no meio. Ferramentas: `langchain.text_splitter.SemanticChunker`.
    - 2.1.1.3. **Late Chunking:** Técnica moderna onde o documento é embedado inteiro primeiro (preservando contexto global), e os chunks são extraídos _depois_ a partir dos embeddings do documento completo. Produz chunks semanticamente mais ricos.
    - 2.1.1.4. **Proposição Chunking (Propositional Chunking):** Cada chunk é reescrito como uma proposição atômica e autocontida ("O contrato X vence em 31/12/2025") antes de ser indexado, eliminando ambiguidades causadas por pronomes e referências implícitas.
    - 2.1.1.5. **Parent-Child Chunking:** Indexar chunks pequenos para alta precisão de busca, mas recuperar e enviar ao LLM o chunk "pai" maior para contexto suficiente.
    - 2.1.1.6. **Estratégias de Indexação de Chunk:** Além do texto puro, cada chunk deve ter metadados ricos indexados (data, autor, seção, tipo de documento, entidades extraídas) para permitir filtragem pré-retrieval (pre-filtering) no banco vetorial.
- **2.1.2. Embeddings e Bancos Vetoriais**
    
    - 2.1.2.1. **Modelos de Embedding Modernos:** A geração 2024-2026 de modelos como `text-embedding-3-large` (OpenAI), `voyage-3` (Anthropic/Voyage), `mxbai-embed-large` e `nomic-embed-text` superam os modelos anteriores em benchmarks MTEB.
    - 2.1.2.2. **Dimensionalidade e Matryoshka Embeddings:** Modelos MRL (Matryoshka Representation Learning) permitem truncar o vetor de 1536 para 512 ou 256 dimensões sem perda significativa de qualidade. Isso reduz custo de armazenamento e velocidade de busca em até 3x.
    - 2.1.2.3. **Embeddings Multi-modais:** Modelos como CLIP e seus sucessores (ImageBind, ALIGN) embedam imagens e texto no mesmo espaço vetorial. Essencial para RAG sobre PDFs com gráficos, plantas arquitetônicas ou apresentações com imagens.
    - 2.1.2.4. **Vector Databases:** Armazenamento focado em cálculo de distância (Cosine Similarity). Ferramentas de mercado: Pinecone, Milvus, Qdrant ou PGVector.
- **2.1.3. Algoritmos de Busca Aproximada (ANN)**
    
    - 2.1.3.1. **HNSW (Hierarchical Navigable Small World):** O algoritmo padrão do Qdrant, Weaviate e Milvus. Permite busca em milhões de vetores em milissegundos com altíssima recall (>99%).
    - 2.1.3.2. **IVF (Inverted File Index):** Divide o espaço vetorial em clusters. Mais eficiente em RAM para datasets massivos (>100M vetores).
    - 2.1.3.3. **PQ (Product Quantization):** Comprime cada vetor. Reduz o uso de memória em 8-32x ao custo de pequena perda de precisão.
    - 2.1.3.4. **Qdrant Specifics:** Oferece filtering integrado ao índice HNSW, evitando pós-filtragem que desperdiça I/O.

### 2.2 RAG de Próxima Geração (Retrieval Otimizado)

- **2.2.1. Técnicas de Busca e Refinamento**
    
    - 2.2.1.1. **Hybrid Search:** Combinar busca vetorial semântica com busca clássica BM25/Lexical. Fusão de rankings via **Reciprocal Rank Fusion (RRF)**.
    - 2.2.1.2. **BM25 em Detalhe:** Função de scoring probabilística baseada em TF-IDF. Essencial para queries com nomes próprios, IDs, CNPJs e siglas técnicas que embeddings "semantizam" erroneamente.
    - 2.2.1.3. **Self-Querying:** O LLM converte a pergunta em query estruturada de banco de dados antes de buscar.
    - 2.2.1.4. **Reranking:** Recuperar 50 documentos baratos e usar Cross-Encoder especializado (ex: Cohere Rerank v3) para reordenar o Top 5 com precisão cirúrgica.
    - 2.2.1.5. **GraphRAG:** Usar Knowledge Graphs (Neo4j) para entender relações entre entidades. Responde queries globais que o RAG clássico falha.
    - 2.2.1.6. **Small-to-Big Retrieval:** Buscar por chunks pequenos (alta precisão) mas devolver ao LLM a janela de contexto expandida (maior coerência).
- **2.2.2. Query Transformation**
    
    - 2.2.2.1. **HyDE (Hypothetical Document Embedding):** LLM gera documento hipotético que responderia a pergunta; usa seu embedding para buscar documentos reais. Melhora recall em até 30% para queries vagas.
    - 2.2.2.2. **Multi-Query Retrieval:** Reescrever a query em 3-5 variações semânticas, executar retrieval para cada uma e desduplicar.
    - 2.2.2.3. **Step-Back Prompting:** Abstrair a query para nível mais geral antes de buscar, capturando documentos de background.

---

## 2.3 Extração de Dados — Ferramentas Detalhadas _(Domínio Adicionado)_

> Esta seção detalha as ferramentas de Data Extraction da imagem de referência do stack RAG.

### 2.3.1 Web Crawling e Scraping para RAG

- **2.3.1.1. Crawl4AI**
    
    - Framework open-source de web crawling otimizado especificamente para alimentar sistemas de IA.
    - Converte páginas web automaticamente em Markdown limpo, pronto para chunking.
    - Suporta JavaScript rendering (sites dinâmicos), extração de PDFs linkados e respeito a `robots.txt`.
    - Modo assíncrono permite crawlar centenas de páginas em paralelo com controle de rate limiting.
    - **Quando usar:** Coletar documentação técnica, bases de conhecimento públicas ou sites de uma empresa inteira para RAG.
- **2.3.1.2. FireCrawl**
    
    - Serviço gerenciado (API SaaS) de web crawling que retorna conteúdo em Markdown ou JSON estruturado.
    - Lida automaticamente com anti-bot, JavaScript, autenticação e paginação.
    - Integração nativa com LangChain e LlamaIndex via `FireCrawlLoader`.
    - **Quando usar:** Quando não quer gerenciar infraestrutura de crawler. Ideal para MVPs e projetos que precisam de dados da web rapidamente.
- **2.3.1.3. Scrape GraphAI**
    
    - Combina scraping com LLMs em um grafo de processamento de dados.
    - O grafo define fluxos de extração: nó de scraping → nó de LLM → nó de estruturação.
    - Permite extração de dados altamente estruturados de páginas complexas (ex: extrair preço, SKU e disponibilidade de páginas de e-commerce).
    - **Quando usar:** Quando precisa de extração estruturada de dados de páginas dinâmicas ou complexas, não apenas texto livre.

### 2.3.2 Parsers de Documentos para RAG

- **2.3.2.1. MegaParser**
    
    - Parser multi-formato (PDF, DOCX, PPTX, XLSX, imagens, HTML) com foco em preservação de estrutura.
    - Extrai tabelas como JSON estruturado, preserva hierarquia de headers e captura metadados.
    - **Quando usar:** Quando o corpus contém múltiplos tipos de arquivo com estrutura importante (ex: relatórios financeiros com tabelas).
- **2.3.2.2. Docling (IBM Research)**
    
    - Parser de documentos de nível enterprise desenvolvido pela IBM.
    - Especialista em PDFs científicos e corporativos com layout complexo: colunas múltiplas, figuras, equações matemáticas e tabelas aninhadas.
    - Usa modelos de visão computacional para entender o layout da página antes de extrair texto.
    - Exporta para formatos estruturados (JSON, Markdown com hierarquia) preservando a semântica do documento.
    - **Quando usar:** Artigos acadêmicos, relatórios de auditoria, documentos jurídicos com formatação densa. Melhor opção para PDFs complexos.
- **2.3.2.3. LlamaParse**
    
    - Serviço de parsing de PDFs via API, desenvolvido pela equipe do LlamaIndex.
    - Usa LLMs internamente para entender o contexto das tabelas e figuras, gerando Markdown rico.
    - Integração nativa e direta com LlamaIndex (`LlamaParse` como `SimpleDirectoryReader`).
    - **Quando usar:** Quando já usa LlamaIndex no pipeline. Melhor custo-benefício para PDFs com tabelas e gráficos que precisam de interpretação semântica.
- **2.3.2.4. Extract Thinker**
    
    - Framework para extração de dados estruturados de documentos usando LLMs.
    - Define schemas Pydantic para o que quer extrair (ex: campos de uma NF, cláusulas de um contrato) e o LLM preenche automaticamente.
    - Suporta múltiplos backends de LLM (OpenAI, Anthropic, local).
    - **Quando usar:** Quando o objetivo não é indexar o documento inteiro para RAG, mas sim extrair campos específicos de formulários, contratos, notas fiscais ou laudos.

### 2.3.3 Comparativo: Quando Usar Cada Parser

|Necessidade|Ferramenta Recomendada|
|---|---|
|Sites e páginas web|Crawl4AI (open-source) ou FireCrawl (gerenciado)|
|PDFs simples (texto corrido)|LlamaParse ou Docling|
|PDFs complexos (tabelas, colunas, equações)|Docling (IBM)|
|Extração de campos específicos (NF, contratos)|Extract Thinker|
|Múltiplos formatos de arquivo|MegaParser|
|Dados estruturados de páginas dinâmicas|Scrape GraphAI|

---

## 3 Domínio: Embeddings — Tópicos Avançados

### 3.1 Estratégias de Embedding para Produção

- **3.1.1. Modelos e Benchmarks**
    
    - 3.1.1.1. **MTEB Benchmark:** O Massive Text Embedding Benchmark avalia modelos em 56 tarefas. Consultar `huggingface.co/spaces/mteb/leaderboard` antes de escolher o modelo de embedding para produção.
    - 3.1.1.2. **Modelos Soberanos/Locais:** `nomic-embed-text`, `mxbai-embed-large` e `e5-mistral-7b` são open-source, rodam via Ollama. Crítico para LGPD/GDPR.
    - 3.1.1.3. **Modelos Especializados por Domínio:** Embeddings treinados em corpora específicos superam embeddings genéricos em 15-25% de recall no domínio alvo.
- **3.1.2. Estratégias de Fine-tuning de Embeddings**
    
    - 3.1.2.1. **Geração de Pares de Treino Sintéticos:** Usar um LLM para gerar pares (query, documento relevante) do corpus corporativo. Ferramentas: `LlamaIndex` com `EmbeddingAdapterFinetuneEngine`.
    - 3.1.2.2. **Contrastive Learning (triplet loss):** Ensinar o modelo que o embedding da query deve ser próximo do documento correto e distante de documentos negativos.
    - 3.1.2.3. **Asymmetric Embedding:** Separar o encoder de queries e o de documentos (bi-encoder). Otimiza para queries curtas vs. documentos longos.
- **3.1.3. Embedding de Dados Estruturados**
    
    - 3.1.3.1. **Tabelas e Schema SQL:** Técnicas como `TURL` e `DITTO` transformam linhas de tabela em embeddings, habilitando busca semântica sobre dados relacionais.
    - 3.1.3.2. **Código-fonte:** Modelos como `CodeBERT`, `UniXcoder` e `StarEncoder` permitem busca semântica em repositórios ("encontre funções que validam CPF").

---

## 4 Domínio: Orquestração e Frameworks de Agentes

### 4.1 Padrões de Autonomia (Agentic Workflows)

- **4.1.1. Raciocínio e Planejamento**
    
    - 4.1.1.1. **ReAct Pattern (Reasoning + Acting):** O agente observa, raciocina, escolhe uma ferramenta, executa, observa o resultado e decide se já pode responder.
    - 4.1.1.2. **Plan-and-Execute:** O agente escreve um plano detalhado de N passos e um executor segue a lista rigorosamente.
    - 4.1.1.3. **LATS (Language Agent Tree Search):** Combina Monte Carlo Tree Search com LLMs. Estado da arte em benchmarks de coding e raciocínio.
- **4.1.2. Protocolos de Comunicação de Agentes (2025-2026)**
    
    - 4.1.2.1. **MCP — Model Context Protocol (Anthropic):** Protocolo aberto que padroniza como agentes consomem ferramentas e fontes de dados externas. Arquitetura cliente-servidor via JSON-RPC.
    - 4.1.2.2. **A2A — Agent-to-Agent Protocol (Google):** Protocolo para comunicação entre agentes de diferentes fornecedores. Complementar ao MCP: MCP conecta agente a ferramentas, A2A conecta agente a agente.
    - 4.1.2.3. **OpenAI Responses API:** Primitivas stateful de agentes com gerenciamento de estado pela plataforma.

### 4.2 Frameworks, Estado e Memória

- **4.2.1. Frameworks de Mercado**
    
    - 4.2.1.1. **LangChain / LlamaIndex:** Integração rápida com fontes de dados e ferramentas (nível iniciante/intermediário).
    - 4.2.1.2. **LangGraph:** Framework de baixo nível baseado em Grafos Cíclicos Direcionados. Padrão corporativo com checkpointing de estado e execução paralela de nós.
    - 4.2.1.3. **CrewAI / AutoGen:** Orquestração multi-agente com debate, delegação e colaboração baseada em papéis.
    - 4.2.1.4. **smolagents (Hugging Face):** Framework minimalista — o agente gera e executa código Python como ferramenta principal.
    - 4.2.1.5. **Pydantic AI:** Framework fortemente tipado que integra Pydantic v2 diretamente no loop agêntico.
- **4.2.2. Gestão de Memória e Human-in-the-Loop**
    
    - 4.2.2.1. **Memória de Curto vs Longo Prazo:** Guardar histórico na RAM vs extrair fatos e salvar em banco vetorial (Mem0, Zep).
    - 4.2.2.2. **Human-in-the-Loop (HITL):** Pausar o grafo para aprovação humana antes de ações destrutivas.
    - 4.2.2.3. **MemoryOS (2025):** Arquitetura inspirada em SO com memória de trabalho, episódica e semântica. Supera o RAG de memória flat.

---

## 5 Domínio: Ferramentas (Tool Calling) e Interação com o Mundo

### 5.1 Invocações e Segurança

- **5.1.1. Function Calling**
    
    - 5.1.1.1. O LLM não executa código — ele gera um JSON pedindo para o back-end executar a API e devolver o resultado.
    - 5.1.1.2. **Parallel Tool Calling:** LLM chama múltiplas ferramentas em paralelo em um único turno.
- **5.1.2. Computer Use e Multimodal Agents**
    
    - 5.1.2.1. **Computer Use (Anthropic Claude):** Claude controla interfaces gráficas via screenshots + ações. Automação RPA baseada em IA sem APIs estruturadas.
    - 5.1.2.2. **Browser Use:** Playwright integrado a LLMs para automação web com entendimento semântico da página.
    - 5.1.2.3. **Voice Agents (Speech-to-Speech):** APIs como OpenAI Realtime API permitem agentes de voz com latência <500ms.
- **5.1.3. Sandboxing e Execução de Código**
    
    - 5.1.3.1. **Riscos:** RCE (Remote Code Execution) é risco crítico ao deixar IA executar código no servidor.
    - 5.1.3.2. **Isolamento:** Docker Containers, WebAssembly (Pyodide) ou E2B para execução segura.

---

## 6 Domínio: Fine-Tuning e Adaptação de Modelos

### 6.1 Técnicas de Fine-Tuning

- **6.1.1. Full Fine-Tuning vs Parameter-Efficient**
    
    - 6.1.1.1. **Full Fine-Tuning:** Atualizar todos os parâmetros. Custo proibitivo. Só para adaptação de domínio pesado com datasets massivos.
    - 6.1.1.2. **LoRA (Low-Rank Adaptation):** Treina apenas ~0.1-1% dos parâmetros com qualidade comparável ao full fine-tuning. Padrão da indústria.
    - 6.1.1.3. **QLoRA:** LoRA + quantização 4-bit. Permite fine-tuning de modelos 70B em uma única GPU A100.
    - 6.1.1.4. **DoRA (2024):** Decompõe pesos em magnitude e direção. Melhora qualidade do LoRA sem custo adicional.
- **6.1.2. Alinhamento e RLHF**
    
    - 6.1.2.1. **RLHF:** SFT → Reward Model → PPO. Pipeline clássico de alinhamento do ChatGPT/Claude original.
    - 6.1.2.2. **DPO:** Substitui Reward Model + PPO por loss function direta. Mais estável. Padrão atual para open-source.
    - 6.1.2.3. **ORPO / SimPO (2024-2025):** Variantes do DPO que eliminam o modelo de referência. Metade do consumo de memória.
- **6.1.3. Dados para Fine-Tuning**
    
    - 6.1.3.1. **Dados Sintéticos:** Usar modelos de fronteira para gerar datasets de alta qualidade para modelos menores.
    - 6.1.3.2. **Data Curation > Data Volume:** Qualidade supera quantidade. Pipelines como Datatrove e Dolma.
    - 6.1.3.3. **Quando NÃO usar Fine-Tuning:** Fine-tuning ensina estilo/formato/tom. RAG injeta conhecimento factual. Usar FT para fatos novos = mais alucinação.

---

## 7 Domínio: LLMOps, Observabilidade e Governança

### 7.1 Operacionalizando IA no Back-end

- **7.1.1. Observabilidade (Rastreio e Custos)**
    
    - 7.1.1.1. **O Problema da Caixa Preta:** Agentes que erram são impossíveis de debugar com logs clássicos.
    - 7.1.1.2. **Tracing:** LangSmith, Langfuse e Arize Phoenix mapeiam a árvore exata de execução com custo em dólares por request.
    - 7.1.1.3. **OpenTelemetry para LLMs:** `OpenInference` e `OpenLLMetry` adicionam spans específicos de LLM ao stack de observabilidade (Grafana, Datadog).
- **7.1.2. Evaluation Harness (Avaliação Sistemática)**
    
    - 7.1.2.1. **O que é um Eval Harness:** Framework de avaliação automatizado que executa casos de teste contra o sistema e produz métricas agregadas. Essencial para CI/CD de LLMs.
    - 7.1.2.2. **Ragas:** Especializado em RAG. Métricas: faithfulness, answer relevancy, context precision/recall.
    - 7.1.2.3. **DeepEval:** Avaliação geral de LLMs com LLM-as-Judge.
    - 7.1.2.4. **Giskard:** Detecção de viés, alucinações e vulnerabilidades de segurança em modelos de IA. Cria relatórios estruturados de riscos. Ideal para auditoria e compliance (LGPD, regulações financeiras).
    - 7.1.2.5. **inspect_ai (UK AISI):** Avaliação de segurança e capacidades de LLMs. Usado por governos para benchmarks de segurança.
    - 7.1.2.6. **PromptFoo:** Testes de regressão de prompts integrados ao CI/CD. Garante que mudanças de prompt não quebram qualidade.
    - 7.1.2.7. **LLM-as-a-Judge:** LLM maior (GPT-4o, Claude) avalia output de menor com notas de 0 a 100. Métricas: Fidelidade, Relevância de Contexto, Alucinação.
    - 7.1.2.8. **Golden Dataset e Regression Testing:** Dataset de ~200-500 pares executado a cada deploy. Queda >5% em qualquer métrica bloqueia o CI/CD.
- **7.1.3. LLM Routing e Cascading**
    
    - 7.1.3.1. **LLM Routing:** Queries simples → modelos baratos (Haiku, gpt-4o-mini). Queries complexas → modelos poderosos (Opus, o3). Ferramentas: RouteLLM, LiteLLM.
    - 7.1.3.2. **Cascading:** Tenta com modelo barato; se confiança baixa, escala para modelo poderoso. Reduz custo em 60-80% mantendo qualidade.
- **7.1.4. AI Guardrails (Segurança de Entrada e Saída)**
    
    - 7.1.4.1. Camada interceptadora (NeMo Guardrails, Llama Guard 3) que verifica inputs e outputs do LLM contra Prompt Injections e PII.
    - 7.1.4.2. **Prompt Injection Detection:** Marcar conteúdo externo com delimitadores (`<user_document>`) e instruir o modelo a não seguir instruções dentro dessas tags.

### 7.2 Infraestrutura de Inferência

- **7.2.1. Serving e Otimização**
    
    - 7.2.1.1. **vLLM:** PagedAttention multiplica throughput em 5-24x vs naive serving. Padrão para deploy de modelos open-source.
    - 7.2.1.2. **Speculative Decoding:** Draft model menor propõe tokens que o modelo principal verifica em paralelo. 2-3x mais rápido.
    - 7.2.1.3. **Quantização:** GPTQ, GGUF, AWQ. Modelos 7B em 4-bit com qualidade comparável ao 16-bit e 4x menos VRAM.
    - 7.2.1.4. **Ollama / LM Studio:** Deploy local em um comando (`ollama run llama3.2`).

---

## 8 Domínio: Projeto Prático "Nexus Architect"

### 8.1 Especificação do Sistema Multi-Agente

- **8.1.1. O Desafio:** API de orquestração que recebe requisito de negócio em texto e entrega esqueleto de código de MVP (Diagramas, Banco e Código Base).
    
- **8.1.2. A Topologia do CrewAI/LangGraph**
    
    - 8.1.2.1. **Agente Analista (Supervisor):** Aplica Plan-and-Execute, quebra requisitos em épicos e coordena a equipe.
    - 8.1.2.2. **Agente de Infra (DB Specialist):** Gera Schema SQL validando 3ª Forma Normal. Ferramenta: Sandboxing para validar compilação.
    - 8.1.2.3. **Agente de Backend (Java/Spring):** Gera Entities, Repositories, Services e Controllers com Soft Delete.
    - 8.1.2.4. **Agente de Frontend (Angular):** Em paralelo, cria interfaces, componentes e diagrama Mermaid da arquitetura.
    - 8.1.2.5. **Eval Harness:** Avalia se SQL compila, endpoints seguem REST e componentes têm routing correto. Métricas binárias + LLM-as-judge.

---

## 9 Domínio: Fronteiras e Tópicos Emergentes (2025-2026)

### 9.1 Multimodalidade e Novos Paradigmas

- **9.1.1. RAG Multimodal:** Indexar e recuperar imagens, tabelas de PDFs, áudio e vídeo. GPT-4V, Claude 3.5 e Gemini 1.5 Pro eliminam necessidade de OCR pré-processado.
- **9.1.2. Mixture of Experts (MoE):** Router ativa apenas 2-8 especialistas por token. Grok-1, Mixtral, GPT-4 e Gemini 1.5 usam MoE. Mais baratos de rodar, mas requerem mais VRAM.
- **9.1.3. Small Language Models (SLMs) para Edge:** Phi-3.5-mini, Gemma 2 2B e Llama 3.2 cabem em smartphones. Alternativa viável às APIs na nuvem para dados sensíveis.
- **9.1.4. Model Distillation:** Modelo menor ("student") aprende a imitar modelo maior ("teacher"). DeepSeek R1 foi destilado produzindo capacidades de raciocínio surpreendentes a custo reduzido.

### 9.2 Segurança e Alinhamento

- **9.2.1. Prompt Injection:** Documentos ou emails de terceiros injetam instruções no agente. Defesa: separação de canais de instrução e dados.
- **9.2.2. Constitutional AI:** Conjunto de princípios para auto-revisão do modelo durante treinamento RLAIF (substituindo parcialmente anotadores humanos).
- **9.2.3. Red Teaming Automatizado:** `garak` automatiza testes de jailbreak, extração de informações e viés, produzindo relatórios de vulnerabilidade.

---

## 🗂️ Mapa de Tecnologias por Categoria (2026)

| Categoria            | Ferramentas Principais                                                                |
| -------------------- | ------------------------------------------------------------------------------------- |
| **LLMs**             | GPT-4o/o3, Claude 3.x, Gemini 2.0, Llama 4, Mistral, Phi-4, Gemma 3, DeepSeek, Qwen 3 |
| **Embedding Models** | voyage-3, text-embedding-3-large, nomic-embed-text, mxbai-embed-large, SBERT          |
| **Vector DBs**       | Qdrant, Pinecone, Weaviate, PGVector, Milvus, Chroma                                  |
| **RAG Frameworks**   | LlamaIndex, LangChain, Haystack, Txtai                                                |
| **Data Extraction**  | Crawl4AI, FireCrawl, Scrape GraphAI, Docling, LlamaParse, Extract Thinker, MegaParser |
| **Open LLMs Access** | Hugging Face, Ollama, Groq, Together AI                                               |
| **Agent Frameworks** | LangGraph, CrewAI, smolagents, Pydantic AI, AutoGen                                   |
| **Agent Protocols**  | MCP (Anthropic), A2A (Google), OpenAI Responses API                                   |
| **Fine-Tuning**      | LoRA/QLoRA via `peft` + `trl`, Axolotl, Unsloth                                       |
| **Serving/Infra**    | vLLM, Ollama, llama.cpp, TGI (Hugging Face)                                           |
| **Observability**    | LangSmith, Langfuse, Arize Phoenix, OpenLLMetry                                       |
| **Evals / Harness**  | Ragas, DeepEval, Giskard, inspect_ai, PromptFoo                                       |
| **Guardrails**       | NeMo Guardrails, Llama Guard 3, Rebuff                                                |
| **Caching**          | GPTCache, Redis/RedisVL, Prompt Caching (Anthropic/OpenAI)                            |
| **Routing**          | RouteLLM, LiteLLM, Martian                                                            |

---

_Guia atualizado em 2026. Ecossistema em constante evolução — verificar changelogs dos frameworks mensalmente._