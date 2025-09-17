O .NET Core oferece múltiplas abordagens, com destaque para o uso de _middlewares_ e filtros.

- **Middleware de Tratamento de Exceções**: No `.NET Core`, a principal forma de lidar com erros globais é através do middleware. Você configura isso no arquivo `Startup.cs` ou `Program.cs`. O método `app.UseExceptionHandler()` captura todas as exceções não tratadas na sua aplicação e as redireciona para um caminho específico.
    
    - Dentro desse caminho, você pode criar uma _pipeline_ para formatar a resposta de erro, registrá-la em um log e retornar uma resposta JSON com os detalhes.
        

Exemplo de configuração no `Program.cs` para ASP.NET Core 6:

C#

```
app.UseExceptionHandler(errorApp =>
{
    errorApp.Run(async context =>
    {
        context.Response.StatusCode = 500;
        context.Response.ContentType = "application/json";

        var exceptionHandlerPathFeature =
            context.Features.Get<IExceptionHandlerPathFeature>();
        
        // Log the exception
        // logger.LogError(exceptionHandlerPathFeature.Error, "Ocorreu um erro inesperado.");

        // Return a custom error response
        await context.Response.WriteAsJsonAsync(new { 
            error = "Ocorreu um erro inesperado." 
        });
    });
});
```

- **Filtros de Exceção (`Exception Filters`)**: Semelhante ao `@ControllerAdvice` do Spring, os filtros de exceção em `.NET` permitem que você defina a lógica de tratamento de erro para ações de controladores ou para a aplicação inteira. Você cria uma classe que herda de `IExceptionFilter` e a aplica com a anotação `[TypeFilter(typeof(CustomExceptionFilter))]` ou globalmente em `Program.cs`.
    

Exemplo de um filtro de exceção:

C#

```
public class CustomExceptionFilter : IExceptionFilter
{
    public void OnException(ExceptionContext context)
    {
        // Define o código de status e a resposta
        context.Result = new ObjectResult(new { error = "Ocorreu um erro no servidor." })
        {
            StatusCode = StatusCodes.Status500InternalServerError
        };
        context.ExceptionHandled = true; // Indica que a exceção foi tratada
    }
}
```

### Pontos em Comum e Diferenças

- **Centralização**: Ambas as plataformas incentivam o tratamento de erros em um lugar central, em vez de espalhar blocos `try-catch` por todo o seu código.
    
- **Abordagem**: O Spring usa **anotações** para decorar classes e métodos, enquanto o .NET usa **middlewares** para a abordagem global e **filtros** para uma abordagem mais granular, ambos com suas próprias interfaces e atributos.
    
- **Controle**: Ambas as abordagens permitem um controle total sobre o código de status HTTP, o corpo da resposta e o registro de erros.
### Performance em .NET Core

No .NET Core, a abordagem mais performática e recomendada é o **Middleware de Tratamento de Exceções**.

- **Por que é performático?**
    
    - **Fluxo de Pipeline:** O middleware atua como um "catch-all" global no início da _pipeline_ de requisição. Ele captura qualquer exceção que "escapa" de outras camadas (como um controlador) sem a necessidade de aninhar múltiplos blocos `try...catch` nas suas ações.
        
    - **Mínima sobrecarga:** Ele é ativado apenas quando uma exceção ocorre. Em condições normais (sem erros), o middleware de tratamento de exceções não adiciona praticamente nenhuma sobrecarga ao fluxo de requisição.
        
    - **Controle centralizado:** Assim como no Spring, a centralização do tratamento de erros economiza recursos de processamento e simplifica a lógica da aplicação.
        
- **O que evitar:**
    
    - **Filtros de exceção em excesso:** Embora os filtros de exceção (`Exception Filters`) sejam uma ferramenta poderosa, usá-los em excesso pode introduzir um custo adicional de processamento. A forma mais performática é usar o middleware globalmente e reservar os filtros para casos muito específicos, como para lidar com exceções de um único controlador ou ação.
        
    - **Registrar _stack trace_ em detalhes excessivos:** Gerar e registrar o _stack trace_ completo em cada exceção pode ter um impacto no desempenho, especialmente em ambientes de alta carga. É importante registrar apenas o que é necessário para a depuração.