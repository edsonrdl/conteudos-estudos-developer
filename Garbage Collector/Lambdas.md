Uma **expressão lambda** é, de forma simplificada, uma **função anônima** (sem nome) que você pode criar em uma única linha de código.

Ela é usada principalmente para criar funções pequenas, descartáveis e que não precisam ser reutilizadas em várias partes do seu código.

### Principais Características

1. **Função Anônima (Sem Nome):** Ao contrário das funções tradicionais (como as definidas com `def` em Python ou com o nome de um método), uma expressão lambda não exige um nome formal.
    
2. **Concisa e Expressiva:** Ela permite escrever blocos de funcionalidade de forma muito mais enxuta. Reduz a verbosidade do código, especialmente em linguagens como Java ou C#.
    
3. **Geralmente em uma Linha:** Lambdas são projetadas para conter lógica simples, tipicamente uma única expressão cujo resultado é o valor de retorno da função.
    
4. **Uso Comum:** São frequentemente passadas como argumentos para outras funções que esperam uma função como parâmetro (conhecidas como funções de ordem superior), como as funções de `map`, `filter`, `sort` ou a API de Streams em Java.
    

### Raízes no Cálculo Lambda

O conceito de expressão lambda tem suas raízes no **Cálculo Lambda** (λ-cálculo), um sistema formal na lógica matemática e ciência da computação criado por Alonzo Church na década de 1930. Ele trata de funções, suas aplicações e recursão de uma forma pura e fundamental, e é a base para o **paradigma de programação funcional**.

### Exemplo em Python

Em Python, a sintaxe básica é `lambda argumentos: expressão`.

**Função tradicional:**

Python

```
def dobrar(x):
    return x * 2
```

**Expressão Lambda Equivalente:**

Python

```
dobrar_lambda = lambda x: x * 2
```

Você pode então usar:

Python

```
print(dobrar_lambda(5)) # Saída: 10
```

E no contexto mais comum, passá-la para uma função:

Python

```
lista = [1, 2, 3, 4]
# Usando lambda para dobrar todos os elementos da lista
dobrada = list(map(lambda x: x * 2, lista)) 
# dobrada será [2, 4, 6, 8]
```

Em resumo, a expressão lambda é uma ferramenta poderosa que traz o estilo de **programação funcional** para várias linguagens, permitindo que você trate funções como "cidadãs de primeira classe" – ou seja, que elas possam ser passadas como dados.