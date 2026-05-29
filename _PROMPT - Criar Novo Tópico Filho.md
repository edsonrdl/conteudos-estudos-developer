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

Meu workspace tem 4 níveis:
1. Glossário.md — índice geral com todos os tópicos
2. 0 [Nome] (Tópicos ).md — índice do tópico: lista domínios, subtópicos e itens
3. N. [Domínio]/ — PASTA por domínio (nunca um arquivo)
4. N.N.N.N. Descrição.md — arquivo folha com o conteúdo real

Dentro das pastas de domínio pode haver subpastas de subtópico quando necessário.

---

## REGRA — PASTA OU ARQUIVO?

- Se um nó tem múltiplos filhos → criar PASTA
- Se um nó tem apenas um filho → criar ARQUIVO DIRETAMENTE (sem pasta intermediária)

Exemplos:
- Domínio com 3 subtópicos → pasta do domínio com 3 arquivos (ou subpastas)
- Domínio com 1 subtópico com 1 item → pasta do domínio + arquivo único
- Subtópico com 3 itens N.N.N.N. → subpasta do subtópico com 3 arquivos
- Subtópico com 1 item N.N.N.N. → apenas 1 arquivo direto na pasta do domínio

---

## REGRA FUNDAMENTAL DE LINKS

O link no arquivo índice (nível 2) fica SEMPRE no `###` (nível de subtópico), apontando para o arquivo folha.
O `##` de domínio é TEXTO PURO — domínio é pasta, não arquivo, então não pode ter link.

ERRADO:
## [[link|1 Domínio: Fundamentos]]   ← link no domínio, ERRADO (domínio é pasta)
### [[link|1.1 Subtópico A]]         ← link no subtópico, ERRADO

CERTO (com arquivo criado):
## 1 Domínio: Fundamentos                  ← texto puro sempre
### 1.1 Subtópico A                        ← texto puro sempre
- **1.1.1. Seção**
  - [[caminho|1.1.1.1. Descrição]]        ← link no item folha, apenas se o arquivo existir

CERTO (sem arquivo ainda):
## 1 Domínio: Fundamentos            ← texto puro
### 1.1 Subtópico A                  ← texto puro
- **1.1.1. Seção**
  - 1.1.1.1. Descrição               ← texto puro

NUNCA criar [[link]] apontando para arquivo que ainda não existe.

---

## O QUE VOCÊ DEVE GERAR

### ARQUIVO 1 — Índice do tópico
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
  - 1.1.1.2. [O que será aprendido]

### 1.2 Nome do Subtópico
- **1.2.1. [Seção]**
  - 1.2.1.1. [O que será aprendido]

---

## 2 Domínio: Nome do Domínio

### 2.1 Nome do Subtópico
- **2.1.1. [Seção]**
  - 2.1.1.1. [O que será aprendido]

---

> **Links Relacionados:**
> [Tópico relacionado 1]
> [Tópico relacionado 2]

---

### ARQUIVOS 2 em diante — Arquivos folha (N.N.N.N.)

Para cada item `N.N.N.N.` no índice, gerar um arquivo com esse nome exato.
Caminho: dentro da pasta do domínio (e subpasta do subtópico se tiver múltiplos itens).

Siga EXATAMENTE este template para cada arquivo folha:

---
### [Título descritivo]

[Parágrafo introdutório curto contextualizando dentro do tópico pai]

---

## [Seção principal]

[Explicação clara. Use analogias quando o conceito for abstrato. Tabelas e diagramas ASCII quando ajudarem.]

---

## [Seção seguinte]

[Conteúdo]

---

## 🧩 Quiz de Fixação

1. [Pergunta 1]
2. [Pergunta 2]
3. [Pergunta 3]

**Respostas:**
1) [Resposta]
2) [Resposta]
3) [Resposta]

---

## REGRAS OBRIGATÓRIAS

- O nome do arquivo índice SEMPRE termina com `(Tópicos ).md` — manter o espaço antes do `.md`
- Pastas de domínio SEMPRE numeradas: `1. Nome/`, `2. Nome/`
- **`##` domínio é SEMPRE texto puro** — `## N Domínio: Nome`. Domínio é pasta, nunca tem link
- **`###` subtópico é SEMPRE texto puro** — nunca recebe link
- **Item folha `  - N.N.N.N.`** recebe link `[[caminho|N.N.N.N. Descrição]]` APENAS após o arquivo existir
- Nome dos arquivos folha: exatamente `N.N.N.N. Descrição como no índice.md`
- Seções no índice: `**N.N.N. Título**` (bold, 3 níveis)
- Itens no índice: `  - N.N.N.N. Descrição` (2 espaços de indentação)
- Separador entre domínios: `---`
- Rodapé do índice: `> **Links Relacionados:**` com tópicos do mesmo universo
- Emoji fixo no título do índice: `📚`
- Tags na linha 2 do índice: `#tag #categoria #subcategoria`
- Conteúdo em português, linguagem direta e técnica
- Não adicionar conteúdo que não esteja no template acima

---

## ESTRUTURA DE EXEMPLO REAL — TCP IP

Este é o tópico canônico que implementa esta arquitetura:

TCP IP/
  0 TCP IP (Tópicos ).md           ← índice
  1. Fundamentos e Arquitetura/    ← pasta de domínio
    1.1.1.1. Explicação do TCP IP...md   ← arquivo único (domínio tinha 1 filho)
  2. Endereçamento e Roteamento/   ← pasta de domínio
    2.1 Endereçamento IP/          ← subpasta (subtópico com múltiplos filhos)
      2.1.1. Classes Tradicionais (A, B, C)/
        2.1.1.1. O que são...md
        2.1.1.2. Intervalos numéricos...md
        2.1.1.3. Quando usar cada uma...md
      2.1.2. Máscara de Rede/
        2.1.2.1. Explicação simples...md
        2.1.2.2. Exemplos práticos...md
    2.2 Redes e Sub-redes/
      2.2.1.1. Diferença entre Rede principal e Sub-rede.md  ← sem subpasta (1 filho)
      2.2.2.1. Conceito e necessidade de segmentação...md
  3. Transporte e Comunicação (Layer 4)/
    3.1 Protocolos TCP e UDP/
      3.1.1.1. Confiabilidade, Handshake...md
      3.1.2.1. Sem conexão, rápido...md
    3.2 Portas e Sockets/
      3.2.1.1. Mapeamento dos serviços...md
      3.2.2.1. Como o SO forma o Socket...md
  4. Operação e Troubleshooting/
    4.1 Ferramentas e Exemplos Práticos/
      4.1.1. Rastreamento e Conectividade/
        4.1.1.1. Ping...md
        4.1.1.2. TraceRoute...md
      4.1.2. Análise Local e Inspeção/
        4.1.2.1. Netstat...md
        4.1.2.2. Wireshark...md

Agora gere os arquivos para o tópico [TÓPICO]. Entregue cada arquivo claramente separado, com o caminho completo como título antes do conteúdo.
```
