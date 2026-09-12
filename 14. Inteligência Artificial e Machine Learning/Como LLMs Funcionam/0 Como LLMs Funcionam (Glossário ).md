# 📚 Guia de Estudos: Como LLMs Funcionam — Transformers, Tokenização e Embeddings

#ia #llm #transformers #tokenizacao #embeddings #fundamentos

> [!info] Visão Geral
> Este guia cobre o que a maioria dos guias de "engenharia de IA" pressupõe como conhecido: **como um LLM realmente processa texto por dentro**. Consolida três perguntas que costumam ficar espalhadas — como o texto vira números (tokenização), como o modelo entende relação entre palavras (arquitetura Transformer) e como palavras viram vetores com significado (embeddings) — em um único lugar, do zero. Este é o complemento **teórico** do [[14. Inteligência Artificial e Machine Learning/AI Engineering (Engenharia Aplicada)/0 Engenharia de IA Aplicada (AI Engineering) ( Glossário )|Engenharia de IA Aplicada (AI Engineering)]], que cobre o lado **prático**. Leia este primeiro se os termos "attention", "BPE" ou "vetor semântico" ainda parecem caixas-pretas.

---

## 1 Domínio: Tokenização — Do Texto aos Números

### 1.1 Por que Tokenizar
- **1.1.1. O Problema e a Solução de Subpalavras**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/1. Tokenização - Do Texto aos Números/1.1 Por que Tokenizar/1.1.1. O Problema e a Solução de Subpalavras/1.1.1.1. Por que Redes Neurais Precisam Tokenizar em Subpalavras|1.1.1.1. Por que Redes Neurais Precisam Tokenizar em Subpalavras]]

### 1.2 Algoritmos de Tokenização por Subpalavras
- **1.2.1. BPE (Byte Pair Encoding)**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/1. Tokenização - Do Texto aos Números/1.2 Algoritmos de Tokenização por Subpalavras/1.2.1. BPE (Byte Pair Encoding)/1.2.1.1. BPE — O Algoritmo por Trás da Maioria dos LLMs|1.2.1.1. BPE — O Algoritmo por Trás da Maioria dos LLMs]]
- **1.2.2. WordPiece e SentencePiece**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/1. Tokenização - Do Texto aos Números/1.2 Algoritmos de Tokenização por Subpalavras/1.2.2. WordPiece e SentencePiece/1.2.2.1. WordPiece e SentencePiece — As Alternativas ao BPE|1.2.2.1. WordPiece e SentencePiece — As Alternativas ao BPE]]

### 1.3 Do Token ao Vocabulário
- **1.3.1. Vocabulário e Tokens Especiais**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/1. Tokenização - Do Texto aos Números/1.3 Do Token ao Vocabulário/1.3.1. Vocabulário e Tokens Especiais/1.3.1.1. Vocabulário, Tokens Especiais e a Causa Raiz do Custo de API|1.3.1.1. Vocabulário, Tokens Especiais e a Causa Raiz do Custo de API]]

---

## 2 Domínio: A Arquitetura Transformer

### 2.1 O Problema que o Transformer Resolveu
- **2.1.1. Antes do Transformer**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/2. A Arquitetura Transformer/2.1 O Problema que o Transformer Resolveu/2.1.1. Antes do Transformer/2.1.1.1. RNNs, LSTMs e o Nascimento do Transformer|2.1.1.1. RNNs, LSTMs e o Nascimento do Transformer]]

### 2.2 Self-Attention — O Mecanismo Central
- **2.2.1. A Ideia e o Mecanismo Matemático**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/2. A Arquitetura Transformer/2.2 Self-Attention - O Mecanismo Central/2.2.1. A Ideia e o Mecanismo Matemático/2.2.1.1. Self-Attention — Query, Key, Value na Prática|2.2.1.1. Self-Attention — Query, Key, Value na Prática]]

### 2.3 Multi-Head Attention e Positional Encoding
- **2.3.1. Múltiplas Cabeças e Ordem das Palavras**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/2. A Arquitetura Transformer/2.3 Multi-Head Attention e Positional Encoding/2.3.1. Múltiplas Cabeças e Ordem das Palavras/2.3.1.1. Multi-Head Attention e Positional Encoding|2.3.1.1. Multi-Head Attention e Positional Encoding]]

### 2.4 Encoder, Decoder e a Pilha Completa
- **2.4.1. As Três Famílias de Arquitetura**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/2. A Arquitetura Transformer/2.4 Encoder, Decoder e a Pilha Completa/2.4.1. As Três Famílias de Arquitetura/2.4.1.1. Encoder-only, Decoder-only e Encoder-Decoder|2.4.1.1. Encoder-only, Decoder-only e Encoder-Decoder]]
- **2.4.2. Feed-Forward, Residual e Layer Norm**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/2. A Arquitetura Transformer/2.4 Encoder, Decoder e a Pilha Completa/2.4.2. Feed-Forward, Residual e Layer Norm/2.4.2.1. A Pilha Completa de um Bloco Transformer|2.4.2.1. A Pilha Completa de um Bloco Transformer]]

---

## 3 Domínio: Embeddings — De Palavras a Vetores Semânticos

### 3.1 O Que é um Embedding
- **3.1.1. Representar Significado como Números**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/3. Embeddings - De Palavras a Vetores Semânticos/3.1 O Que é um Embedding/3.1.1. Representar Significado como Números/3.1.1.1. O Que é um Embedding|3.1.1.1. O Que é um Embedding]]

### 3.2 Embeddings Estáticos vs Contextuais
- **3.2.1. Duas Gerações de Embeddings**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/3. Embeddings - De Palavras a Vetores Semânticos/3.2 Embeddings Estáticos vs Contextuais/3.2.1. Duas Gerações de Embeddings/3.2.1.1. Embeddings Estáticos vs Contextuais|3.2.1.1. Embeddings Estáticos vs Contextuais]]

### 3.3 Espaço Vetorial, Similaridade e Dimensionalidade
- **3.3.1. Medindo Proximidade Semântica**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/3. Embeddings - De Palavras a Vetores Semânticos/3.3 Espaço Vetorial, Similaridade e Dimensionalidade/3.3.1. Medindo Proximidade Semântica/3.3.1.1. Similaridade de Cosseno e Dimensionalidade|3.3.1.1. Similaridade de Cosseno e Dimensionalidade]]

---

## 4 Domínio: Do Token ao Texto Gerado (Inferência)

### 4.1 Next-Token Prediction e Decodificação
- **4.1.1. Como um LLM Decide o Próximo Token**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/4. Do Token ao Texto Gerado (Inferência)/4.1 Next-Token Prediction e Decodificação/4.1.1. Como um LLM Decide o Próximo Token/4.1.1.1. Geração Autorregressiva e Estratégias de Decodificação|4.1.1.1. Geração Autorregressiva e Estratégias de Decodificação]]

---

## 5 Domínio: A Ponte com a Engenharia Aplicada

### 5.1 De Volta à Prática
- **5.1.1. Onde Cada Fundamento Reaparece**
  - [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/5. A Ponte com a Engenharia Aplicada/5.1 De Volta à Prática/5.1.1. Onde Cada Fundamento Reaparece/5.1.1.1. Do Fundamento Teórico à Engenharia Aplicada|5.1.1.1. Do Fundamento Teórico à Engenharia Aplicada]]

---

> **Glossário Pai:** [[0 Glossário/Glossário|Base de Conhecimento Tecnológico]]
>
> **Links Relacionados:**
> [[14. Inteligência Artificial e Machine Learning/AI Engineering (Engenharia Aplicada)/0 Engenharia de IA Aplicada (AI Engineering) ( Glossário )|Engenharia de IA Aplicada (AI Engineering) — o lado prático]]
