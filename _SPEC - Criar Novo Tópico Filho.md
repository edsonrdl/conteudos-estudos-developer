# Especificação — Criar Novo Tópico Filho

Este documento define o padrão para criar um novo tópico de estudo neste workspace.
Quando o usuário disser **"crie o tópico [Nome]"**, a IA deve seguir exatamente esta especificação.

---

## Hierarquia do Workspace

```
Glossário.md                         ← PAI: índice geral, lista todos os tópicos
│
└── [Seção]/[Subcategoria]/[Nome]/
      │
      ├── 0 [Nome] (Tópicos ).md     ← FILHO: índice do tópico (domínios + subtópicos)
      └── N. [Domínio].md            ← NETO: um arquivo por domínio
```

> **Regra fundamental:** cada `## N Domínio` do arquivo índice (filho) linka para UM arquivo (neto).
> Os `### N.x` dentro do índice são texto puro — descrevem o conteúdo do arquivo do domínio, sem links próprios.
>
> **Regra de link:** o `[[]]` no `## N Domínio` só deve ser adicionado quando o arquivo de conteúdo do domínio já existir.
> Enquanto o arquivo não existir, o domínio fica como texto puro: `## N Domínio: Nome`.
> Nunca criar link apontando para arquivo inexistente.

---

## Passo 1 — Atualizar o Glossário pai

No arquivo `0 Glossário/Glossário.md`, localizar o item correspondente ao tópico (que está como `- [ ] Nome`) e substituir pelo link:

```markdown
- [ ] [[Seção/Subcategoria/Nome/0 Nome (Tópicos )|Nome]]
```

**Exemplo:**
```markdown
- [ ] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/0 DNS (Tópicos )|DNS]]
```

---

## Passo 2 — Criar a pasta do tópico

Criar a estrutura de pastas dentro da seção correta:

```
[Número]. [NOME DA SEÇÃO]/[Subcategoria]/[Nome do Tópico]/
```

**Exemplo:** `1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/`

---

## Passo 3 — Criar o arquivo índice (Glossário Filho)

**Nome do arquivo:** `0 [Nome do Tópico] (Tópicos ).md`
> Atenção: manter o espaço antes do `.md` — faz parte do padrão de nomenclatura.

### Template do arquivo índice:

```markdown
# 📚 Guia de Estudos: [Nome do Tópico]
#tag #[categoria] #[subcategoria]

> [!info] Visão Geral
> [Parágrafo de 2 a 3 linhas explicando o que é o tópico, por que ele importa e o que este guia cobre.]

---

## 1 Domínio: Nome do Domínio

### 1.1 Nome do Subtópico
- **1.1.1. [Nome da Seção]**
  - 1.1.1.1. [Descrição do que será aprendido nesta seção.]
- **1.1.2. [Nome da Seção]**
  - 1.1.2.1. [Descrição.]

### 1.2 Nome do Subtópico
- **1.2.1. [Nome da Seção]**
  - 1.2.1.1. [Descrição.]

---

## 2 Domínio: Nome do Domínio

### 2.1 Nome do Subtópico
- **2.1.1. [Nome da Seção]**
  - 2.1.1.1. [Descrição.]

### 2.2 Nome do Subtópico
- **2.2.1. [Nome da Seção]**
  - 2.2.1.1. [Descrição.]

---

## 3 Domínio: Nome do Domínio

### 3.1 Nome do Subtópico
- **3.1.1. [Nome da Seção]**
  - 3.1.1.1. [Descrição.]

---

> **Links Relacionados:**
> [Tópico relacionado 1]
> [Tópico relacionado 2]
> [Tópico relacionado 3]
```

---

## Passo 4 — Criar os arquivos de conteúdo (Netos)

Para cada **domínio** listado no índice, criar UM arquivo `.md`.
Um arquivo por domínio — os subtópicos `### N.x` são seções dentro desse arquivo, não arquivos separados.

**Nome do arquivo:** `N. [Nome do Domínio].md`

### Template do arquivo de conteúdo (domínio):

```markdown
[Parágrafo introdutório — 1 a 2 linhas contextualizando o domínio dentro do tópico pai.]

---

### 🔹 N.1 [Título do Subtópico]

[Explicação clara e objetiva. Use analogias simples quando o conceito for abstrato.]

[Tabelas, listas ou diagramas ASCII quando ajudar a visualizar.]

---

### 🔹 N.2 [Título do Subtópico]

[Conteúdo.]

---

### 🧩 Quiz de Fixação

1. [Pergunta 1]
2. [Pergunta 2]
3. [Pergunta 3]

**Respostas:** 1) [Resposta], 2) [Resposta], 3) [Resposta].

---

**[Pergunta aberta convidando a aprofundar ou seguir para o próximo domínio.]**
```

---

## Regras gerais

| Elemento | Padrão |
|---|---|
| Nome do arquivo índice | `0 [Nome] (Tópicos ).md` |
| Nome dos arquivos de domínio | `N. [Nome do Domínio].md` (numerados a partir de 1) |
| Link no índice | **No `##` domínio, apenas quando o arquivo existir** — `## [[caminho\|N Domínio: Nome]]`. Sem arquivo → texto puro `## N Domínio: Nome` |
| Subtópicos no índice | `### N.x Nome` — texto puro, **sem link** |
| Seções dentro do subtópico no índice | `**N.N.N. Título**` (bold, numerado) |
| Itens dentro da seção no índice | `  - N.N.N.N. Descrição` (2 espaços de indentação) |
| Seções dentro do arquivo de domínio | `### 🔹 N.x Título` |
| Quiz no arquivo de domínio | `### 🧩 Quiz de Fixação` |
| Separador entre domínios no índice | `---` |
| Rodapé do índice | `> **Links Relacionados:**` seguido dos tópicos relacionados |
| Emoji no título do índice | `📚` fixo |
| Tags | `#tag #categoria #subcategoria` na linha 2 do índice |

---

## Exemplo de uso

**Usuário diz:** "Crie o tópico DNS"

**IA deve:**
1. Localizar `- [ ] DNS` no Glossário e substituir pelo link
2. Criar a pasta `1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/`
3. Criar `0 DNS (Tópicos ).md` com domínios (ex: Fundamentos, Tipos de Registro, Resolução, Segurança DNS)
4. Para cada domínio, criar UM arquivo de conteúdo: `1. Fundamentos.md`, `2. Tipos de Registro.md`, etc.
5. No índice, o `##` de cada domínio linka para seu respectivo arquivo **após criá-lo** — enquanto não existir, fica texto puro. Os `###` dentro são sempre texto puro

---

## Referência

Template baseado em: `1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/0 TCP IP (Tópicos ).md`
