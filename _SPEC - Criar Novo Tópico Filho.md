# Especificação — Criar Novo Tópico Filho

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

## Regra de links no índice

| Nível | Regra |
|---|---|
| `## N Domínio: Nome` | **Texto puro — sem link.** Domínio é uma pasta, não um arquivo linkável |
| `### N.x Nome do Subtópico` | **Texto puro — sem link.** Subtópico pode ser pasta ou agrupador lógico |
| `**N.N.N. Seção**` | Texto puro (bold) |
| `  - N.N.N.N. Descrição` | **Link `[[]]` quando o arquivo existir** — é aqui que o link vai |

> **Regra de link:** o `[[]]` fica no item folha `  - N.N.N.N.`, substituindo o texto puro pela versão linkada.
> Enquanto o arquivo não existir, o item fica como texto puro.
> **Nunca criar `[[link]]` apontando para arquivo inexistente.**

---

## Passo 1 — Atualizar o Glossário pai

No arquivo `0 Glossário/Glossário.md`, localizar o item correspondente e substituir pelo link:

```markdown
- [ ] [[Seção/Subcategoria/Nome/0 Nome (Tópicos )|Nome]]
```

**Exemplo:**
```markdown
- [ ] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/0 DNS (Tópicos )|DNS]]
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

### Template do arquivo índice:

```markdown
# 📚 Guia de Estudos: [Nome do Tópico]
#tag #[categoria] #[subcategoria]

> [!info] Visão Geral
> [2 a 3 linhas explicando o que é, por que importa e o que este guia cobre.]

---

## 1 Domínio: Nome do Domínio

### [[caminho/para/arquivo|1.1 Nome do Subtópico]]
- **1.1.1. [Nome da Seção]**
  - 1.1.1.1. [Descrição do conteúdo.]
  - 1.1.1.2. [Descrição do conteúdo.]

### [[caminho/para/arquivo|1.2 Nome do Subtópico]]
- **1.2.1. [Nome da Seção]**
  - 1.2.1.1. [Descrição do conteúdo.]

---

## 2 Domínio: Nome do Domínio

### [[caminho/para/arquivo|2.1 Nome do Subtópico]]
- **2.1.1. [Nome da Seção]**
  - 2.1.1.1. [Descrição do conteúdo.]

---

> **Links Relacionados:**
> [Tópico relacionado 1]
> [Tópico relacionado 2]
```

> Nos `###` acima, o link aponta para o arquivo folha. Se o subtópico tem uma pasta com múltiplos arquivos, o link aponta para o primeiro ou mais relevante — ou omite o link e mantém texto puro.

---

## Passo 4 — Criar os arquivos de conteúdo (folhas)

Para cada item `N.N.N.N.` no índice, criar um arquivo `.md` com o mesmo nome.

**Nome do arquivo:** `N.N.N.N. Descrição exata como no índice.md`

### Template do arquivo de conteúdo (folha):

```markdown
### [Título descritivo do conteúdo]

[Parágrafo introdutório — 1 a 2 linhas contextualizando dentro do tópico pai.]

---

## [Seção principal]

[Explicação clara e objetiva. Use analogias quando o conceito for abstrato.]

[Tabelas, listas ou diagramas ASCII quando ajudar a visualizar.]

---

## [Seção seguinte]

[Conteúdo.]

---

## 🧩 Quiz de Fixação

1. [Pergunta 1]
2. [Pergunta 2]
3. [Pergunta 3]

**Respostas:**
1) [Resposta]
2) [Resposta]
3) [Resposta]
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
| Link no índice — item folha | `  - [[caminho\|N.N.N.N. Descrição]]` só quando o arquivo existir |
| Seções no índice | `**N.N.N. Título**` (bold, 3 níveis) |
| Itens no índice | `  - N.N.N.N. Descrição` (2 espaços, texto puro até o arquivo existir) |
| Separador entre domínios | `---` |
| Rodapé do índice | `> **Links Relacionados:**` |
| Emoji no título do índice | `📚` fixo |
| Tags | `#tag #categoria #subcategoria` na linha 2 do índice |

---

## Exemplo de uso

**Usuário diz:** "Crie o tópico DNS"

**IA deve:**
1. Localizar `- [ ] DNS` no Glossário e substituir pelo link
2. Criar a pasta `1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/`
3. Criar `0 DNS (Tópicos ).md` com os domínios em texto puro (sem links ainda)
4. Criar as subpastas de domínio: `1. Fundamentos/`, `2. Tipos de Registro/`, etc.
5. Dentro de cada domínio, criar os arquivos folha: `1.1.1.1. O que é DNS.md`, etc.
6. Após criar os arquivos, atualizar o índice adicionando os `[[links]]` nos `###`

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
  3. Transporte e Comunicação (Layer 4)/
    3.1 Protocolos TCP e UDP/
      3.1.1.1. Confiabilidade, Handshake de 3 vias...md
      3.1.2.1. Sem conexão, rápido...md
      3.1.3.1. Diferenças e casos de uso...md
    3.2 Portas e Sockets/
      3.2.1.1. Mapeamento dos serviços principais...md
      3.2.2.1. Como o SO forma o Socket...md
  4. Operação e Troubleshooting/
    4.1 Ferramentas e Exemplos Práticos/
      4.1.1. Rastreamento e Conectividade/
        4.1.1.1. Ping...md
        4.1.1.2. TraceRoute...md
      4.1.2. Análise Local e Inspeção/
        4.1.2.1. Netstat...md
        4.1.2.2. Wireshark...md
```
