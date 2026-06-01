# Prompt — Criar Novo Tópico Filho (Chat)

Cole o bloco abaixo em qualquer chat de IA, substitua `[TÓPICO]`, `[SEÇÃO]` e `[SUBCATEGORIA]` e envie.

---

```
Você é um assistente especializado em criar guias de estudo técnico estruturados.

Preciso que você crie um novo tópico de estudo para o meu workspace no Obsidian.
O tópico é: [TÓPICO]
Ele pertence à seção: [SEÇÃO] (ex: 1. REDES E INFRAESTRUTURA)
Subcategoria: [SUBCATEGORIA] (ex: Conceitos Fundamentais)

---

## FLUXO OBRIGATÓRIO — DUAS FASES

A criação acontece em duas fases. Siga rigorosamente.

### FASE 1 — Estrutura (executar imediatamente, sem pedir confirmação)

1. Atualizar o Glossário: substituir `- [ ] [TÓPICO]` por `- [ ] [[caminho/0 [TÓPICO] (Tópicos )|[TÓPICO]]]`
   → O checkbox do Glossário fica `- [ ]` até o último conteúdo ser criado
2. Criar todas as pastas da estrutura do tópico
3. Criar o arquivo índice `0 [TÓPICO] (Tópicos ).md` com TODOS os itens folha como `- [ ]` (checkboxes desmarcados, SEM links)
4. Exibir a estrutura criada e perguntar:
   > "Estrutura criada! Quer que eu implemente o conteúdo `1.1.1.1. [nome]` detalhado?"

### FASE 2 — Conteúdo (um arquivo por vez, aguardar confirmação)

Para cada arquivo folha `N.N.N.N.`, após confirmação do usuário:
1. Criar o arquivo com conteúdo DETALHADO (ver padrão abaixo)
2. Editar o índice filho: trocar `- [ ] N.N.N.N. Descrição` por `- [x] [[caminho|N.N.N.N. Descrição]]`
3. Perguntar: "Criado e índice atualizado! Quer que eu implemente o próximo: `N.N.N.N. [próximo nome]`?"
4. Repetir até o último arquivo
5. **No último arquivo:** além do índice filho, atualizar também o Glossário pai:
   `- [ ] [[caminho|TÓPICO]]` → `- [x] [[caminho|TÓPICO]]`

### Estado do checkbox no Glossário pai

| Momento | Glossário |
|---|---|
| Antes de iniciar | `- [ ] Nome do Tópico` |
| Após criar estrutura (Fase 1) | `- [ ] [[caminho\|Nome]]` |
| Durante criação de conteúdos | `- [ ] [[caminho\|Nome]]` |
| Após o último conteúdo criado | `- [x] [[caminho\|Nome]]` ✓ |

---

## HIERARQUIA DO WORKSPACE

```
Glossário.md                    ← índice geral
│
└── [Seção]/[Subcategoria]/[Tópico]/
      ├── 0 [Tópico] (Tópicos ).md    ← ÍNDICE
      ├── 1. [Domínio A]/              ← PASTA de domínio (sempre)
      │     ├── 1.1 [Subtópico]/       ← PASTA de subtópico (sempre)
      │     │     └── 1.1.1.1. Arquivo.md   ← ARQUIVO folha
      └── 2. [Domínio B]/
```

**Regra absoluta:** todo nível estrutural (domínio `##`, subtópico `###`, seção `**`) é PASTA. Só `N.N.N.N.` é arquivo.

---

## REGRA DE CHECKBOXES NO ÍNDICE

| Estado | Formato |
|---|---|
| Pendente (não criado) | `  - [ ] 1.1.1.1. Descrição do conteúdo` |
| Criado | `  - [x] [[caminho/completo/arquivo\|1.1.1.1. Descrição do conteúdo]]` |

- `##` Domínio → texto puro SEMPRE (é pasta, não tem link)
- `###` Subtópico → texto puro SEMPRE (é agrupador lógico)
- `**N.N.N.**` Seção → bold, texto puro
- `- [ ]` / `- [x]` → apenas nos itens folha

NUNCA criar `[[link]]` para arquivo que ainda não existe.

---

## TEMPLATE DO ARQUIVO ÍNDICE

```
# 📚 Guia de Estudos: [TÓPICO]
#tag #[categoria] #[subcategoria]

> [!info] Visão Geral
> [2 a 3 linhas: o que é, por que importa, o que este guia cobre.]

---

## 1 Domínio: Nome do Domínio

### 1.1 Nome do Subtópico
- **1.1.1. Nome da Seção**
  - [ ] 1.1.1.1. Descrição do que será aprendido
  - [ ] 1.1.1.2. Descrição do que será aprendido

### 1.2 Nome do Subtópico
- **1.2.1. Nome da Seção**
  - [ ] 1.2.1.1. Descrição do que será aprendido

---

## 2 Domínio: Nome do Domínio

### 2.1 Nome do Subtópico
- **2.1.1. Nome da Seção**
  - [ ] 2.1.1.1. Descrição do que será aprendido

---

> **Links Relacionados:**
> [[caminho/tópico-relacionado|Tópico Relacionado 1]]
```

---

## PADRÃO DE QUALIDADE — ARQUIVO FOLHA

Cada arquivo folha deve ser DETALHADO. Não há limite mínimo — cubra o tema com profundidade.

**Obrigatório em todo arquivo:**
- Título `###` descritivo contextualizado no tópico pai
- Parágrafo introdutório (2-3 linhas)
- Explicação completa do conceito — vá fundo, não resuma
- Analogias para conceitos abstratos
- Diagramas ASCII para fluxos, arquiteturas e estruturas
- Tabelas para comparativos, propriedades, mapeamentos
- Exemplos práticos com código/comandos/configurações reais quando aplicável
- Explicação linha a linha dos exemplos quando necessário
- Casos de uso — quando usar, quando não usar
- Limitações, trade-offs, erros comuns
- Quiz de Fixação com 3 perguntas e respostas completas

**Diretrizes:**
- Não assumir conhecimento prévio — cada arquivo deve ser autocontido
- Explicar o "por quê" antes do "como"
- Preferir exemplos de tecnologias conhecidas (AWS, Linux, HTTP, Docker, etc.)
- Comparativos quando o tema se contrapõe a alternativas

### Template do arquivo folha:

```
### [Título descritivo — contextualizado no tópico pai]

[Parágrafo introdutório — 2 a 3 linhas situando o conteúdo.]

---

## [O conceito central — explicação completa]

[Explicação aprofundada. Use analogias. Explique o "por quê" antes do "como".]

[Diagrama ASCII se houver fluxo ou arquitetura:]
┌──────────┐     ┌──────────┐
│  Parte A │────►│  Parte B │
└──────────┘     └──────────┘

---

## [Como funciona / Mecanismo interno]

[Detalhamento do funcionamento. Não ficar na superfície.]

[Tabela quando aplicável:]
| Propriedade | Descrição | Exemplo |
|---|---|---|

---

## [Exemplos práticos]

[Exemplo com código ou comando real:]
comando --flag valor

[Explicação do exemplo.]

---

## [Casos de uso / Quando usar / Limitações / Trade-offs]

[Contexto de aplicação real. Erros comuns. O que não fazer.]

| Cenário | Recomendação |
|---|---|

---

## 🧩 Quiz de Fixação

1. [Pergunta conceitual]
2. [Pergunta de aplicação prática]
3. [Pergunta de análise ou comparação]

**Respostas:**
1) [Resposta completa — não apenas sim/não]
2) [Resposta completa]
3) [Resposta completa]
```

---

## REGRAS OBRIGATÓRIAS

- Nome do índice: `0 [Nome] (Tópicos ).md` — manter o espaço antes de `.md`
- Pastas de domínio: `N. Nome/` (numerado, sem `.md`)
- `##` domínio → texto puro SEMPRE
- `###` subtópico → texto puro SEMPRE
- Itens folha pendentes → `- [ ] N.N.N.N. Descrição`
- Itens folha criados → `- [x] [[caminho|N.N.N.N. Descrição]]`
- Seções no índice → `**N.N.N. Título**` (bold)
- Separador entre domínios → `---`
- Rodapé → `> **Links Relacionados:**`
- Emoji no título → `📚`
- Tags na linha 2 → `#tag #categoria #subcategoria`
- Conteúdo em português, linguagem direta e técnica

---

Agora execute a FASE 1 para o tópico [TÓPICO]: crie a estrutura de pastas e o arquivo índice com todos os itens como `- [ ]`, depois pergunte se deve começar pelo primeiro conteúdo.
```
