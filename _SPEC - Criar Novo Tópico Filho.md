# Especificação — Criar Novo Tópico Filho

> [!tip] Instrução para a IA
> **Se você já leu este arquivo na sessão atual e conhece o fluxo, não o releia — execute o comando diretamente.**
> Só processe este documento novamente se a sessão for nova ou se houver dúvida sobre alguma regra específica.

Este documento define o padrão para criar um novo tópico de estudo neste workspace.
Quando o usuário disser **"crie o tópico [Nome]"**, a IA deve seguir exatamente esta especificação.

---

## Hierarquia do Workspace

```
Glossário.md                                    ← PAI: índice geral
│
└── [Seção]/[Subcategoria]/[Tópico]/
      │
      ├── 0 [Tópico] (Tópicos ).md              ← ÍNDICE: domínios e subtópicos
      │
      ├── 1. [Domínio A]/                        ← PASTA de domínio (se tiver múltiplos filhos)
      │     ├── 1.1 [Subtópico]/                 ← PASTA de subtópico (se tiver múltiplos filhos)
      │     │     ├── 1.1.1.1. Descrição.md      ← ARQUIVO folha
      │     │     └── 1.1.1.2. Descrição.md
      │     └── 1.2 [Subtópico]/
      │           └── 1.2.1.1. Descrição.md
      │
      └── 2. [Domínio B]/
            └── 2.1.1.1. Descrição.md            ← arquivo único se só um filho
```

---

## Regra fundamental — Pasta ou Arquivo?

**Todo nível estrutural (domínio `##`, subtópico `###`, seção `**`) cria uma pasta, independente de quantos filhos tem.**
Somente o item folha `N.N.N.N.` é um arquivo.

| Nível | Sempre cria |
|---|---|
| `## N Domínio` | Pasta `N. Nome do Domínio/` |
| `### N.x Subtópico` | Pasta `N.x Nome do Subtópico/` dentro do domínio |
| `**N.N.N. Seção**` | Pasta `N.N.N. Nome da Seção/` dentro do subtópico |
| `  - N.N.N.N. Descrição` | Arquivo `N.N.N.N. Descrição.md` dentro da pasta da seção |

Mesmo com um único filho em qualquer nível, a pasta é criada.

---

## Regra de links e checkboxes no índice

| Nível | Regra |
|---|---|
| `## N Domínio: Nome` | **Texto puro — sem link.** Domínio é pasta, não arquivo linkável |
| `### N.x Nome do Subtópico` | **Texto puro — sem link.** Subtópico é agrupador lógico |
| `**N.N.N. Seção**` | Texto puro (bold) |
| `  - [ ] N.N.N.N. Descrição` | **Checkbox desmarcado** enquanto o arquivo ainda não existir |
| `  - [x] [[caminho\|N.N.N.N. Descrição]]` | **Checkbox marcado + link** após o arquivo ser criado |

> **Regra de checkbox:**
> - `- [ ]` = conteúdo pendente de criação (texto puro, sem link)
> - `- [x]` = conteúdo já criado (com link `[[]]` para o arquivo)
> **Nunca criar `[[link]]` apontando para arquivo inexistente.**

---

## ⚙️ Fluxo de Criação — Passo a Passo

A criação de um tópico acontece em **duas fases distintas**, com confirmação do usuário entre cada arquivo de conteúdo.

### FASE 1 — Estrutura (automática, sem confirmação)

Executar imediatamente ao receber o pedido:

1. **Atualizar o Glossário** — substituir o item de texto pelo link correto
2. **Criar todas as pastas** da estrutura do tópico
3. **Criar o arquivo índice** `0 [Nome] (Tópicos ).md` com todos os itens como `- [ ]` (checkboxes desmarcados, sem links)

Ao final da Fase 1, exibir a estrutura criada e perguntar:

> **"Estrutura criada! Quer que eu implemente o conteúdo `1.1.1.1. [nome]` detalhado?"**

### FASE 2 — Conteúdo (um arquivo por vez, com confirmação)

Para cada arquivo folha `N.N.N.N.`, **aguardar confirmação** antes de criar:

1. Receber confirmação do usuário
2. Criar o arquivo com **conteúdo detalhado** (ver padrão abaixo)
3. **Atualizar o índice**: trocar `- [ ] N.N.N.N. Descrição` por `- [x] [[caminho|N.N.N.N. Descrição]]`
4. Perguntar ao usuário se deseja criar o próximo (ver regra abaixo)

Repetir até o último arquivo. Ao final:

> **"Tópico [Nome] concluído! Todos os [N] arquivos foram criados."**

### Regra de pergunta para o próximo conteúdo

Ao perguntar se o usuário quer criar o próximo item, a IA deve **avaliar a complexidade** do próximo conteúdo e formular a pergunta de forma diferente conforme o caso:

#### Caso A — Próximo é um arquivo folha simples

```
"Quer que eu implemente o próximo conteúdo detalhado:
`N.N.N.N. [Descrição]` — [breve preview do que será coberto]?"
```

#### Caso B — Próximo é um serviço/tema complexo que merece sub-glossário

Se o próximo item é um **serviço qualquer complexo** ou **tema com 5+ conceitos distintos**, indicar claramente que será um sub-glossário:

```
"Quer que eu implemente o próximo:
`N.N.N.N. [Nome do Serviço]` — como **sub-glossário** (pasta com índice próprio
e conteúdos organizados em domínios), igual fizemos com EC2 e Lambda?"
```

Critério para propor sub-glossário (avaliar as três condições):
1. O item tem **5 ou mais conceitos distintos** que merecem arquivos separados
2. Tem **múltiplos domínios** naturais (fundamentos, configuração, casos de uso, etc.)
3. O estudo aprofundado **não cabe num único arquivo** sem ficar superficial

Exemplos que **devem** ser sub-glossário:
- Serviços AWS principais (EC2, Lambda, RDS, VPC, S3, ECS, EKS, DynamoDB...)
- Tópicos amplos de infraestrutura (Kubernetes, Terraform, Docker...)
- Frameworks grandes (Spring Boot, Django, React...)

Exemplos que **não precisam** de sub-glossário (arquivo folha basta):
- Conceitos pontuais (On-Demand Pricing, MFA, Elastic IP...)
- Comparativos (EC2 vs Lambda, SQL vs NoSQL...)
- Funcionalidades específicas (Lambda SnapStart, RDS Multi-AZ...)

---

## Padrão de Qualidade do Conteúdo

Cada arquivo folha deve ser **detalhado e didático**. Não há limite mínimo de tamanho — o conteúdo deve cobrir o tema com profundidade suficiente para que o leitor entenda sem precisar de fontes externas.

### Obrigatório em todo arquivo folha

| Elemento | Descrição |
|---|---|
| **Título descritivo** (`###`) | Contextualiza o arquivo dentro do tópico pai |
| **Parágrafo introdutório** | 2-3 linhas situando o conteúdo |
| **Explicação principal** | Conceito explicado com clareza, sem superficialidade |
| **Analogias** | Quando o conceito for abstrato, use analogia concreta |
| **Diagramas ASCII** | Para fluxos, arquiteturas, comparativos visuais |
| **Tabelas** | Para comparativos, listas de propriedades, mapeamentos |
| **Exemplos práticos** | Código, comandos, configurações reais quando aplicável |
| **Casos de uso / Quando usar** | Contexto de aplicação real |
| **Erros comuns / Limitações** | O que não fazer, armadilhas, trade-offs |
| **Quiz de Fixação** | 3 perguntas com respostas |

### Diretrizes de profundidade

- **Não resumir** — se o tema tem nuances, explique todas
- **Não assumir conhecimento prévio** — cada arquivo deve ser autocontido
- **Exemplos reais** — preferir exemplos de tecnologias conhecidas (AWS, Linux, HTTP, etc.)
- **Diagramas sempre que houver fluxo ou arquitetura** envolvida
- **Comparativos** quando o tema se contrapõe a alternativas

---

## Passo 1 — Atualizar o Glossário pai

### Estados do item no Glossário

O checkbox do Glossário reflete o estado geral do tópico:

| Estado | Formato | Significado |
|---|---|---|
| Não iniciado | `- [ ] Nome do Tópico` | Nenhum arquivo criado ainda |
| Em andamento | `- [ ] [[caminho/0 Nome (Tópicos )\|Nome]]` | Estrutura criada, conteúdos em produção |
| Concluído | `- [x] [[caminho/0 Nome (Tópicos )\|Nome]]` | **Todos** os arquivos folha criados |

**Fluxo no Glossário:**
1. **Ao criar a estrutura (Fase 1):** `- [ ] Nome` → `- [ ] [[caminho|Nome]]` (link adicionado, checkbox ainda desmarcado)
2. **Durante a criação de conteúdos (Fase 2):** permanece `- [ ] [[caminho|Nome]]`
3. **Ao criar o último arquivo folha:** `- [ ] [[caminho|Nome]]` → `- [x] [[caminho|Nome]]` ✓

No arquivo `0 Glossário/Glossário.md`, localizar o item e substituir pelo link:

```markdown
- [ ] [[Seção/Subcategoria/Nome/0 Nome (Tópicos )|Nome]]
```

**Exemplo após criar a estrutura:**
```markdown
- [ ] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/0 DNS (Tópicos )|DNS]]
```

**Exemplo após criar todos os conteúdos:**
```markdown
- [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/0 DNS (Tópicos )|DNS]]
```

---

## Passo 2 — Criar a pasta do tópico e subpastas

Criar a estrutura de pastas de acordo com a regra "pasta ou arquivo":

```
[Seção]/[Subcategoria]/[Tópico]/
  1. [Domínio A]/
    1.1 [Subtópico]/
    1.2 [Subtópico]/
  2. [Domínio B]/
  ...
```

---

## Passo 3 — Criar o arquivo índice

**Nome:** `0 [Nome do Tópico] (Tópicos ).md`
> Manter o espaço antes do `.md` — faz parte do padrão de nomenclatura.

### Template do arquivo índice (com checkboxes desmarcados):

```markdown
# 📚 Guia de Estudos: [Nome do Tópico]
#tag #[categoria] #[subcategoria]

> [!info] Visão Geral
> [2 a 3 linhas explicando o que é, por que importa e o que este guia cobre.]

---

## 1 Domínio: Nome do Domínio

### 1.1 Nome do Subtópico
- **1.1.1. [Nome da Seção]**
  - [ ] 1.1.1.1. [Descrição do conteúdo.]
  - [ ] 1.1.1.2. [Descrição do conteúdo.]

### 1.2 Nome do Subtópico
- **1.2.1. [Nome da Seção]**
  - [ ] 1.2.1.1. [Descrição do conteúdo.]

---

## 2 Domínio: Nome do Domínio

### 2.1 Nome do Subtópico
- **2.1.1. [Nome da Seção]**
  - [ ] 2.1.1.1. [Descrição do conteúdo.]

---

> **Links Relacionados:**
> [Tópico relacionado 1]
> [Tópico relacionado 2]
```

> Todos os itens folha começam como `- [ ]` (sem link). O link e o `[x]` são adicionados após a criação do arquivo na Fase 2.

---

## Passo 4 — Criar os arquivos de conteúdo (folhas)

Para cada item `N.N.N.N.` no índice, criar um arquivo `.md` **detalhado** após confirmação do usuário.

**Nome do arquivo:** `N.N.N.N. Descrição exata como no índice.md`

### Template do arquivo de conteúdo (folha):

```markdown
### [Título descritivo do conteúdo]

[Parágrafo introdutório — 2 a 3 linhas contextualizando dentro do tópico pai.]

---

## [Seção principal — o conceito central]

[Explicação completa e aprofundada. Não resumir — detalhar.
Use analogias quando o conceito for abstrato.
Explique o "por quê" antes do "como".]

[Diagrama ASCII se houver fluxo, arquitetura ou estrutura:]
```
[diagrama]
```

---

## [Como funciona / Funcionamento interno]

[Explicação do mecanismo. Vá fundo — não fique na superfície.]

[Tabela de propriedades, modos ou variações se aplicável:]

| Propriedade | Valor | Descrição |
|---|---|---|

---

## [Exemplos práticos / Casos de uso]

[Exemplo real com código, comando ou configuração quando aplicável:]
```
[código/comando/configuração]
```

[Explicação linha a linha do exemplo quando necessário.]

---

## [Comparativo / Quando usar / Limitações]

[Trade-offs, alternativas, erros comuns, o que não fazer.]

| Aspecto | Opção A | Opção B |
|---|---|---|

---

## 🧩 Quiz de Fixação

1. [Pergunta conceitual]
2. [Pergunta de aplicação]
3. [Pergunta de análise / comparação]

**Respostas:**
1) [Resposta completa, não só sim/não]
2) [Resposta completa]
3) [Resposta completa]
```

---

## Passo 5 — Atualizar o índice após criar cada arquivo

Após criar cada arquivo folha, **editar o índice** trocando o checkbox:

```markdown
ANTES (pendente):
  - [ ] 1.1.1.1. Descrição do conteúdo.

DEPOIS (criado):
  - [x] [[caminho/completo/para/arquivo|1.1.1.1. Descrição do conteúdo]]
```

---

## Regras gerais

| Elemento | Padrão |
|---|---|
| Nome do arquivo índice | `0 [Nome] (Tópicos ).md` |
| Nome das pastas de domínio | `N. Nome do Domínio/` (numerado, sem `.md`) |
| Nome das pastas de subtópico | `N.x Nome do Subtópico/` (quando tiver múltiplos filhos) |
| Nome dos arquivos folha | `N.N.N.N. Descrição exata como no índice.md` |
| Link no índice — domínio `##` | **Texto puro sempre** — domínio é pasta, não arquivo |
| Link no índice — subtópico `###` | **Texto puro sempre** — subtópico é agrupador lógico |
| Item pendente no índice | `  - [ ] N.N.N.N. Descrição` (checkbox desmarcado) |
| Item criado no índice | `  - [x] [[caminho\|N.N.N.N. Descrição]]` (checkbox marcado + link) |
| Seções no índice | `**N.N.N. Título**` (bold, 3 níveis) |
| Separador entre domínios | `---` |
| Rodapé do índice | `> **Links Relacionados:**` |
| Emoji no título do índice | `📚` fixo |
| Tags | `#tag #categoria #subcategoria` na linha 2 do índice |

---

## Exemplo de uso

**Usuário diz:** "Crie o tópico DNS"

**IA deve:**

**FASE 1 (imediata):**
1. Atualizar o Glossário com o link para DNS
2. Criar toda a estrutura de pastas do DNS
3. Criar `0 DNS (Tópicos ).md` com todos os itens como `- [ ]`
4. Perguntar: *"Estrutura criada! Quer que eu implemente o conteúdo `1.1.1.1. O que é DNS...` detalhado?"*

**FASE 2 (um por um):**
5. Ao confirmar: criar `1.1.1.1. O que é DNS.md` com conteúdo detalhado
6. Atualizar índice: `- [ ] 1.1.1.1.` → `- [x] [[caminho|1.1.1.1.]]`
7. Perguntar: *"Criado! Quer que eu implemente o próximo: `1.2.1.1. Hierarquia DNS...`?"*
8. Repetir até o último arquivo

---

## Quando criar sub-glossário dentro de um tópico

Para tópicos que são **serviços complexos** (ex: Amazon EC2, AWS Lambda, Amazon RDS, Amazon VPC), em vez de criar um único arquivo folha com todo o conteúdo, crie uma pasta com um sub-glossário próprio seguindo exatamente o mesmo padrão:

```
3.3.1. Computação/
  3.3.1.1. Amazon EC2/            ← pasta do serviço (não arquivo)
    0 Amazon EC2 (Tópicos ).md   ← sub-glossário com [ ] em todos os itens
    1. Fundamentos/
      1.1 O que é EC2/
        ...
    2. Tipos de Instância/
      ...
    3. Modelos de Compra/
      ...
```

No glossário pai, o link aponta para o sub-glossário (não para arquivo de conteúdo):

```markdown
- [ ] [[caminho/3.3.1.1. Amazon EC2/0 Amazon EC2 (Tópicos )|3.3.1.1. Amazon EC2]]
```

Critério para usar sub-glossário:
- O serviço tem 5+ conceitos distintos que merecem arquivos separados
- O tema é amplo o suficiente para ter domínios (fundamentos, configuração, casos de uso, etc.)
- O estudo do serviço em profundidade exige mais do que um arquivo pode cobrir

---

## Referência real

`1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/` é o exemplo canônico desta arquitetura.
Estrutura atual do TCP IP:
```
TCP IP/
  0 TCP IP (Tópicos ).md
  1. Fundamentos e Arquitetura/
    1.1.1.1. Explicação do TCP IP...md
  2. Endereçamento e Roteamento/
    2.1 Endereçamento IP/
      2.1.1. Classes Tradicionais (A, B, C)/
        2.1.1.1. O que são e como dividem...md
        2.1.1.2. Intervalos numéricos...md
        2.1.1.3. Quando usar cada uma...md
      2.1.2. Máscara de Rede/
        2.1.2.1. Explicação simples...md
        2.1.2.2. Exemplos práticos...md
      2.1.3. CIDR/
        2.1.3.1. Por que foi criado...md
        2.1.3.2. Exemplo prático...md
    2.2 Redes e Sub-redes/
      2.2.1.1. Diferença entre Rede principal e Sub-rede.md
      2.2.2.1. Conceito e necessidade de segmentação...md
      2.2.3.1. Como calcular sub-redes...md
      2.2.4.1. Como otimizar o desperdício de IPs...md
    IPs Reservados.md
```
