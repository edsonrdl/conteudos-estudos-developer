## 1. Usando Spring Events (Recomendado)

### Criando um Evento Customizado


```java
import org.springframework.context.ApplicationEvent;

public class ContainerTriggerEvent extends ApplicationEvent {
    private final String containerName;
    private final String action;
    
    public ContainerTriggerEvent(Object source, String containerName, String action) {
        super(source);
        this.containerName = containerName;
        this.action = action;
    }
    
    // getters
    public String getContainerName() { return containerName; }
    public String getAction() { return action; }
}
```

### Observer (Event Listener)


```java
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class ContainerEventObserver {
    
    @EventListener
    public void handleContainerEvent(ContainerTriggerEvent event) {
        System.out.println("Container: " + event.getContainerName() + 
                          " - Action: " + event.getAction());
        
        // Lógica para manipular o container
        switch(event.getAction()) {
            case "START":
                startContainer(event.getContainerName());
                break;
            case "STOP":
                stopContainer(event.getContainerName());
                break;
            case "RESTART":
                restartContainer(event.getContainerName());
                break;
        }
    }
    
    private void startContainer(String containerName) {
        // Implementar lógica de start
        System.out.println("Starting container: " + containerName);
    }
    
    private void stopContainer(String containerName) {
        // Implementar lógica de stop
        System.out.println("Stopping container: " + containerName);
    }
    
    private void restartContainer(String containerName) {
        // Implementar lógica de restart
        System.out.println("Restarting container: " + containerName);
    }
}
```

### Publisher (Disparador do Evento)


```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Service;

@Service
public class ContainerTriggerService {
    
    @Autowired
    private ApplicationEventPublisher eventPublisher;
    
    public void triggerContainerAction(String containerName, String action) {
        // Disparar o evento
        ContainerTriggerEvent event = new ContainerTriggerEvent(this, containerName, action);
        eventPublisher.publishEvent(event);
    }
}
```

### Controller para Testar


```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/container")
public class ContainerController {
    
    @Autowired
    private ContainerTriggerService triggerService;
    
    @PostMapping("/{containerName}/{action}")
    public String triggerContainer(@PathVariable String containerName, 
                                 @PathVariable String action) {
        triggerService.triggerContainerAction(containerName, action);
        return "Trigger enviado para " + containerName + " - " + action;
    }
}
```

## 2. Implementação com Observer Pattern Tradicional

### Interface Observer


```java
public interface ContainerObserver {
    void update(String containerName, String action);
}
```

### Subject (Observable)


```java
import org.springframework.stereotype.Component;
import java.util.ArrayList;
import java.util.List;

@Component
public class ContainerSubject {
    private List<ContainerObserver> observers = new ArrayList<>();
    
    public void addObserver(ContainerObserver observer) {
        observers.add(observer);
    }
    
    public void removeObserver(ContainerObserver observer) {
        observers.remove(observer);
    }
    
    public void notifyObservers(String containerName, String action) {
        for (ContainerObserver observer : observers) {
            observer.update(containerName, action);
        }
    }
    
    public void triggerContainer(String containerName, String action) {
        System.out.println("Triggering: " + containerName + " - " + action);
        notifyObservers(containerName, action);
    }
}
```

### Observer Concreto


```java
import org.springframework.stereotype.Component;

@Component
public class ContainerActionObserver implements ContainerObserver {
    
    @Override
    public void update(String containerName, String action) {
        System.out.println("Observer notificado - Container: " + containerName + 
                          ", Action: " + action);
        executeContainerAction(containerName, action);
    }
    
    private void executeContainerAction(String containerName, String action) {
        // Implementar ações do container aqui
        switch(action.toLowerCase()) {
            case "start":
                // Lógica para iniciar container
                break;
            case "stop":
                // Lógica para parar container
                break;
            case "restart":
                // Lógica para reiniciar container
                break;
        }
    }
}
```

## 3. Eventos Assíncronos (Para melhor performance)


```java
import org.springframework.context.event.EventListener;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Component;

@Component
public class AsyncContainerObserver {
    
    @EventListener
    @Async
    public void handleContainerEventAsync(ContainerTriggerEvent event) {
        System.out.println("Processando assincronamente: " + 
                          event.getContainerName() + " - " + event.getAction());
        
        // Processamento longo pode ser feito aqui sem bloquear
        try {
            Thread.sleep(2000); // Simula processamento
            System.out.println("Processamento concluído para: " + event.getContainerName());
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### Configuração para Async


```java
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;

@Configuration
@EnableAsync
public class AsyncConfig {
    // Configuração automática do Spring para @Async
}
```

## 4. Uso Prático

Para usar o sistema:



```java
// Em um controller ou service
@Autowired
private ContainerTriggerService triggerService;

// Disparar eventos
triggerService.triggerContainerAction("my-app-container", "START");
triggerService.triggerContainerAction("database-container", "RESTART");
```

A abordagem com **Spring Events** é geralmente preferível porque:

- Desacopla completamente o publisher dos observers
- Suporta múltiplos listeners automaticamente
- Integra bem com o ecossistema Spring
- Permite processamento assíncrono facilmente
- Fornece melhor testabilidade