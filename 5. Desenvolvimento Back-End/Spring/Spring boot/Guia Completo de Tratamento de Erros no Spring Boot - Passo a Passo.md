## **Guia Completo de Tratamento de Erros no Spring Boot - Passo a Passo**

### **📁 Estrutura do Projeto**

```
src/main/java/com/exemplo/
├── exception/          # Pacote para exceções customizadas
├── handler/           # Pacote para handlers de exceção
├── dto/              # Pacote para DTOs (incluindo ErrorResponse)
├── controller/       # Pacote para controllers
└── service/         # Pacote para serviços
```

---

### **Passo 1: Criar a Classe de Resposta de Erro Padronizada**

java

```java
// ErrorResponse.java
package com.exemplo.dto;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import com.fasterxml.jackson.annotation.JsonInclude;

import java.time.LocalDateTime;
import java.util.Map;

/**
 * Classe que representa a estrutura padronizada de resposta de erro
 * Esta classe será serializada como JSON e enviada ao cliente
 * 
 * @Data - Gera getters, setters, toString, equals e hashCode
 * @Builder - Permite criar objetos usando o padrão Builder
 * @JsonInclude - Remove campos nulos do JSON final
 */
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
@JsonInclude(JsonInclude.Include.NON_NULL) // Não incluir campos nulos no JSON
public class ErrorResponse {
    
    // Momento em que o erro ocorreu
    private LocalDateTime timestamp;
    
    // Código de status HTTP (404, 400, 500, etc)
    private int status;
    
    // Tipo do erro (Not Found, Bad Request, etc)
    private String error;
    
    // Mensagem descritiva do erro para o usuário
    private String message;
    
    // Path da requisição que gerou o erro
    private String path;
    
    // Mapa de erros de validação (campo -> mensagem de erro)
    // Usado quando há múltiplos erros de validação
    private Map<String, String> validationErrors;
    
    // Informações adicionais (opcional)
    private Map<String, Object> additionalInfo;
}
```

---

### **Passo 2: Criar Exceções Customizadas do Domínio**

```java
// ResourceNotFoundException.java
package com.exemplo.exception;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

/**
 * Exceção lançada quando um recurso não é encontrado no sistema
 * 
 * @ResponseStatus - Define o status HTTP que será retornado (404 NOT FOUND)
 * Extende RuntimeException para ser uma exceção não-checada
 */
@ResponseStatus(value = HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException {
    
    // Informações sobre o recurso não encontrado
    private String resourceName;  // Nome do recurso (User, Product, etc)
    private String fieldName;      // Campo usado na busca (id, email, etc)
    private Object fieldValue;     // Valor do campo buscado
    
    /**
     * Construtor padrão com mensagem simples
     */
    public ResourceNotFoundException(String message) {
        super(message);
    }
    
    /**
     * Construtor com detalhes do recurso não encontrado
     * Gera uma mensagem padronizada e informativa
     */
    public ResourceNotFoundException(String resourceName, 
                                    String fieldName, 
                                    Object fieldValue) {
        // Formata a mensagem de erro de forma padronizada
        super(String.format("%s não encontrado com %s : '%s'",
                resourceName, fieldName, fieldValue));
        this.resourceName = resourceName;
        this.fieldName = fieldName;
        this.fieldValue = fieldValue;
    }
    
    // Getters para acessar os detalhes do erro se necessário
    public String getResourceName() {
        return resourceName;
    }
    
    public String getFieldName() {
        return fieldName;
    }
    
    public Object getFieldValue() {
        return fieldValue;
    }
}
```

java

```java
// BusinessException.java
package com.exemplo.exception;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

/**
 * Exceção para regras de negócio violadas
 * Retorna status 400 BAD REQUEST
 */
@ResponseStatus(HttpStatus.BAD_REQUEST)
public class BusinessException extends RuntimeException {
    
    private String errorCode; // Código de erro específico do negócio
    
    public BusinessException(String message) {
        super(message);
    }
    
    public BusinessException(String message, String errorCode) {
        super(message);
        this.errorCode = errorCode;
    }
    
    public String getErrorCode() {
        return errorCode;
    }
}
```

java

```java
// ValidationException.java  
package com.exemplo.exception;

import java.util.Map;

/**
 * Exceção para erros de validação com múltiplos campos
 */
public class ValidationException extends RuntimeException {
    
    private Map<String, String> violations;
    
    public ValidationException(String message, Map<String, String> violations) {
        super(message);
        this.violations = violations;
    }
    
    public Map<String, String> getViolations() {
        return violations;
    }
}
```

---

### **Passo 3: Criar o Handler Global de Exceções**

java

```java
// GlobalExceptionHandler.java
package com.exemplo.handler;

import com.exemplo.dto.ErrorResponse;
import com.exemplo.exception.ResourceNotFoundException;
import com.exemplo.exception.BusinessException;
import com.exemplo.exception.ValidationException;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import org.springframework.web.context.request.WebRequest;
import org.springframework.web.servlet.NoHandlerFoundException;

import lombok.extern.slf4j.Slf4j;

import java.time.LocalDateTime;
import java.util.HashMap;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * Classe responsável por interceptar e tratar todas as exceções da aplicação
 * 
 * @RestControllerAdvice - Combina @ControllerAdvice + @ResponseBody
 * - @ControllerAdvice: Torna a classe um handler global de exceções
 * - @ResponseBody: Garante que as respostas sejam serializadas como JSON
 * 
 * @Slf4j - Adiciona um logger (log) à classe via Lombok
 */
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {
    
    /**
     * Trata exceções de recurso não encontrado
     * 
     * @ExceptionHandler - Define qual tipo de exceção este método irá tratar
     * @param ex - A exceção capturada
     * @param request - Informações sobre a requisição HTTP
     * @return ResponseEntity com o erro formatado e status 404
     */
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFoundException(
            ResourceNotFoundException ex, 
            WebRequest request) {
        
        // Log da exceção (nível INFO pois não é erro crítico)
        log.info("Recurso não encontrado: {}", ex.getMessage());
        
        // Constrói a resposta de erro usando o padrão Builder
        ErrorResponse errorResponse = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.NOT_FOUND.value())  // 404
            .error("Not Found")
            .message(ex.getMessage())
            // Remove "uri=" do path para ficar mais limpo
            .path(request.getDescription(false).replace("uri=", ""))
            .build();
        
        // Retorna a resposta com status HTTP apropriado
        return new ResponseEntity<>(errorResponse, HttpStatus.NOT_FOUND);
    }
    
    /**
     * Trata exceções de regra de negócio
     */
    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ErrorResponse> handleBusinessException(
            BusinessException ex,
            WebRequest request) {
        
        // Log como warning pois é um erro esperado de negócio
        log.warn("Erro de negócio: {}", ex.getMessage());
        
        ErrorResponse errorResponse = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.BAD_REQUEST.value())  // 400
            .error("Business Rule Violation")
            .message(ex.getMessage())
            .path(request.getDescription(false).replace("uri=", ""))
            .build();
        
        // Adiciona código de erro se existir
        if (ex.getErrorCode() != null) {
            Map<String, Object> additionalInfo = new HashMap<>();
            additionalInfo.put("errorCode", ex.getErrorCode());
            errorResponse.setAdditionalInfo(additionalInfo);
        }
        
        return new ResponseEntity<>(errorResponse, HttpStatus.BAD_REQUEST);
    }
    
    /**
     * Trata erros de validação do Bean Validation (@Valid)
     * Esta exceção é lançada quando a validação de um @RequestBody falha
     */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationExceptions(
            MethodArgumentNotValidException ex,
            WebRequest request) {
        
        log.warn("Erro de validação nos campos da requisição");
        
        // Extrai todos os erros de validação campo por campo
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> {
            String fieldName = error.getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });
        
        // Cria uma mensagem resumida com a quantidade de erros
        String message = String.format("Erro na validação de %d campo(s)", 
                                      errors.size());
        
        ErrorResponse errorResponse = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.BAD_REQUEST.value())  // 400
            .error("Validation Failed")
            .message(message)
            .path(request.getDescription(false).replace("uri=", ""))
            .validationErrors(errors)  // Adiciona o mapa de erros
            .build();
        
        return new ResponseEntity<>(errorResponse, HttpStatus.BAD_REQUEST);
    }
    
    /**
     * Trata exceções customizadas de validação
     */
    @ExceptionHandler(ValidationException.class)
    public ResponseEntity<ErrorResponse> handleCustomValidation(
            ValidationException ex,
            WebRequest request) {
        
        log.warn("Erro de validação customizada: {}", ex.getMessage());
        
        ErrorResponse errorResponse = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.BAD_REQUEST.value())
            .error("Validation Failed")
            .message(ex.getMessage())
            .path(request.getDescription(false).replace("uri=", ""))
            .validationErrors(ex.getViolations())
            .build();
        
        return new ResponseEntity<>(errorResponse, HttpStatus.BAD_REQUEST);
    }
    
    /**
     * Trata erro 404 quando endpoint não existe
     */
    @ExceptionHandler(NoHandlerFoundException.class)
    public ResponseEntity<ErrorResponse> handleNoHandlerFound(
            NoHandlerFoundException ex,
            WebRequest request) {
        
        log.warn("Endpoint não encontrado: {}", ex.getRequestURL());
        
        ErrorResponse errorResponse = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.NOT_FOUND.value())
            .error("Endpoint Not Found")
            .message("O endpoint requisitado não existe")
            .path(ex.getRequestURL())
            .build();
        
        return new ResponseEntity<>(errorResponse, HttpStatus.NOT_FOUND);
    }
    
    /**
     * Trata IllegalArgumentException (argumentos inválidos)
     */
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<ErrorResponse> handleIllegalArgument(
            IllegalArgumentException ex,
            WebRequest request) {
        
        log.error("Argumento ilegal: {}", ex.getMessage());
        
        ErrorResponse errorResponse = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.BAD_REQUEST.value())
            .error("Invalid Argument")
            .message(ex.getMessage())
            .path(request.getDescription(false).replace("uri=", ""))
            .build();
        
        return new ResponseEntity<>(errorResponse, HttpStatus.BAD_REQUEST);
    }
    
    /**
     * Trata qualquer exceção não tratada especificamente (catch-all)
     * IMPORTANTE: Este handler deve ser sempre o último na classe
     */
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGlobalException(
            Exception ex,
            WebRequest request) {
        
        // Log como ERROR pois é uma exceção não esperada
        log.error("Erro não esperado: ", ex);
        
        // Não expor detalhes internos em produção
        String message = "Ocorreu um erro interno. Por favor, tente novamente mais tarde.";
        
        // Em ambiente de desenvolvimento, pode mostrar a mensagem real
        // if (environment.equals("dev")) {
        //     message = ex.getMessage();
        // }
        
        ErrorResponse errorResponse = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.INTERNAL_SERVER_ERROR.value())  // 500
            .error("Internal Server Error")
            .message(message)
            .path(request.getDescription(false).replace("uri=", ""))
            .build();
        
        return new ResponseEntity<>(errorResponse, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

---

### **Passo 4: Criar o Controller com Exemplos de Uso**

java

```java
// UserController.java
package com.exemplo.controller;

import com.exemplo.dto.UserDTO;
import com.exemplo.entity.User;
import com.exemplo.exception.BusinessException;
import com.exemplo.exception.ResourceNotFoundException;
import com.exemplo.service.UserService;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.util.List;

/**
 * Controller REST para gerenciar usuários
 * Demonstra o uso das exceções customizadas
 */
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    /**
     * Busca um usuário por ID
     * Lança ResourceNotFoundException se não encontrar
     */
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        
        // Simulação: Se ID for 999, lançar exceção para teste
        if (id == 999) {
            throw new ResourceNotFoundException("User", "id", id);
        }
        
        // Busca normal usando Optional
        User user = userService.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException(
                "User", "id", id));
        
        return ResponseEntity.ok(user);
    }
    
    /**
     * Cria um novo usuário
     * @Valid dispara a validação automática do Bean Validation
     */
    @PostMapping
    public ResponseEntity<User> createUser(
            @Valid @RequestBody UserDTO userDTO) {
        
        // Verifica regra de negócio: email não pode ser duplicado
        if (userService.emailExists(userDTO.getEmail())) {
            throw new BusinessException(
                "Email já cadastrado no sistema", 
                "EMAIL_DUPLICATE"
            );
        }
        
        // Verifica regra de negócio: idade mínima
        if (userDTO.getAge() < 18) {
            throw new BusinessException(
                "Usuário deve ter no mínimo 18 anos",
                "AGE_RESTRICTION"
            );
        }
        
        User user = userService.create(userDTO);
        return new ResponseEntity<>(user, HttpStatus.CREATED);
    }
    
    /**
     * Atualiza um usuário existente
     */
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(
            @PathVariable Long id,
            @Valid @RequestBody UserDTO userDTO) {
        
        // Verifica se o usuário existe
        User existingUser = userService.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException(
                "User", "id", id));
        
        // Atualiza o usuário
        User updatedUser = userService.update(id, userDTO);
        return ResponseEntity.ok(updatedUser);
    }
    
    /**
     * Deleta um usuário
     */
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        
        // Verifica se existe antes de deletar
        if (!userService.existsById(id)) {
            throw new ResourceNotFoundException("User", "id", id);
        }
        
        // Verifica regra de negócio: não pode deletar admin
        User user = userService.findById(id).get();
        if ("ADMIN".equals(user.getRole())) {
            throw new BusinessException(
                "Não é permitido deletar usuários administradores",
                "ADMIN_DELETE_FORBIDDEN"
            );
        }
        
        userService.deleteById(id);
        return ResponseEntity.noContent().build();
    }
    
    /**
     * Endpoint para testar exceção genérica
     */
    @GetMapping("/test-error")
    public String testError() {
        // Simula um erro inesperado
        throw new RuntimeException("Erro simulado para teste");
    }
}
```

---

### **Passo 5: Criar o DTO com Validações**

java

```java
// UserDTO.java
package com.exemplo.dto;

import lombok.Data;
import javax.validation.constraints.*;

/**
 * DTO para criação/atualização de usuários
 * As anotações de validação são processadas automaticamente com @Valid
 */
@Data
public class UserDTO {
    
    @NotBlank(message = "Nome é obrigatório")
    @Size(min = 3, max = 100, message = "Nome deve ter entre 3 e 100 caracteres")
    private String name;
    
    @NotBlank(message = "Email é obrigatório")
    @Email(message = "Email deve ser válido")
    private String email;
    
    @NotNull(message = "Idade é obrigatória")
    @Min(value = 0, message = "Idade deve ser maior que zero")
    @Max(value = 150, message = "Idade deve ser menor que 150")
    private Integer age;
    
    @Pattern(regexp = "^\\+?[1-9]\\d{1,14}$", 
             message = "Telefone deve ser válido")
    private String phone;
    
    private String role;
}
```

---

### **Passo 6: Configurações no application.yml**

yaml

```yaml
# application.yml
server:
  error:
    # Sempre incluir mensagem de erro na resposta
    include-message: always
    # Incluir erros de binding (validação)
    include-binding-errors: always
    # Incluir stack trace apenas quando parâmetro trace=true
    include-stacktrace: on_param
    # Não incluir o nome da exceção na resposta
    include-exception: false
    # Habilitar o tratamento de 404 pelo Spring
    whitelabel:
      enabled: false

spring:
  # Configuração do Jackson (serialização JSON)
  jackson:
    # Não incluir campos nulos no JSON
    default-property-inclusion: non_null
    # Formato de data/hora
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: America/Sao_Paulo
  
  # Habilitar tratamento de 404
  mvc:
    throw-exception-if-no-handler-found: true
  web:
    resources:
      add-mappings: false

# Configuração de logging
logging:
  level:
    # Nível de log para o package da aplicação
    com.exemplo: DEBUG
    # Logs do Spring
    org.springframework.web: INFO
    # Logs do Hibernate
    org.hibernate: WARN
  # Padrão de log
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} - %msg%n"
```

---

### **Passo 7: Testes Unitários do Handler**

java

```java
// GlobalExceptionHandlerTest.java
package com.exemplo.handler;

import com.exemplo.dto.UserDTO;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import com.fasterxml.jackson.databind.ObjectMapper;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

/**
 * Testes para verificar se o tratamento de erros está funcionando
 */
@SpringBootTest
@AutoConfigureMockMvc
public class GlobalExceptionHandlerTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Autowired
    private ObjectMapper objectMapper;
    
    /**
     * Testa se retorna 404 quando recurso não existe
     */
    @Test
    public void whenResourceNotFound_thenReturns404() throws Exception {
        mockMvc.perform(get("/api/users/999"))
            .andExpect(status().isNotFound())
            .andExpect(jsonPath("$.status").value(404))
            .andExpect(jsonPath("$.error").value("Not Found"))
            .andExpect(jsonPath("$.message").exists())
            .andExpect(jsonPath("$.timestamp").exists())
            .andExpect(jsonPath("$.path").exists());
    }
    
    /**
     * Testa validação de campos obrigatórios
     */
    @Test
    public void whenValidationFails_thenReturns400() throws Exception {
        UserDTO invalidUser = new UserDTO();
        // Não preenche campos obrigatórios
        
        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(invalidUser)))
            .andExpect(status().isBadRequest())
            .andExpect(jsonPath("$.status").value(400))
            .andExpect(jsonPath("$.error").value("Validation Failed"))
            .andExpect(jsonPath("$.validationErrors").exists())
            .andExpect(jsonPath("$.validationErrors.name").exists())
            .andExpect(jsonPath("$.validationErrors.email").exists());
    }
    
    /**
     * Testa endpoint inexistente
     */
    @Test
    public void whenEndpointNotFound_thenReturns404() throws Exception {
        mockMvc.perform(get("/api/endpoint-inexistente"))
            .andExpect(status().isNotFound())
            .andExpect(jsonPath("$.error").value("Endpoint Not Found"));
    }
}
```

---

### **Como o Sistema Funciona - Fluxo Completo:**

1. **Cliente faz requisição** → `GET /api/users/100`
2. **Spring Boot roteia** para o método correto no Controller
3. **Controller processa** a requisição e pode lançar exceção
4. **Se exceção ocorrer**, Spring Boot a intercepta antes de retornar ao cliente
5. **Spring busca** um `@ExceptionHandler` apropriado no `@RestControllerAdvice`
6. **Handler encontrado** processa a exceção e cria o `ErrorResponse`
7. **ErrorResponse é serializado** para JSON pelo Jackson
8. **Cliente recebe** resposta padronizada com status HTTP apropriado

---

### **Performance e Melhores Práticas:**

✅ **Por que essa abordagem é performática:**

- **Centralizada**: Um único ponto de tratamento evita duplicação
- **Otimizada pelo Spring**: Framework já otimiza o fluxo de exceções
- **Sem try-catch espalhados**: Reduz overhead em cada método
- **Lazy loading**: Exceções só são criadas quando necessário

❌ **O que evitar:**

- Try-catch em cada método do controller
- Criar stack traces desnecessários
- Expor detalhes técnicos em produção
- Não fazer log adequado dos erros
- Retornar status HTTP incorretos

---

### **Resultado Exemplo - JSON de Erro:**
B
json

```json
{
  "timestamp": "2024-01-15T10:30:45",
  "status": 404,
  "error": "Not Found",
  "message": "User não encontrado com id : '100'",
  "path": "/api/users/100"
}
```

json

```json
{
  "timestamp": "2024-01-15T10:32:15",
  "status": 400,
  "error": "Validation Failed",
  "message": "Erro na validação de 2 campo(s)",
  "path": "/api/users",
  "validationErrors": {
    "name": "Nome é obrigatório",
    "email": "Email deve ser válido"
  }
}
```

Este guia completo fornece uma solução robusta, escalável e de fácil manutenção para tratamento de erros no Spring Boot, seguindo as melhores práticas da indústria!

