## 🚀 **Principais vantagens desta arquitetura:**

### **1. Multi-API Management**

- **Configuração centralizada** de endpoints por ambiente
- **Autenticação específica** por API (JWT, API Key, OAuth)
- **Timeout e retry** personalizados por serviço
- **Headers específicos** para cada API

### **2. Lazy Loading Inteligente**

- **Módulos carregados sob demanda**
- **Preloading strategies** customizadas
- **Feature flags** para controlar funcionalidades
- **Cache de módulos** para performance

### **3. Gerenciamento de Estado**

- **Facade Pattern** para cada módulo
- **Cache inteligente** com TTL
- **Estado reativo** com RxJS
- **Invalidação de cache** automática

### **4. Estrutura Modular**

```
📁 features/
├── products/     → API: products-api.com
├── users/        → API: users-api.com  
├── orders/       → API: orders-api.com
├── analytics/    → API: analytics-api.com
└── payments/     → API: payments-gateway.com
```

### **5. Roteamento Avançado**

- **Lazy loading** por módulo completo (não só componentes)
- **Resolvers** para pre-loading de dados
- **Guards** com feature flags e permissions
- **Layouts múltiplos** (user/admin)

### **6. APIs com Configurações Específicas**

typescript

```typescript
// Produto: 30s timeout, 2 retries
products: { timeout: 30000, retryAttempts: 2 }

// Pagamentos: 20s timeout, 3 retries  
payments: { timeout: 20000, retryAttempts: 3 }

// Analytics: 60s timeout, 2 retries
analytics: { timeout: 60000, retryAttempts: 2 }
```

### **7. Performance Otimizada**

- **Cache por módulo** com invalidação inteligente
- **Preloading seletivo** baseado em prioridade
- **Retry strategies** com exponential backoff
- **Interceptors especializados** por tipo de API

### **8. Exemplo de URLs Finais**

```
/products                    → ProductsModule (lazy)
/products/123               → ProductDetailPage  
/admin/users                → UsersModule (lazy)
/admin/analytics            → AnalyticsModule (lazy + feature flag)
/orders                     → OrdersModule (lazy)
```

Esta arquitetura é **altamente escalável** e permite que diferentes equipes trabalhem em módulos independentes, cada um com suas próprias APIs, sem interferências! 🎯

**Próximos passos sugeridos:**

1. Implementar **testes unitários** para cada serviço
2. Adicionar **interceptor de logs** para debugging
3. Criar **service workers** para cache offline
4. Implementar **micro-frontends** se necessário

# Arquitetura Multi-API com Lazy Loading Modular

## 1. Estrutura de Pastas para Múltiplas APIs
```
my-enterprise-app/
├── src/
│   ├── app/
│   │   ├── core/
│   │   │   ├── services/
│   │   │   │   ├── base-api.service.ts
│   │   │   │   ├── products-api.service.ts
│   │   │   │   ├── users-api.service.ts
│   │   │   │   ├── orders-api.service.ts
│   │   │   │   └── analytics-api.service.ts
│   │   │   ├── config/
│   │   │   │   ├── api-endpoints.config.ts
│   │   │   │   └── environment-config.service.ts
│   │   │   ├── interceptors/
│   │   │   │   ├── multi-api-auth.interceptor.ts
│   │   │   │   └── api-selector.interceptor.ts
│   │   │   └── core.module.ts
│   │   │
│   │   ├── features/
│   │   │   ├── products/
│   │   │   │   ├── products.module.ts
│   │   │   │   ├── products-routing.module.ts
│   │   │   │   ├── pages/
│   │   │   │   ├── components/
│   │   │   │   └── services/
│   │   │   │
│   │   │   ├── users/
│   │   │   │   ├── users.module.ts
│   │   │   │   ├── users-routing.module.ts
│   │   │   │   ├── pages/
│   │   │   │   ├── components/
│   │   │   │   └── services/
│   │   │   │
│   │   │   ├── orders/
│   │   │   │   ├── orders.module.ts
│   │   │   │   ├── orders-routing.module.ts
│   │   │   │   └── pages/
│   │   │   │
│   │   │   └── analytics/
│   │   │       ├── analytics.module.ts
│   │   │       ├── analytics-routing.module.ts
│   │   │       └── pages/
│   │   │
│   │   ├── layout/
│   │   │   ├── main-layout/
│   │   │   └── admin-layout/
│   │   │
│   │   └── app-routing.module.ts
│   └── environments/
│       ├── environment.ts
│       └── environment.prod.ts
```

## 2. Configuração de Múltiplas APIs
### environments/environment.ts
```typescript
export const environment = {
  production: false,
  apis: {
    products: {
      baseUrl: 'https://products-api.dev.company.com',
      version: 'v1',
      timeout: 30000,
      retryAttempts: 2
    },
    users: {
      baseUrl: 'https://users-api.dev.company.com',
      version: 'v2',
      timeout: 15000,
      retryAttempts: 3
    },
    orders: {
      baseUrl: 'https://orders-api.dev.company.com',
      version: 'v1',
      timeout: 45000,
      retryAttempts: 1
    },
    analytics: {
      baseUrl: 'https://analytics-api.dev.company.com',
      version: 'v3',
      timeout: 60000,
      retryAttempts: 2
    },
    payments: {
      baseUrl: 'https://payments-gateway.dev.company.com',
      version: 'v1',
      timeout: 20000,
      retryAttempts: 3
    }
  },
  features: {
    analytics: true,
    payments: true,
    advancedReporting: false
  }
};
```

### core/config/api-endpoints.config.ts
```typescript
import { InjectionToken } from '@angular/core';

export interface ApiConfig {
  baseUrl: string;
  version: string;
  timeout: number;
  retryAttempts: number;
}

export interface ApiEndpointsConfig {
  products: ApiConfig;
  users: ApiConfig;
  orders: ApiConfig;
  analytics: ApiConfig;
  payments: ApiConfig;
}

export const API_ENDPOINTS_CONFIG = new InjectionToken<ApiEndpointsConfig>('api.endpoints.config');

// Factory function para configuração
export function createApiEndpointsConfig(): ApiEndpointsConfig {
  return {
    products: {
      baseUrl: 'https://products-api.company.com',
      version: 'v1',
      timeout: 30000,
      retryAttempts: 2
    },
    users: {
      baseUrl: 'https://users-api.company.com', 
      version: 'v2',
      timeout: 15000,
      retryAttempts: 3
    },
    orders: {
      baseUrl: 'https://orders-api.company.com',
      version: 'v1', 
      timeout: 45000,
      retryAttempts: 1
    },
    analytics: {
      baseUrl: 'https://analytics-api.company.com',
      version: 'v3',
      timeout: 60000,
      retryAttempts: 2
    },
    payments: {
      baseUrl: 'https://payments-gateway.company.com',
      version: 'v1',
      timeout: 20000,
      retryAttempts: 3
    }
  };
}
```

## 3. Serviço Base para Múltiplas APIs
### core/services/base-api.service.ts
```typescript
import { Injectable } from '@angular/core';
import { HttpClient, HttpParams, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError, timer } from 'rxjs';
import { catchError, retry, retryWhen, mergeMap, finalize } from 'rxjs/operators';
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
  protected abstract apiConfig: ApiConfig;

  constructor(protected http: HttpClient) {}

  protected getFullUrl(endpoint: string): string {
    const cleanEndpoint = endpoint.startsWith('/') ? endpoint.slice(1) : endpoint;
    return `${this.apiConfig.baseUrl}/api/${this.apiConfig.version}/${cleanEndpoint}`;
  }

  // GET Request
  protected get<T>(endpoint: string, options?: ApiOptions): Observable<T> {
    return this.http.get<T>(this.getFullUrl(endpoint), {
      params: options?.params,
      headers: this.buildHeaders(options?.headers, options?.skipAuth),
      timeout: options?.timeout || this.apiConfig.timeout
    }).pipe(
      retryWhen(errors => this.getRetryStrategy(errors, options?.retries)),
      catchError(this.handleError.bind(this))
    );
  }

  // POST Request
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

  // PUT Request
  protected put<T>(endpoint: string, data: any, options?: ApiOptions): Observable<T> {
    return this.http.put<T>(this.getFullUrl(endpoint), data, {
      params: options?.params,
      headers: this.buildHeaders(options?.headers, options?.skipAuth),
      timeout: options?.timeout || this.apiConfig.timeout
    }).pipe(
      retryWhen(errors => this.getRetryStrategy(errors, options?.retries)),
      catchError(this.handleError.bind(this))
    );
  }

  // DELETE Request
  protected delete<T>(endpoint: string, options?: ApiOptions): Observable<T> {
    return this.http.delete<T>(this.getFullUrl(endpoint), {
      params: options?.params,
      headers: this.buildHeaders(options?.headers, options?.skipAuth),
      timeout: options?.timeout || this.apiConfig.timeout
    }).pipe(
      retryWhen(errors => this.getRetryStrategy(errors, options?.retries)),
      catchError(this.handleError.bind(this))
    );
  }

  // PATCH Request
  protected patch<T>(endpoint: string, data: any, options?: ApiOptions): Observable<T> {
    return this.http.patch<T>(this.getFullUrl(endpoint), data, {
      params: options?.params,
      headers: this.buildHeaders(options?.headers, options?.skipAuth),
      timeout: options?.timeout || this.apiConfig.timeout
    }).pipe(
      retryWhen(errors => this.getRetryStrategy(errors, options?.retries)),
      catchError(this.handleError.bind(this))
    );
  }

  private buildHeaders(customHeaders?: { [key: string]: string }, skipAuth?: boolean): { [key: string]: string } {
    const headers: { [key: string]: string } = {
      'Content-Type': 'application/json',
      'Accept': 'application/json'
    };

    // Adicionar headers customizados
    if (customHeaders) {
      Object.assign(headers, customHeaders);
    }

    // Adicionar header de identificação da API
    headers['X-API-Source'] = this.getApiIdentifier();

    return headers;
  }

  protected abstract getApiIdentifier(): string;

  private getRetryStrategy(errors: Observable<any>, customRetries?: number) {
    return errors.pipe(
      mergeMap((error, index) => {
        const retryAttempts = customRetries ?? this.apiConfig.retryAttempts;
        
        if (index >= retryAttempts) {
          return throwError(() => error);
        }

        // Só fazer retry para erros específicos
        if (error.status === 500 || error.status === 502 || error.status === 503 || error.status === 504) {
          const delay = Math.pow(2, index) * 1000; // Exponential backoff
          return timer(delay);
        }

        return throwError(() => error);
      })
    );
  }

  private handleError(error: HttpErrorResponse): Observable<never> {
    let errorMessage = 'Ocorreu um erro desconhecido!';
    
    if (error.error instanceof ErrorEvent) {
      // Erro do lado do cliente
      errorMessage = `Erro: ${error.error.message}`;
    } else {
      // Erro do lado do servidor
      switch (error.status) {
        case 400:
          errorMessage = 'Requisição inválida';
          break;
        case 401:
          errorMessage = 'Não autorizado';
          break;
        case 403:
          errorMessage = 'Acesso negado';
          break;
        case 404:
          errorMessage = 'Recurso não encontrado';
          break;
        case 408:
          errorMessage = 'Timeout da requisição';
          break;
        case 429:
          errorMessage = 'Muitas requisições. Tente novamente em alguns minutos';
          break;
        case 500:
          errorMessage = 'Erro interno do servidor';
          break;
        case 502:
          errorMessage = 'Bad Gateway';
          break;
        case 503:
          errorMessage = 'Serviço indisponível';
          break;
        case 504:
          errorMessage = 'Gateway Timeout';
          break;
        default:
          errorMessage = `Erro ${error.status}: ${error.error?.message || error.message}`;
      }
    }

    console.error(`[${this.getApiIdentifier()}] API Error:`, errorMessage, error);
    return throwError(() => new Error(errorMessage));
  }
}
```

## 4. Serviços Específicos para Cada API
### core/services/products-api.service.ts
```typescript
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
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

@Injectable({
  providedIn: 'root'
})
export class ProductsApiService extends BaseApiService {
  protected apiConfig = environment.apis.products;

  protected getApiIdentifier(): string {
    return 'PRODUCTS-API';
  }

  getProducts(filters?: any): Observable<ProductsResponse> {
    const options: ApiOptions = {
      params: filters,
      timeout: 30000 // Override timeout específico para esta operação
    };
    
    return this.get<ProductsResponse>('products', options);
  }

  getProductById(id: number): Observable<Product> {
    return this.get<{ data: Product }>(`products/${id}`)
      .pipe(map(response => response.data));
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

  // Método específico da API de produtos
  bulkUpdatePrices(updates: { id: number; price: number }[]): Observable<void> {
    const options: ApiOptions = {
      timeout: 60000, // Operação que pode demorar mais
      retries: 1
    };
    
    return this.patch<void>('products/bulk-update-prices', { updates }, options);
  }
}
```

### core/services/users-api.service.ts
```typescript
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { BaseApiService, ApiOptions } from './base-api.service';
import { environment } from '../../../environments/environment';

export interface User {
  id?: number;
  name: string;
  email: string;
  role: string;
  isActive: boolean;
  lastLogin?: Date;
}

export interface UsersResponse {
  data: User[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

@Injectable({
  providedIn: 'root'
})
export class UsersApiService extends BaseApiService {
  protected apiConfig = environment.apis.users;

  protected getApiIdentifier(): string {
    return 'USERS-API';
  }

  getUsers(filters?: any): Observable<UsersResponse> {
    return this.get<UsersResponse>('users', { params: filters });
  }

  getUserById(id: number): Observable<User> {
    return this.get<{ data: User }>(`users/${id}`)
      .pipe(map(response => response.data));
  }

  createUser(user: Omit<User, 'id'>): Observable<User> {
    return this.post<{ data: User }>('users', user)
      .pipe(map(response => response.data));
  }

  updateUser(id: number, user: Partial<User>): Observable<User> {
    return this.put<{ data: User }>(`users/${id}`, user)
      .pipe(map(response => response.data));
  }

  deleteUser(id: number): Observable<void> {
    return this.delete<void>(`users/${id}`);
  }

  // Métodos específicos da API de usuários
  resetUserPassword(userId: number): Observable<void> {
    return this.post<void>(`users/${userId}/reset-password`, {});
  }

  getUserPermissions(userId: number): Observable<string[]> {
    return this.get<{ data: string[] }>(`users/${userId}/permissions`)
      .pipe(map(response => response.data));
  }
}
```

### core/services/orders-api.service.ts
```typescript
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { BaseApiService, ApiOptions } from './base-api.service';
import { environment } from '../../../environments/environment';

export interface Order {
  id?: number;
  userId: number;
  items: OrderItem[];
  status: 'pending' | 'processing' | 'shipped' | 'delivered' | 'cancelled';
  total: number;
  createdAt?: Date;
  updatedAt?: Date;
}

export interface OrderItem {
  productId: number;
  quantity: number;
  price: number;
}

@Injectable({
  providedIn: 'root'
})
export class OrdersApiService extends BaseApiService {
  protected apiConfig = environment.apis.orders;

  protected getApiIdentifier(): string {
    return 'ORDERS-API';
  }

  getOrders(filters?: any): Observable<{ data: Order[]; pagination: any }> {
    const options: ApiOptions = {
      params: filters,
      timeout: 45000 // Orders podem ter queries complexas
    };
    
    return this.get<{ data: Order[]; pagination: any }>('orders', options);
  }

  getOrderById(id: number): Observable<Order> {
    return this.get<{ data: Order }>(`orders/${id}`)
      .pipe(map(response => response.data));
  }

  createOrder(order: Omit<Order, 'id' | 'createdAt' | 'updatedAt'>): Observable<Order> {
    return this.post<{ data: Order }>('orders', order)
      .pipe(map(response => response.data));
  }

  updateOrderStatus(id: number, status: Order['status']): Observable<Order> {
    return this.patch<{ data: Order }>(`orders/${id}/status`, { status })
      .pipe(map(response => response.data));
  }

  cancelOrder(id: number, reason: string): Observable<void> {
    return this.post<void>(`orders/${id}/cancel`, { reason });
  }

  // Método específico para relatórios
  getOrdersReport(startDate: string, endDate: string): Observable<any> {
    const options: ApiOptions = {
      params: { startDate, endDate },
      timeout: 120000 // Relatórios podem demorar mais
    };
    
    return this.get<any>('orders/reports/summary', options);
  }
}
```

## 5. Interceptor Multi-API
### core/interceptors/multi-api-auth.interceptor.ts
```typescript
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable()
export class MultiApiAuthInterceptor implements HttpInterceptor {
  
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Determinar qual API está sendo chamada baseado na URL
    const apiType = this.getApiType(req.url);
    
    // Aplicar autenticação específica baseada no tipo de API
    const authReq = this.addAuthToRequest(req, apiType);
    
    return next.handle(authReq);
  }

  private getApiType(url: string): string {
    if (url.includes('products-api')) return 'products';
    if (url.includes('users-api')) return 'users';
    if (url.includes('orders-api')) return 'orders';
    if (url.includes('analytics-api')) return 'analytics';
    if (url.includes('payments-gateway')) return 'payments';
    
    return 'default';
  }

  private addAuthToRequest(req: HttpRequest<any>, apiType: string): HttpRequest<any> {
    // Skip auth se o header estiver presente
    if (req.headers.has('X-Skip-Auth')) {
      return req.clone({
        headers: req.headers.delete('X-Skip-Auth')
      });
    }

    let authReq = req;

    switch (apiType) {
      case 'products':
      case 'orders':
        // JWT Token para APIs internas
        const jwtToken = localStorage.getItem('jwt_token');
        if (jwtToken) {
          authReq = req.clone({
            headers: req.headers.set('Authorization', `Bearer ${jwtToken}`)
          });
        }
        break;
        
      case 'users':
        // API Key para API de usuários
        const apiKey = localStorage.getItem('users_api_key');
        if (apiKey) {
          authReq = req.clone({
            headers: req.headers.set('X-API-Key', apiKey)
          });
        }
        break;
        
      case 'payments':
        // Token específico para gateway de pagamentos
        const paymentToken = localStorage.getItem('payment_token');
        if (paymentToken) {
          authReq = req.clone({
            headers: req.headers.set('X-Payment-Auth', paymentToken)
          });
        }
        break;
        
      case 'analytics':
        // OAuth2 para analytics
        const oauthToken = localStorage.getItem('oauth_token');
        if (oauthToken) {
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

## 6. Lazy Loading com Módulos Completos
### app-routing.module.ts - Roteamento Principal
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AuthGuard } from './core/guards/auth.guard';
import { MainLayoutComponent } from './layout/main-layout/main-layout.component';
import { AdminLayoutComponent } from './layout/admin-layout/admin-layout.component';

const routes: Routes = [
  {
    path: '',
    redirectTo: '/dashboard',
    pathMatch: 'full'
  },
  
  // Layout principal para usuários comuns
  {
    path: '',
    component: MainLayoutComponent,
    canActivate: [AuthGuard],
    children: [
      {
        path: 'dashboard',
        loadChildren: () => import('./features/dashboard/dashboard.module').then(m => m.DashboardModule),
        data: { 
          breadcrumb: 'Dashboard',
          preload: true // Indica que deve ser pre-carregado
        }
      },
      {
        path: 'products',
        loadChildren: () => import('./features/products/products.module').then(m => m.ProductsModule),
        data: { 
          breadcrumb: 'Produtos',
          apiRequired: ['products'],
          permissions: ['products.read']
        }
      },
      {
        path: 'orders',
        loadChildren: () => import('./features/orders/orders.module').then(m => m.OrdersModule),
        data: { 
          breadcrumb: 'Pedidos',
          apiRequired: ['orders', 'products'],
          permissions: ['orders.read']
        }
      }
    ]
  },
  
  // Layout administrativo
  {
    path: 'admin',
    component: AdminLayoutComponent,
    canActivate: [AuthGuard],
    children: [
      {
        path: 'users',
        loadChildren: () => import('./features/users/users.module').then(m => m.UsersModule),
        data: { 
          breadcrumb: 'Usuários',
          apiRequired: ['users'],
          permissions: ['users.manage']
        }
      },
      {
        path: 'analytics',
        loadChildren: () => import('./features/analytics/analytics.module').then(m => m.AnalyticsModule),
        data: { 
          breadcrumb: 'Analytics',
          apiRequired: ['analytics'],
          permissions: ['analytics.view'],
          feature: 'analytics' // Feature flag
        }
      },
      {
        path: 'reports',
        loadChildren: () => import('./features/reports/reports.module').then(m => m.ReportsModule),
        data: { 
          breadcrumb: 'Relatórios',
          apiRequired: ['orders', 'users', 'analytics'],
          permissions: ['reports.view']
        }
      }
    ]
  },
  
  // Rotas de autenticação (sem layout)
  {
    path: 'auth',
    loadChildren: () => import('./features/auth/auth.module').then(m => m.AuthModule)
  },
  
  // Página 404
  {
    path: '**',
    loadChildren: () => import('./features/error-pages/error-pages.module').then(m => m.ErrorPagesModule)
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, {
    // Estratégia de preloading customizada
    preloadingStrategy: CustomPreloadingStrategy,
    enableTracing: false, // Apenas para desenvolvimento
    scrollPositionRestoration: 'top'
  })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## 7. Estratégia de Preloading Customizada
### core/strategies/custom-preloading.strategy.ts
```typescript
import { Injectable } from '@angular/core';
import { PreloadingStrategy, Route } from '@angular/router';
import { Observable, of, timer } from 'rxjs';
import { switchMap } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class CustomPreloadingStrategy implements PreloadingStrategy {
  
  preload(route: Route, fn: () => Observable<any>): Observable<any> {
    // Não precarregar se não há dados
    if (!route.data) {
      return of(null);
    }

    // Precarregar imediatamente se marcado como prioritário
    if (route.data['preload'] === true) {
      console.log(`Preloading: ${route.path}`);
      return fn();
    }

    // Precarregar com delay para módulos menos prioritários
    if (route.data['preload'] === 'delayed') {
      console.log(`Delayed preloading: ${route.path}`);
      return timer(5000).pipe(switchMap(() => fn()));
    }

    // Não precarregar módulos administrativos pesados a menos que necessário
    if (route.path?.includes('admin') && !route.data['preload']) {
      return of(null);
    }

    // Precarregar baseado em feature flags
    if (route.data['feature']) {
      const featureEnabled = this.isFeatureEnabled(route.data['feature']);
      if (featureEnabled) {
        return timer(2000).pipe(switchMap(() => fn()));
      }
      return of(null);
    }

    return of(null);
  }

  private isFeatureEnabled(feature: string): boolean {
    // Verificar feature flags do environment ou API
    const features = (window as any).APP_FEATURES || {};
    return features[feature] === true;
  }
}
```

## 8. Módulo de Produtos Completo
### features/products/products.module.ts
```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule, FormsModule } from '@angular/forms';

// Routing
import { ProductsRoutingModule } from './products-routing.module';

// Shared
import { SharedModule } from '../../shared/shared.module';

// Services específicos do módulo
import { ProductsFacadeService } from './services/products-facade.service';
import { ProductsCacheService } from './services/products-cache.service';

// Pages
import { ProductsListPageComponent } from './pages/products-list-page/products-list-page.component';
import { ProductCreatePageComponent } from './pages/product-create-page/product-create-page.component';
import { ProductEditPageComponent } from './pages/product-edit-page/product-edit-page.component';
import { ProductDetailPageComponent } from './pages/product-detail-page/product-detail-page.component';
import { ProductsBulkPageComponent } from './pages/products-bulk-page/products-bulk-page.component';

// Components
import { ProductFormComponent } from './components/product-form/product-form.component';
import { ProductCardComponent } from './components/product-card/product-card.component';
import { ProductFiltersComponent } from './components/product-filters/product-filters.component';
import {     ProductStatsComponent
  ],
  imports: [
    CommonModule,
    ReactiveFormsModule,
    FormsModule,
    ProductsRoutingModule,
    SharedModule
  ],
  providers: [
    // Serviços específicos do módulo
    ProductsFacadeService,
    ProductsCacheService,
    
    // Resolvers
    ProductResolver,
    ProductsListResolver
  ]
})
export class ProductsModule { }
```

### features/products/products-routing.module.ts
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

// Guards
import { CanDeactivateGuard } from '../../core/guards/can-deactivate.guard';

// Pages
import { ProductsListPageComponent } from './pages/products-list-page/products-list-page.component';
import { ProductCreatePageComponent } from './pages/product-create-page/product-create-page.component';
import { ProductEditPageComponent } from './pages/product-edit-page/product-edit-page.component';
import { ProductDetailPageComponent } from './pages/product-detail-page/product-detail-page.component';
import { ProductsBulkPageComponent } from './pages/products-bulk-page/products-bulk-page.component';

// Resolvers
import { ProductResolver } from './resolvers/product.resolver';
import { ProductsListResolver } from './resolvers/products-list.resolver';

const routes: Routes = [
  {
    path: '',
    component: ProductsListPageComponent,
    resolve: {
      productsData: ProductsListResolver
    },
    data: { 
      title: 'Lista de Produtos',
      breadcrumb: 'Lista',
      cacheable: true
    }
  },
  {
    path: 'create',
    component: ProductCreatePageComponent,
    canDeactivate: [CanDeactivateGuard],
    data: { 
      title: 'Criar Produto',
      breadcrumb: 'Criar'
    }
  },
  {
    path: 'bulk',
    component: ProductsBulkPageComponent,
    data: { 
      title: 'Operações em Lote',
      breadcrumb: 'Operações em Lote',
      permissions: ['products.bulk']
    }
  },
  {
    path: ':id',
    component: ProductDetailPageComponent,
    resolve: {
      product: ProductResolver
    },
    data: { 
      title: 'Detalhes do Produto',
      breadcrumb: 'Detalhes',
      cacheable: true
    }
  },
  {
    path: ':id/edit',
    component: ProductEditPageComponent,
    resolve: {
      product: ProductResolver
    },
    canDeactivate: [CanDeactivateGuard],
    data: { 
      title: 'Editar Produto',
      breadcrumb: 'Editar'
    }
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class ProductsRoutingModule { }
```

## 9. Service Facade Pattern
### features/products/services/products-facade.service.ts
```typescript
import { Injectable } from '@angular/core';
import { Observable, BehaviorSubject, combineLatest } from 'rxjs';
import { map, tap, shareReplay, finalize } from 'rxjs/operators';

// Core Services
import { ProductsApiService, Product, ProductsResponse } from '../../../core/services/products-api.service';
import { LoadingService } from '../../../core/services/loading.service';
import { NotificationService } from '../../../core/services/notification.service';

// Module Services
import { ProductsCacheService } from './products-cache.service';

export interface ProductsState {
  products: Product[];
  loading: boolean;
  error: string | null;
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
  filters: {
    search?: string;
    category?: string;
    status?: 'active' | 'inactive';
    priceRange?: { min: number; max: number };
  };
}

@Injectable()
export class ProductsFacadeService {
  private stateSubject = new BehaviorSubject<ProductsState>({
    products: [],
    loading: false,
    error: null,
    pagination: { page: 1, limit: 10, total: 0, totalPages: 0 },
    filters: {}
  });

  public state$ = this.stateSubject.asObservable();
  public products$ = this.state$.pipe(map(state => state.products));
  public loading$ = this.state$.pipe(map(state => state.loading));
  public error$ = this.state$.pipe(map(state => state.error));
  public pagination$ = this.state$.pipe(map(state => state.pagination));

  constructor(
    private productsApiService: ProductsApiService,
    private productsCache: ProductsCacheService,
    private loadingService: LoadingService,
    private notificationService: NotificationService
  ) {}

  // Estado atual
  get currentState(): ProductsState {
    return this.stateSubject.value;
  }

  // Carregar produtos com cache inteligente
  loadProducts(filters?: any, useCache: boolean = true): Observable<ProductsResponse> {
    const cacheKey = this.productsCache.generateCacheKey('products-list', filters);
    
    // Verificar cache primeiro
    if (useCache) {
      const cachedData = this.productsCache.get<ProductsResponse>(cacheKey);
      if (cachedData) {
        this.updateState({ 
          products: cachedData.data,
          pagination: cachedData.pagination,
          loading: false,
          error: null 
        });
        return new Observable(subscriber => {
          subscriber.next(cachedData);
          subscriber.complete();
        });
      }
    }

    // Atualizar estado com loading
    this.updateState({ loading: true, error: null });

    return this.productsApiService.getProducts(filters).pipe(
      tap(response => {
        // Salvar no cache
        this.productsCache.set(cacheKey, response, 300000); // 5 minutos
        
        // Atualizar estado
        this.updateState({
          products: response.data,
          pagination: response.pagination,
          loading: false,
          error: null,
          filters: filters || {}
        });
      }),
      shareReplay(1),
      finalize(() => this.updateState({ loading: false }))
    );
  }

  // Criar produto
  createProduct(product: Omit<Product, 'id'>): Observable<Product> {
    this.updateState({ loading: true, error: null });

    return this.productsApiService.createProduct(product).pipe(
      tap(newProduct => {
        // Invalidar cache relacionado
        this.productsCache.invalidatePattern('products-');
        
        // Adicionar produto à lista atual
        const currentProducts = [...this.currentState.products, newProduct];
        this.updateState({ 
          products: currentProducts,
          loading: false 
        });

        this.notificationService.success('Produto criado com sucesso!');
      }),
      finalize(() => this.updateState({ loading: false }))
    );
  }

  // Atualizar produto
  updateProduct(id: number, product: Partial<Product>): Observable<Product> {
    this.updateState({ loading: true, error: null });

    return this.productsApiService.updateProduct(id, product).pipe(
      tap(updatedProduct => {
        // Invalidar cache
        this.productsCache.invalidatePattern('products-');
        this.productsCache.invalidate(`product-${id}`);
        
        // Atualizar produto na lista
        const currentProducts = this.currentState.products.map(p => 
          p.id === id ? updatedProduct : p
        );
        
        this.updateState({ 
          products: currentProducts,
          loading: false 
        });

        this.notificationService.success('Produto atualizado com sucesso!');
      }),
      finalize(() => this.updateState({ loading: false }))
    );
  }

  // Excluir produto
  deleteProduct(id: number): Observable<void> {
    this.updateState({ loading: true, error: null });

    return this.productsApiService.deleteProduct(id).pipe(
      tap(() => {
        // Invalidar cache
        this.productsCache.invalidatePattern('products-');
        this.productsCache.invalidate(`product-${id}`);
        
        // Remover produto da lista
        const currentProducts = this.currentState.products.filter(p => p.id !== id);
        this.updateState({ 
          products: currentProducts,
          loading: false 
        });

        this.notificationService.success('Produto excluído com sucesso!');
      }),
      finalize(() => this.updateState({ loading: false }))
    );
  }

  // Buscar produto específico
  getProduct(id: number, useCache: boolean = true): Observable<Product> {
    const cacheKey = `product-${id}`;
    
    if (useCache) {
      const cached = this.productsCache.get<Product>(cacheKey);
      if (cached) {
        return new Observable(subscriber => {
          subscriber.next(cached);
          subscriber.complete();
        });
      }
    }

    return this.productsApiService.getProductById(id).pipe(
      tap(product => {
        this.productsCache.set(cacheKey, product, 600000); // 10 minutos
      }),
      shareReplay(1)
    );
  }

  // Atualização em lote
  bulkUpdatePrices(updates: { id: number; price: number }[]): Observable<void> {
    this.updateState({ loading: true, error: null });

    return this.productsApiService.bulkUpdatePrices(updates).pipe(
      tap(() => {
        // Invalidar todo o cache de produtos
        this.productsCache.invalidatePattern('products-');
        
        // Recarregar produtos
        this.loadProducts(this.currentState.filters, false).subscribe();
        
        this.notificationService.success(`${updates.length} produtos atualizados!`);
      }),
      finalize(() => this.updateState({ loading: false }))
    );
  }

  // Filtrar produtos localmente
  applyFilters(filters: any): void {
    this.updateState({ filters });
    this.loadProducts(filters);
  }

  // Limpar filtros
  clearFilters(): void {
    this.updateState({ filters: {} });
    this.loadProducts();
  }

  // Atualizar estado
  private updateState(partialState: Partial<ProductsState>): void {
    const currentState = this.currentState;
    const newState = { ...currentState, ...partialState };
    this.stateSubject.next(newState);
  }

  // Limpar estado (útil ao sair do módulo)
  clearState(): void {
    this.stateSubject.next({
      products: [],
      loading: false,
      error: null,
      pagination: { page: 1, limit: 10, total: 0, totalPages: 0 },
      filters: {}
    });
  }
}
```

## 10. Service de Cache Inteligente
### features/products/services/products-cache.service.ts
```typescript
import { Injectable } from '@angular/core';

interface CacheItem<T> {
  data: T;
  timestamp: number;
  ttl: number;
}

@Injectable()
export class ProductsCacheService {
  private cache = new Map<string, CacheItem<any>>();
  private readonly DEFAULT_TTL = 300000; // 5 minutos

  set<T>(key: string, data: T, ttl: number = this.DEFAULT_TTL): void {
    const item: CacheItem<T> = {
      data,
      timestamp: Date.now(),
      ttl
    };
    
    this.cache.set(key, item);
    
    // Auto limpeza após TTL
    setTimeout(() => {
      this.cache.delete(key);
    }, ttl);
  }

  get<T>(key: string): T | null {
    const item = this.cache.get(key);
    
    if (!item) {
      return null;
    }

    // Verificar se expirou
    if (Date.now() - item.timestamp > item.ttl) {
      this.cache.delete(key);
      return null;
    }

    return item.data as T;
  }

  has(key: string): boolean {
    return this.cache.has(key) && this.get(key) !== null;
  }

  invalidate(key: string): void {
    this.cache.delete(key);
  }

  invalidatePattern(pattern: string): void {
    const keysToDelete = Array.from(this.cache.keys()).filter(key => 
      key.includes(pattern)
    );
    
    keysToDelete.forEach(key => this.cache.delete(key));
  }

  clear(): void {
    this.cache.clear();
  }

  // Gerar chave de cache baseada em parâmetros
  generateCacheKey(prefix: string, params?: any): string {
    if (!params) {
      return prefix;
    }

    const sortedParams = Object.keys(params)
      .sort()
      .reduce((result, key) => {
        if (params[key] !== undefined && params[key] !== null) {
          result[key] = params[key];
        }
        return result;
      }, {} as any);

    const paramsString = JSON.stringify(sortedParams);
    return `${prefix}-${btoa(paramsString)}`;
  }

  // Estatísticas do cache
  getStats(): { size: number; keys: string[] } {
    return {
      size: this.cache.size,
      keys: Array.from(this.cache.keys())
    };
  }
}
```

## 11. Page Component Example
### features/products/pages/products-list-page/products-list-page.component.ts
```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { Subject, takeUntil } from 'rxjs';

// Services
import { ProductsFacadeService } from '../../services/products-facade.service';
import { Product } from '../../../../core/services/products-api.service';

@Component({
  selector: 'app-products-list-page',
  templateUrl: './products-list-page.component.html',
  styleUrls: ['./products-list-page.component.scss']
})
export class ProductsListPageComponent implements OnInit, OnDestroy {
  private destroy$ = new Subject<void>();

  // Streams do facade
  products$ = this.productsFacade.products$;
  loading$ = this.productsFacade.loading$;
  error$ = this.productsFacade.error$;
  pagination$ = this.productsFacade.pagination$;

  constructor(
    private productsFacade: ProductsFacadeService,
    private route: ActivatedRoute,
    private router: Router
  ) {}

  ngOnInit(): void {
    // Carregar dados do resolver ou fazer nova busca
    const resolvedData = this.route.snapshot.data['productsData'];
    
    if (resolvedData) {
      // Dados já foram carregados pelo resolver
      console.log('Dados carregados pelo resolver');
    } else {
      // Carregar dados normalmente
      this.productsFacade.loadProducts().pipe(
        takeUntil(this.destroy$)
      ).subscribe();
    }
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }

  onFiltersChange(filters: any): void {
    this.productsFacade.applyFilters(filters);
  }

  onPageChange(page: number): void {
    const currentFilters = this.productsFacade.currentState.filters;
    this.productsFacade.loadProducts({ ...currentFilters, page });
  }

  onProductDelete(product: Product): void {
    if (product.id && confirm(`Excluir produto "${product.name}"?`)) {
      this.productsFacade.deleteProduct(product.id).pipe(
        takeUntil(this.destroy$)
      ).subscribe();
    }
  }

  onCreateProduct(): void {
    this.router.navigate(['./create'], { relativeTo: this.route });
  }

  onEditProduct(product: Product): void {
    this.router.navigate(['./', product.id, 'edit'], { relativeTo: this.route });
  }

  onViewProduct(product: Product): void {
    this.router.navigate(['./', product.id], { relativeTo: this.route });
  }
}
```

## 12. Guard para Feature Flags
### core/guards/feature-flag.guard.ts
```typescript
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, Router } from '@angular/router';
import { FeatureFlagService } from '../services/feature-flag.service';

@Injectable({
  providedIn: 'root'
})
export class FeatureFlagGuard implements CanActivate {

  constructor(
    private featureFlagService: FeatureFlagService,
    private router: Router
  ) {}

  canActivate(route: ActivatedRouteSnapshot): boolean {
    const requiredFeature = route.data['feature'];
    
    if (!requiredFeature) {
      return true; // Não há feature flag definida
    }

    const isEnabled = this.featureFlagService.isEnabled(requiredFeature);
    
    if (!isEnabled) {
      this.router.navigate(['/feature-not-available']);
      return false;
    }

    return true;
  }
}
```

## 13. Exemplo de Core Module com Múltiplas APIs
### core/core.module.ts - Configuração Completa
```typescript
import { NgModule, Optional, SkipSelf } from '@angular/core';
import { CommonModule } from '@angular/common';
import { HTTP_INTERCEPTORS } from '@angular/common/http';

// Environment config
import { API_ENDPOINTS_CONFIG, createApiEndpointsConfig } from './config/api-endpoints.config';

// Services
import { BaseApiService } from './services/base-api.service';
import { ProductsApiService } from './services/products-api.service';
import { UsersApiService } from './services/users-api.service';
import { OrdersApiService } from './services/orders-api.service';
import { AnalyticsApiService } from './services/analytics-api.service';
import { LoadingService } from './services/loading.service';
import { NotificationService } from './services/notification.service';
import { FeatureFlagService } from './services/feature-flag.service';

// Guards
import { AuthGuard } from './guards/auth.guard';
import { FeatureFlagGuard } from './guards/feature-flag.guard';
import { PermissionGuard } from './guards/permission.guard';

// Interceptors
import { MultiApiAuthInterceptor } from './interceptors/multi-api-auth.interceptor';
import { ErrorInterceptor } from './interceptors/error.interceptor';
import { LoadingInterceptor } from './interceptors/loading.interceptor';
import { CacheInterceptor } from './interceptors/cache.interceptor';

// Strategies
import { CustomPreloadingStrategy } from './strategies/custom-preloading.strategy';

@NgModule({
  imports: [CommonModule],
  providers: [
    // Configuration
    {
      provide: API_ENDPOINTS_CONFIG,
      useFactory: createApiEndpointsConfig
    },
    
    // Services
    BaseApiService,
    ProductsApiService,
    UsersApiService,
    OrdersApiService,
    AnalyticsApiService,
    LoadingService,
    NotificationService,
    FeatureFlagService,
    
    // Guards
    AuthGuard,
    FeatureFlagGuard,
    PermissionGuard,
    
    // Strategies
    CustomPreloadingStrategy,
    
    // Interceptors (ordem importa!)
    {
      provide: HTTP_INTERCEPTORS,
      useClass: CacheInterceptor,
      multi: true
    },
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MultiApiAuthInterceptor,
      multi: true
    },
    {
      provide: HTTP_INTERCEPTORS,
      useClass: ErrorInterceptor,
      multi: true
    },
    {
      provide: HTTP_INTERCEPTORS,
      useClass: LoadingInterceptor,
      multi: true
    }
  ]
})
export class CoreModule {
  constructor(@Optional() @SkipSelf() parentModule: CoreModule) {
    if (parentModule) {
      throw new Error('CoreModule já foi carregado. Importe apenas no AppModule.');
    }
  }
}
```

Esta arquitetura avançada oferece:

✅ **Múltiplas APIs** com configurações específicas
✅ **Lazy loading modular** completo  
✅ **Cache inteligente** com TTL e invalidação
✅ **Facade pattern** para gerência de estado
✅ **Feature flags** e **permissions**
✅ **Preloading strategies** customizadas
✅ **Interceptors especializados** por API
✅ **Guards avançados** para proteção
✅ **Resolvers** para pre-loading de dados
✅ **Estratégias de retry** personalizadas } from './components/product-stats/product-