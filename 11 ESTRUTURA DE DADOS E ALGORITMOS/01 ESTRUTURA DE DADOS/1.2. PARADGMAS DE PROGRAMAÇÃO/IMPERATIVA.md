## 1. O que é o Paradigma Imperativo

No paradigma imperativo, o programa é tratado como uma sequência de **instruções** que **modificam** um estado compartilhado (variáveis, memória). Você diz ao computador **como** fazer algo, passo a passo.

> Analogia: é como dar ordens a um assistente – “pegue o balde, encha com água, derrame na planta”.

---

## 2. Características principais

1. **Estado mutável**
    - Variáveis podem ser criadas, atribuídas e reatribuídas ao longo do programa.
2. **Controle explícito**
    - Uso de comandos como `assign` (atribuição), `if/else`, `for`, `while` e até `goto`.
3. **Fluxo de execução sequencial**
    - O programa avança linha a linha, a menos que um comando de salto (loop, condição) altere o caminho.
4. **Efeitos colaterais**
    - Funções e procedimentos podem alterar variáveis externas ou I/O (arquivos, console).

---

## 3. Exemplo em C

```c
c
CopiarEditar
#include <stdio.h>

// Calcula o fatorial de n de forma imperativa
int fatorial(int n) {
    int resultado = 1;              // variável mutável
    for (int i = 2; i <= n; i++) {  // laço imperativo
        resultado = resultado * i;  // modificação de estado
    }
    return resultado;
}

int main() {
    int x = 5;
    printf("Fatorial de %d = %d\\n", x, fatorial(x));
    return 0;
}

```

- **Atribuição**: `resultado = resultado * i`
- **Loop**: `for`
- **Sequência**: cada linha executa em ordem

---

## 4. Exemplo em Java

```java
java
CopiarEditar
public int somaArray(int[] nums) {
    int soma = 0;                    // estado inicial
    for (int i = 0; i < nums.length; i++) {
        soma += nums[i];             // acumula resultado
    }
    return soma;
}

```

- Variável `soma` é mutada em cada iteração.

---

## 5. Vantagens

- **Simplicidade conceitual**
    - Fácil de entender: “faça isto, depois aquilo”.
- **Performance**
    - Sem overhead de frameworks; código direto executa rápido.
- **Controle fino de recursos**
    - Você decide exatamente quando alocar e liberar memória, quando ler/gravar I/O.
- **Onipresença**
    - Quase toda linguagem de programação suporta estilo imperativo.

---

## 6. Desvantagens

- **Código verboso**
    - Muitas linhas para gerenciar estado e fluxo.
- **Maior propensão a bugs**
    - Variáveis mutáveis e efeitos colaterais podem gerar erros difíceis de rastrear.
- **Menos modular**
    - Funções com efeitos colaterais misturam lógica de negócio e manipulação de estado.
- **Difícil paralelismo**
    - Concorrência exige mecanismos extras (locks, semáforos) para proteger estado compartilhado.

---

## 7. Quando usar

- Rotinas de **baixo nível** ou **sistemas embarcados**, onde você precisa de controle preciso de hardware e recursos.
- Algoritmos **simples** ou de **alto desempenho**, onde a sobrecarga de abstrações deve ser mínima.
- Códigos legados ou linguagens que não suportam bem outros paradigmas.