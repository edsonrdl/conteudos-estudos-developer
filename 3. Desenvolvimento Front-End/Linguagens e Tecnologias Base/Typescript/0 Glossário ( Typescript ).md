## 1. Fundamentos e Tipos Primitivos

### 1.1. Tipos Primitivos (Sistemas de Tipos do JS)

- 1.1.1. **Number**: Tratamento de inteiros e ponto flutuante (IEEE 754).
    
- 1.1.2. **String**: Manipulação de textos e Template Literals.
    
- 1.1.3. **Boolean**: Valores lógicos.
    
- 1.1.4. **Null & Undefined**: Diferenças semânticas e o impacto de `strictNullChecks`.
    
- 1.1.5. **Symbol**: Identificadores únicos e imutáveis.
    
- 1.1.6. **BigInt**: Manipulação de inteiros de precisão arbitrária.
    

## 2. Variáveis e Operadores

### 2.1. Escopo e Declaração

- 2.1.1. **var**: Escopo de função e Hoisting (Legado).
    
- 2.1.2. **let**: Escopo de bloco e Temporal Dead Zone.
    
- 2.1.3. **const**: Imutabilidade de referência vs. imutabilidade de valor.
    

### 2.2. Operadores Avançados

- 2.2.1. **Aritméticos**: Operações base.
    
- 2.2.2. **Lógicos**: `&&`, `||`, `!`.
    
- 2.2.3. **Comparação**: Igualdade estrita (`===`) vs. Ampla (`==`).
    
- 2.2.4. **Ternários & Nullish Coalescing**: `? :` e o operador `??`.
    

## 3. Estruturas de Controle e Repetição

### 3.1. Condicionais

- 3.1.1. **if / else / else if**: Fluxo básico.
    
- 3.1.2. **switch / case**: Avaliação de expressões e Type Exhaustiveness.
    

### 3.2. Loops e Iteração

- 3.2.1. **For**: Tradicional, `for...in` (chaves) e `for...of` (valores/iteráveis).
    
- 3.2.2. **While & Do...While**: Loops baseados em condição.
    

## 4. Funções e Programação Funcional

### 4.1. Anatomia da Função

- 4.1.1. **Declaração vs. Expressão**: Diferenças de içamento.
    
- 4.1.2. **Arrow Functions**: Léxico do `this` e sintaxe concisa.
    

### 4.2. Parâmetros e Retorno

- 4.2.1. **Parâmetros Opcionais (`?`)**.
    
- 4.2.2. **Default Parameters**: Valores padrão em tempo de execução.
    
- 4.2.3. **Rest Parameters (`...args`)**: Captura de múltiplos argumentos.
    
- 4.2.4. **Type Annotation**: Definição explícita de tipos de retorno.
    

### 4.3. Assincronismo e Callbacks

- 4.3.1. **Callbacks**: Passagem de funções como argumento.
    
- 4.3.2. **Promessas e Async/Await**: Abstração de operações não-bloqueantes.
    

## 5. Estruturas de Dados e Tipos Específicos do TS

### 5.1. Arrays e Tuplas

- 5.1.1. **Arrays**: `T[]` ou `Array<T>`, métodos de alta ordem (`map`, `filter`, `reduce`).
    
- 5.1.2. **Tuplas**: Arrays de comprimento fixo com tipos posicionais específicos.
    

### 5.2. Enums

- 5.2.1. **Numeric Enums**: Comportamento de auto-incremento.
    
- 5.2.2. **String Enums**: Clareza em logs e depuração.
    
- 5.2.3. **Const Enums**: Inlining para performance em tempo de compilação.
    

### 5.3. Tipos de "Fuga" e Especiais

- 5.3.1. **Any**: Desativação do checking (uso desencorajado).
    
- 5.3.2. **Unknown**: O "any seguro" (exige type guard).
    
- 5.3.3. **Void**: Ausência de retorno.
    
- 5.3.4. **Never**: Tipos que representam valores que nunca ocorrem (ex: exceções).
    

## 6. Programação Orientada a Objetos (POO)

### 6.1. Classes

- 6.1.1. **Construtores e Parameter Properties**.
    
- 6.1.2. **Modificadores de Acesso**: `public`, `private`, `protected`.
    
- 6.1.3. **Readonly**: Proteção contra escrita após inicialização.
    
- 6.1.4. **Getters & Setters**: Encapsulamento de lógica de acesso.
    

### 6.2. Herança e Abstração

- 6.2.1. **Extends & Super**: Reuso de lógica de classe base.
    
- 6.2.2. **Abstract Classes**: Contratos que não podem ser instanciados.
    

### 6.3. Interfaces

- 6.3.1. **Declaração & Implementação**: `implements`.
    
- 6.3.2. **Extensão de Interfaces**: Composição de contratos.
    
- 6.3.3. **Optional & Readonly Properties**.
    

## 7. Módulos e Organização de Código

### 7.1. Sistema de Módulos (ESM)

- 7.1.1. **Export / Import**: Nomeados vs. Default.
    
- 7.1.2. **Dynamic Imports**: Code splitting e Lazy Loading.
    

### 7.2. Namespaces (Legado/Organização Interna)

- 7.2.1. Declaração e aninhamento.
    

## 8. Tipagem Avançada (Generic & Meta-Programming)

### 8.1. Generics

- 8.1.1. **Generic Constraints**: `T extends K`.
    
- 8.1.2. **Generic Classes & Interfaces**.
    

### 8.2. Composição de Tipos

- 8.2.1. **Union Types (`|`)**: Multiplicidade de tipos.
    
- 8.2.2. **Intersection Types (`&`)**: Mixins e junção de contratos.
    
- 8.2.3. **Type Aliases**: Apelidos para tipos complexos.
    

### 8.3. Mecanismos de Checagem

- 8.3.1. **Type Inference**: Como o TS deduz tipos sem anotação.
    
- 8.3.2. **Type Guards**: `typeof`, `instanceof`, User-defined type guards (`is`).
    
- 8.3.3. **Type Assertions**: `as Type` (Cast de tipos).
    
- 8.3.4. **Satisfies Operator**: Validação de conformidade sem mudar o tipo inferido.
    

### 8.4. Tipos Utilitários e Condicionais

- 8.4.1. **Mapped Types**: Transformação de chaves e valores.
    
- 8.4.2. **Conditional Types**: `T extends U ? X : Y`.
    
- 8.4.3. **Utility Types**: `Partial`, `Readonly`, `Pick`, `Omit`, `Record`.
    

## 9. Decoradores e Metadados

### 9.1. Decorators (Experimental/TC39)

- 9.1.1. **Class Decorators**: Modificação de construtores.
    
- 9.1.2. **Method & Property Decorators**: Interceptação de lógica.
    
- 9.1.3. **Accessor & Parameter Decorators**.
    

### 9.2. Reflection API

- 10.1.1. **Reflect Metadata**: Armazenamento de informações em tempo de execução.
    
- 10.1.2. Integração com Frameworks (ex: NestJS, Inversify).