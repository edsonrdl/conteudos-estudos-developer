## 1. O que é o Paradigma Procedural

No modelo procedural você organiza o programa em **procedimentos** ou **funções** que executam tarefas específicas. É um refinamento do imperativo, onde o foco é dividir o código em sub-rotinas para evitar repetição.

> Analogia: imagine uma fábrica onde cada máquina (procedimento) faz uma parte do trabalho — uma máquina mistura ingredientes, outra assa, outra embala — e todas funcionam em sequência.

---

## 2. Características principais

1. **Modularização por funções**
    - Divide o programa em blocos (funções/procedimentos) que recebem parâmetros e retornam resultados.
2. **Fluxo de controle clássico**
    - Usa `if/else`, `for`, `while`, etc., dentro de funções.
3. **Reuso de código**
    - Funções podem ser chamadas várias vezes de diferentes pontos.
4. **Estado compartilhado**
    - Variáveis globais ou passadas como parâmetro podem ser lidas e modificadas por procedimentos.

---

## 3. Exemplo em C

```c
c
CopiarEditar
#include <stdio.h>

// Procedimento que calcula o máximo de dois números
int maximo(int a, int b) {
    if (a > b)
        return a;
    else
        return b;
}

// Programa principal
int main() {
    int x = 7, y = 12;
    int m = maximo(x, y);    // chama o procedimento
    printf("O maior é %d\\n", m);
    return 0;
}

```

- `main` orquestra o fluxo e chama `maximo` para obter o resultado.

---

## 4. Vantagens

- **Clareza e organização**
    - Blocos lógicos separados facilitam leitura e manutenção.
- **Reuso de código**
    - Funções genéricas podem servir a várias partes do sistema.
- **Testabilidade**
    - Cada função pode ser testada isoladamente.
- **Diminuição de duplicação**
    - Evita trechos repetidos ao centralizar lógica em procedimentos.

---

## 5. Desvantagens

- **Acoplamento por variáveis globais**
    - Uso excessivo de variáveis globais dificulta rastrear quem modifica o quê.
- **Escala limitada**
    - Em sistemas muito grandes, muitas funções podem ficar dispersas e difíceis de organizar.
- **Separação rígida**
    - Dados e comportamentos ficam separados; não há encapsulamento forte.
- **Gerenciamento de dependências**
    - Funções que dependem de muitas variáveis externas podem ser frágeis e difíceis de reutilizar.

---

## 6. Quando usar

- Rotinas matemáticas ou de processamento de dados simples.
- Ferramentas de linha de comando, scripts e utilitários onde OO ou funcional seriam um overhead.
- Sistemas embarcados e de baixo nível, onde recurso e performance são críticos.