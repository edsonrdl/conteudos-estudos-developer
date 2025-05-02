### 1. **Convencional Commits**

> [!NOTE]
> O padrão **Conventional Commits** é amplamente adotado e padroniza a estrutura dos commits. Cada commit segue uma estrutura específica, tornando o histórico mais legível e permitindo gerar automaticamente changelogs.

### Estrutura:

```
tipos:
- feat: nova funcionalidade
- fix: correção de bug
- docs: alterações na documentação
- style: formatação (sem alterações de lógica)
- refactor: refatoração de código (sem alterações externas)
- perf: melhorias de performance
- test: adição ou modificação de testes
- chore: mudanças que não afetam o código (ex: build, configuração, ferramentas)

Exemplo de commit:
feat(auth): adiciona autenticação de dois fatores
fix(cart): corrige erro de cálculo no carrinho de compras

```

### 2. **Gitmoji Commits**

Uma abordagem divertida, onde se usa emojis para descrever o tipo de alteração feita no código.

### Estrutura:

```
:emoji: <mensagem>
```

### Exemplos de emojis:

- 🚀 `:rocket:` - Funcionalidades novas
- 🐛 `:bug:` - Correção de bugs
- ✨ `:sparkles:` - Melhorias
- 📝 `:memo:` - Documentação
- 🔧 `:wrench:` - Ferramentas/configurações

Exemplo:

```
:rocket: Adiciona nova funcionalidade de pagamento
:bug: Corrige erro de exibição na página de login
```

### 3. **Padrão de Commits para Times**

Se o seu time está desenvolvendo um projeto e você deseja seguir um padrão simples e eficaz, uma forma pode ser categorizar os commits com prefixos claros:

### Exemplo de prefixos:

- `feat`: Funcionalidades novas
- `fix`: Correções de bugs
- `docs`: Documentação
- `style`: Estilo e formatação
- `test`: Testes

Exemplo de commits:

```
feat: implementa funcionalidade de login
fix: corrige erro de navegação
docs: atualiza README com novas instruções
```

### 4. **Regras gerais para bons commits**

- **Use o tempo presente**: "Adiciona" em vez de "Adicionado" ou "Adicionou".
- **Seja conciso, mas descritivo**: A mensagem deve ser curta (geralmente menos de 72 caracteres na linha de título) e explicar o que foi feito, sem ser excessivamente vaga.
- **Referencie tickets ou tarefas**: Se o seu projeto utiliza algum sistema de gerenciamento de tarefas, inclua o número do ticket (ex: `fix #123`).
- **Evite mensagens vagas**: Por exemplo, "corrige coisa" ou "ajustes diversos" não são informativas.