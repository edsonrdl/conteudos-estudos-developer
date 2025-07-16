## 1. O que é o Paradigma Estruturado

O paradigma estruturado foca em três construções básicas de fluxo de controle:

1. **Sequência**: execução de comandos um após o outro.
2. **Seleção**: escolha de caminhos com `if/else` ou `switch`.
3. **Repetição**: laços como `for`, `while` e `do/while`.

Ele desencoraja o uso de `goto`, promovendo código mais legível e previsível.

> Analogia: é como seguir uma receita de bolo — você segue cada passo na ordem, decide algo (ex.: “se a massa estiver seca, adicione leite”), e repete ações (como bater a massa 100 vezes).

---

## 2. Exemplo em C

```c
#include <stdio.h>

// Função que calcula a soma de 1 até n
int somaAte(int n) {
    int total = 0;
    for (int i = 1; i <= n; i++) {      // Repetição
        total += i;                     // Sequência
    }
    return total;
}

int main() {
    int n;
    printf("Digite um número: ");
    scanf("%d", &n);                    // Sequência
    if (n > 0) {                        // Seleção
        printf("Soma = %d\\n", somaAte(n));
    } else {
        printf("Número inválido\\n");
    }
    return 0;
}

```

- **Sequência**: `scanf` → `if` → `printf`.
- **Seleção**: `if (n > 0) … else …`.
- **Repetição**: `for (int i = 1; i <= n; i++)`.

---

## 3. Vantagens

1. **Legibilidade**
    - Fluxo de controle claro: você lê de cima para baixo.
    - Menos “saltos” inesperados (evita `goto`).
2. **Facilidade de manutenção**
    - Cada bloco (`if`, `for`, `while`) isola uma parte lógica.
    - É mais simples encontrar erros, pois o caminho de execução é previsível.
3. **Modularização**
    - Funções e procedimentos dividem tarefas em “caixinhas” menores.
    - Você testa cada módulo separadamente.
4. **Amplamente suportado**
    - Quase todas as linguagens (C, Pascal, Python, Java) implementam essas estruturas.

---

## 4. Desvantagens

1. **Escalabilidade limitada**
    - Em sistemas muito grandes, ter só funções e laços pode tornar o projeto confuso.
    - Falta abstração de dados; tudo vira funções que manipulam variáveis globais ou parâmetros.
2. **Reuso de código**
    - Sem orientação a objetos, fica mais difícil compartilhar comportamento entre tipos de dados.
    - Pode levar a duplicação de código (copy-paste).
3. **Modelo de dados simples demais**
    - Não oferece encapsulamento forte (dados + comportamento juntos).
    - Você precisa tomar cuidado extra para não violar invariantes (valores válidos de variáveis).
4. **Fluxos complexos**
    - Para lógica de negócio com muitas regras, vários `if` e `for` aninhados podem ficar difíceis de entender.

---

## 5. Quando usar

- **Algoritmos matemáticos** e **rotinas de baixo nível** (ex.: drivers, sistemas embarcados).
- **Scripts simples** e **provas de conceito**, onde não há grande necessidade de abstração de dados.

Para **projetos maiores** ou onde os dados têm comportamentos complexos, vale combinar o estruturado com **Orientação a Objetos** ou **Funcional**, ganhando melhores ferramentas de organização.