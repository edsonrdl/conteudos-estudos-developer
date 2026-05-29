# 📚 Agentic Workflows e Orquestração

#agentes #agentic #orquestração #llm #harness #hitl #mcp #langgraph #react

> [!info] Visão Geral Agentic Workflows são sistemas onde LLMs não apenas respondem — eles **agem**: chamam ferramentas, tomam decisões em múltiplos passos, gerenciam estado e colaboram com outros agentes. Este guia mapeia os padrões de raciocínio, frameworks, harness de controle, protocolos e supervisão humana necessários para produção.

---

## 1 Domínio: Padrões de Raciocínio e Planejamento

### 1.1 Padrões de Autonomia

- **1.1.1. ReAct (Reasoning + Acting):** Padrão fundamental de todos os agentes modernos. Loop: Observar → Pensar → Agir → Observar resultado → repetir. Base de LangChain, LangGraph, Bedrock Agents.
    
- **1.1.2. Plan-and-Execute:** Agente escreve plano completo antes de executar. Separa planejamento de execução — mais previsível e auditável que ReAct puro.
    
- **1.1.3. LATS (Language Agent Tree Search):** Combina Monte Carlo Tree Search com LLMs. Explora múltiplos caminhos de raciocínio com backtracking. Estado da arte em benchmarks de coding.
    
- **1.1.4. Self-Reflection / Reflexion:** Agente avalia seu próprio output com um LLM crítico antes de finalizar. Reduz erros silenciosos em geração de código e análise documental.
    

---

## 2 Domínio: Camadas de um Sistema Agêntico

### 2.1 As Quatro Camadas

- **2.1.1. Spec (constitution.md / spec.md / SOUL.md):** Define O QUÊ o agente pode e não pode fazer. Contrato formal versionado como código.
    
- **2.1.2. Harness / Orquestrador:** Controla COMO o agente age — executa tools, valida, loga, retenta, pausa para humano. Implementações: Step Functions, LangGraph, OpenClaw daemon.
    
- **2.1.3. LLM (o cérebro):** DECIDE o que fazer — raciocina, planeja, escolhe a tool certa. Não executa código. Exemplos: Claude, Hermes 3/4, GPT-4o, Llama 4.
    
- **2.1.4. Tools (lambdas / APIs):** EXECUTAM ações no mundo real. O LLM emite `tool_call`, o harness executa.
    

---

## 3 Domínio: Frameworks de Orquestração

### 3.1 Frameworks para Construir Agentes

- **3.1.1. LangChain:** Prototipagem rápida, muitas integrações prontas. Nível iniciante/intermediário.
    
- **3.1.2. LlamaIndex:** Foco específico em RAG e indexação de documentos dentro de agentes.
    
- **3.1.3. LangGraph:** Grafo Cíclico Direcionado — padrão corporativo para produção. Checkpointing de estado, execução paralela de nós, suporte nativo a HITL.
    
- **3.1.4. CrewAI:** Orquestração multi-agente com papéis definidos (pesquisador, redator, revisor). Modelo mental de equipe.
    
- **3.1.5. AutoGen:** Debate e delegação entre agentes via conversação estruturada.
    
- **3.1.6. smolagents (Hugging Face):** Framework minimalista — agente gera e executa código Python como ferramenta principal.
    
- **3.1.7. Pydantic AI:** Framework fortemente tipado, integra Pydantic v2 diretamente no loop agêntico.
    

### 3.2 Agent Applications (prontas para usar)

- **3.2.1. OpenClaw:** Agente pronto configurado via `SOUL.md` sem escrever código. Daemon Node.js que conecta Telegram, WhatsApp, Discord, Slack. Troca de LLM sem mudança de código.
    
- **3.2.2. Open Interpreter:** Agente que executa código em ambiente local via linguagem natural.
    
- **3.2.3. Eliza (a16z):** Framework open-source para agentes com personalidade em múltiplas plataformas.
    

### 3.3 LLMs Otimizados para Agentes

- **3.3.1. Nous Hermes 3 / 4:** Treinado especificamente para tool calling estruturado e confiável. Emite `<tool_call>` em JSON dentro de um único turno. Tokens de raciocínio transparentes: `<THINKING>`, `<PLAN>`, `<EXECUTION>`, `<REFLECTION>`.
    
- **3.3.2. Llama 4 + Function Calling:** Base open-source com suporte nativo a tool use. 128k context nativo.
    
- **3.3.3. Qwen 3 Coder:** SOTA para codificação e estruturação de JSON em function calling.
    

---

## 4 Domínio: Harness — Runtime de Controle

### 4.1 Responsabilidades do Agent Harness

- **4.1.1. Execução controlada de tools:** Recebe o `tool_call` do LLM, valida, executa a função real e devolve o resultado. Sem harness, o LLM só fala — não age.
    
- **4.1.2. Enforcement de guardrails em runtime:** Verifica se a ação está dentro do que a spec define antes de executar. A spec vira enforcement — não apenas documentação.
    
- **4.1.3. Controle de fluxo e resiliência:** Retry com backoff, fallback quando tool falha, abort com estado consistente quando loop não converge.
    
- **4.1.4. HITL gates:** Pausa execução, notifica responsável e aguarda aprovação antes de continuar — quando a spec exige.
    
- **4.1.5. Observabilidade granular:** Loga cada step: tool chamada, parâmetros, resposta, custo em tokens, latência.
    

### 4.2 Distinção Crítica: Agent Harness vs Eval Harness

- **4.2.1. Eval Harness:** Age pós-execução. Pergunta: "o agente respondeu certo?". Exemplos: Ragas, DeepEval, PromptFoo. → coberto em [[LLMOps e Governança]]
    
- **4.2.2. Agent Harness:** Age em tempo real. Pergunta: "o agente pode executar isso?". Exemplos: Step Functions, LangGraph runtime, OpenClaw gateway.
    

### 4.3 Implementações de Harness

- **4.3.1. AWS Step Functions + Bedrock:** Produção enterprise, IaC via Terraform, compliance. Padrão para stack AWS.
    
- **4.3.2. LangGraph runtime:** Python, cloud-agnostic, controle fino de estado.
    
- **4.3.3. OpenClaw gateway daemon:** Node.js, self-hosted, integração com mensageria.
    
- **4.3.4. Custom Lambda loop:** Máximo controle quando nenhum framework atende.
    

---

## 5 Domínio: Supervisão Humana

### 5.1 Os Três Modelos de Controle

- **5.1.1. HITL — Human-in-the-Loop:** Humano aprova cada decisão antes da execução. Máxima segurança, menor velocidade. Casos: crédito, exclusão de dados, pagamentos.
    
- **5.1.2. HOTL — Human-on-the-Loop:** Agente age automaticamente, humano monitora e pode intervir. Equilíbrio velocidade × controle. Casos: atendimento, automação de processo.
    
- **5.1.3. HIC — Human-in-Command:** Humano define políticas uma vez, agente opera dentro dos limites sem supervisão granular. Máxima escala. Casos: sistemas maduros, baixo risco.
    

### 5.2 Regra de Progressão

- **5.2.1. Começar em HITL:** Todo novo sistema começa com aprovação humana em cada passo relevante.
    
- **5.2.2. Migrar para HOTL:** Quando comportamento estiver previsível e testado por Golden Dataset.
    
- **5.2.3. Considerar HIC:** Apenas com histórico comprovado de confiabilidade e spec rigorosa.
    

---

## 6 Domínio: Protocolos de Comunicação

### 6.1 Protocolos Padrão de Mercado (2025-2026)

- **6.1.1. MCP — Model Context Protocol (Anthropic):** Padroniza como agentes consomem ferramentas e fontes de dados externas. Arquitetura cliente-servidor via JSON-RPC. MCP é para agentes o que REST foi para APIs.
    
- **6.1.2. A2A — Agent-to-Agent Protocol (Google):** Comunicação entre agentes de diferentes fornecedores. Complementar ao MCP: MCP conecta agente ↔ ferramenta. A2A conecta agente ↔ agente.
    
- **6.1.3. OpenAI Responses API:** Primitivas stateful de agentes com gerenciamento de estado pela plataforma OpenAI.
    

---

## 7 Domínio: Memória de Agentes

### 7.1 Tipos de Memória

- **7.1.1. Working Memory (curto prazo):** Histórico da conversa atual na RAM / context window.
    
- **7.1.2. Episódica:** Conversas passadas resumidas em banco vetorial (Mem0, Zep).
    
- **7.1.3. Semântica:** Fatos e preferências extraídos das conversas e indexados (Mem0).
    
- **7.1.4. Procedural:** Funções, tools e skills disponíveis para o agente executar.
    

### 7.2 Ferramentas de Memória

- **7.2.1. Mem0:** Extrai automaticamente fatos relevantes das conversas — memória episódica + semântica.
    
- **7.2.2. Zep:** Resumo e compressão de histórico de longa duração.
    
- **7.2.3. MemoryOS (2025):** Arquitetura inspirada em SO com working memory, episódica e semântica integradas.
    

---

## 8 Domínio: Multi-Agent Systems

### 8.1 Padrões de Colaboração

- **8.1.1. Supervisor + Workers:** Agente supervisor quebra tarefa e delega para agentes especializados. Padrão do projeto Nexus Architect.
    
- **8.1.2. Pipeline Sequencial:** Agentes em fila — cada um processa e passa adiante. Ex: Coletor → Analisador → Redator → Revisor.
    
- **8.1.3. Debate / Self-Consistency:** Múltiplos agentes respondem à mesma pergunta. Supervisor elege a melhor resposta por votação majoritária.
    

### 8.2 Quando Usar Multi-Agente

- **8.2.1. Use quando:** Tarefa tem etapas especializadas distintas, validação crítica é necessária, ou documento é grande demais para um único contexto.
    
- **8.2.2. Não use quando:** Tarefa é simples e direta. Cada agente adicional = mais latência, custo e complexidade de debugging.
    

---

## 🗂️ Mapa de Ferramentas

|Categoria|Ferramentas|
|---|---|
|**Frameworks (construir)**|LangGraph, LangChain, LlamaIndex, CrewAI, AutoGen, smolagents, Pydantic AI|
|**Agent Applications (usar)**|OpenClaw, Eliza (a16z), Open Interpreter|
|**LLMs p/ Agentes**|Claude, Hermes 3/4 (Nous), GPT-4o, Llama 4, Qwen 3 Coder|
|**Harness AWS**|Step Functions + Bedrock Agents + Lambda|
|**Protocolos**|MCP (Anthropic), A2A (Google), OpenAI Responses API|
|**Memória**|Mem0, Zep, MemoryOS, Redis|
|**Observabilidade**|LangSmith, Langfuse, Arize Phoenix, Datadog|

---

## 🔗 Notas Relacionadas

- 0 Engenharia de IA Aplicada (AI Engineering) ( Glossário )
- Harness — Runtime de Controle de Agentes
- Spec-Driven Development (SDD)
- Supervisão Humana — HITL, HOTL e HIC
- Protocolos de Agentes — MCP e A2A
- LLMOps e Governança
- Retrieval-Augmented Generation (RAG)
- Infraestrutura de Inferência — vLLM, Quantização, Routing

---

_Atualizado em 2026. Ver changelogs de LangGraph e Bedrock Agents mensalmente._