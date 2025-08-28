## 🧩 **1. Anotações de Inicialização e Configuração Base**

|Anotação|Função|
|---|---|
|`@SpringBootApplication`|Inicializa a aplicação, combinando `@Configuration`, `@EnableAutoConfiguration` e `@ComponentScan`.|
|`@EnableAutoConfiguration`|Ativa a configuração automática com base nas dependências do classpath.|
|`@SpringBootConfiguration`|Indica que a classe é uma configuração Spring Boot.|
|`@ComponentScan`|Define onde o Spring deve procurar componentes.|

---

## 🏗️ **2. Anotações de Componentes e Inversão de Controle (IoC)**

|Anotação|Função|
|---|---|
|`@Component`|Define um bean genérico.|
|`@Service`|Define um bean de serviço (camada de lógica de negócio).|
|`@Repository`|Define um bean de repositório (camada de persistência).|
|`@Autowired`|Injeta dependências automaticamente.|
|`@Qualifier`|Especifica qual bean deve ser injetado quando há múltiplas implementações.|
|`@Primary`|Define o bean preferido entre múltiplos candidatos.|
|`@Bean`|Cria um bean manualmente dentro de uma classe `@Configuration`.|

---

## ⚙️ **3. Anotações de Propriedades e Configuração Externa**

|Anotação|Função|
|---|---|
|`@Value`|Injeta valores de propriedades (ex: `application.properties`).|
|`@ConfigurationProperties`|Mapeia grupos de propriedades externas para uma classe.|
|`@PropertySource`|Especifica arquivos de propriedades externos.|
|`@Profile`|Ativa um bean somente sob perfis específicos (dev, prod, etc.).|
|`@Conditional`|Condiciona a criação de beans à presença de alguma condição.|

---

## 🌐 **4. Anotações de Web (Spring MVC / REST)**

|Anotação|Função|
|---|---|
|`@Controller`|Define uma classe como controlador MVC.|
|`@RestController`|Combina `@Controller` e `@ResponseBody` para APIs REST.|
|`@RequestMapping`|Mapeia requisições HTTP para controladores ou métodos.|
|`@GetMapping`, `@PostMapping`, etc.|Mapeamentos específicos para métodos HTTP.|
|`@PathVariable`|Captura variáveis da URL.|
|`@RequestParam`|Captura parâmetros de query string.|
|`@RequestHeader`|Captura cabeçalhos HTTP.|
|`@CookieValue`|Captura cookies.|
|`@CrossOrigin`|Habilita CORS.|
|`@ModelAttribute`|Injeta atributos comuns aos métodos do controlador.|
|`@ResponseBody`|Retorna o resultado diretamente no corpo da resposta.|
|`@ResponseStatus`|Define o status HTTP da resposta.|
|`@SessionAttributes`|Armazena atributos em sessão HTTP.|

---

## 🔒 **5. Anotações de Transações e Persistência**

|Anotação|Função|
|---|---|
|`@Transactional`|Define métodos ou classes que operam dentro de uma transação.|

---

## 💥 **6. Anotações para Tratamento de Erros e Exceções**

|Anotação|Função|
|---|---|
|`@ControllerAdvice`|Define uma classe global para tratamento de exceções.|
|`@ExceptionHandler`|Trata exceções específicas nos controladores.|

---

## ⏱️ **7. Anotações para Agendamentos e Execução Assíncrona**

|Anotação|Função|
|---|---|
|`@Scheduled`|Define tarefas agendadas com base em tempo.|
|`@Async`|Executa métodos de forma assíncrona (thread separada).|

---

## 🧪 **8. Anotações para Testes**

|Anotação|Função|
|---|---|
|`@SpringBootTest`|Testa a aplicação carregando o contexto completo do Spring.|
|`@DataJpaTest`|Testa a camada de repositórios (JPA).|
|`@WebMvcTest`|Testa controladores MVC isoladamente.|
|`@JsonTest`|Testa a serialização e desserialização de JSON.|
|`@WebFluxTest`|Testa controladores reativos (WebFlux).|

---

## ⚡ **9. Anotações de Cache**

|Anotação|Função|
|---|---|
|`@Cacheable`|Armazena em cache o retorno de métodos.|

- 1. @SpringBootApplication
    
    - **Função**: Esta anotação é a principal do Spring Boot. Ela combina `@Configuration`, `@EnableAutoConfiguration` e `@ComponentScan`.
        
    - **Exemplo**:
        
        ```java
        @SpringBootApplication
        public class MinhaAplicacao {
            public static void main(String[] args) {
                SpringApplication.run(MinhaAplicacao.class, args);
            }
        }
        
        ```
        

---

## 2. @EnableAutoConfiguration

- **Função**: Habilita a configuração automática do Spring Boot, permitindo que ele configure automaticamente os beans e componentes com base nas dependências do seu projeto.
- **Exemplo**:

```java
java
Copiar
@EnableAutoConfiguration
public class MinhaConfiguracao {
    // Configurações personalizadas, se necessário
}

```

---

## 3. @SpringBootConfiguration

- **Função**: Indica que uma classe fornece configurações específicas do Spring Boot.
- **Exemplo**:

```java
java
Copiar
@SpringBootConfiguration
public class MinhaConfiguracao {
    // Configurações personalizadas
}

```

---

## 4. @ComponentScan

- **Função**: Especifica os pacotes onde o Spring deve procurar por componentes, configurações e serviços.
- **Exemplo**:

```java
java
Copiar
@ComponentScan(basePackages = "com.exemplo.minhaaplicacao")
public class MinhaAplicacao {
    // ...
}

```

---

## 5. @RestController, @Controller

- **Função**: Anotações para definir controladores web. `@RestController` combina `@Controller` e `@ResponseBody`.
- **Exemplo**:

```java
java
Copiar
@RestController
public class MeuControlador {
    // Métodos para lidar com requisições web
}

```

---

## 6. @RequestMapping

- **Função**: Mapeia requisições web para métodos específicos do controlador.
- **Exemplo**:

```java
java
Copiar
@RequestMapping("/usuarios")
public class MeuControlador {
    // ...
}

```

---

## 7. @GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping

- **Função**: Especializações de `@RequestMapping` para métodos HTTP específicos (GET, POST, PUT, DELETE, PATCH).
- **Exemplo**:

```java
java
Copiar
@GetMapping("/usuarios/{id}")
public Usuario buscarUsuario(@PathVariable Long id) {
    // ...
}

```

---

## 8. @ResponseBody

- **Função**: Indica que o retorno de um método deve ser escrito diretamente no corpo da resposta HTTP.
- **Exemplo**:

```java
java
Copiar
@GetMapping("/usuarios")
@ResponseBody
public List<Usuario> listarUsuarios() {
    // ...
}

```

---

## 9. @PathVariable, @RequestParam

- **Função**: Anotações para acessar parâmetros de requisições web. `@PathVariable` para parâmetros na URL e `@RequestParam` para parâmetros de consulta.
- **Exemplo**:

```java
java
Copiar
@GetMapping("/usuarios/{id}")
public Usuario buscarUsuario(@PathVariable Long id, @RequestParam String nome) {
    // ...
}

```

---

## 10. @CrossOrigin

- **Função**: Habilita requisições de origem cruzada (CORS) para permitir que seu backend seja acessado por diferentes domínios.
- **Exemplo**:

```java
java
Copiar
@CrossOrigin(origins = "<http://localhost:3000>")
@RestController
public class MeuControlador {
    // ...
}

```

---

## 11. @Component, @Service, @Repository

- **Função**: Anotações para definir componentes gerenciados pelo Spring. `@Component` é genérico, `@Service` para serviços e `@Repository` para acesso a dados.
- **Exemplo**:

```java
java
Copiar
@Service
public class MeuServico {
    // Lógica de negócios
}

```

---

## 12. @Autowired

- **Função**: Injeta dependências em um componente.
- **Exemplo**:

```java
java
Copiar
@Service
public class MeuServico {
    @Autowired
    private MeuRepositorio meuRepositorio;
    // ...
}

```

---

## 13. @Qualifier

- **Função**: Usado quando há várias implementações de uma dependência, permitindo especificar qual deve ser usada.
- **Exemplo**:

```java
java
Copiar
@Service
public class MeuServico {
    @Autowired
    @Qualifier("meuRepositorio")
    private MeuRepositorio meuRepositorio;
    // ...
}

```

---

## 14. @Primary

- **Função**: Indica que um bean deve ter preferência quando há várias opções para autowiring.
- **Exemplo**:

```java
java
Copiar
@Repository
@Primary
public class MeuRepositorioImpl implements MeuRepositorio {
    // ...
}

```

---

## 15. @Bean

- **Função**: Anotação para métodos que produzem beans a serem gerenciados pelo Spring.
- **Exemplo**:

```java
java
Copiar
@Configuration
public class MinhaConfiguracao {
    @Bean
    public MeuServico meuServico(MeuRepositorio meuRepositorio) {
        return new MeuServico(meuRepositorio);
    }
}

```

---

## 16. @Value

- **Função**: Injeta valores de propriedades em campos ou parâmetros.
- **Exemplo**:

```java
java
Copiar
@Component
public class MeuComponente {
    @Value("${minha.propriedade}")
    private String minhaPropriedade;
    // ...
}

```

---

## 17. @ConfigurationProperties

- **Função**: Vincula e valida configurações externas a um objeto de configuração.
- **Exemplo**:

```java
java
Copiar
@ConfigurationProperties(prefix = "minha")
public class MinhasPropriedades {
    private String propriedade1;
    private int propriedade2;
    // Getters e setters
}

```

---

## 18. @PropertySource

- **Função**: Especifica a localização de arquivos de propriedades a serem adicionados ao ambiente do Spring.
- **Exemplo**:

```java
java
Copiar
@PropertySource("classpath:minhas-configuracoes.properties")
@Configuration
public class MinhaConfiguracao {
    // ...
}

```

---

## 19. @Profile

- **Função**: Indica que um componente está disponível apenas quando determinados perfis estão ativos.
- **Exemplo**:

```java
java
Copiar
@Component
@Profile("desenvolvimento")
public class MeuComponente {
    // ...
}

```

---

## 20. @Conditional

- **Função**: Inclui ou exclui partes da configuração com base em condições.
- **Exemplo**:

```java
java
Copiar
@Configuration
@ConditionalOnProperty(name = "minha.propriedade", havingValue = "true")
public class MinhaConfiguracao {
    // ...
}

```

---

## 21. @Scheduled

- **Função**: Marca um método para ser executado em intervalos periódicos.
- **Exemplo**:

```java
java
Copiar
@Component
public class MeuComponente {
    @Scheduled(fixedRate = 1000)
    public void executarTarefa() {
        // ...
    }
}

```

---

## 22. @SpringBootTest, @DataJpaTest, @WebMvcTest, @JsonTest, @WebFluxTest

- **Função**: Anotações para diferentes tipos de testes no Spring Boot.
- **Exemplo**:

```java
java
Copiar
@SpringBootTest
public class MeuTeste {
    // ...
}

```

---

## 23. @ControllerAdvice

- **Função**: Permite criar um componente que intercepta exceções lançadas por controladores. Isso é útil para centralizar o tratamento de erros e fornecer respostas mais consistentes para o cliente.
- **Exemplo**:

```java
java
Copiar
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(value = { IllegalArgumentException.class, HttpRequestMethodNotSupportedException.class })
    public ResponseEntity<Object> handleBadRequest(Exception ex) {
        return new ResponseEntity<>(new ErrorResponse(400, "Requisição inválida"), HttpStatus.BAD_REQUEST);
    }

    // Outros métodos para tratar diferentes tipos de exceções
}

```

---

## 24. @ExceptionHandler

- **Função**: Usada em conjunto com `@ControllerAdvice`, esta anotação define métodos que tratam exceções específicas.
- **Exemplo**:

```java
java
Copiar
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(value = EntityNotFoundException.class)
    public ResponseEntity<Object> handleNotFound(EntityNotFoundException ex) {
        return new ResponseEntity<>(new ErrorResponse(404, "Entidade não encontrada"), HttpStatus.NOT_FOUND);
    }

    // Outros métodos para tratar diferentes tipos de exceções
}

```

---

## 25. @ModelAttribute

- **Função**: Permite vincular um objeto a um método do controlador, que pode ser usado para pré-popular dados ou realizar alguma ação antes do método principal ser executado.
- **Exemplo**:

```java
java
Copiar
@Controller
public class MeuControlador {

    @ModelAttribute("usuario")
    public Usuario preencherUsuario() {
        return new Usuario(); // Ou buscar um usuário existente
    }

    @PostMapping("/usuarios")
    public String salvarUsuario(@ModelAttribute("usuario") Usuario usuario) {
        // Salvar o usuário
    }
}

```

---

## 26. @RequestHeader

- **Função**: Permite acessar os cabeçalhos da requisição HTTP.
- **Exemplo**:

```java
java
Copiar
@GetMapping("/usuarios")
public String listarUsuarios(@RequestHeader("X-Token") String token) {
    // Usar o token para autenticação
}

```

---

## 27. @CookieValue

- **Função**: Permite acessar os valores de cookies.
- **Exemplo**:

```java
java
Copiar
@GetMapping("/usuarios")
public String listarUsuarios(@CookieValue("meuCookie") String cookie) {
    // Usar o valor do cookie
}

```

---

## 28. @SessionAttributes

- **Função**: Permite armazenar atributos na sessão HTTP.
- **Exemplo**:

```java
java
Copiar
@Controller
@SessionAttributes("usuario")
public class MeuControlador {
    // ...
}

```

---

## 29. @ResponseStatus

- **Função**: Define o código de status HTTP da resposta.
- **Exemplo**:

```java
java
Copiar
@GetMapping("/usuarios")
@ResponseStatus(HttpStatus.OK)
public List<Usuario> listarUsuarios() {
    // ...
}

```

---

## 30. @Transactional

- **Função**: Anotação para métodos que devem ser executados dentro de uma transação.
- **Exemplo**:

```java
java
Copiar
@Service
public class MeuServico {

    @Transactional
    public void salvarUsuario(Usuario usuario) {
        // Salvar o usuário e outras entidades relacionadas
    }
}

```

---

## 31. @Cacheable

- **Função**: Permite habilitar o cache para o retorno de um método.
- **Exemplo**:

```java
java
Copiar
@Service
public class MeuServico {

    @Cacheable("usuarios")
    public List<Usuario> listarUsuarios() {
        // Buscar usuários no banco de dados
    }
}

```

---

## 32. @Async

- **Função**: Marca um método para ser executado de forma assíncrona.
- **Exemplo**:

```java
java
Copiar
@Service
public class MeuServico {

    @Async
    public void enviarEmail(Usuario usuario) {
        // Enviar email para o usuário
    }
}

```