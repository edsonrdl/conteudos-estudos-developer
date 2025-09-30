Primeiro, precisamos pedir ao Angular para criar um "relatório" detalhado do seu projeto durante o build de produção.

Abra o terminal na pasta raiz do seu projeto e execute o seguinte comando:

Bash

```
ng build --configuration production --stats-json
```

**O que este comando faz?**

Isso fará exatamente o que o antigo `--prod` fazia (minificar o código, remover código não utilizado, etc.) e também irá gerar o arquivo `stats.json` que você precisa.

Aguarde o processo de build terminar.

---

#### **Passo 2: Execute o Analisador**

Agora, vamos usar o `npx` (que já vem com o Node.js) para rodar o analisador e ler o arquivo que acabamos de criar.

No mesmo terminal, execute o comando abaixo. Lembre-se de substituir `nome-do-seu-projeto` pelo nome real da pasta do seu projeto dentro de `dist/`.

Bash

```
npx webpack-bundle-analyzer ./dist/nome-do-seu-projeto/stats.json
```

**O que este comando faz?**

- `npx webpack-bundle-analyzer`: Baixa e executa temporariamente a ferramenta de análise.
    
- `./dist/nome-do-seu-projeto/stats.json`: Informa à ferramenta qual arquivo ela deve analisar.
    

---

#### **Passo 3: Investigue o Mapa Visual**

Pronto! Uma nova aba deve abrir automaticamente no seu navegador, exibindo um mapa de retângulos coloridos. Este mapa é a sua aplicação.

**Como ler o mapa:**

1. **Encontre os Maiores Retângulos:** Eles representam as maiores partes do seu código. Geralmente são bibliotecas (localizadas dentro de `node_modules`).
    
2. **Passe o Mouse:** Passe o cursor sobre qualquer retângulo para ver detalhes como o nome do arquivo e seu tamanho exato.
    
3. **Identifique os Culpados:** Procure por bibliotecas pesadas que talvez você não precise carregar de início. Exemplos comuns são bibliotecas de gráficos, editores de texto rico, ou manipuladores de datas como o `moment.js`.
    

Com base no que você encontrar, você poderá decidir se vale a pena procurar uma biblioteca alternativa mais leve ou, o que é mais comum, aplicar **Lazy Loading** nas rotas que utilizam esses módulos pesados, para que eles não impactem o carregamento inicial da sua aplicação.