 ## 💻 Caso Real Completo em Java com Spring Data JPA

Para o propósito da Clean Architecture, focaremos na **separação lógica**. Assumimos que o Spring Framework será usado apenas na camada de **Infraestrutura** e **Apresentação**, mantendo o Domínio e a Aplicação puros.

### 1. Camada de Domínio (`com.app.domain`) - PURA

Esta camada **não conhece Spring ou JPA**.

#### `com.app.domain.Pagamento.java` (Entidade de Domínio)

(Mantemos a mesma classe anterior, pura, com as regras de negócio.)

#### `com.app.domain.PagamentoRepository.java` (Interface - Contrato do Domínio)

(Mantemos a mesma interface pura.)

Java

```
// PACOTE: com.app.domain
public interface PagamentoRepository {
    // Métodos de Contrato que o Use Case precisa
    Pagamento getById(String id);
    void save(Pagamento pagamento);
}
```

---

### 2. Camada de Aplicação (`com.app.application`) - PURA

O Caso de Uso continua dependendo apenas da Interface de Domínio.

#### `com.app.application.ProcessarPagamentoUseCase.java`

(O código do Use Case é **exatamente o mesmo**, pois ele não se importa com a tecnologia de persistência. Ele só usa o `PagamentoRepository` como um contrato.)

---

### 3. Camada de Infraestrutura (`com.app.infrastructure`) - FRAMEWORKS AQUI

Esta é a camada que introduz o **Spring** e o **JPA**.

#### **A) DTO de Persistência (Opcional, mas Recomendado)**

Muitos projetos usam um "Model" separado para o JPA para evitar sujar a Entidade de Domínio com anotações de banco de dados.

Java

```
// PACOTE: com.app.infrastructure.model

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "pagamentos")
public class PagamentoJpaModel {
    
    @Id
    private String id;
    private double valor;
    private String status; // Mapeado para String/Enum no DB

    // Construtores, Getters e Setters
    // ...
}
```

#### **B) Repositório de JPA (Spring Data)**

Esta é a interface do Spring Data.

Java

```
// PACOTE: com.app.infrastructure.repository

import org.springframework.data.jpa.repository.JpaRepository;
import com.app.infrastructure.model.PagamentoJpaModel;

// Repositório de baixo nível do Spring Data
public interface PagamentoJpaRepository extends JpaRepository<PagamentoJpaModel, String> {
    // O Spring implementa automaticamente
}
```

#### **C) Adaptador de Repositório (A Ponte)**

Este adaptador é a classe que **implementa a interface do Domínio** (`PagamentoRepository`) usando o repositório do Spring Data (`PagamentoJpaRepository`). Esta é a **Inversão de Dependência** em ação.

Java

```
package com.app.infrastructure.adapter;

import com.app.domain.Pagamento;
import com.app.domain.PagamentoRepository; // Implementa a Interface do DOMÍNIO!
import com.app.infrastructure.repository.PagamentoJpaRepository;
import com.app.infrastructure.model.PagamentoJpaModel;
import org.springframework.stereotype.Component;

@Component // Indica que é um bean Spring (Detalhhe de Infraestrutura)
public class PagamentoRepositoryAdapter implements PagamentoRepository {
    
    private final PagamentoJpaRepository jpaRepository;
    
    // Injeção do repositório de baixo nível do Spring Data
    public PagamentoRepositoryAdapter(PagamentoJpaRepository jpaRepository) {
        this.jpaRepository = jpaRepository;
    }

    // MAPPER: Converte Entidade de Domínio para Model JPA e vice-versa
    private PagamentoJpaModel toJpaModel(Pagamento domain) {
        // Lógica de conversão
        PagamentoJpaModel model = new PagamentoJpaModel();
        model.setId(domain.getId());
        model.setValor(domain.getValor());
        model.setStatus(domain.getStatus().name());
        return model;
    }
    
    private Pagamento toDomain(PagamentoJpaModel model) {
        // Lógica de conversão
        // ... (Reconstrói a Entidade de Domínio a partir dos dados do DB)
        return new Pagamento(model.getId(), model.getValor()); // Simplificado
    }

    // Implementação do Contrato de Domínio usando JPA
    @Override
    public Pagamento getById(String id) {
        return jpaRepository.findById(id).map(this::toDomain).orElse(null);
    }

    @Override
    public void save(Pagamento pagamento) {
        PagamentoJpaModel model = toJpaModel(pagamento); // Converte
        jpaRepository.save(model); // Salva usando a ferramenta de infraestrutura
    }
}
```

---

### 4. Camada de Apresentação (`com.app.presentation`) - Entrada/Saída

O Controller usa o Spring para receber a requisição e injeta o Use Case.

Java

```
package com.app.presentation;

import com.app.application.ProcessarPagamentoUseCase;
import com.app.domain.Pagamento;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class PagamentoController {

    private final ProcessarPagamentoUseCase useCase;

    // O Spring injeta o Use Case (que por sua vez injeta o Adaptador da Infra)
    public PagamentoController(ProcessarPagamentoUseCase useCase) {
        this.useCase = useCase;
    }

    // DTO de Entrada do Cliente
    public static class PagamentoRequest {
        public String pagamentoId;
        public double valor;
        public String numeroCartao;
    }

    @PostMapping("/pagamentos")
    public Pagamento processar(@RequestBody PagamentoRequest request) {
        
        // Mapeia o DTO de Entrada para o Request do Caso de Uso
        ProcessarPagamentoUseCase.Request useCaseRequest = new ProcessarPagamentoUseCase.Request();
        useCaseRequest.pagamentoId = request.pagamentoId;
        useCaseRequest.valor = request.valor;
        useCaseRequest.numeroCartao = request.numeroCartao;
        
        // Chama o Use Case (Regra de Negócio Pura)
        return useCase.execute(useCaseRequest);
    }
}
```

### O Fluxo Completo da Dependência (Inversão Garantida)

1. **`PagamentoController`** (Apresentação) $\rightarrow$ Depende de **`ProcessarPagamentoUseCase`** (Aplicação).
    
2. **`ProcessarPagamentoUseCase`** (Aplicação) $\rightarrow$ Depende de **`PagamentoRepository`** (Interface do Domínio).
    
3. **`PagamentoRepositoryAdapter`** (Infraestrutura) $\rightarrow$ Implementa **`PagamentoRepository`** (Interface do Domínio).
    
4. **`PagamentoRepositoryAdapter`** (Infraestrutura) $\rightarrow$ Usa **`PagamentoJpaRepository`** (Ferramenta de Baixo Nível/JPA).
    

O segredo está no **Adaptador de Repositório**. Ele é a classe na Infraestrutura que atua como tradutor e ponte, garantindo que o Use Case (a camada de alto valor) nunca precise saber que o Spring Data JPA existe.

##  Caso Real Completo em c# com Entity framework
 
 1. Camada de Domínio (Abstração - Contrato)

Esta é a camada mais interna e de **Alto Nível**. Ela **define** a regra de negócio e os contratos de que precisa, sem saber como serão implementados.

|**Arquivo**|**Conteúdo**|**Propósito**|
|---|---|---|
|**`IUserRepository.cs`**|`public interface IUserRepository { ... }`|**Contrato:** Define o que precisa ser feito (`Save`, `Get`). A **Infraestrutura** terá que seguir este contrato.|
|**`User.cs`**|`public class User { ... }`|A Entidade de Domínio.|

C#

```
// # Camada de Domínio (Camada Interna)

// 1. A Interface (Abstração) VIVE AQUI
// O Domínio diz: "Eu preciso de um objeto que saiba salvar e buscar usuários."
public interface IUserRepository
{
    void Save(User user);
    User GetByEmail(string email);
}

// A Entidade de Domínio
public class User
{
    public string Name { get; set; }
    public string Email { get; set; }
    // ... lógica de domínio
}
```

---

## 2. Camada de Aplicação / Use Case (Lógica - Política)

Esta camada de **Alto Nível** contém a orquestração e as regras de negócio. Ela depende **APENAS** da `IUserRepository` (a Interface do Domínio).

C#

```
// # Camada de Aplicação (Use Case)

public class CreateUserUseCase
{
    // O Use Case depende da ABSTRAÇÃO (IUserRepository)
    private readonly IUserRepository _userRepository;

    // Injeção de Dependência: Recebe uma IMPLEMENTAÇÃO do IUserRepository
    // Não importa se é SQL, MongoDB, ou um Fake para teste.
    public CreateUserUseCase(IUserRepository userRepository)
    {
        // O Use Case só se importa com o CONTRATO (a interface).
        _userRepository = userRepository;
    }

    public void Execute(User user)
    {
        // 1. Regra de Negócio: Não pode ter e-mail duplicado.
        if (_userRepository.GetByEmail(user.Email) != null)
        {
            throw new Exception("Erro: Usuário já existe.");
        }
        
        // 2. Usa o contrato (Save) para persistir o objeto.
        _userRepository.Save(user);
    }
}
```

---

## 3. Camada de Infraestrutura (Implementação - Detalhe)

Esta é a camada mais externa e de **Baixo Nível**. Ela contém os detalhes tecnológicos (bibliotecas, banco de dados) e é a única que **implementa** a Interface do Domínio.

C#

```
// # Camada de Infraestrutura (Camada Externa)

// 2. A Implementação CONCRETA VIVE AQUI
// Ela inverte a dependência: a Infraestrutura depende do Domínio (a interface).
public class SqlUserRepository : IUserRepository
{
    private readonly DbContext _dbContext; // Detalhe de baixo nível (ex: Entity Framework)

    public SqlUserRepository(DbContext dbContext)
    {
        _dbContext = dbContext;
    }

    // A Infraestrutura PREENCHE o contrato do Domínio.
    public void Save(User user)
    {
        // Detalhes técnicos específicos (conexão SQL)
        _dbContext.Users.Add(user);
        _dbContext.SaveChanges();
    }

    public User GetByEmail(string email)
    {
        return _dbContext.Users.FirstOrDefault(u => u.Email == email);
    }
}
```

## Conclusão: A Inversão de Dependência

|**Camada**|**Tipo**|**Depende de**|**Direção da Seta**|
|---|---|---|---|
|**Infraestrutura** (`SqlUserRepository`)|Baixo Nível|**Domínio** (`IUserRepository`)|**APONTA PARA DENTRO** (Correto)|
|**Aplicação** (`CreateUserUseCase`)|Alto Nível|**Domínio** (`IUserRepository`)|**APONTA PARA DENTRO** (Correto)|

O Use Case (lógica de negócio) está totalmente **desacoplado** da tecnologia de banco de dados (`SqlUserRepository`).

### O Benefício Prático (Testes)

O maior ganho é na testabilidade. Para testar o `CreateUserUseCase`, você **não precisa** de um banco de dados real. Você pode criar um `FakeRepository` na sua camada de teste:

C#

```
// # Camada de Teste

// Implementação Fake/Mock para teste (em memória)
public class FakeUserRepository : IUserRepository
{
    private readonly List<User> _store = new List<User>();

    public void Save(User user) { _store.Add(user); }
    public User GetByEmail(string email) { 
        return _store.FirstOrDefault(u => u.Email == email); 
    }
}

[Fact] // Teste de unidade
public void Deve_Falhar_AoCriarUsuarioExistente()
{
    // ARRANGE: Injetamos o FAKE Repository
    var fakeRepo = new FakeUserRepository();
    fakeRepo.Save(new User { Email = "teste@email.com" });

    var useCase = new CreateUserUseCase(fakeRepo); // Depende apenas da Interface

    // ACT & ASSERT: Testamos apenas a lógica de negócio do Use Case
    var novoUsuario = new User { Email = "teste@email.com" };
    
    // O teste valida que a regra de negócio (não duplicar e-mail) foi cumprida.
    Assert.Throws<Exception>(() => useCase.Execute(novoUsuario));
}
```

Ao fazer o **Domínio** definir o contrato (`IUserRepository`), garantimos que a lógica de negócio essencial (Use Case) nunca precise de um detalhe externo (Infraestrutura) para funcionar ou ser testada.