# 📚 Guia de Estudos: Engenharia de IA Aplicada (AI Engineering) — 2026

#ia #agentes #rag #llmops #arquitetura #finetuning #embedding #cache #retrieval

> [!info] Visão Geral A Engenharia de IA Aplicada (AI Engineering) não foca em treinar modelos do zero (matemática pura), mas sim na orquestração, integração e otimização de sistemas distribuídos que consomem Modelos de Linguagem (LLMs). Este guia mapeia desde a dominação do contexto (Prompting e RAG) até a construção de fluxos agênticos autônomos e a infraestrutura necessária para monitorar essas decisões em produção (LLMOps). **Atualizado em 2026 com os tópicos mais modernos do ecossistema.**

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
    - 1.1.3.2. **Tradeoffs:** O custo de tokens de raciocínio é alto (pode ser 5-10x o custo de uma resposta normal). A estratégia de engenharia é usar modelos de raciocínio apenas para tarefas que exigem planejamento complexo, e modelos menores para tarefas rotineiras (ver LLM Routing no domínio 5).
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
    - 2.1.1.6. **Estratégias de Indexação de Chunk:** Além do texto puro, cada chunk deve ter metadados ricos indexados (data, autor, seção, tipo de documento, entidades extraídas) para permitir filtragem pré-retrieval (pre-filtering) no banco vetorial, reduzindo o espaço de busca antes do cálculo de similaridade.
- **2.1.2. Embeddings e Bancos Vetoriais**
    
    - 2.1.2.1. **Modelos de Embedding Modernos:** A geração 2024-2026 de modelos como `text-embedding-3-large` (OpenAI), `voyage-3` (Anthropic/Voyage), `mxbai-embed-large` e `nomic-embed-text` superam os modelos anteriores em benchmarks MTEB. A escolha do modelo impacta diretamente a qualidade do RAG.
    - 2.1.2.2. **Dimensionalidade e Matryoshka Embeddings:** Modelos MRL (Matryoshka Representation Learning) permitem truncar o vetor de 1536 para 512 ou 256 dimensões sem perda significativa de qualidade. Isso reduz custo de armazenamento e velocidade de busca em até 3x.
    - 2.1.2.3. **Embeddings Multi-modais:** Modelos como CLIP e seus sucessores (ImageBind, ALIGN) embedam imagens e texto no mesmo espaço vetorial. Essencial para RAG sobre PDFs com gráficos, plantas arquitetônicas ou apresentações com imagens.
    - 2.1.2.4. **Vector Databases:** Armazenamento focado em cálculo de distância (Cosine Similarity). Ferramentas de mercado: Pinecone, Milvus, Qdrant ou PGVector (extensão nativa do PostgreSQL para infraestruturas clássicas).
- **2.1.3. Algoritmos de Busca Aproximada (ANN) — Qdrant e Outros**
    
    - 2.1.3.1. **HNSW (Hierarchical Navigable Small World):** O algoritmo padrão do Qdrant, Weaviate e Milvus. Constrói um grafo hierárquico onde cada nó tem conexões com vizinhos próximos em múltiplas camadas. Permite busca em milhões de vetores em milissegundos com altíssima recall (>99%). Parâmetros críticos: `m` (número de conexões por nó) e `ef_construction` (qualidade do índice na escrita).
    - 2.1.3.2. **IVF (Inverted File Index):** Divide o espaço vetorial em clusters (Voronoi). Na busca, verifica apenas os `nprobe` clusters mais próximos do vetor de query. Mais eficiente em RAM para datasets massivos (>100M vetores).
    - 2.1.3.3. **PQ (Product Quantization):** Comprime cada vetor dividindo-o em sub-vetores quantizados. Reduz o uso de memória em 8-32x ao custo de pequena perda de precisão. Usado em combinação com IVF (IVF-PQ) para escala extrema.
    - 2.1.3.4. **Qdrant Specifics:** O Qdrant oferece `ScaNN`-like filtering: ao usar filtros de metadados (ex: `WHERE category = 'legal'`), o índice HNSW é percorrido com a restrição aplicada durante a busca, evitando pós-filtragem que desperdiça I/O.

### 2.2 RAG de Próxima Geração (Retrieval Otimizado)

- **2.2.1. Técnicas de Busca e Refinamento**
    
    - 2.2.1.1. **Hybrid Search:** Combinar a busca vetorial semântica (entende o contexto) com a busca clássica BM25/Lexical (encontra palavras-chave exatas, nomes de métodos e IDs). A fusão dos rankings é feita com **Reciprocal Rank Fusion (RRF)**.
    - 2.2.1.2. **Recuperação Híbrida BM25 em Detalhe:** BM25 é uma função de scoring probabilística baseada em TF-IDF. Para cada termo de query, calcula relevância ponderada pela frequência do termo no documento (TF) e raridade no corpus (IDF), com normalização por comprimento do documento. Frameworks: Elasticsearch, OpenSearch ou a biblioteca pura `rank_bm25` em Python. Em RAG, o BM25 é essencial para queries com nomes próprios, IDs de produto, CnPJ e siglas técnicas que os embeddings tendem a "semantizar" erroneamente.
    - 2.2.1.3. **Self-Querying:** O LLM analisa a pergunta do usuário ("Quais contratos de 2023 falam sobre multas?") e a converte numa query estruturada de banco de dados (`WHERE year=2023 AND text LIKE...`) antes de buscar.
    - 2.2.1.4. **Reranking:** Recuperar 50 documentos baratos na primeira busca, e usar um modelo de _Cross-Encoder_ especializado (ex: Cohere Rerank v3, `ms-marco-MiniLM`) para reordenar o Top 5 com precisão cirúrgica antes de enviar ao LLM.
    - 2.2.1.5. **GraphRAG:** Usar Knowledge Graphs (Neo4j) em conjunto com vetores para que o RAG entenda a relação entre entidades. Versão Microsoft do GraphRAG cria comunidades de entidades e sumariza hierarquicamente, respondendo queries globais ("Quais são os temas principais deste corpus?") que o RAG clássico falha.
    - 2.2.1.6. **Retrieval Chunk vs Context Chunk:** Estratégia de "small-to-big retrieval" — buscar por chunks pequenos (alta precisão) mas devolver ao LLM a janela de contexto expandida ao redor do chunk encontrado (maior coerência). LlamaIndex implementa isso como `SentenceWindowNodeParser`.
- **2.2.2. Query Transformation**
    
    - 2.2.2.1. **HyDE (Hypothetical Document Embedding):** Pedir ao LLM para gerar um documento hipotético que _responderia_ a pergunta, e usar o embedding desse documento hipotético para buscar documentos reais. Melhora recall em até 30% para queries vagas.
    - 2.2.2.2. **Multi-Query Retrieval:** Reescrever a query original em 3-5 variações semânticas diferentes, executar retrieval para cada uma e desduplicar os resultados antes do reranking.
    - 2.2.2.3. **Step-Back Prompting:** Abstrair a query para um nível mais geral ("Qual o princípio físico por trás disso?") antes de buscar, capturando documentos de background que melhoram a resposta.

---

## 3 Domínio: Embeddings — Tópicos Avançados

### 3.1 Estratégias de Embedding para Produção

- **3.1.1. Modelos e Benchmarks**
    
    - 3.1.1.1. **MTEB Benchmark:** O Massive Text Embedding Benchmark avalia modelos em 56 tarefas (retrieval, reranking, clustering, classificação). Consultar sempre o leaderboard atualizado em `huggingface.co/spaces/mteb/leaderboard` antes de escolher o modelo de embedding para produção.
    - 3.1.1.2. **Modelos Soberanos/Locais:** `nomic-embed-text`, `mxbai-embed-large` e `e5-mistral-7b` são open-source, rodam localmente via Ollama, e competem com modelos proprietários. Crítico para compliance de dados sensíveis (LGPD/GDPR).
    - 3.1.1.3. **Modelos Especializados por Domínio:** Embeddings treinados em corpora específicos (código: `codebert`, jurídico: modelos fine-tunados) superam embeddings genéricos em 15-25% de recall no domínio alvo.
- **3.1.2. Estratégias de Fine-tuning de Embeddings**
    
    - 3.1.2.1. **Geração de Pares de Treino Sintéticos:** Usar um LLM para gerar pares (query, documento relevante) a partir do corpus corporativo. Ferramentas: `LlamaIndex` com `EmbeddingAdapterFinetuneEngine`. Poucas centenas de pares já produzem melhoria mensurável.
    - 3.1.2.2. **Contrastive Learning (triplet loss):** O fine-tuning consiste em ensinar o modelo: o embedding da query deve ser próximo do documento correto e distante de documentos negativos (negativos difíceis — "hard negatives" — são os mais importantes para qualidade).
    - 3.1.2.3. **Asymmetric Embedding:** Separar o encoder de queries e o encoder de documentos (como no modelo `bi-encoder`). Permite otimizar para o padrão de uso real onde queries são curtas e documentos são longos.
- **3.1.3. Embedding de Dados Estruturados**
    
    - 3.1.3.1. **Tabelas e Schema SQL:** Techniques como `TURL` e `DITTO` transformam linhas de tabela e schemas de banco em embeddings, habilitando busca semântica sobre dados relacionais sem precisar de SQL explícito.
    - 3.1.3.2. **Código-fonte:** Modelos como `CodeBERT`, `UniXcoder` e `StarEncoder` geram embeddings especializados para código, permitindo busca semântica em repositórios ("encontre funções que validam CPF").

---

## 4 Domínio: Orquestração e Frameworks de Agentes

### 4.1 Padrões de Autonomia (Agentic Workflows)

- **4.1.1. Raciocínio e Planejamento**
    
    - 4.1.1.1. **ReAct Pattern (Reasoning + Acting):** O loop principal. O agente observa, raciocina sobre o que falta, escolhe uma ferramenta (API), executa, observa o resultado e decide se já pode responder.
    - 4.1.1.2. **Plan-and-Execute:** Para tarefas complexas, o agente primeiro escreve um plano detalhado de N passos e, em seguida, um executor segue a lista rigorosamente.
    - 4.1.1.3. **LATS (Language Agent Tree Search):** Combina Monte Carlo Tree Search com LLMs. O agente explora múltiplos ramos de ação simultaneamente, usa uma função de avaliação para poda e seleciona o caminho ótimo. Estado da arte em benchmarks de coding e raciocínio.
- **4.1.2. Protocolos de Comunicação de Agentes (2025-2026)**
    
    - 4.1.2.1. **MCP — Model Context Protocol (Anthropic):** Protocolo aberto que padroniza como agentes/LLMs consomem ferramentas e fontes de dados externas. Substitui integrações ad-hoc com uma camada de abstração universal: qualquer serviço que implemente um servidor MCP pode ser consumido por qualquer cliente MCP (Claude, Cursor, IDEs). Arquitetura cliente-servidor via JSON-RPC.
    - 4.1.2.2. **A2A — Agent-to-Agent Protocol (Google):** Protocolo para comunicação entre agentes heterogêneos de diferentes fornecedores. Define um esquema de "agent card" (capacidades, autenticação) e um protocolo de delegação de tarefas. Complementar ao MCP: enquanto MCP conecta agente a ferramentas, A2A conecta agente a agente.
    - 4.1.2.3. **OpenAI Responses API:** Nova API da OpenAI que fornece primitivas stateful de agentes (thread de memória, file attachments, tool calls) com gerenciamento de estado gerenciado pela plataforma.

### 4.2 Frameworks, Estado e Memória

- **4.2.1. Frameworks de Mercado**
    
    - 4.2.1.1. **LangChain / LlamaIndex:** Padrões para integração rápida com fontes de dados e ferramentas (nível iniciante/intermediário).
    - 4.2.1.2. **LangGraph:** Framework de baixo nível baseado em Grafos Cíclicos Direcionados. Padrão corporativo para fluxos agênticos, com suporte a checkpointing de estado, time travel debugging e execução paralela de nós.
    - 4.2.1.3. **CrewAI / AutoGen:** Focados em orquestração multi-agente (debate, delegação e colaboração baseada em papéis).
    - 4.2.1.4. **smolagents (Hugging Face):** Framework minimalista lançado em 2025. Filosofia de agentes como "código Python puro" — o agente gera e executa código Python como ferramenta principal, em vez de JSON function calls. Mais flexível e auditável.
    - 4.2.1.5. **Pydantic AI:** Framework fortemente tipado que integra Pydantic v2 diretamente no loop agêntico. O modelo retorna objetos Pydantic validados em cada step, eliminando erros de schema em produção.
- **4.2.2. Gestão de Memória e Human-in-the-Loop**
    
    - 4.2.2.1. **Memória de Curto vs Longo Prazo:** Guardar o histórico da sessão atual na RAM vs Extrair fatos importantes do usuário e salvar num banco vetorial (usando bibliotecas como Mem0 ou Zep).
    - 4.2.2.2. **Human-in-the-Loop (HITL):** Configurar o grafo de execução (ex: LangGraph) para pausar e pedir a aprovação de um humano antes do agente executar ações destrutivas.
    - 4.2.2.3. **MemoryOS (2025):** Arquitetura de memória inspirada no sistema operacional humano com memória de trabalho (in-context), memória episódica (log comprimido de eventos recentes) e memória semântica (fatos extraídos e indexados). Supera o padrão RAG de memória flat.

---

## 5 Domínio: Ferramentas (Tool Calling) e Interação com o Mundo

### 5.1 Invocações e Segurança

- **5.1.1. Function Calling (O Cérebro da API)**
    
    - 5.1.1.1. O desenvolvedor passa um esquema JSON ou uma especificação OpenAPI detalhada instruindo o LLM sobre quais APIs existem, quais parâmetros elas aceitam e as regras de negócio. O LLM não "executa" o código, ele gera um JSON pedindo para o seu back-end executar a API e devolver o resultado a ele.
    - 5.1.1.2. **Parallel Tool Calling:** APIs modernas permitem que o LLM chame múltiplas ferramentas em paralelo em um único turno, reduzindo latência de agentes multi-step de forma drástica.
- **5.1.2. Computer Use e Multimodal Agents**
    
    - 5.1.2.1. **Computer Use (Anthropic Claude):** Claude pode controlar interfaces gráficas de desktops/browsers (mover mouse, clicar, digitar) através de screenshots + ações. Abre o paradigma de automação RPA baseada em IA sem precisar de APIs estruturadas.
    - 5.1.2.2. **Browser Use:** Ferramentas como Playwright integrado a LLMs permitem automação web com entendimento semântico da página (não apenas seletores CSS), navegando sites como um humano.
    - 5.1.2.3. **Voice Agents (Speech-to-Speech):** APIs como OpenAI Realtime API e Ultravox permitem construir agentes de voz com latência <500ms usando modelos speech-to-speech (sem transcrição intermediária), viabilizando call centers e assistentes de voz para produção.
- **5.1.3. Sandboxing e Execução de Código**
    
    - 5.1.3.1. **Riscos de Segurança:** Permitir que uma IA escreva e execute código diretamente no servidor é um risco crítico (RCE - Remote Code Execution).
    - 5.1.3.2. **Isolamento:** Uso de ambientes efêmeros como Docker Containers isolados, WebAssembly (via Pyodide para Python no browser) ou plataformas nativas como E2B para garantir que o código gerado rode de forma segura.

---

## 6 Domínio: Fine-Tuning e Adaptação de Modelos

### 6.1 Técnicas de Fine-Tuning

- **6.1.1. Full Fine-Tuning vs Parameter-Efficient**
    
    - 6.1.1.1. **Full Fine-Tuning:** Atualizar todos os parâmetros do modelo. Custo computacional proibitivo para a maioria das empresas (requer clusters de GPU A100/H100). Faz sentido apenas para casos de adaptação de domínio pesado (medicina, direito, código proprietário) com datasets massivos.
    - 6.1.1.2. **LoRA (Low-Rank Adaptation):** Congela os pesos originais e injeta matrizes de rank baixo nos layers de atenção. Treina apenas ~0.1-1% dos parâmetros com qualidade comparável ao full fine-tuning. É o padrão da indústria para customização de modelos open-source.
    - 6.1.1.3. **QLoRA (Quantized LoRA):** Combina LoRA com quantização 4-bit (NF4) do modelo base. Permite fine-tuning de modelos 70B em uma única GPU A100 80GB. Framework: `bitsandbytes` + `peft` + `trl`.
    - 6.1.1.4. **DoRA (Weight-Decomposed LoRA, 2024):** Decompõe os pesos em magnitude e direção, atualizando-os separadamente. Melhora a qualidade do LoRA sem custo computacional adicional.
- **6.1.2. Alinhamento e RLHF**
    
    - 6.1.2.1. **RLHF (Reinforcement Learning from Human Feedback):** O pipeline clássico de alinhamento: SFT (Supervised Fine-Tuning) → Treinamento de Reward Model → PPO (Proximal Policy Optimization). Usado para treinar ChatGPT/Claude originalmente.
    - 6.1.2.2. **DPO (Direct Preference Optimization):** Substitui o Reward Model + PPO por uma loss function direta que otimiza preferências humanas sem RL explícito. Mais estável e simples de implementar. Tornou-se o padrão para alinhamento de modelos open-source.
    - 6.1.2.3. **ORPO / SimPO (2024-2025):** Variantes mais eficientes do DPO que eliminam o modelo de referência, reduzindo consumo de memória pela metade durante o treinamento.
- **6.1.3. Dados para Fine-Tuning**
    
    - 6.1.3.1. **Geração de Dados Sintéticos:** Usar modelos de fronteira (GPT-4o, Claude 3.5 Sonnet) para gerar datasets de fine-tuning de alta qualidade para modelos menores. Técnica usada no paper "Alpaca" e refinada com "Evol-Instruct" (WizardLM).
    - 6.1.3.2. **Data Curation > Data Volume:** A qualidade supera a quantidade. Pipelines como **Datatrove** (Hugging Face) e **Dolma** filtram deduplicam e pontuam a qualidade dos dados antes do treinamento.
    - 6.1.3.3. **Quando Fine-Tuning NÃO é a solução:** Fine-tuning é para ensinar _estilo, formato e tom_. RAG é para injetar _conhecimento factual atualizado_. Um erro comum é tentar usar fine-tuning para ensinar fatos novos ao modelo — o resultado é alucinação aumentada.

---

## 7 Domínio: LLMOps, Observabilidade e Governança

### 7.1 Operacionalizando IA no Back-end

- **7.1.1. Observabilidade (Rastreio e Custos)**
    
    - 7.1.1.1. **O Problema da Caixa Preta:** Quando um agente toma uma decisão errada, é impossível debugar apenas com logs clássicos.
    - 7.1.1.2. **Tracing:** Ferramentas como LangSmith, Langfuse e Arize Phoenix mapeiam a árvore exata de execução: qual foi o prompt exato, quanto tempo demorou o RAG, qual ferramenta foi chamada e o custo exato em dólares por request.
    - 7.1.1.3. **OpenTelemetry para LLMs:** O padrão `OpenInference` e o `OpenLLMetry` são camadas de instrumentação compatíveis com OpenTelemetry que adicionam spans específicos de LLM (prompt, completion, embeddings) ao stack de observabilidade existente (Grafana, Datadog).
- **7.1.2. Evaluation Harness (Avaliação Sistemática)**
    
    - 7.1.2.1. **O que é um Eval Harness:** Um framework de avaliação automatizado que executa um conjunto de casos de teste (input → expected output) contra o sistema de IA e produz métricas agregadas. Essencial para CI/CD de LLMs — impede regressões ao trocar de modelo ou modificar prompts.
    - 7.1.2.2. **Frameworks:** `Ragas` (especializado em RAG — métricas: faithfulness, answer relevancy, context precision/recall), `DeepEval` (avaliação geral de LLMs com LLM-as-judge), `inspect_ai` (UK AISI, avaliação de segurança e capacidades).
    - 7.1.2.3. **LLM-as-a-Judge:** Um LLM maior (GPT-4o, Claude 3.5) avalia o output de um menor com notas de 0 a 100. Métricas: Fidelidade (a resposta é suportada pelos documentos?), Relevância do Contexto (os documentos recuperados são relevantes à query?) e Alucinação.
    - 7.1.2.4. **Golden Dataset e Regression Testing:** Manter um dataset "golden" de ~200-500 pares (query, resposta esperada) que é executado a cada deploy. Qualquer queda acima de 5% em qualquer métrica bloqueia o pipeline de CI/CD.
- **7.1.3. LLM Routing e Cascading**
    
    - 7.1.3.1. **LLM Routing:** Classificar a complexidade/tipo da query de entrada e roteá-la para o modelo mais custo-eficiente. Queries simples vão para `claude-haiku` ou `gpt-4o-mini`; queries complexas vão para `claude-opus` ou `o3`. Ferramentas: `RouteLLM` (Stanford), `LiteLLM`.
    - 7.1.3.2. **Cascading:** Tentar primeiro com um modelo barato; se a resposta tiver baixa confiança (medida por log-probability ou auto-avaliação), escalar para um modelo mais poderoso. Reduz custo médio por query em 60-80% mantendo qualidade.
- **7.1.4. AI Guardrails (Segurança de Entrada e Saída)**
    
    - 7.1.4.1. Camada interceptadora (ex: NeMo Guardrails, Llama Guard 3) que verifica o input do usuário para barrar _Prompt Injections_ e checa a resposta do LLM para garantir que ele não exiba PII ou fale sobre tópicos proibidos.
    - 7.1.4.2. **Prompt Injection Detection:** Em sistemas agênticos com acesso a fontes externas (emails, documentos de usuários), proteger contra injeção de instruções maliciosas no contexto. Técnica de defesa: marcar explicitamente conteúdo externo com delimitadores (`<user_document>`) e instruir o modelo a não seguir instruções dentro dessas tags.

### 7.2 Infraestrutura de Inferência

- **7.2.1. Serving e Otimização**
    - 7.2.1.1. **vLLM:** Framework de serving de LLMs open-source com PagedAttention (gestão dinâmica de KV-cache similar à paginação de memória de SO). Multiplica o throughput de servir requisições concorrentes em 5-24x vs naive serving. Padrão para deploy de modelos open-source em produção.
    - 7.2.1.2. **Speculative Decoding:** Usar um modelo rascunho (draft model) menor para propor múltiplos tokens, que são verificados em paralelo pelo modelo principal. Acelera geração em 2-3x sem perda de qualidade.
    - 7.2.1.3. **Quantização em Produção:** GPTQ (quantização pós-treinamento), GGUF (formato otimizado para llama.cpp/CPU), AWQ (Activation-aware Weight Quantization). Rodar modelos 7B em 4-bit gera qualidade comparável ao 16-bit com 4x menos VRAM.
    - 7.2.1.4. **Modelos Locais (Ollama / LM Studio):** Para desenvolvimento local, compliance ou edge computing, ferramentas como Ollama abstraem o download e serving de modelos open-source em um único comando (`ollama run llama3.2`).

---

## 8 Domínio: Projeto Prático "Nexus Architect"

### 8.1 Especificação do Sistema Multi-Agente

- **8.1.1. O Desafio (Problema a Resolver)**
    
    - 8.1.1.1. Criar uma API de orquestração que recebe um requisito de negócio em texto simples e, através do trabalho conjunto de múltiplos agentes de software, planeja, projeta e cospe o esqueleto de código de um MVP (Diagramas, Banco e Código Base).
- **8.1.2. A Topologia do CrewAI/LangGraph**
    
    - 8.1.2.1. **Agente Analista (O Supervisor/Roteador):** Recebe o prompt do usuário, aplica o padrão _Plan-and-Execute_, quebra os requisitos em épicos e coordena a ordem de execução da equipe abaixo.
    - 8.1.2.2. **Agente de Infra (DB Specialist):** Gera o Schema Relacional (SQL) validando as regras da 3ª Forma Normal. Ferramenta liberada: Sandboxing para validar se o script SQL compila.
    - 8.1.2.3. **Agente de Backend (Java/Spring Specialist):** Lê o output do DB Specialist e gera as Entities, Repositories, Services e Controllers com Soft Delete e Structured Outputs.
    - 8.1.2.4. **Agente de Frontend (Angular Specialist):** Em paralelo, lê os esquemas JSON da API e cria interfaces, componentes Angular e mapeamento de _Routing_, retornando também um diagrama Mermaid da arquitetura.
    - 8.1.2.5. **Eval Harness do Projeto:** Para este sistema, o harness executa queries de requisito como "Criar sistema de e-commerce" e avalia: (1) O SQL gerado compila sem erros? (2) Os endpoints REST seguem a convenção RESTful? (3) Os componentes Angular têm o routing correto? Métricas binárias + LLM-as-judge para qualidade do código.

---

## 9 Domínio: Fronteiras e Tópicos Emergentes (2025-2026)

### 9.1 Multimodalidade e Novos Paradigmas

- **9.1.1. RAG Multimodal:** Indexar e recuperar não apenas texto, mas imagens, tabelas de PDFs, áudio transcrito e vídeo (frames + transcrição). Modelos como GPT-4V, Claude 3.5 e Gemini 1.5 Pro processam imagens diretamente no contexto, eliminando a necessidade de OCR pré-processado.
- **9.1.2. Mixture of Experts (MoE):** Arquitetura onde o modelo é composto de múltiplos "especialistas" (sub-networks) e um router ativa apenas 2-8 especialistas por token. Grok-1, Mistral (Mixtral), GPT-4 e Gemini 1.5 usam MoE. Para engenheiros: modelos MoE são mais baratos de rodar (menos FLOPS ativos) mas requerem mais VRAM (todos os pesos precisam estar carregados).
- **9.1.3. Small Language Models (SLMs) para Edge:** Modelos como Phi-3.5-mini (3.8B), Gemma 2 2B e Llama 3.2 1B/3B alcançam qualidade de GPT-3.5 em tasks específicas, cabendo em smartphones e dispositivos IoT. Para aplicações de processamento local de dados sensíveis, SLMs são a alternativa viável às APIs na nuvem.
- **9.1.4. Model Distillation:** Técnica onde um modelo menor ("student") aprende a imitar um modelo maior ("teacher") usando seus outputs como rótulos de treinamento. DeepSeek R1 foi destilado de modelos maiores, produzindo capacidades de raciocínio surpreendentes a custo reduzido.

### 9.2 Segurança e Alinhamento

- **9.2.1. Prompt Injection em Sistemas Agênticos:** O vetor de ataque mais relevante de 2025: documentos ou emails de terceiros injetam instruções para o agente, desviando seu comportamento. Defesas: separação de canais de instrução e dados, modelos de classificação de injeção dedicados.
- **9.2.2. Constitutional AI e Diretrizes de Sistema:** Anthropic popularizou o conceito de Constitutional AI — um conjunto de princípios usados para auto-revisão do modelo durante o treinamento RLAIF (RL from AI Feedback), substituindo parcialmente os anotadores humanos.
- **9.2.3. Evals de Segurança (Red Teaming Automatizado):** Frameworks como `garak` automatizam o red teaming de LLMs, testando sistematicamente centenas de vetores de jailbreak, extração de informações e viés, produzindo relatórios estruturados de vulnerabilidade.

---

## 🗂️ Mapa de Tecnologias por Categoria (2026)

|Categoria|Ferramentas Principais|
|---|---|
|**Embedding Models**|voyage-3, text-embedding-3-large, nomic-embed-text, mxbai-embed-large|
|**Vector DBs**|Qdrant, Pinecone, Weaviate, PGVector, Milvus|
|**RAG Frameworks**|LlamaIndex, LangChain, Haystack|
|**Agent Frameworks**|LangGraph, CrewAI, smolagents, Pydantic AI, AutoGen|
|**Agent Protocols**|MCP (Anthropic), A2A (Google), OpenAI Responses API|
|**Fine-Tuning**|LoRA/QLoRA via `peft` + `trl`, Axolotl, Unsloth|
|**Serving/Infra**|vLLM, Ollama, llama.cpp, TGI (Hugging Face)|
|**Observability**|LangSmith, Langfuse, Arize Phoenix, OpenLLMetry|
|**Evals / Harness**|Ragas, DeepEval, inspect_ai, PromptFoo|
|**Guardrails**|NeMo Guardrails, Llama Guard 3, Rebuff|
|**Caching**|GPTCache, Redis/RedisVL, Prompt Caching (Anthropic/OpenAI)|
|**Routing**|RouteLLM, LiteLLM, Martian|

---

_Guia atualizado em 2026. Ecossistema em constante evolução — verificar changelogs dos frameworks mensalmente._