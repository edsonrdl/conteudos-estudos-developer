# 📚 Guia de Estudos: Spring Boot
#tag #backend #spring #java

> [!info] Visão Geral
> Spring Boot é um framework opinionated sobre o Spring Framework que elimina a configuração manual e permite criar aplicações Java prontas para produção com mínimo de setup. Este guia cobre desde os fundamentos da arquitetura até desenvolvimento de APIs REST, persistência, segurança, testes e implantação.

---

## 1 Domínio: Fundamentos e Arquitetura

### 1.1 Introdução ao Spring Boot
- **1.1.1. O que é e por que usar**
  - 1.1.1.1. O que é o Spring Boot, sua relação com o Spring Framework e o conceito de opinionated defaults.
  - 1.1.1.2. Vantagens: auto-configuração, servidor embutido, starter dependencies e sem XML.

### 1.2 Spring Framework Core
- **1.2.1. Injeção de Dependência e IoC**
  - 1.2.1.1. O que é Inversão de Controle (IoC) e como o contêiner Spring gerencia beans.
  - 1.2.1.2. Injeção de Dependência via `@Autowired`, `@Component`, `@Service`, `@Repository`.
- **1.2.2. Configuração da Aplicação**
  - 1.2.2.1. `@SpringBootApplication` e o que ela agrupa (`@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan`).
  - 1.2.2.2. `application.properties` e `application.yml` — propriedades externas e perfis (`@Profile`).

---

## 2 Domínio: Desenvolvimento Web e APIs REST

### 2.1 Spring MVC e Controladores
- **2.1.1. Controladores e Rotas**
  - 2.1.1.1. `@RestController` vs `@Controller`, mapeamento de rotas com `@RequestMapping`, `@GetMapping`, `@PostMapping`.
  - 2.1.1.2. Parâmetros de rota (`@PathVariable`), query params (`@RequestParam`) e corpo da requisição (`@RequestBody`).
- **2.1.2. Respostas e Status HTTP**
  - 2.1.2.1. Retornando `ResponseEntity`, manipulando status codes e headers de resposta.

### 2.2 Documentação de API
- **2.2.1. Swagger e OpenAPI**
  - 2.2.1.1. Configuração do SpringDoc OpenAPI (Swagger UI) para documentar e testar endpoints.

---

## 3 Domínio: Persistência de Dados

### 3.1 JPA e Hibernate
- **3.1.1. Entidades e Mapeamento**
  - 3.1.1.1. Anotações JPA: `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, relacionamentos (`@OneToMany`, `@ManyToOne`).
- **3.1.2. Repositórios com Spring Data**
  - 3.1.2.1. `JpaRepository` e `CrudRepository` — métodos prontos e query methods derivados do nome.
  - 3.1.2.2. Consultas customizadas com `@Query` (JPQL e SQL nativo).

### 3.2 Configuração de Banco de Dados
- **3.2.1. Conexão e Migrations**
  - 3.2.1.1. Configuração de datasource no `application.properties`, integração com Flyway ou Liquibase.

---

## 4 Domínio: Segurança

### 4.1 Spring Security
- **4.1.1. Autenticação**
  - 4.1.1.1. Configuração básica do Spring Security, `UserDetailsService` e autenticação em memória vs banco.
  - 4.1.1.2. JWT (JSON Web Tokens) — geração, validação e filtro de autenticação stateless.
- **4.1.2. Autorização**
  - 4.1.2.1. Autorização baseada em roles (`@PreAuthorize`, `hasRole`, `hasAuthority`).
  - 4.1.2.2. OAuth2 — integração com provedores externos (Google, GitHub) e Authorization Server.

---

## 5 Domínio: Testes

### 5.1 Testes Unitários
- **5.1.1. JUnit e Mockito**
  - 5.1.1.1. Testes unitários com JUnit 5, `@Mock`, `@InjectMocks` e verificação de comportamento com Mockito.

### 5.2 Testes de Integração
- **5.2.1. Spring Boot Test**
  - 5.2.1.1. `@SpringBootTest`, `@WebMvcTest`, `MockMvc` para testar controllers sem subir o servidor completo.
  - 5.2.1.2. Testes com banco em memória (H2) e `@DataJpaTest` para testar repositórios isoladamente.

---

## 6 Domínio: Implantação e Monitoramento

### 6.1 Empacotamento e Deploy
- **6.1.1. Build e Execução**
  - 6.1.1.1. Gerenciamento de dependências com Maven e Gradle, build de JAR executável (`mvn package`).
  - 6.1.1.2. Containerização com Docker — Dockerfile para aplicações Spring Boot.
  - 6.1.1.3. Deploy em cloud (AWS Elastic Beanstalk, Heroku, Railway).

### 6.2 Monitoramento
- **6.2.1. Spring Boot Actuator**
  - 6.2.1.1. Endpoints de saúde, métricas e informações da aplicação via Actuator (`/actuator/health`, `/actuator/metrics`).

---

> **Links Relacionados:**
> Java
> Spring Security
> Docker
> REST APIs
> Bancos de Dados Relacionais
