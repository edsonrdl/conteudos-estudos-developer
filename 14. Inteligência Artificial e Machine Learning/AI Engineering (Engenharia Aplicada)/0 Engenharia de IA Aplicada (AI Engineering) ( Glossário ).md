# 📚 Guia de Estudos: Engenharia de IA Aplicada (AI Engineering)
#ia #agentes #rag #llmops #arquitetura

> [!info] Visão Geral
> A Engenharia de IA Aplicada (AI Engineering) não foca em treinar modelos do zero (matemática pura), mas sim na orquestração, integração e otimização de sistemas distribuídos que consomem Modelos de Linguagem (LLMs). Este guia mapeia desde a dominação do contexto (Prompting e RAG) até a construção de fluxos agenticos autônomos e a infraestrutura necessária para monitorar essas decisões em produção (LLMOps).

---

## 1 Domínio: Fundamentos de LLM e Unidades de Controle

### 1.1 Contexto, Tokens e Inferência
- **1.1.1. Gestão de Context Window**
  - 1.1.1.1. **Tokenização:** Como o modelo quebra palavras (BPE - Byte Pair Encoding) e como isso afeta o custo financeiro da API (input/output tokens).
  - 1.1.1.2. **Sliding Window e Truncation:** Estratégias para lidar com históricos de conversas que ultrapassam o limite do modelo sem perder o fio da meada.
- **1.1.2. Tuning de Parâmetros de API**
  - 1.1.2.1. **Temperature & Top-P:** Controle matemático da criatividade vs. determinismo. Para agentes de software e geradores de código, a *Temperature* deve ser sempre próxima de `0.0` para evitar alucinações de sintaxe.
  - 1.1.2.2. **Semantic Caching:** Uso de ferramentas como Redis/RedisVL para fazer cache de respostas baseadas em similaridade semântica (se dois usuários fazem a mesma pergunta com palavras diferentes, o sistema não paga o processamento do LLM novamente).

### 1.2 Engenharia de Prompts Avançada
- **1.2.1. Técnicas de Raciocínio (Reasoning)**
  - 1.2.1.1. **Few-shot Prompting:** Fornecer exemplos de input/output no próprio prompt para padronizar a resposta.
  - 1.2.1.2. **Chain-of-Thought (CoT):** Forçar o modelo a gerar o passo a passo lógico ("Pense passo a passo") antes de dar a resposta final, reduzindo erros matemáticos e lógicos.
  - 1.2.1.3. **Self-Consistency:** Pedir ao modelo para gerar 3 a 5 respostas de raciocínio em paralelo e eleger a mais consistente (votação majoritária).
- **1.2.2. Structured Outputs (O Pilar da Integração)**
  - 1.2.2.1. **Mecânica:** A API do LLM não deve retornar "texto", mas sim objetos JSON estritos.
  - 1.2.2.2. **Validação:** Uso de bibliotecas de schema (Pydantic no Python ou Zod no TypeScript) para garantir que o LLM devolva as chaves exatas que o seu Back-end precisa processar.

---

## 2 Domínio: Retrieval-Augmented Generation (RAG) Avançado


### 2.1 Ingestão e Processamento (Pipeline de Dados)
- **2.1.1. Chunking Strategies (O Segredo do RAG)**
  - 2.1.1.1. **Fixed-size vs Recursive Chunking:** Dividir PDFs/Textos por número de caracteres ou respeitando os parágrafos.
  - 2.1.1.2. **Semantic Chunking:** Usar IA para agrupar o texto com base no significado das frases, e não apenas no tamanho, evitando cortar conceitos no meio.
- **2.1.2. Embeddings e Bancos Vetoriais**
  - 2.1.2.1. Transformar texto em arrays matemáticos de centenas de dimensões (ex: `text-embedding-3-small`).
  - 2.1.2.2. **Vector Databases:** Armazenamento focado em cálculo de distância (Cosine Similarity). Ferramentas de mercado: Pinecone, Milvus, Qdrant ou PGVector (extensão nativa do PostgreSQL para infraestruturas clássicas).

### 2.2 RAG de Próxima Geração (Retrieval Otimizado)
- **2.2.1. Técnicas de Busca e Refinamento**
  - 2.2.1.1. **Hybrid Search:** Combinar a busca vetorial semântica (entende o contexto) com a busca clássica BM25/Lexical (encontra palavras-chave exatas, nomes de métodos e IDs).
  - 2.2.1.2. **Self-Querying:** O LLM analisa a pergunta do usuário ("Quais contratos de 2023 falam sobre multas?") e a converte numa query estruturada de banco de dados (`WHERE year=2023 AND text LIKE...`) antes de buscar.
  - 2.2.1.3. **Reranking:** Recuperar 50 documentos baratos na primeira busca, e usar um modelo de *Cross-Encoder* especializado (ex: Cohere Rerank) para reordenar o Top 5 com precisão cirúrgica antes de enviar ao LLM.
  - 2.2.1.4. **GraphRAG:** Usar Knowledge Graphs (Neo4j) em conjunto com vetores para que o RAG entenda a relação entre entidades (Ex: "A tabela Users" -> *tem chave estrangeira com* -> "Tabela Orders").

---

## 3 Domínio: Orquestração e Frameworks de Agentes


### 3.1 Padrões de Autonomia (Agentic Workflows)
- **3.1.1. Raciocínio e Planejamento**
  - 3.1.1.1. **ReAct Pattern (Reasoning + Acting):** O loop principal. O agente observa, raciocina sobre o que falta, escolhe uma ferramenta (API), executa, observa o resultado e decide se já pode responder.
  - 3.1.1.2. **Plan-and-Execute:** Para tarefas complexas, o agente primeiro escreve um plano detalhado de 5 passos e, em seguida, um executor segue a lista rigorosamente, evitando que a IA "se perca" no meio do caminho.

### 3.2 Frameworks, Estado e Memória
- **3.2.1. Frameworks de Mercado**
  - 3.2.1.1. **LangChain / LlamaIndex:** Padrões para integração rápida com fontes de dados e ferramentas (nível iniciante/intermediário).
  - 3.2.1.2. **LangGraph:** Framework de baixo nível baseado em Grafos Cíclicos. É o padrão corporativo atual para fluxos agenticos, gerindo transições de estado robustas.
  - 3.2.1.3. **CrewAI / AutoGen:** Focados em orquestração multi-agente (debate, delegação e colaboração baseada em papéis).
- **3.2.2. Gestão de Memória e Human-in-the-Loop**
  - 3.2.2.1. **Memória de Curto vs Longo Prazo:** Guardar o histórico da sessão atual na RAM vs Extrair fatos importantes do usuário e salvar num banco vetorial (usando bibliotecas como Mem0 ou Zep).
  - 3.2.2.2. **Human-in-the-Loop (HITL):** Configurar o grafo de execução (ex: LangGraph) para pausar e pedir a aprovação de um humano antes do Agente executar ações destrutivas (ex: fazer um `DROP TABLE` ou enviar um email a um cliente).

---

## 4 Domínio: Ferramentas (Tool Calling) e Interação com o Mundo

### 4.1 Invocações e Segurança
- **4.1.1. Function Calling (O Cérebro da API)**
  - 4.1.1.1. O desenvolvedor passa um esquema JSON ou uma especificação OpenAPI detalhada instruindo o LLM sobre quais APIs existem, quais parâmetros elas aceitam e as regras de negócio. O LLM não "executa" o código, ele gera um JSON pedindo para o seu back-end executar a API e devolver o resultado a ele.
- **4.1.2. Sandboxing e Execução de Código**
  - 4.1.2.1. **Riscos de Segurança:** Permitir que uma IA escreva e execute código (Python, SQL, Bash) diretamente no seu servidor é um risco crítico (RCE - Remote Code Execution).
  - 4.1.2.2. **Isolamento:** Uso de ambientes efêmeros como Docker Containers isolados, WebAssembly ou plataformas nativas como E2B para garantir que o código gerado pela IA rode de forma segura.

---

## 5 Domínio: LLMOps, Observabilidade e Governança

### 5.1 Operacionalizando IA no Back-end
- **5.1.1. Observabilidade (Rastreio e Custos)**
  - 5.1.1.1. **O Problema da Caixa Preta:** Quando um agente toma uma decisão errada, é impossível debugar apenas com logs clássicos.
  - 5.1.1.2. **Tracing:** Ferramentas como LangSmith ou Langfuse mapeiam a árvore exata de execução: qual foi o prompt exato, quanto tempo demorou o RAG, qual ferramenta foi chamada e qual foi o custo exato em dólares (Token Tracking) por request.
- **5.1.2. Avaliação de Modelos (Evals)**
  - 5.1.2.1. Abandonar o "achismo visual". Uso de frameworks como Ragas ou DeepEval para ter um "LLM-as-a-judge" (Um LLM maior avaliando o output de um menor com notas de 0 a 100 para Fidelidade, Relevância do Contexto e Alucinação).
- **5.1.3. AI Guardrails (Segurança de Entrada e Saída)**
  - 5.1.3.1. Camada interceptadora (ex: NeMo Guardrails) que verifica o input do usuário para barrar *Prompt Injections* e checa a resposta do LLM para garantir que ele não exiba PII (dados sensíveis) ou fale sobre tópicos proibidos pelos SLAs da empresa.

---

## 6 Domínio: Projeto Prático "Nexus Architect"

### 6.1 Especificação do Sistema Multi-Agente
- **6.1.1. O Desafio (Problema a Resolver)**
  - 6.1.1.1. Criar uma API de orquestração que recebe um requisito de negócio em texto simples e, através do trabalho conjunto de múltiplos agentes de software, planeja, projeta e cospe o esqueleto de código de um MVP (Diagramas, Banco e Código Base).
- **6.1.2. A Topologia do CrewAI/LangGraph**
  - 6.1.2.1. **Agente Analista (O Supervisor/Roteador):** Recebe o prompt do usuário, aplica o padrão *Plan-and-Execute*, quebra os requisitos em épicos e coordena a ordem de execução da equipe abaixo.
  - 6.1.2.2. **Agente de Infra (DB Specialist):** O input dele é o plano do Supervisor. Ele usa seu contexto para gerar o Schema Relacional (SQL) validando as regras da 3ª Forma Normal. Ferramenta liberada: Sandboxing para validar se o script SQL compila.
  - 6.1.2.3. **Agente de Backend (Java/Spring Specialist):** Lê o output do DB Specialist e gera as Entities, Repositories, Services e Controllers. *Instrução rigorosa no Prompt Base:* "Sempre aplique a regra de Soft Delete em vez de deleção física e retorne Structured Outputs validando os endpoints REST".
  - 6.1.2.4. **Agente de Frontend (Angular Specialist):** Em paralelo, lê os esquemas JSON da API definidos pelo Backend e cria as interfaces, componentes Angular e mapeamento do *Routing*. Retorna também um diagrama Mermaid com a arquitetura dos componentes visuais.