# Prompt — Criar Novo Tópico Filho (Chat)

Cole o bloco abaixo em qualquer chat de IA, substitua `[TÓPICO]`, `[SEÇÃO]` e `[SUBCATEGORIA]` e envie.

---

```
Você é um assistente especializado em criar guias de estudo técnico estruturados.

Preciso que você gere os arquivos de um novo tópico de estudo para o meu workspace no Obsidian.
O tópico é: [TÓPICO]
Ele pertence à seção: [SEÇÃO] (ex: Redes e Infraestrutura)
Subcategoria: [SUBCATEGORIA] (ex: Conceitos Fundamentais)

---

## HIERARQUIA DO WORKSPACE

Meu workspace tem 3 níveis:
1. Glossário.md — índice geral com todos os tópicos
2. 0 [Nome] (Tópicos ).md — glossário filho: índice do tópico com domínios
3. N. [Domínio].md — um arquivo por domínio com o conteúdo aprofundado

---

## REGRA FUNDAMENTAL DE LINKS

O link no arquivo índice (nível 2) fica SEMPRE no `##` (nível de Domínio), apontando para o arquivo do domínio.
Os `### N.x` dentro do índice são TEXTO PURO — descrevem o que está no arquivo, sem links próprios.

ERRADO:
## 1 Domínio: Fundamentos
### 1.1 [[link|Subtópico A]]   ← link no filho, ERRADO

CERTO (com arquivo criado):
## [[link|1 Domínio: Fundamentos]]   ← link no pai, apenas se o arquivo existir
### 1.1 Subtópico A                  ← texto puro, sem link

CERTO (sem arquivo ainda):
## 1 Domínio: Fundamentos            ← texto puro, sem link
### 1.1 Subtópico A                  ← texto puro, sem link

NUNCA criar [[link]] apontando para arquivo que ainda não existe.

---

## O QUE VOCÊ DEVE GERAR

### ARQUIVO 1 — Índice do tópico (Glossário Filho)
Nome: `0 [TÓPICO] (Tópicos ).md`
Caminho: `[SEÇÃO]/[SUBCATEGORIA]/[TÓPICO]/0 [TÓPICO] (Tópicos ).md`

Siga EXATAMENTE este template:

---
# 📚 Guia de Estudos: [TÓPICO]
#tag #[categoria] #[subcategoria]

> [!info] Visão Geral
> [2 a 3 linhas explicando o que é, por que importa e o que este guia cobre]

---

## 1 Domínio: Nome do Domínio

### 1.1 Nome do Subtópico
- **1.1.1. [Seção]**
  - 1.1.1.1. [O que será aprendido]
- **1.1.2. [Seção]**
  - 1.1.2.1. [O que será aprendido]

### 1.2 Nome do Subtópico
- **1.2.1. [Seção]**
  - 1.2.1.1. [O que será aprendido]

---

## 2 Domínio: Nome do Domínio

### 2.1 Nome do Subtópico
- **2.1.1. [Seção]**
  - 2.1.1.1. [O que será aprendido]

### 2.2 Nome do Subtópico
- **2.2.1. [Seção]**
  - 2.2.1.1. [O que será aprendido]

---

## 3 Domínio: Nome do Domínio

### 3.1 Nome do Subtópico
- **3.1.1. [Seção]**
  - 3.1.1.1. [O que será aprendido]

---

> **Links Relacionados:**
> [Tópico relacionado 1]
> [Tópico relacionado 2]
> [Tópico relacionado 3]

---

### ARQUIVOS 2 em diante — Um arquivo por domínio
Nome: `N. [Nome do Domínio].md`
Gere UM arquivo para cada domínio listado no índice.
Os subtópicos `### N.x` do domínio são SEÇÕES dentro desse único arquivo, não arquivos separados.

Siga EXATAMENTE este template para cada arquivo de domínio:

---
[Parágrafo introdutório curto contextualizando o domínio dentro do tópico pai]

---

### 🔹 N.1 [Título do Subtópico]

[Explicação clara. Use analogias quando o conceito for abstrato. Tabelas e listas quando ajudarem.]

---

### 🔹 N.2 [Título do Subtópico]

[Conteúdo]

---

### 🧩 Quiz de Fixação

1. [Pergunta 1]
2. [Pergunta 2]
3. [Pergunta 3]

**Respostas:** 1) [Resposta], 2) [Resposta], 3) [Resposta].

---

**[Pergunta aberta convidando a aprofundar ou seguir para o próximo domínio]**

---

## REGRAS OBRIGATÓRIAS

- O nome do arquivo índice SEMPRE termina com `(Tópicos ).md` — manter o espaço antes do `.md`
- Arquivos de domínio SEMPRE numerados: `1. Nome.md`, `2. Nome.md`
- **`##` domínio começa SEM link** — `## N Domínio: Nome`. O link `[[caminho|N Domínio: Nome]]` só é adicionado depois que o arquivo de conteúdo for criado. Nunca criar `[[link]]` para arquivo inexistente
- **`###` subtópicos NO ÍNDICE são texto puro** — nunca têm link próprio
- Domínios no índice: `## [[caminho|N Domínio: Nome]]` (sem `:` após o número, com `:` após "Domínio")
- Seções no índice: `**N.N.N. Título**` (bold, numerado com 3 níveis)
- Itens no índice: `  - N.N.N.N. Descrição` (2 espaços de indentação)
- Separador entre domínios: `---`
- Rodapé do índice: `> **Links Relacionados:**` com tópicos do mesmo universo
- Emoji fixo no título do índice: `📚`
- Emoji nas seções do arquivo de domínio: `🔹`; no quiz: `🧩`
- Tags na linha 2 do índice: `#tag #categoria #subcategoria`
- Descriptions em português, linguagem direta e técnica
- Não adicionar nada que não esteja no template acima

---

## REFERÊNCIA REAL — use como base fiel

Este é o arquivo índice real do tópico TCP/IP. Os `[[links]]` aparecem aqui porque os arquivos de domínio JÁ EXISTEM neste caso.
Para novos tópicos que você está criando agora, os domínios devem ser TEXTO PURO (sem `[[]]`) pois os arquivos ainda não existem.

# 📚 Guia de Estudos: TCP/IP
#tag #redes #infraestrutura

> [!info] Visão Geral
> O TCP/IP é a suíte de protocolos fundamental que sustenta a Internet e a maioria das redes corporativas. Este guia mapeia desde a fundação da pilha de protocolos até a matemática de roteamento (CIDR e Subnets) e o comportamento das camadas de transporte (TCP/UDP) e ferramentas de troubleshooting.

---

## [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/1. Visão Geral|1 Domínio: Fundamentos e Arquitetura]]

### 1.1 Visão Geral e Histórico
- **1.1.1. Propósito do Protocolo**
  - 1.1.1.1. Explicação resumida do TCP/IP, histórico e propósito de interconexão de redes heterogêneas.

### 1.2 Como Funciona a Pilha TCP/IP
- **1.2.1. Estrutura em Camadas**
  - 1.2.1.1. Explicação da pilha (Aplicação, Transporte, Internet, Enlace/Acesso à Rede).
- **1.2.2. Modelos de Referência**
  - 1.2.2.1. OSI vs TCP/IP (Comparativo entre as 7 camadas conceituais e as 4 camadas práticas).
- **1.2.3. Tráfego de Dados**
  - 1.2.3.1. Encapsulamento (Como os dados descem a pilha ganhando cabeçalhos e sobem a pilha sendo desencapsulados).

---

## [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/3. Endereçamento IP|2 Domínio: Endereçamento e Roteamento]]

### 2.1 Endereçamento IP
- **2.1.1. Classes Tradicionais (A, B, C)**
  - 2.1.1.1. O que são e como dividem a rede dos hosts.
- **2.1.2. Máscara de Rede**
  - 2.1.2.1. Explicação simples de como ela separa a porção de "Rede" da porção de "Host".
- **2.1.3. CIDR (Classless Inter-Domain Routing)**
  - 2.1.3.1. Por que foi criado (esgotamento de IPs e rigidez das Classes).

### 2.2 Redes e Sub-redes
- **2.2.1. Arquitetura Lógica**
  - 2.2.1.1. Diferença entre Rede principal e Sub-rede.
- **2.2.2. Sub-redes (Subnets)**
  - 2.2.2.1. Conceito e necessidade de segmentação de broadcast e segurança.

---

## [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/5. Protocolos TCP e UDP|3 Domínio: Transporte e Comunicação (Layer 4)]]

### 3.1 Protocolos TCP e UDP
- **3.1.1. TCP (Transmission Control Protocol)**
  - 3.1.1.1. Confiabilidade, Handshake de 3 vias e ordenação de pacotes.
- **3.1.2. UDP (User Datagram Protocol)**
  - 3.1.2.1. Sem conexão, rápido, melhor esforço e sem garantia de entrega.

### 3.2 Portas e Sockets
- **3.2.1. Portas Bem Conhecidas (Well-Known Ports)**
  - 3.2.1.1. Mapeamento dos serviços principais (ex: 80 HTTP, 443 HTTPS, 22 SSH).
- **3.2.2. Sockets**
  - 3.2.2.1. Como o SO forma o Socket (IP + Porta) para multiplexar conexões.

---

## [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/7. Ferramentas e Troubleshooting|4 Domínio: Operação e Troubleshooting]]

### 4.1 Ferramentas e Exemplos Práticos
- **4.1.1. Rastreamento e Conectividade**
  - 4.1.1.1. Ping — Testa alcançabilidade usando ICMP.
  - 4.1.1.2. TraceRoute — Mapeia os saltos até o destino alterando o TTL.
- **4.1.2. Análise Local e Inspeção**
  - 4.1.2.1. Netstat — Verifica portas abertas e conexões ativas.
  - 4.1.2.2. Wireshark — Sniffer de rede para análise profunda de pacotes.

---

> **Links Relacionados:**
> Internet
> DNS
> NAT
> Firewall

---

Agora gere os arquivos para o tópico [TÓPICO]. Entregue cada arquivo claramente separado, com o nome do arquivo como título antes do conteúdo.
```
