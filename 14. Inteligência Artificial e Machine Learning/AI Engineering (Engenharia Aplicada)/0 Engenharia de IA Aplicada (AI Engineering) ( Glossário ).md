# 📚 Guia de Estudos: Engenharia de IA Aplicada (AI Engineering)

#ia #agentes #rag #llmops #arquitetura #finetuning #embedding #cache #retrieval

> [!info] Visão Geral
> A Engenharia de IA Aplicada (AI Engineering) não foca em treinar modelos do zero, mas na orquestração, integração e otimização de sistemas que consomem Modelos de Linguagem (LLMs) em produção. Este guia mapeia desde o domínio do contexto (prompting, RAG) até fluxos agênticos autônomos e a infraestrutura para monitorar essas decisões (LLMOps). É o complemento **prático** do guia [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/0 Como LLMs Funcionam (Glossário )|Como LLMs Funcionam]], que cobre o lado **teórico**.

---

## 0 Domínio: Guia de Orientação — "O Que Faz Cada Coisa?"

### 0.1 O Mapa Mental do Sistema RAG
- **0.1.1. Visão Geral do Pipeline de 7 Responsabilidades**
  - [ ] 0.1.1.1. O diagrama de um sistema de IA em produção dividido em 7 responsabilidades distintas — extração, chunking, embedding, banco vetorial, framework, LLM e avaliação.

### 0.2 As 7 Categorias de Ferramentas
- **0.2.1. LLMs (Os Cérebros)**
  - [ ] 0.2.1.1. Comparativo de LLMs (GPT-4o/o3, Claude, Gemini, Llama, Mistral, Phi, Cohere) e quando usar cada um.
- **0.2.2. Frameworks de Orquestração**
  - [ ] 0.2.2.1. Comparativo de frameworks (LangChain, LlamaIndex, Haystack, Txtai, LangGraph, CrewAI/AutoGen) e quando usar cada um.
- **0.2.3. Bancos Vetoriais**
  - [ ] 0.2.3.1. Comparativo de bancos vetoriais (Qdrant, Pinecone, Weaviate, Milvus, Chroma, PGVector) e quando usar cada um.
- **0.2.4. Extração de Dados**
  - [ ] 0.2.4.1. Comparativo de ferramentas de extração (Crawl4AI, FireCrawl, Scrape GraphAI, MegaParser, Docling, LlamaParse, Extract Thinker) e quando usar cada uma.
- **0.2.5. Acesso a LLMs Open-Source**
  - [ ] 0.2.5.1. Comparativo de plataformas (Hugging Face, Ollama, Groq, Together AI) e quando usar cada uma.
- **0.2.6. Text Embeddings**
  - [ ] 0.2.6.1. Comparativo de modelos de embedding (OpenAI, Voyage AI, SBERT, Nomic, Google, Cohere) e quando usar cada um.
- **0.2.7. Avaliação**
  - [ ] 0.2.7.1. Comparativo de ferramentas de avaliação (Ragas, DeepEval, Giskard, inspect_ai, PromptFoo, LangSmith) e quando usar cada uma.

### 0.3 Como as Categorias se Conectam na Prática
- **0.3.1. Exemplo de Pipeline Completo**
  - [ ] 0.3.1.1. Exemplo passo a passo — construir um chatbot de contratos combinando as 7 categorias de ferramentas em um único pipeline.

---

## 1 Domínio: Fundamentos de LLM e Unidades de Controle

### 1.1 Contexto, Tokens e Inferência
- **1.1.1. Gestão de Context Window**
  - [ ] 1.1.1.1. Tokenização e custo de API, Sliding Window/Truncation, Long Context e "Lost in the Middle", e Prompt Caching nativo (Anthropic/OpenAI).
- **1.1.2. Tuning de Parâmetros de API**
  - [ ] 1.1.2.1. Temperature e Top-P, Semantic Caching (Redis/RedisVL), e estratégias de cache em múltiplas camadas (GPTCache).
- **1.1.3. Modelos de Raciocínio (Thinking Models)**
  - [ ] 1.1.3.1. O paradigm shift dos modelos de raciocínio (o1/o3, Extended Thinking), seus tradeoffs de custo, e o controle de budget tokens.

### 1.2 Engenharia de Prompts Avançada
- **1.2.1. Técnicas de Raciocínio (Reasoning)**
  - [ ] 1.2.1.1. Few-shot Prompting, Chain-of-Thought (CoT) e Self-Consistency.
  - [ ] 1.2.1.2. Tree of Thought (ToT) e Meta-Prompting.
- **1.2.2. Structured Outputs**
  - [ ] 1.2.2.1. Mecânica de JSON estrito, validação com Pydantic/Zod, e Constrained Decoding (Outlines, Guidance).

---

## 2 Domínio: Retrieval-Augmented Generation (RAG) Avançado

### 2.1 Ingestão e Processamento (Pipeline de Dados)
- **2.1.1. Chunking Strategies**
  - [ ] 2.1.1.1. Fixed-size vs Recursive Chunking, Semantic Chunking e Late Chunking.
  - [ ] 2.1.1.2. Propositional Chunking, Parent-Child Chunking e estratégias de indexação de metadados.
- **2.1.2. Embeddings e Bancos Vetoriais**
  - [ ] 2.1.2.1. Modelos de embedding modernos e Dimensionalidade/Matryoshka Embeddings.
  - [ ] 2.1.2.2. Embeddings multimodais (CLIP) e o papel dos Vector Databases.
- **2.1.3. Algoritmos de Busca Aproximada (ANN)**
  - [ ] 2.1.3.1. HNSW, IVF e Product Quantization — como bancos vetoriais buscam em milhões de vetores rapidamente.

### 2.2 RAG de Próxima Geração (Retrieval Otimizado)
- **2.2.1. Técnicas de Busca e Refinamento**
  - [ ] 2.2.1.1. Hybrid Search (BM25 + vetorial com RRF) e Self-Querying.
  - [ ] 2.2.1.2. Reranking com Cross-Encoder, GraphRAG e Small-to-Big Retrieval.
- **2.2.2. Query Transformation**
  - [ ] 2.2.2.1. HyDE, Multi-Query Retrieval e Step-Back Prompting.

### 2.3 Extração de Dados — Ferramentas Detalhadas
- **2.3.1. Web Crawling e Scraping para RAG**
  - [ ] 2.3.1.1. Crawl4AI, FireCrawl e Scrape GraphAI — quando usar cada ferramenta de coleta web.
- **2.3.2. Parsers de Documentos para RAG**
  - [ ] 2.3.2.1. MegaParser, Docling, LlamaParse e Extract Thinker — quando usar cada parser de documentos.
- **2.3.3. Comparativo de Ferramentas de Extração**
  - [ ] 2.3.3.1. Tabela de decisão consolidada: qual ferramenta de extração usar para cada tipo de necessidade.

---

## 3 Domínio: Embeddings — Tópicos Avançados

### 3.1 Estratégias de Embedding para Produção
- **3.1.1. Modelos e Benchmarks**
  - [ ] 3.1.1.1. O benchmark MTEB, modelos soberanos/locais, e embeddings especializados por domínio.
- **3.1.2. Estratégias de Fine-tuning de Embeddings**
  - [ ] 3.1.2.1. Geração de pares sintéticos, Contrastive Learning (triplet loss) e Asymmetric Embedding.
- **3.1.3. Embedding de Dados Estruturados**
  - [ ] 3.1.3.1. Embeddings de tabelas/SQL (TURL, DITTO) e de código-fonte (CodeBERT, UniXcoder).

---

## 4 Domínio: Orquestração e Frameworks de Agentes

### 4.1 Padrões de Autonomia (Agentic Workflows)
- **4.1.1. Raciocínio e Planejamento**
  - [ ] 4.1.1.1. ReAct Pattern, Plan-and-Execute e LATS (Language Agent Tree Search).
- **4.1.2. Protocolos de Comunicação de Agentes**
  - [ ] 4.1.2.1. MCP (Model Context Protocol), A2A (Agent-to-Agent) e OpenAI Responses API.

### 4.2 Frameworks, Estado e Memória
- **4.2.1. Frameworks de Mercado**
  - [ ] 4.2.1.1. LangChain/LlamaIndex (iniciante) e LangGraph (produção, grafos cíclicos).
  - [ ] 4.2.1.2. CrewAI/AutoGen (multi-agente), smolagents e Pydantic AI.
- **4.2.2. Gestão de Memória e Human-in-the-Loop**
  - [ ] 4.2.2.1. Memória de curto vs longo prazo (Mem0, Zep), HITL e MemoryOS.

### 4.3 Loop Engineering — Do Prompt ao Sistema Autônomo
- **4.3.1. Definição e Motivação**
  - [ ] 4.3.1.1. O que é Loop Engineering, a diferença para Prompt Engineering, e por que emergiu em 2026.
- **4.3.2. Os Quatro Componentes de um Loop Confiável**
  - [ ] 4.3.2.1. Trigger, Goal Verificável, Verifier e Stop Rules.
- **4.3.3. Blocos de Construção de Loops em Produção**
  - [ ] 4.3.3.1. Automations, Worktrees, Skills, Plugins/Connectors, Sub-agentes, e boas práticas.

### 4.4 Graph Engineering — A Topologia da Orquestração Multi-Agente
- **4.4.1. Definição e Motivação**
  - [ ] 4.4.1.1. O que é Graph Engineering e a diferença em relação a Knowledge Graphs.
- **4.4.2. Componentes de um Grafo de Agentes**
  - [ ] 4.4.2.1. Nodes, Edges, State e Conditional Routing.
- **4.4.3. Padrão Prático — Ciclo Avaliador-Otimizador**
  - [ ] 4.4.3.1. O fluxo Planner → Researcher → Writer → Evaluator com loop de revisão condicional.
- **4.4.4. Governança, Custo e Observabilidade em Produção**
  - [ ] 4.4.4.1. Identidade por nó, controle de custo (fan-out/retries) e observabilidade de topologia em runtime.
- **4.4.5. Loop Engineering vs Graph Engineering**
  - [ ] 4.4.5.1. Tabela comparativa das duas disciplinas e como elas se complementam.

---

## 5 Domínio: Ferramentas (Tool Calling) e Interação com o Mundo

### 5.1 Invocações e Segurança
- **5.1.1. Function Calling**
  - [ ] 5.1.1.1. Como o LLM invoca ferramentas via JSON, e Parallel Tool Calling.
- **5.1.2. Computer Use e Multimodal Agents**
  - [ ] 5.1.2.1. Computer Use (Claude), Browser Use (Playwright) e Voice Agents (Speech-to-Speech).
- **5.1.3. Sandboxing e Execução de Código**
  - [ ] 5.1.3.1. Riscos de RCE e isolamento via Docker, WebAssembly ou E2B.

---

## 6 Domínio: Fine-Tuning e Adaptação de Modelos

### 6.1 Técnicas de Fine-Tuning
- **6.1.1. Full Fine-Tuning vs Parameter-Efficient**
  - [ ] 6.1.1.1. Full Fine-Tuning, LoRA, QLoRA e DoRA.
- **6.1.2. Alinhamento e RLHF**
  - [ ] 6.1.2.1. RLHF clássico, DPO e as variantes ORPO/SimPO.
- **6.1.3. Dados para Fine-Tuning**
  - [ ] 6.1.3.1. Dados sintéticos, Data Curation vs Data Volume, e quando NÃO usar fine-tuning (vs RAG).

---

## 7 Domínio: LLMOps, Observabilidade e Governança

### 7.1 Operacionalizando IA no Back-end
- **7.1.1. Observabilidade (Rastreio e Custos)**
  - [ ] 7.1.1.1. O problema da caixa preta, Tracing (LangSmith/Langfuse/Arize) e OpenTelemetry para LLMs.
- **7.1.2. Evaluation Harness (Avaliação Sistemática)**
  - [ ] 7.1.2.1. O que é um Eval Harness, e as ferramentas Ragas, DeepEval e Giskard.
  - [ ] 7.1.2.2. inspect_ai, PromptFoo, LLM-as-a-Judge e Golden Dataset/Regression Testing.
- **7.1.3. LLM Routing e Cascading**
  - [ ] 7.1.3.1. Roteamento por complexidade (RouteLLM/LiteLLM) e Cascading de modelos.
- **7.1.4. AI Guardrails**
  - [ ] 7.1.4.1. Camada interceptadora (NeMo Guardrails, Llama Guard) e detecção de Prompt Injection.

### 7.2 Infraestrutura de Inferência
- **7.2.1. Serving e Otimização**
  - [ ] 7.2.1.1. vLLM/PagedAttention, Speculative Decoding, Quantização (GPTQ/GGUF/AWQ) e deploy local.

---

## 8 Domínio: Projeto Prático "Nexus Architect"

### 8.1 Especificação do Sistema Multi-Agente
- **8.1.1. O Desafio**
  - [ ] 8.1.1.1. A especificação do sistema: API de orquestração que transforma requisito de negócio em esqueleto de MVP.
- **8.1.2. A Topologia do CrewAI/LangGraph**
  - [ ] 8.1.2.1. Agente Analista (Supervisor) e Agente de Infra (DB Specialist).
  - [ ] 8.1.2.2. Agente de Backend, Agente de Frontend e o Eval Harness do projeto.

---

## 9 Domínio: Fronteiras e Tópicos Emergentes

### 9.1 Multimodalidade e Novos Paradigmas
- **9.1.1. RAG Multimodal**
  - [ ] 9.1.1.1. Indexação e recuperação de imagens, tabelas, áudio e vídeo sem OCR pré-processado.
- **9.1.2. Mixture of Experts (MoE)**
  - [ ] 9.1.2.1. Como o roteamento de especialistas por token reduz custo de inferência.
- **9.1.3. Small Language Models (SLMs) para Edge**
  - [ ] 9.1.3.1. Modelos pequenos (Phi, Gemma, Llama) como alternativa a APIs na nuvem.
- **9.1.4. Model Distillation**
  - [ ] 9.1.4.1. Como um modelo "student" aprende a imitar um modelo "teacher" maior.

### 9.2 Segurança e Alinhamento
- **9.2.1. Prompt Injection**
  - [ ] 9.2.1.1. Como conteúdo externo injeta instruções em um agente, e a defesa por separação de canais.
- **9.2.2. Constitutional AI**
  - [ ] 9.2.2.1. Princípios de auto-revisão do modelo durante treinamento RLAIF.
- **9.2.3. Red Teaming Automatizado**
  - [ ] 9.2.3.1. Ferramentas como `garak` para automatizar testes de jailbreak e vulnerabilidade.

---

> **Glossário Pai:** [[0 Glossário/Glossário|Base de Conhecimento Tecnológico]]
>
> **Links Relacionados:**
> [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/0 Como LLMs Funcionam (Glossário )|Como LLMs Funcionam — o lado teórico]]
