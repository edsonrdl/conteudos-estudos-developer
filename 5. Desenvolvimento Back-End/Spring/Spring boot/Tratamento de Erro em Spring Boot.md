Em Spring Boot, a principal forma de lidar com erros é usando anotações especializadas.

- **`@ControllerAdvice`**: Essa anotação é a mais comum. Ela permite que você crie uma classe global para lidar com exceções lançadas por qualquer um dos seus controladores. Dentro dessa classe, você pode usar outras anotações para mapear exceções específicas.
    
    - **`@ExceptionHandler`**: Anotada em um método dentro de uma classe `@ControllerAdvice`, ela define qual tipo de exceção esse método irá tratar. Por exemplo, você pode ter um método que lida especificamente com `ResourceNotFoundException`.
        
    - **`@ResponseStatus`**: Você pode usar essa anotação junto com `@ExceptionHandler` para definir o código de status HTTP que será retornado para o cliente (por exemplo, `HttpStatus.NOT_FOUND` para uma exceção de "não encontrado").
        

Aqui está um exemplo conceitual:

Java

```
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    @ResponseBody
    public ErrorResponse handleResourceNotFoundException(ResourceNotFoundException ex) {
        return new ErrorResponse("Recurso não encontrado.");
    }

    @ExceptionHandler(IllegalArgumentException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ResponseBody
    public ErrorResponse handleIllegalArgumentException(IllegalArgumentException ex) {
        return new ErrorResponse("Requisição inválida: " + ex.getMessage());
    }
}
```

- **`HandlerExceptionResolver`**: Essa é uma interface mais de baixo nível. Você pode implementá-la para ter um controle mais granular sobre como as exceções são resolvidas, embora a maioria das necessidades possa ser atendida com `@ControllerAdvice`.
### Passo 1: Criar uma Exceção Personalizada

É uma boa prática criar exceções específicas para o seu domínio de negócio. Isso torna o código mais legível e permite um tratamento mais preciso.

Java

```
// ResourceNotFoundException.java
package com.exemplo.erros.exception;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

// @ResponseStatus pode ser usada aqui para simplificar, mas
// usaremos o @ExceptionHandler no ControllerAdvice para demonstração
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

---

### Passo 2: Criar a Classe de Tratamento de Erro Global

Essa classe será o seu ponto central para lidar com as exceções que ocorrerem em qualquer controlador da sua aplicação.

Java

```
// GlobalExceptionHandler.java
package com.exemplo.erros.handler;

import com.exemplo.erros.exception.ResourceNotFoundException;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

@ControllerAdvice
public class GlobalExceptionHandler {

    // Define qual tipo de exceção este método vai tratar
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<Object> handleResourceNotFoundException(ResourceNotFoundException ex) {
        // Cria uma estrutura de resposta para o erro
        Map<String, Object> body = new HashMap<>();
        body.put("timestamp", new Date());
        body.put("status", HttpStatus.NOT_FOUND.value());
        body.put("error", "Not Found");
        body.put("message", ex.getMessage());

        // Retorna a resposta com o status HTTP 404
        return new ResponseEntity<>(body, HttpStatus.NOT_FOUND);
    }

    // Exemplo de como tratar uma exceção genérica
    @ExceptionHandler(Exception.class)
    public ResponseEntity<Object> handleGenericException(Exception ex) {
        Map<String, Object> body = new HashMap<>();
        body.put("timestamp", new Date());
        body.put("status", HttpStatus.INTERNAL_SERVER_ERROR.value());
        body.put("error", "Internal Server Error");
        body.put("message", "Ocorreu um erro inesperado. Por favor, tente novamente mais tarde.");

        return new ResponseEntity<>(body, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

### Passo 3: Criar o Controlador que Lança a Exceção

Agora, vamos criar um controlador que simule um cenário onde a exceção `ResourceNotFoundException` é lançada.

Java

```
// ItemController.java
package com.exemplo.erros.controller;

import com.exemplo.erros.exception.ResourceNotFoundException;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ItemController {

    // Simula uma busca por ID
    @GetMapping("/items/{id}")
    public String getItemById(@PathVariable Long id) {
        // Se o ID for 100, simula que o recurso não foi encontrado
        if (id == 100) {
            throw new ResourceNotFoundException("Item com ID " + id + " não encontrado.");
        }
        
        return "Item encontrado: " + id;
    }
}
```

### Como Funciona

1. Um cliente faz uma requisição para a sua API, por exemplo, `GET /items/100`.
    
2. O `ItemController` recebe a requisição.
    
3. A lógica interna verifica o ID e, como ele é 100, lança uma `ResourceNotFoundException`.
    
4. O **Spring Boot intercepta** essa exceção antes que ela cause um _crash_ na aplicação.
    
5. Ele procura por uma classe anotada com `@ControllerAdvice` que tenha um método com `@ExceptionHandler` que trate `ResourceNotFoundException.class`.
    
6. O método `handleResourceNotFoundException` é encontrado e executado.
    
7. Esse método constrói a resposta de erro (`Map<String, Object> body`) e a retorna com o **status HTTP 404 Not Found**, conforme definido em `new ResponseEntity<>(body, HttpStatus.NOT_FOUND)`.
    
8. O cliente recebe uma resposta JSON formatada com as informações de erro, em vez de uma página de erro genérica do servidor.
    

Essa abordagem centraliza o tratamento de erros, tornando seu código mais limpo, fácil de manter e mais robusto, seguindo as melhores práticas do Spring.

### Performance em Spring Boot

Em geral, o uso de `@ControllerAdvice` com `@ExceptionHandler` é a abordagem mais performática e recomendada.

- **Por que é performático?**
    
    - **Tratamento centralizado:** Evita o custo de `try...catch` em cada método. Em vez de envolver cada bloco de código em uma lógica de tratamento, você tem um único ponto de entrada para todas as exceções.
        
    - **Otimizado para o framework:** O Spring Boot otimiza o uso dessas anotações, e elas são parte do fluxo de trabalho padrão do framework. Isso significa que o processamento de exceções é feito de forma nativa e eficiente, sem a necessidade de lógica de roteamento extra.
        
    - **Menos sobrecarga de `new Exception()`:** A exceção é lançada e tratada, mas você não precisa criar objetos de exceção adicionais apenas para sinalizar um erro. Isso é particularmente importante em um ambiente de alta concorrência, pois a criação de objetos de exceção pode ser uma operação cara em termos de alocação de memória e processamento do _stack trace_.
        
- **O que evitar:**
    
    - **Blocos `try...catch` espalhados:** Usar `try...catch` em cada método de serviço ou controlador é a abordagem menos performática. Ela não só torna o código mais difícil de ler e manter, mas também adiciona uma sobrecarga em cada chamada de método, independentemente de um erro ocorrer ou não.
        
    - **Capturar a exceção e lançar uma nova:** Evite a prática de capturar uma exceção e lançar uma nova exceção genérica. Isso cria um custo de processamento desnecessário, pois o _stack trace_ (o caminho de chamada da exceção) precisa ser gerado duas vezes.

### Criar uma Exceção Personalizada

É uma boa prática criar exceções específicas para o seu domínio de negócio. Isso torna o código mais legível e permite um tratamento mais preciso.

Java

```
// ResourceNotFoundException.java
package com.exemplo.erros.exception;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

// @ResponseStatus pode ser usada aqui para simplificar, mas
// usaremos o @ExceptionHandler no ControllerAdvice para demonstração
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

---

### Passo 2: Criar a Classe de Tratamento de Erro Global

Essa classe será o seu ponto central para lidar com as exceções que ocorrerem em qualquer controlador da sua aplicação.

Java

```
// GlobalExceptionHandler.java
package com.exemplo.erros.handler;

import com.exemplo.erros.exception.ResourceNotFoundException;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

@ControllerAdvice
public class GlobalExceptionHandler {

    // Define qual tipo de exceção este método vai tratar
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<Object> handleResourceNotFoundException(ResourceNotFoundException ex) {
        // Cria uma estrutura de resposta para o erro
        Map<String, Object> body = new HashMap<>();
        body.put("timestamp", new Date());
        body.put("status", HttpStatus.NOT_FOUND.value());
        body.put("error", "Not Found");
        body.put("message", ex.getMessage());

        // Retorna a resposta com o status HTTP 404
        return new ResponseEntity<>(body, HttpStatus.NOT_FOUND);
    }

    // Exemplo de como tratar uma exceção genérica
    @ExceptionHandler(Exception.class)
    public ResponseEntity<Object> handleGenericException(Exception ex) {
        Map<String, Object> body = new HashMap<>();
        body.put("timestamp", new Date());
        body.put("status", HttpStatus.INTERNAL_SERVER_ERROR.value());
        body.put("error", "Internal Server Error");
        body.put("message", "Ocorreu um erro inesperado. Por favor, tente novamente mais tarde.");

        return new ResponseEntity<>(body, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

### Passo 3: Criar o Controlador que Lança a Exceção

Agora, vamos criar um controlador que simule um cenário onde a exceção `ResourceNotFoundException` é lançada.

Java

```
// ItemController.java
package com.exemplo.erros.controller;

import com.exemplo.erros.exception.ResourceNotFoundException;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ItemController {

    // Simula uma busca por ID
    @GetMapping("/items/{id}")
    public String getItemById(@PathVariable Long id) {
        // Se o ID for 100, simula que o recurso não foi encontrado
        if (id == 100) {
            throw new ResourceNotFoundException("Item com ID " + id + " não encontrado.");
        }
        
        return "Item encontrado: " + id;
    }
}
```

### Como Funciona

1. Um cliente faz uma requisição para a sua API, por exemplo, `GET /items/100`.
    
2. O `ItemController` recebe a requisição.
    
3. A lógica interna verifica o ID e, como ele é 100, lança uma `ResourceNotFoundException`.
    
4. O **Spring Boot intercepta** essa exceção antes que ela cause um _crash_ na aplicação.
    
5. Ele procura por uma classe anotada com `@ControllerAdvice` que tenha um método com `@ExceptionHandler` que trate `ResourceNotFoundException.class`.
    
6. O método `handleResourceNotFoundException` é encontrado e executado.
    
7. Esse método constrói a resposta de erro (`Map<String, Object> body`) e a retorna com o **status HTTP 404 Not Found**, conforme definido em `new ResponseEntity<>(body, HttpStatus.NOT_FOUND)`.
    
8. O cliente recebe uma resposta JSON formatada com as informações de erro, em vez de uma página de erro genérica do servidor.