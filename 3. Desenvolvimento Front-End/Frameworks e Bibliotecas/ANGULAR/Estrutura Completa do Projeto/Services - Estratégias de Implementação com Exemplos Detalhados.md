
## 1. Abordagem Herança - BaseApiService

### **Conceito:**

Service abstrato que força um padrão comum através de herança obrigatória.

typescript

```typescript
// core/services/base-api.service.ts
import { Injectable } from '@angular/core';
import { HttpClient, HttpParams, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError, timer } from 'rxjs';
import { catchError, retryWhen, mergeMap } from 'rxjs/operators';
import { ApiConfig } from '../config/api-endpoints.config';

export interface ApiOptions {
  params?: HttpParams | { [key: string]: any };
  headers?: { [key: string]: string };
  timeout?: number;
  retries?: number;
  skipAuth?: boolean;
}

@Injectable({
  providedIn: 'root'
})
export abstract class BaseApiService {
  // FORÇA implementação - todos os filhos devem ter
  protected abstract apiConfig: ApiConfig;
  protected abstract getApiIdentifier(): string;

  constructor(protected http: HttpClient) {}

  /**
   * FUNCIONALIDADE HERDADA: URL automática
   * Monta URL seguindo padrão rígido: ${baseUrl}/api/${version}/${endpoint}
   * Problema: Nem todas APIs seguem este formato
   */
  protected getFullUrl(endpoint: string): string {
    const cleanEndpoint = endpoint.startsWith('/') ? endpoint.slice(1) : endpoint;
    // URL FIXA - todos devem seguir este padrão
    return `${this.apiConfig.baseUrl}/api/${this.apiConfig.version}/${cleanEndpoint}`;
  }

  /**
   * FUNCIONALIDADE HERDADA: GET Request padronizado
   * Inclui retry automático + error handling + headers padrão
   */
  protected get<T>(endpoint: string, options?: ApiOptions): Observable<T> {
    return this.http.get<T>(this.getFullUrl(endpoint), {
      params: options?.params,
      headers: this.buildHeaders(options?.headers, options?.skipAuth),
      timeout: options?.timeout || this.apiConfig.timeout
    }).pipe(
      // RETRY AUTOMÁTICO para erros específicos
      retryWhen(errors => this.getRetryStrategy(errors, options?.retries)),
      // ERROR HANDLING padronizado
      catchError(this.handleError.bind(this))
    );
  }

  // POST, PUT, DELETE, PATCH seguem o mesmo padrão...
  protected post<T>(endpoint: string, data: any, options?: ApiOptions): Observable<T> {
    return this.http.post<T>(this.getFullUrl(endpoint), data, {
      params: options?.params,
      headers: this.buildHeaders(options?.headers, options?.skipAuth),
      timeout: options?.timeout || this.apiConfig.timeout
    }).pipe(
      retryWhen(errors => this.getRetryStrategy(errors, options?.retries)),
      catchError(this.handleError.bind(this))
    );
  }

  /**
   * FUNCIONALIDADE HERDADA: Headers padrão
   * FORÇA headers específicos - pode não funcionar com APIs externas
   */
  private buildHeaders(customHeaders?: { [key: string]: string }, skipAuth?: boolean): { [key: string]: string } {
    const headers: { [key: string]: string } = {
      'Content-Type': 'application/json',
      'Accept': 'application/json'
    };

    if (customHeaders) {
      Object.assign(headers, customHeaders);
    }

    // HEADER FORÇADO - pode causar problemas com APIs externas
    headers['X-API-Source'] = this.getApiIdentifier();

    return headers;
  }

  /**
   * FUNCIONALIDADE HERDADA: Retry automático
   * Apenas para erros 500, 502, 503, 504 com exponential backoff
   */
  private getRetryStrategy(errors: Observable<any>, customRetries?: number) {
    return errors.pipe(
      mergeMap((error, index) => {
        const retryAttempts = customRetries ?? this.apiConfig.retryAttempts;
        
        if (index >= retryAttempts) {
          return throwError(() => error);
        }

        // RETRY LIMITADO - só para erros de servidor
        if (error.status === 500 || error.status === 502 || error.status === 503 || error.status === 504) {
          const delay = Math.pow(2, index) * 1000; // Exponential backoff
          return timer(delay);
        }

        return throwError(() => error);
      })
    );
  }

  /**
   * FUNCIONALIDADE HERDADA: Error handling padronizado
   * Traduz códigos HTTP para mensagens em português
   */
  private handleError(error: HttpErrorResponse): Observable<never> {
    let errorMessage = 'Ocorreu um erro desconhecido!';
    
    if (error.error instanceof ErrorEvent) {
      errorMessage = `Erro: ${error.error.message}`;
    } else {
      // MAPEAMENTO FIXO de códigos HTTP
      switch (error.status) {
        case 400: errorMessage = 'Requisição inválida'; break;
        case 401: errorMessage = 'Não autorizado'; break;
        case 403: errorMessage = 'Acesso negado'; break;
        case 404: errorMessage = 'Recurso não encontrado'; break;
        case 408: errorMessage = 'Timeout da requisição'; break;
        case 429: errorMessage = 'Muitas requisições. Tente novamente em alguns minutos'; break;
        case 500: errorMessage = 'Erro interno do servidor'; break;
        case 502: errorMessage = 'Bad Gateway'; break;
        case 503: errorMessage = 'Serviço indisponível'; break;
        case 504: errorMessage = 'Gateway Timeout'; break;
        default: errorMessage = `Erro ${error.status}: ${error.error?.message || error.message}`;
      }
    }

    console.error(`[${this.getApiIdentifier()}] API Error:`, errorMessage, error);
    return throwError(() => new Error(errorMessage));
  }
}
```

### **Implementação do Service Filho:**

typescript

```typescript
// core/services/products-api.service.ts
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { BaseApiService, ApiOptions } from './base-api.service';
import { environment } from '../../../environments/environment';

export interface Product {
  id?: number;
  name: string;
  description: string;
  price: number;
  category: string;
  stock: number;
  isActive: boolean;
}

export interface ProductsResponse {
  data: Product[];
  pagination: { page: number; limit: number; total: number; totalPages: number; };
}

@Injectable({
  providedIn: 'root'
})
export class ProductsApiService extends BaseApiService {
  // IMPLEMENTAÇÃO OBRIGATÓRIA - abstrações forçadas pela base
  protected apiConfig = environment.apis.products;
  protected getApiIdentifier(): string {
    return 'PRODUCTS-API';
  }

  /**
   * FUNCIONALIDADE ESPECÍFICA: Métodos de negócio
   * Usa funcionalidades herdadas automaticamente
   */
  getProducts(filters?: any): Observable<ProductsResponse> {
    const options: ApiOptions = {
      params: filters,
      timeout: 30000 // TIMEOUT CUSTOMIZADO para esta operação
    };
    
    // HERDA automaticamente: retry, error handling, headers, URL building
    return this.get<ProductsResponse>('products', options);
    // URL final: ${apiConfig.baseUrl}/api/${apiConfig.version}/products
  }

  /**
   * FUNCIONALIDADE ESPECÍFICA: Transformação de dados
   * Extrai 'data' da resposta da API usando map()
   */
  getProductById(id: number): Observable<Product> {
    return this.get<{ data: Product }>(`products/${id}`)
      .pipe(
        map(response => response.data) // TRANSFORMAÇÃO específica
      );
  }

  createProduct(product: Omit<Product, 'id'>): Observable<Product> {
    return this.post<{ data: Product }>('products', product)
      .pipe(map(response => response.data));
  }

  updateProduct(id: number, product: Partial<Product>): Observable<Product> {
    return this.put<{ data: Product }>(`products/${id}`, product)
      .pipe(map(response => response.data));
  }

  deleteProduct(id: number): Observable<void> {
    return this.delete<void>(`products/${id}`);
  }

  getCategories(): Observable<string[]> {
    return this.get<{ data: string[] }>('products/categories')
      .pipe(map(response => response.data));
  }

  /**
   * FUNCIONALIDADE ESPECÍFICA: Operação com timeout customizado
   * Override de configurações para operações específicas
   */
  bulkUpdatePrices(updates: { id: number; price: number }[]): Observable<void> {
    const options: ApiOptions = {
      timeout: 60000, // TIMEOUT MAIOR para operação pesada
      retries: 1       // RETRY REDUZIDO para operação crítica
    };
    
    return this.patch<void>('products/bulk-update-prices', { updates }, options);
  }
}
```

---

## 2. Abordagem Composição - HttpUtilsService

### **Conceito:**

Service de utilitários que oferece funcionalidades opcionais através de composição.

typescript

```typescript
// core/services/http-utils.service.ts
import { Injectable } from '@angular/core';
import { HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError, timer } from 'rxjs';
import { retryWhen, mergeMap, catchError } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class HttpUtilsService {
  
  /**
   * UTILITÁRIO OPCIONAL: Retry customizável
   * Pode ser usado quando necessário, com configurações específicas
   */
  withRetry<T>(attempts: number = 3, statusCodes: number[] = [500, 502, 503, 504]): (source: Observable<T>) => Observable<T> {
    return (source: Observable<T>) => source.pipe(
      retryWhen(errors => errors.pipe(
        mergeMap((error, index) => {
          // FLEXÍVEL - pode definir quais status codes fazem retry
          if (index >= attempts || !statusCodes.includes(error.status)) {
            return throwError(() => error);
          }

          const delay = Math.pow(2, index) * 1000;
          return timer(delay);
        })
      ))
    );
  }

  /**
   * UTILITÁRIO OPCIONAL: Error handling flexível
   * Pode ser customizado por service
   */
  handleErrors(customMessages?: { [key: number]: string }): (source: Observable<any>) => Observable<any> {
    return (source: Observable<any>) => source.pipe(
      catchError((error: HttpErrorResponse) => {
        let errorMessage = 'Erro desconhecido';

        if (customMessages && customMessages[error.status]) {
          errorMessage = customMessages[error.status];
        } else {
          // MAPEAMENTO PADRÃO - mas pode ser sobrescrito
          const defaultMessages: { [key: number]: string } = {
            400: 'Dados inválidos',
            401: 'Não autorizado',
            403: 'Sem permissão',
            404: 'Não encontrado',
            500: 'Erro do servidor'
          };
          errorMessage = defaultMessages[error.status] || `Erro ${error.status}`;
        }

        console.error('HTTP Error:', errorMessage, error);
        return throwError(() => new Error(errorMessage));
      })
    );
  }

  /**
   * UTILITÁRIO OPCIONAL: Headers builder
   * Flexível para diferentes necessidades
   */
  buildHeaders(baseHeaders: any = {}, customHeaders: any = {}): any {
    const defaultHeaders = {
      'Content-Type': 'application/json',
      'Accept': 'application/json'
    };

    return { ...defaultHeaders, ...baseHeaders, ...customHeaders };
  }

  /**
   * UTILITÁRIO OPCIONAL: URL builder
   * Pode construir URLs de diferentes padrões
   */
  buildUrl(baseUrl: string, path: string, version?: string): string {
    const cleanPath = path.startsWith('/') ? path.slice(1) : path;
    
    if (version) {
      return `${baseUrl}/api/${version}/${cleanPath}`;
    }
    
    return `${baseUrl}/${cleanPath}`;
  }
}
```

### **Implementação com Composição:**

typescript

```typescript
// core/services/products-api.service.ts (versão composição)
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { HttpUtilsService } from './http-utils.service';
import { environment } from '../../../environments/environment';

@Injectable({
  providedIn: 'root'
})
export class ProductsApiService {
  private config = environment.apis.products;

  constructor(
    private http: HttpClient,
    private httpUtils: HttpUtilsService // COMPOSIÇÃO - injeta utilitários
  ) {}

  /**
   * FLEXIBILIDADE TOTAL: Pode escolher quais utilitários usar
   * URL customizada, retry específico, error handling próprio
   */
  getProducts(filters?: any): Observable<ProductsResponse> {
    // URL FLEXÍVEL - pode seguir qualquer padrão
    const url = this.httpUtils.buildUrl(this.config.baseUrl, 'products', this.config.version);
    
    // HEADERS CUSTOMIZADOS para esta API
    const headers = this.httpUtils.buildHeaders(
      { 'X-Products-API': 'v1' }, // Headers específicos desta API
      { 'Custom-Header': 'value' } // Headers desta operação
    );

    return this.http.get<ProductsResponse>(url, { params: filters, headers })
      .pipe(
        // USA utilitários apenas onde faz sentido
        this.httpUtils.withRetry(2, [500, 502, 503]), // Retry customizado
        this.httpUtils.handleErrors({
          404: 'Produtos não encontrados', // Mensagem específica
          400: 'Filtros inválidos'
        })
      );
  }

  /**
   * API DIFERENTE: Pode usar padrão completamente diferente
   * Sem herança forçada
   */
  getProductById(id: number): Observable<Product> {
    // URL PERSONALIZADA - não segue padrão /api/v1/
    const url = `${this.config.baseUrl}/produtos/${id}`; // Português na URL
    
    return this.http.get<{ produto: Product }>(url)
      .pipe(
        // SÓ error handling, sem retry para esta operação
        this.httpUtils.handleErrors({
          404: 'Produto não existe'
        }),
        // TRANSFORMAÇÃO específica - campo 'produto' ao invés de 'data'
        map(response => response.produto)
      );
  }

  /**
   * OPERAÇÃO CRÍTICA: Sem utilitários automáticos
   * Controle total sobre a requisição
   */
  deleteProduct(id: number): Observable<void> {
    const url = `${this.config.baseUrl}/products/${id}`;
    
    // SEM utilitários - operação crítica com controle total
    return this.http.delete<void>(url, {
      headers: { 'X-Confirm-Delete': 'true' }
    });
    // Sem retry automático, sem error handling padrão
  }

  /**
   * INTEGRAÇÃO COM API EXTERNA: Completamente diferente
   * Demonstra flexibilidade total
   */
  syncWithExternalAPI(): Observable<any> {
    // API externa com padrão completamente diferente
    const externalUrl = 'https://external-api.com/sync';
    
    return this.http.post(externalUrl, {}, {
      headers: {
        'Authorization': 'Bearer token',
        'X-External-API': 'true'
      },
      timeout: 120000 // 2 minutos
    }).pipe(
      // Retry específico para API externa
      this.httpUtils.withRetry(5, [408, 429, 500, 502, 503, 504]),
      this.httpUtils.handleErrors({
        429: 'API externa com muitas requisições - aguarde',
        503: 'API externa indisponível'
      })
    );
  }
}
```

---

## Comparação Detalhada das Abordagens:

|Aspecto|Herança (BaseApiService)|Composição (HttpUtilsService)|
|---|---|---|
|**Flexibilidade**|Baixa - padrão rígido `/api/v1/`|Alta - qualquer padrão de URL|
|**Acoplamento**|Alto - mudança na base quebra todos|Baixo - utilitários independentes|
|**Reutilização**|Forçada - herda tudo automaticamente|Seletiva - usa só o necessário|
|**Manutenção**|Mudança na base afeta todos os filhos|Mudança em utility é isolada|
|**Facilidade de uso**|Automática - funcionalidades "grátis"|Manual - precisa invocar explicitamente|
|**Adaptabilidade**|Difícil para APIs com padrões diferentes|Fácil adaptação para qualquer API|
|**Headers**|Forçados - `X-API-Source` obrigatório|Flexíveis - cada API define os seus|
|**Error Handling**|Fixo - mensagens pré-definidas|Customizável - mensagens específicas|
|**Retry Strategy**|Limitado - só erros 5xx|Configurável - qualquer status code|
|**URL Pattern**|Rígido - `/api/version/endpoint`|Livre - qualquer padrão|

### **Funcionalidades Detalhadas:**

#### **Herança - Funcionalidades Automáticas:**

- **URL automática**: `${baseUrl}/api/${version}/${endpoint}` (inflexível)
- **Retry automático**: Apenas para erros 500, 502, 503, 504 (limitado)
- **Error handling**: Tratamento fixo com mensagens em português (rígido)
- **Headers padrão**: Content-Type, Accept, X-API-Source (forçados)

#### **Herança - Funcionalidades Específicas:**

- **Métodos de negócio**: getProducts, createProduct definidos no filho
- **Timeouts customizados**: Override via ApiOptions para operações específicas
- **Transformação de dados**: map() para extrair response.data no filho

#### **Composição - Funcionalidades Opcionais:**

- **URL flexível**: Qualquer padrão através de buildUrl()
- **Retry configurável**: Qualquer status code, tentativas customizadas
- **Error handling personalizado**: Mensagens específicas por operação
- **Headers dinâmicos**: Diferentes para cada API/operação

A abordagem de herança oferece consistência automática mas sacrifica flexibilidade, enquanto a composição oferece máxima flexibilidade mas requer mais código explícito.