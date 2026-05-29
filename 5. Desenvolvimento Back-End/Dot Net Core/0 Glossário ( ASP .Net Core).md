# 📚 Guia de Estudos: ASP.NET Core
#tag #backend #dotnet #csharp

> [!info] Visão Geral
> ASP.NET Core é o framework web moderno, multiplataforma e open source da Microsoft para construir APIs REST, aplicações MVC e serviços em tempo real com C#. Este guia cobre desde a arquitetura do pipeline de requisições até acesso a dados com EF Core, autenticação, segurança, testes e implantação em produção.

---

## 1 Domínio: Fundamentos e Arquitetura

### 1.1 Visão Geral
- **1.1.1. Introdução e Arquitetura**
  - 1.1.1.1. O que é ASP.NET Core, diferenças em relação ao ASP.NET clássico e por que é multiplataforma.
  - 1.1.1.2. Arquitetura geral: Host, DI Container, Middleware Pipeline e Kestrel.

### 1.2 Pipeline de Requisições
- **1.2.1. Middleware**
  - 1.2.1.1. O que é middleware, como funciona o pipeline e ordem de execução (`Use`, `Run`, `Map`).
  - 1.2.1.2. Ciclo de vida de uma requisição HTTP — do recebimento até a resposta.
- **1.2.2. Injeção de Dependência**
  - 1.2.2.1. Container de DI nativo — registros (`AddSingleton`, `AddScoped`, `AddTransient`) e resolução automática.

---

## 2 Domínio: Desenvolvimento Web e APIs REST

### 2.1 Controllers e Rotas
- **2.1.1. MVC e Roteamento**
  - 2.1.1.1. Padrão MVC no ASP.NET Core — `Controller`, `View`, `Model` e roteamento convencional vs por atributo.
  - 2.1.1.2. `[Route]`, `[HttpGet]`, `[HttpPost]` — mapeamento de endpoints e parâmetros de rota.
- **2.1.2. Manipulação de Requisições**
  - 2.1.2.1. Binding de dados: `[FromRoute]`, `[FromQuery]`, `[FromBody]`, `[FromForm]`.
  - 2.1.2.2. Upload de arquivos com `IFormFile`.

### 2.2 Validação e Formulários
- **2.2.1. Validação de Dados**
  - 2.2.1.1. Data Annotations (`[Required]`, `[MaxLength]`, `[Range]`) e `ModelState.IsValid`.
  - 2.2.1.2. Validação customizada com `IValidatableObject` e FluentValidation.

### 2.3 APIs RESTful
- **2.3.1. Construção de APIs**
  - 2.3.1.1. `[ApiController]` e retornos com `IActionResult` e `ActionResult<T>` — status codes semânticos.
  - 2.3.1.2. Versionamento de APIs (`v1`, `v2`) com `Microsoft.AspNetCore.Mvc.Versioning`.
- **2.3.2. Documentação**
  - 2.3.2.1. Configuração do Swagger (Swashbuckle / NSwag) para documentar e testar endpoints.

---

## 3 Domínio: Acesso a Dados

### 3.1 Entity Framework Core
- **3.1.1. Mapeamento e Entidades**
  - 3.1.1.1. `DbContext`, `DbSet<T>`, mapeamento de entidades com Data Annotations e Fluent API.
  - 3.1.1.2. Relacionamentos: `HasOne`, `HasMany`, `WithForeignKey` — 1:1, 1:N, N:N.
- **3.1.2. Operações e Migrations**
  - 3.1.2.1. CRUD com EF Core — `Add`, `Update`, `Remove`, `SaveChangesAsync`.
  - 3.1.2.2. Migrations — criação, aplicação e reversão de schema com `dotnet ef migrations`.
- **3.1.3. Consultas**
  - 3.1.3.1. LINQ to Entities, consultas eager (`Include`) vs lazy loading, projeções com `Select`.

### 3.2 Gerenciamento de Pacotes
- **3.2.1. NuGet**
  - 3.2.1.1. Adição e gerenciamento de dependências via NuGet CLI e `PackageReference` no `.csproj`.

---

## 4 Domínio: Autenticação e Autorização

### 4.1 Autenticação
- **4.1.1. Cookies e Tokens**
  - 4.1.1.1. Autenticação baseada em cookies — `AddCookie`, `SignInAsync`, fluxo de login/logout.
  - 4.1.1.2. Autenticação com JWT — geração de token, validação com `AddJwtBearer` e claims.
- **4.1.2. Provedores Externos**
  - 4.1.2.1. OAuth2 com Google, Microsoft e GitHub via `AddAuthentication().AddGoogle()`.

### 4.2 Autorização
- **4.2.1. Roles e Policies**
  - 4.2.1.1. Autorização baseada em roles (`[Authorize(Roles = "Admin")]`) e claims.
  - 4.2.1.2. Policies customizadas com `AddAuthorization`, `IAuthorizationRequirement` e handlers.

---

## 5 Domínio: Segurança

### 5.1 Proteção contra Ataques Comuns
- **5.1.1. XSS, CSRF e SQL Injection**
  - 5.1.1.1. Prevenção de XSS — encoding automático do Razor e Content Security Policy.
  - 5.1.1.2. Prevenção de CSRF — Anti-Forgery Tokens com `[ValidateAntiForgeryToken]`.
  - 5.1.1.3. Prevenção de SQL Injection — uso de parâmetros via EF Core e Dapper.
- **5.1.2. Criptografia e Transporte**
  - 5.1.2.1. HTTPS obrigatório com `UseHttpsRedirection`, HSTS e configuração de certificados SSL/TLS.

---

## 6 Domínio: Testes

### 6.1 Testes Unitários
- **6.1.1. xUnit e Mocks**
  - 6.1.1.1. Testes unitários com xUnit, `Fact`, `Theory` e isolamento de dependências com Moq.

### 6.2 Testes de Integração
- **6.2.1. WebApplicationFactory**
  - 6.2.1.1. `WebApplicationFactory<T>` e `HttpClient` para testar endpoints sem deploy real.
  - 6.2.1.2. Banco de dados em memória (`UseInMemoryDatabase`) para isolar testes de persistência.

---

## 7 Domínio: Implantação e Performance

### 7.1 Deploy
- **7.1.1. Publicação e Hospedagem**
  - 7.1.1.1. Publicação como JAR auto-contido ou dependente do runtime com `dotnet publish`.
  - 7.1.1.2. Hospedagem no IIS com `AspNetCoreModule` e como serviço Linux com `systemd`.
  - 7.1.1.3. Containerização com Docker — Dockerfile multi-stage para ASP.NET Core.
- **7.1.2. Cloud**
  - 7.1.2.1. Deploy no Azure App Service — configuração, variáveis de ambiente e slots de staging.

### 7.2 Performance e Monitoramento
- **7.2.1. Caching**
  - 7.2.1.1. In-memory cache (`IMemoryCache`), distributed cache com Redis e Response Caching.
  - 7.2.1.2. Compressão de resposta com `AddResponseCompression` e caching de assets estáticos.
- **7.2.2. Logs e Observabilidade**
  - 7.2.2.1. Logging nativo com `ILogger<T>`, integração com Serilog e gerenciamento de logs em produção.

---

> **Links Relacionados:**
> C#
> Entity Framework Core
> Docker
> Azure
> REST APIs
