### O que é um "Harness" de IA?

**Harness** (arnês/estrutura de controle) é tudo que **envolve e sustenta** o modelo de IA — sem isso, você não tem um produto, tem só uma API respondendo aleatoriamente.

É a diferença entre um motor e um carro.

---

### Os Pilares Fundamentais

#### 1. 🎯 Definição do Problema (o mais ignorado)

- **Qual é a tarefa exata?** — não "usar IA", mas "classificar intenção do usuário em 5 categorias"
- **Quem é o usuário?** — persona, contexto, nível técnico
- **O que é sucesso mensurável?** — sem métrica, você nunca sabe se funcionou

---

#### 2. 🧠 Camada de Prompt (o coração do sistema)

- **System prompt sólido** — define identidade, restrições, formato de saída
- **Poucos exemplos bem escolhidos** (few-shot) — valem mais que 1000 palavras de instrução
- **Controle de temperatura e parâmetros** — criatividade vs. precisão é uma escolha de design
- **Versionamento de prompts** — trate prompt como código: git, changelog, testes

---

#### 3. 🔄 Gerenciamento de Contexto

- O modelo **não tem memória** — você é responsável por entregar o histórico certo
- **Janela de contexto é recurso escasso** — o que entra e o que é jogado fora é decisão arquitetural
- Técnicas: summarização progressiva, memória seletiva, RAG (busca antes de enviar)

---

#### 4. 📦 RAG — Geração Aumentada de Recuperação

- Nunca coloque todo o conhecimento no prompt
- **Busque antes, injete só o relevante** — vector DB, busca semântica, ou até busca simples
- A qualidade do seu retrieval define 80% da qualidade da resposta

---

#### 5. 🛡️ Guardrails — Controle de Saída

- **Validação estrutural** — se pediu JSON, garanta que é JSON antes de usar
- **Filtros de conteúdo** — o que o modelo não pode dizer/fazer no seu contexto
- **Fallbacks** — o que acontece quando o modelo erra ou recusa? O sistema não pode travar
- **Retry com variação** — falha silenciosa é o pior bug de IA

---

#### 6. 📊 Observabilidade — sem isso você está voando cego

- **Log de cada chamada**: prompt enviado, resposta, latência, custo, token count
- **Rastreabilidade por sessão** — conseguir reproduzir qualquer conversa
- **Alertas** — se a latência subiu ou o custo disparou, você precisa saber agora
- Ferramentas: LangSmith, Helicone, Langfuse, ou log próprio num banco

---

#### 7. 🧪 Avaliação Contínua (Evals)

- **Conjunto de casos de teste fixo** — golden set — que você roda a cada mudança de prompt
- **Métricas automatizadas** + **revisão humana periódica**
- Sem eval, cada mudança é uma aposta
- Regra de ouro: _se você não pode medir, você não pode melhorar_

---

#### 8. 🏗️ Arquitetura de Fluxo

```
Entrada do usuário
    → Pré-processamento (sanitização, detecção de intenção)
    → Retrieval (RAG se necessário)
    → Montagem do prompt (contexto + instrução + exemplos + dado)
    → Chamada ao modelo
    → Pós-processamento (parse, validação, fallback)
    → Saída para o usuário
    → Log de tudo
```

---

#### 9. 💰 Gestão de Custo e Latência

- **Cache de respostas** — mesma pergunta, mesma resposta: não chame a API de novo
- **Escolha o modelo certo para cada tarefa** — usar o modelo mais caro pra tudo é erro de arquitetura
- **Streaming** — para UX: mostre a resposta enquanto gera, não faça o usuário esperar

---

#### 10. 🔐 Segurança

- **Prompt injection** — usuário tentando sequestrar as instruções do sistema
- **Dados sensíveis** — o que entra no prompt pode ser logado por terceiros
- **Rate limiting** — proteção contra abuso e custo descontrolado

---

### O que realmente faz uma IA "dar certo"

|❌ Ilusão comum|✅ Realidade|
|---|---|
|Escolher o melhor modelo|Ter o melhor contexto e prompt|
|Mais tokens = melhor|Contexto relevante = melhor|
|IA vai resolver sozinha|Você projeta o fluxo, ela executa|
|Testar na interface|Ter evals automatizados|
|Deploy e esquece|Monitoramento contínuo|

---

### A frase que resume tudo

> **O modelo é o menor problema. O harness é o produto.**

O modelo já existe e funciona. O seu trabalho de engenharia é construir **tudo ao redor dele** de forma que ele entregue valor previsível, seguro, mensurável e escalável.