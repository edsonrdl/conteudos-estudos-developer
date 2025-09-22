

## 1. O que é um HttpInterceptor?

Um **HttpInterceptor** é um "middleware" do Angular que intercepta **TODAS** as requisições HTTP antes que sejam enviadas para o servidor. Ele funciona como um **filtro global** que pode:

- ✅ Adicionar headers (como tokens de autenticação)
- ✅ Modificar a URL da requisição
- ✅ Adicionar parâmetros
- ✅ Tratar erros globalmente
- ✅ Adicionar loading states
- ✅ Fazer logs das requisições

## 2. Fluxo de Funcionamento

```
1. Componente faz requisição HTTP
   ↓
2. HttpInterceptor intercepta ANTES de enviar
   ↓
3. Interceptor analisa a URL de destino
   ↓
4. Identifica qual API está sendo chamada
   ↓
5. Adiciona o token/autenticação específica
   ↓
6. Envia a requisição modificada para o servidor
   ↓
7. Servidor responde
   ↓
8. Resposta volta para o componente
```

## 3. Exemplo Prático Detalhado

### Cenário: Aplicação com 4 APIs Diferentes
```typescript
// Configuração das APIs
const APIs = {
  products: 'https://products-api.company.com',     // JWT Token
  users: 'https://users-api.company.com',          // API Key
  payments: 'https://payments-gateway.com',        // Payment Token
  analytics: 'https://analytics-api.company.com'   // OAuth Token
};
```

### Como o Interceptor Funciona na Prática

```typescript
@Injectable()
export class MultiApiAuthInterceptor implements HttpInterceptor {
  
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    console.log('🔍 Interceptando requisição para:', req.url);
    
    // 1. IDENTIFICAR QUAL API
    const apiType = this.getApiType(req.url);
    console.log('🏷️ Tipo de API detectado:', apiType);
    
    // 2. APLICAR AUTENTICAÇÃO ESPECÍFICA
    const authReq = this.addAuthToRequest(req, apiType);
    console.log('🔐 Headers adicionados:', authReq.headers.keys());
    
    // 3. CONTINUAR COM A REQUISIÇÃO
    return next.handle(authReq);
  }

  private getApiType(url: string): string {
    // Análise da URL para determinar o tipo
    if (url.includes('products-api.company.com')) return 'products';
    if (url.includes('users-api.company.com')) return 'users';
    if (url.includes('payments-gateway.com')) return 'payments';
    if (url.includes('analytics-api.company.com')) return 'analytics';
    
    return 'default';
  }

  private addAuthToRequest(req: HttpRequest<any>, apiType: string): HttpRequest<any> {
    // Verificar se deve pular autenticação
    if (req.headers.has('X-Skip-Auth')) {
      console.log('⏭️ Pulando autenticação para esta requisição');
      return req.clone({
        headers: req.headers.delete('X-Skip-Auth')
      });
    }

    let authReq = req;

    switch (apiType) {
      case 'products':
      case 'orders':
        // JWT para APIs internas
        const jwtToken = localStorage.getItem('jwt_token');
        if (jwtToken) {
          console.log('🎫 Adicionando JWT Token');
          authReq = req.clone({
            headers: req.headers.set('Authorization', `Bearer ${jwtToken}`)
          });
        }
        break;
        
      case 'users':
        // API Key para API de usuários
        const apiKey = localStorage.getItem('users_api_key');
        if (apiKey) {
          console.log('🔑 Adicionando API Key');
          authReq = req.clone({
            headers: req.headers.set('X-API-Key', apiKey)
          });
        }
        break;
        
      case 'payments':
        // Token específico para pagamentos
        const paymentToken = localStorage.getItem('payment_token');
        if (paymentToken) {
          console.log('💳 Adicionando Payment Token');
          authReq = req.clone({
            headers: req.headers.set('X-Payment-Auth', paymentToken)
          });
        }
        break;
        
      case 'analytics':
        // OAuth2 para analytics
        const oauthToken = localStorage.getItem('oauth_token');
        if (oauthToken) {
          console.log('📊 Adicionando OAuth Token');
          authReq = req.clone({
            headers: req.headers.set('Authorization', `OAuth ${oauthToken}`)
          });
        }
        break;
    }

    return authReq;
  }
}
```

## 4. Exemplo Real de Uso

### Quando você faz uma requisição no seu componente:

```typescript
// No seu ProductService
export class ProductService {
  constructor(private http: HttpClient) {}

  getProducts(): Observable<Product[]> {
    // Você só faz a requisição normal
    return this.http.get<Product[]>('https://products-api.company.com/api/v1/products');
    
    // 🔥 O INTERCEPTOR VAI:
    // 1. Ver que a URL contém 'products-api.company.com'
    // 2. Identificar como tipo 'products'
    // 3. Pegar o JWT do localStorage
    // 4. Adicionar o header: Authorization: Bearer eyJ0eXAi...
    // 5. Enviar a requisição com o token
  }

  // Exemplo com skip auth
  getPublicData(): Observable<any> {
    const headers = new HttpHeaders({
      'X-Skip-Auth': 'true' // Este header diz para PULAR a autenticação
    });
    
    return this.http.get<any>('https://products-api.company.com/api/v1/public', { headers });
    // O interceptor vai remover este header e NÃO adicionar token
  }
}
```

## 5. Múltiplos Interceptors e Ordem de Execução

```typescript
// No core.module.ts - ORDEM IMPORTA!
providers: [
  {
    provide: HTTP_INTERCEPTORS,
    useClass: CacheInterceptor,      // 1º - Verifica cache
    multi: true
  },
  {
    provide: HTTP_INTERCEPTORS,
    useClass: MultiApiAuthInterceptor, // 2º - Adiciona autenticação
    multi: true
  },
  {
    provide: HTTP_INTERCEPTORS,
    useClass: ErrorInterceptor,      // 3º - Trata erros
    multi: true
  },
  {
    provide: HTTP_INTERCEPTORS,
    useClass: LoadingInterceptor,    // 4º - Gerencia loading
    multi: true
  }
]
```

### Fluxo com Múltiplos Interceptors:
```
Requisição Original
       ↓
1. CacheInterceptor (verifica cache)
       ↓
2. MultiApiAuthInterceptor (adiciona token)
       ↓
3. ErrorInterceptor (prepara tratamento de erro)
       ↓
4. LoadingInterceptor (ativa loading)
       ↓
   SERVIDOR
       ↓
5. LoadingInterceptor (desativa loading)
       ↓
6. ErrorInterceptor (trata erros se houver)
       ↓
7. MultiApiAuthInterceptor (pode processar resposta)
       ↓
8. CacheInterceptor (salva no cache)
       ↓
  Componente Final
```

## 6. Interceptor Avançado com Logs e Debug

```typescript
@Injectable()
export class AdvancedMultiApiAuthInterceptor implements HttpInterceptor {
  
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const startTime = Date.now();
    
    // Log da requisição original
    console.group(`🚀 HTTP Request: ${req.method} ${req.url}`);
    console.log('📋 Headers originais:', this.headersToObject(req.headers));
    console.log('📦 Body:', req.body);
    
    // Determinar tipo de API
    const apiType = this.getApiType(req.url);
    console.log(`🏷️ API Type: ${apiType}`);
    
    // Aplicar autenticação
    const authReq = this.addAuthToRequest(req, apiType);
    console.log('🔐 Headers com auth:', this.headersToObject(authReq.headers));
    
    // Continuar com a requisição e logar resposta
    return next.handle(authReq).pipe(
      tap({
        next: (event) => {
          if (event instanceof HttpResponse) {
            const duration = Date.now() - startTime;
            console.log(`✅ Response (${duration}ms):`, event.status, event.body);
            console.groupEnd();
          }
        },
        error: (error) => {
          const duration = Date.now() - startTime;
          console.log(`❌ Error (${duration}ms):`, error);
          console.groupEnd();
        }
      })
    );
  }

  private headersToObject(headers: HttpHeaders): any {
    const obj: any = {};
    headers.keys().forEach(key => {
      obj[key] = headers.get(key);
    });
    return obj;
  }

  private getApiType(url: string): string {
    // Configuração mais robusta
    const apiConfigs = [
      { pattern: /products-api\.company\.com/, type: 'products' },
      { pattern: /users-api\.company\.com/, type: 'users' },
      { pattern: /payments-gateway\.com/, type: 'payments' },
      { pattern: /analytics-api\.company\.com/, type: 'analytics' }
    ];

    for (const config of apiConfigs) {
      if (config.pattern.test(url)) {
        return config.type;
      }
    }

    return 'default';
  }

  private addAuthToRequest(req: HttpRequest<any>, apiType: string): HttpRequest<any> {
    // Verificar blacklist de URLs que não precisam de auth
    const noAuthUrls = ['/login', '/register', '/public', '/health'];
    if (noAuthUrls.some(path => req.url.includes(path))) {
      console.log('⏭️ URL na whitelist, pulando autenticação');
      return req;
    }

    // Skip manual
    if (req.headers.has('X-Skip-Auth')) {
      console.log('⏭️ Header X-Skip-Auth encontrado');
      return req.clone({
        headers: req.headers.delete('X-Skip-Auth')
      });
    }

    let authReq = req;

    switch (apiType) {
      case 'products':
      case 'orders':
        authReq = this.addJwtAuth(req);
        break;
      case 'users':
        authReq = this.addApiKeyAuth(req);
        break;
      case 'payments':
        authReq = this.addPaymentAuth(req);
        break;
      case 'analytics':
        authReq = this.addOAuthAuth(req);
        break;
      default:
        console.log('⚠️ Tipo de API não reconhecido, usando JWT padrão');
        authReq = this.addJwtAuth(req);
    }

    return authReq;
  }

  private addJwtAuth(req: HttpRequest<any>): HttpRequest<any> {
    const token = localStorage.getItem('jwt_token');
    if (token) {
      console.log('🎫 Adicionando JWT Token');
      return req.clone({
        headers: req.headers.set('Authorization', `Bearer ${token}`)
      });
    }
    console.warn('⚠️ JWT Token não encontrado no localStorage');
    return req;
  }

  private addApiKeyAuth(req: HttpRequest<any>): HttpRequest<any> {
    const apiKey = localStorage.getItem('users_api_key');
    if (apiKey) {
      console.log('🔑 Adicionando API Key');
      return req.clone({
        headers: req.headers.set('X-API-Key', apiKey)
      });
    }
    console.warn('⚠️ API Key não encontrada no localStorage');
    return req;
  }

  private addPaymentAuth(req: HttpRequest<any>): HttpRequest<any> {
    const paymentToken = localStorage.getItem('payment_token');
    if (paymentToken) {
      console.log('💳 Adicionando Payment Token');
      return req.clone({
        headers: req.headers.set('X-Payment-Auth', paymentToken)
      });
    }
    console.warn('⚠️ Payment Token não encontrado no localStorage');
    return req;
  }

  private addOAuthAuth(req: HttpRequest<any>): HttpRequest<any> {
    const oauthToken = localStorage.getItem('oauth_token');
    if (oauthToken) {
      console.log('📊 Adicionando OAuth Token');
      return req.clone({
        headers: req.headers.set('Authorization', `OAuth ${oauthToken}`)
      });
    }
    console.warn('⚠️ OAuth Token não encontrado no localStorage');
    return req;
  }
}
```

## 7. Como os Tokens são Gerenciados

```typescript
// AuthService - Onde os tokens são armazenados
export class AuthService {
  
  login(credentials: any): Observable<any> {
    return this.http.post('/auth/login', credentials).pipe(
      tap(response => {
        // Salvar diferentes tokens para diferentes APIs
        localStorage.setItem('jwt_token', response.jwtToken);
        localStorage.setItem('users_api_key', response.usersApiKey);
        localStorage.setItem('payment_token', response.paymentToken);
        localStorage.setItem('oauth_token', response.oauthToken);
      })
    );
  }

  logout(): void {
    // Limpar todos os tokens
    localStorage.removeItem('jwt_token');
    localStorage.removeItem('users_api_key');
    localStorage.removeItem('payment_token');
    localStorage.removeItem('oauth_token');
  }
}
```

## 8. Resumo do Funcionamento

O interceptor **NÃO recebe nenhum objeto** dizendo se pode carregar a página. Ele:

1. **Intercepta TODAS** as requisições HTTP automaticamente
2. **Analisa a URL** para identificar qual API está sendo chamada
3. **Busca o token apropriado** no localStorage
4. **Adiciona o header de autenticação** correto
5. **Envia a requisição** para o servidor

É um processo **totalmente transparente** para o desenvolvedor. Você apenas faz `http.get()` e o interceptor cuida da autenticação automaticamente! 🚀