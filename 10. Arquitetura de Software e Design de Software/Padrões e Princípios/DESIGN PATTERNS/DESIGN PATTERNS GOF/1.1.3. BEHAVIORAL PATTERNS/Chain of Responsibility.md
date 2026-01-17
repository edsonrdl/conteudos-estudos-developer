## Cadeia de Responsabilidade (Chain of Responsibility)

O Chain of Responsibility é um **padrão de design comportamental** que permite que você passe requisições ao longo de uma **cadeia de manipuladores (handlers)**. Cada manipulador decide se processa a requisição ou se a passa para o próximo manipulador na cadeia.

### Conceito Central

O padrão cria uma cadeia de objetos receptores para uma requisição. Cada receptor contém uma referência para o próximo receptor na cadeia. Se um objeto não pode lidar com a requisição, ele a passa para o próximo receptor, e assim por diante. A requisição viaja ao longo da cadeia até que um objeto a trate ou a cadeia termine.

### Componentes Chave

- **Manipulador (Handler) (Abstrato/Interface):** Define uma interface para lidar com requisições e opcionalmente implementa o link para o sucessor.
    
- **Manipuladores Concretos (Concrete Handlers):** Implementam a interface do manipulador e processam as requisições pelas quais são responsáveis. Se não puderem tratar uma requisição, eles a encaminham para seu sucessor.
    
- **Cliente (Client):** Inicia a requisição para um objeto manipulador no início da cadeia.
    

### Quando Usar

- Quando **mais de um objeto** pode lidar com uma requisição, e o manipulador não é conhecido antecipadamente.
    
- Quando você deseja emitir uma requisição para um de vários objetos **sem especificar o receptor explicitamente**.
    
- Quando o conjunto de objetos que podem lidar com uma requisição deve ser **especificado dinamicamente**.
    
- Quando você deseja **evitar acoplar** o emissor de uma requisição aos seus receptores.
    

---

## Exemplo de Implementação

### Exemplo em Python (Sistema de Suporte)

Python

```
from abc import ABC, abstractmethod

class SupportHandler(ABC):
    """Classe manipuladora abstrata"""
    def __init__(self):
        self._next_handler = None

    def set_next(self, handler):
        self._next_handler = handler
        return handler

    @abstractmethod
    def handle(self, request):
        if self._next_handler:
            return self._next_handler.handle(request)
        return None

class Level1Support(SupportHandler):
    """Lida com problemas técnicos básicos"""
    def handle(self, request):
        if request["priority"] == "low":
            return f"Nível 1: Resolvido {request['issue']}"
        else:
            print("Nível 1: Escalando para o Nível 2")
            return super().handle(request)

class Level2Support(SupportHandler):
    """Lida com problemas técnicos moderados"""
    def handle(self, request):
        if request["priority"] == "medium":
            return f"Nível 2: Resolvido {request['issue']}"
        else:
            print("Nível 2: Escalando para o Nível 3")
            return super().handle(request)

class Level3Support(SupportHandler):
    """Lida com problemas técnicos complexos"""
    def handle(self, request):
        if request["priority"] == "high":
            return f"Nível 3: Resolvido {request['issue']}"
        else:
            print("Nível 3: Escalando para o Gerente")
            return super().handle(request)

class ManagerSupport(SupportHandler):
    """Lida com questões críticas"""
    def handle(self, request):
        return f"Gerente: Lidando com questão crítica - {request['issue']}"

# Uso
if __name__ == "__main__":
    # Cria os manipuladores
    level1 = Level1Support()
    level2 = Level2Support()
    level3 = Level3Support()
    manager = ManagerSupport()

    # Constrói a cadeia
    level1.set_next(level2).set_next(level3).set_next(manager)

    # Testa diferentes requisições
    tickets = [
        {"issue": "Password reset", "priority": "low"},
        {"issue": "Software installation", "priority": "medium"},
        {"issue": "System outage", "priority": "high"},
        {"issue": "Data breach", "priority": "critical"}
    ]

    for ticket in tickets:
        print(f"\nProcessando: {ticket['issue']}")
        result = level1.handle(ticket)
        print(f"Resultado: {result}")
```

### Exemplo em JavaScript (Sistema de Aprovação de Despesas)

JavaScript

```
class ExpenseHandler {
    constructor() {
        this.nextHandler = null;
    }

    setNext(handler) {
        this.nextHandler = handler;
        return handler;
    }

    handle(expense) {
        if (this.nextHandler) {
            return this.nextHandler.handle(expense);
        }
        return `Ninguém pode aprovar $${expense.amount}`;
    }
}

class TeamLead extends ExpenseHandler {
    handle(expense) {
        if (expense.amount <= 1000) {
            return `TeamLead aprovou $${expense.amount} para ${expense.purpose}`;
        }
        return super.handle(expense);
    }
}

class Manager extends ExpenseHandler {
    handle(expense) {
        if (expense.amount <= 5000) {
            return `Gerente aprovou $${expense.amount} para ${expense.purpose}`;
        }
        return super.handle(expense);
    }
}

class Director extends ExpenseHandler {
    handle(expense) {
        if (expense.amount <= 10000) {
            return `Diretor aprovou $${expense.amount} para ${expense.purpose}`;
        }
        return super.handle(expense);
    }
}

class CEO extends ExpenseHandler {
    handle(expense) {
        if (expense.amount <= 50000) {
            return `CEO aprovou $${expense.amount} para ${expense.purpose}`;
        }
        return `Aprovação do Conselho necessária para $${expense.amount}`;
    }
}

// Uso
const teamLead = new TeamLead();
const manager = new Manager();
const director = new Director();
const ceo = new CEO();

// Constrói a cadeia
teamLead.setNext(manager).setNext(director).setNext(ceo);

// Testa despesas
const expenses = [
    { amount: 500, purpose: "Office supplies" },
    { amount: 3000, purpose: "Team building" },
    { amount: 8000, purpose: "New equipment" },
    { amount: 25000, purpose: "Marketing campaign" },
    { amount: 100000, purpose: "Acquisition" }
];

expenses.forEach(expense => {
    console.log(teamLead.handle(expense));
});
```

---

## Vantagens e Desvantagens

### ✅ Vantagens

- **Acoplamento Reduzido:** O remetente não precisa saber qual objeto irá tratar a requisição.
    
- **Flexibilidade:** Você pode adicionar ou remover manipuladores dinamicamente.
    
- **Responsabilidade Única:** Cada manipulador tem apenas um motivo para mudar.
    
- **Princípio Aberto/Fechado:** Você pode introduzir novos manipuladores sem modificar o código existente.
    

### ❌ Desvantagens

- **Sem Garantia de Tratamento:** A requisição pode chegar ao fim da cadeia sem ser tratada.
    
- **Preocupações com Desempenho:** O processamento pode ser lento para cadeias longas.
    
- **Dificuldade de Depuração:** Pode ser mais difícil de depurar devido à natureza dinâmica da cadeia.
    

---

## Aplicações no Mundo Real

O padrão Chain of Responsibility é comumente usado em:

- **Tratamento de Eventos GUI:** Eventos sobem (bubble up) de componentes filhos para pais.
    
- **Sistemas de Log:** Diferentes níveis de log (ex: _DEBUG, INFO, ERROR_) tratados por diferentes _loggers_.
    
- **Autenticação/Autorização:** Múltiplos métodos de autenticação tentados em sequência.
    
- **Processamento de Requisições:** _Middleware_ em _frameworks_ web (Express.js, Django, etc.).
    
- **Tratamento de Exceções:** Blocos _try-catch_ em linguagens de programação.
    

---

## Comparação com Outros Padrões

- **vs Padrão Command:** Chain of Responsibility passa uma requisição sequencialmente até que seja tratada, enquanto Command encapsula uma requisição como um objeto.
    
- **vs Padrão Decorator:** Decorator adiciona responsabilidades a objetos, enquanto Chain of Responsibility passa requisições entre objetos.
    
- **vs Padrão Observer:** Observer notifica múltiplos objetos simultaneamente, enquanto Chain of Responsibility passa para um objeto por vez.
    

## ## ☕ Exemplo do Chain of Responsibility em Java

Usaremos o mesmo conceito de aprovação de despesas com limites diferentes para cada nível hierárquico.

### 1. A Requisição (Classe `Expense`)

A classe que representa o objeto que será processado pela cadeia.

Java

```
// Expense.java
public class Expense {
    private double amount;
    private String purpose;

    public Expense(double amount, String purpose) {
        this.amount = amount;
        this.purpose = purpose;
    }

    public double getAmount() {
        return amount;
    }

    public String getPurpose() {
        return purpose;
    }
}
```

### 2. O Handler (Interface `IExpenseHandler`)

Este é o **Manipulador (Handler)**. A interface define o método de manipulação e o método para construir a cadeia.

Java

```
// IExpenseHandler.java
public interface IExpenseHandler {
    
    /** Define o próximo manipulador na cadeia e o retorna. */
    IExpenseHandler setNext(IExpenseHandler handler);

    /** Processa a requisição e a passa adiante se necessário. */
    String handle(Expense expense);
}
```

### 3. A Implementação Base (Classe Abstrata `AbstractExpenseHandler`)

Esta classe implementa o comportamento padrão de encadeamento.

Java

```
// AbstractExpenseHandler.java
public abstract class AbstractExpenseHandler implements IExpenseHandler {
    
    private IExpenseHandler nextHandler;

    @Override
    public IExpenseHandler setNext(IExpenseHandler handler) {
        this.nextHandler = handler;
        return handler;
    }

    @Override
    public String handle(Expense expense) {
        // Implementa a lógica central de passar a requisição adiante.
        if (this.nextHandler != null) {
            return this.nextHandler.handle(expense);
        }
        // Se a cadeia terminar sem que ninguém trate a requisição.
        return "Rejeitado: A despesa de $" + expense.getAmount() + " requer aprovação superior.";
    }

    // Método abstrato para forçar as subclasses a implementar sua lógica específica.
    protected abstract String processApproval(Expense expense);
}
```

### 4. Manipuladores Concretos (`Concrete Handlers`)

Cada nível de aprovação é um manipulador concreto que define sua responsabilidade.

Java

```
// TeamLeadHandler.java
public class TeamLeadHandler extends AbstractExpenseHandler {
    private final double LIMIT = 1000;

    @Override
    protected String processApproval(Expense expense) {
        if (expense.getAmount() <= LIMIT) {
            return "TeamLead aprovou $" + expense.getAmount() + " para " + expense.getPurpose();
        }
        // Se não puder aprovar, passa para o método handle da classe base.
        return super.handle(expense);
    }
}

// ManagerHandler.java
public class ManagerHandler extends AbstractExpenseHandler {
    private final double LIMIT = 5000;

    @Override
    protected String processApproval(Expense expense) {
        if (expense.getAmount() <= LIMIT) {
            return "Gerente aprovou $" + expense.getAmount() + " para " + expense.getPurpose();
        }
        return super.handle(expense);
    }
}

// DirectorHandler.java
public class DirectorHandler extends AbstractExpenseHandler {
    private final double LIMIT = 15000;

    @Override
    protected String processApproval(Expense expense) {
        if (expense.getAmount() <= LIMIT) {
            return "Diretor aprovou $" + expense.getAmount() + " para " + expense.getPurpose();
        }
        return super.handle(expense);
    }
}
```

### 5. O Cliente (`Client`)

A classe principal que monta a cadeia e inicia as requisições.

Java

```
// Client.java
public class Client {
    public static void main(String[] args) {
        // 1. Criar os manipuladores
        IExpenseHandler teamLead = new TeamLeadHandler();
        IExpenseHandler manager = new ManagerHandler();
        IExpenseHandler director = new DirectorHandler();

        // 2. Construir a cadeia (TeamLead -> Manager -> Director)
        teamLead.setNext(manager).setNext(director);

        // 3. Testar requisições
        Expense e1 = new Expense(800, "Suprimentos de Escritório");
        Expense e2 = new Expense(3500, "Viagem de Negócios");
        Expense e3 = new Expense(12000, "Novo Equipamento de Servidor");
        Expense e4 = new Expense(20000, "Aquisição de Empresa");

        System.out.println("Processando: " + e1.getPurpose() + " | Resultado: " + teamLead.handle(e1));
        System.out.println("Processando: " + e2.getPurpose() + " | Resultado: " + teamLead.handle(e2));
        System.out.println("Processando: " + e3.getPurpose() + " | Resultado: " + teamLead.handle(e3));
        System.out.println("Processando: " + e4.getPurpose() + " | Resultado: " + teamLead.handle(e4));
    }
}

/*
Saída Esperada:
Processando: Suprimentos de Escritório | Resultado: TeamLead aprovou $800.0 para Suprimentos de Escritório
Processando: Viagem de Negócios | Resultado: Gerente aprovou $3500.0 para Viagem de Negócios
Processando: Novo Equipamento de Servidor | Resultado: Diretor aprovou $12000.0 para Novo Equipamento de Servidor
Processando: Aquisição de Empresa | Resultado: Rejeitado: A despesa de $20000.0 requer aprovação superior.
*/
```

---

## 🔎 Análise do Relacionamento entre as Classes

O principal relacionamento que define a estrutura do Chain of Responsibility é a **Associação** que cria a cadeia.

### 1. Relacionamento Chave: Agregação (Associação)

O relacionamento entre um manipulador e o próximo na cadeia é um caso clássico de **Associação** e, mais especificamente, de **Agregação**.

- **Onde:** No campo `private IExpenseHandler nextHandler;` dentro da classe `AbstractExpenseHandler`.
    
- **Detalhe:** Cada manipulador (`TeamLeadHandler`, `ManagerHandler`, etc.) possui uma referência ao seu sucessor (`nextHandler`).
    
- **Agregação, não Composição, por quê?**
    
    - **Independência:** O `TeamLeadHandler` e o `ManagerHandler` são objetos independentes. A destruição do `TeamLeadHandler` **não** implica na destruição do `ManagerHandler`. O `ManagerHandler` pode continuar existindo e ser parte de outra cadeia (ou começar uma nova cadeia).
        
    - **Ciclo de Vida:** Se fôssemos modelar em UML, usaríamos o **losango vazado** (Agregação), pois o ciclo de vida dos objetos não está estritamente ligado. O manipulador atual (o "todo") apenas utiliza ou referencia o próximo manipulador (a "parte"), que é um objeto separado.
        

### 2. Relacionamento de Herança (Generalização)

Este relacionamento é fundamental para o padrão, garantindo que todos os elementos sigam a mesma estrutura.

- **Onde:** Classes `TeamLeadHandler`, `ManagerHandler`, e `DirectorHandler` **estendem** a classe abstrata `AbstractExpenseHandler`.
    
- **Tipo:** **Herança/Generalização**.
    
    - As classes concretas herdam o comportamento de encadeamento (`setNext` e a lógica de passagem adiante no `handle`) e são forçadas a implementar sua lógica específica de aprovação.
        

### 3. Relacionamento de Realização (Implementação)

- **Onde:** A classe `AbstractExpenseHandler` **implementa** a interface `IExpenseHandler`.
    
- **Tipo:** **Realização**.
    
    - Isto garante que a interface do Manipulador seja seguida por todas as implementações concretas e abstratas.
        

Em resumo, a estrutura da cadeia é definida por uma **Agregação** recursiva (cada objeto se associa ao próximo), enquanto o comportamento é unificado pela **Herança** e **Realização** da interface do Manipulador.