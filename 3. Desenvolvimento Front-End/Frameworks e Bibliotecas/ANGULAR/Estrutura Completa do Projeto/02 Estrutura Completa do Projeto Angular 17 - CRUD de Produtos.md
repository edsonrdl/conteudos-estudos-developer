

## 1. Estrutura de Pastas
```
my-ecommerce-app/
├── src/
│   ├── app/
│   │   ├── core/
│   │   │   ├── services/
│   │   │   │   ├── api.service.ts
│   │   │   │   └── auth.service.ts
│   │   │   ├── interceptors/
│   │   │   │   ├── auth.interceptor.ts
│   │   │   │   └── error.interceptor.ts
│   │   │   ├── guards/
│   │   │   │   └── auth.guard.ts
│   │   │   ├── models/
│   │   │   │   ├── api-response.interface.ts
│   │   │   │   └── pagination.interface.ts
│   │   │   └── core.module.ts
│   │   │
│   │   ├── shared/
│   │   │   ├── components/
│   │   │   │   ├── loading/
│   │   │   │   ├── modal/
│   │   │   │   ├── confirmation-dialog/
│   │   │   │   └── pagination/
│   │   │   ├── pipes/
│   │   │   │   └── currency.pipe.ts
│   │   │   ├── validators/
│   │   │   │   └── custom-validators.ts
│   │   │   └── shared.module.ts
│   │   │
│   │   ├── features/
│   │   │   ├── products/
│   │   │   │   ├── pages/
│   │   │   │   │   ├── product-list/
│   │   │   │   │   ├── product-create/
│   │   │   │   │   ├── product-edit/
│   │   │   │   │   └── product-detail/
│   │   │   │   ├── components/
│   │   │   │   │   ├── product-form/
│   │   │   │   │   └── product-card/
│   │   │   │   ├── services/
│   │   │   │   │   └── product.service.ts
│   │   │   │   ├── models/
│   │   │   │   │   └── product.interface.ts
│   │   │   │   ├── resolvers/
│   │   │   │   │   └── product.resolver.ts
│   │   │   │   ├── products-routing.module.ts
│   │   │   │   └── products.module.ts
│   │   │   │
│   │   │   └── dashboard/
│   │   │       ├── dashboard.component.ts
│   │   │       └── dashboard.module.ts
│   │   │
│   │   ├── layout/
│   │   │   ├── header/
│   │   │   ├── sidebar/
│   │   │   └── main-layout/
│   │   │
│   │   ├── app-routing.module.ts
│   │   └── app.module.ts
│   └── environments/
│       ├── environment.ts
│       └── environment.prod.ts
```

## 2. Configuração do Environment

### environment.ts
```typescript
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000/api',
  apiVersion: 'v1'
};
```

## 3. Core - Serviços Fundamentais

### core/services/api.service.ts
```typescript
import { Injectable } from '@angular/core';
import { HttpClient, HttpParams, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError, retry } from 'rxjs/operators';
import { environment } from '../../../environments/environment';

export interface ApiOptions {
  params?: HttpParams | { [key: string]: any };
  headers?: { [key: string]: string };
}

@Injectable({
  providedIn: 'root'
})
export class ApiService {
  private baseUrl = `${environment.apiUrl}/${environment.apiVersion}`;

  constructor(private http: HttpClient) {}

  // GET Request
  get<T>(endpoint: string, options?: ApiOptions): Observable<T> {
    return this.http.get<T>(`${this.baseUrl}/${endpoint}`, {
      params: options?.params,
      headers: options?.headers
    }).pipe(
      retry(2),
      catchError(this.handleError)
    );
  }

  // POST Request
  post<T>(endpoint: string, data: any, options?: ApiOptions): Observable<T> {
    return this.http.post<T>(`${this.baseUrl}/${endpoint}`, data, {
      params: options?.params,
      headers: options?.headers
    }).pipe(
      catchError(this.handleError)
    );
  }

  // PUT Request
  put<T>(endpoint: string, data: any, options?: ApiOptions): Observable<T> {
    return this.http.put<T>(`${this.baseUrl}/${endpoint}`, data, {
      params: options?.params,
      headers: options?.headers
    }).pipe(
      catchError(this.handleError)
    );
  }

  // DELETE Request
  delete<T>(endpoint: string, options?: ApiOptions): Observable<T> {
    return this.http.delete<T>(`${this.baseUrl}/${endpoint}`, {
      params: options?.params,
      headers: options?.headers
    }).pipe(
      catchError(this.handleError)
    );
  }

  // PATCH Request
  patch<T>(endpoint: string, data: any, options?: ApiOptions): Observable<T> {
    return this.http.patch<T>(`${this.baseUrl}/${endpoint}`, data, {
      params: options?.params,
      headers: options?.headers
    }).pipe(
      catchError(this.handleError)
    );
  }

  private handleError(error: HttpErrorResponse) {
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
        case 500:
          errorMessage = 'Erro interno do servidor';
          break;
        default:
          errorMessage = `Erro ${error.status}: ${error.error?.message || error.message}`;
      }
    }

    console.error('API Error:', errorMessage, error);
    return throwError(() => new Error(errorMessage));
  }
}
```

### core/models/api-response.interface.ts
```typescript
export interface ApiResponse<T> {
  success: boolean;
  data: T;
  message?: string;
  errors?: string[];
}

export interface PaginatedResponse<T> {
  data: T[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}
```

### core/interceptors/auth.interceptor.ts
```typescript
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler } from '@angular/common/http';
import { AuthService } from '../services/auth.service';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  constructor(private authService: AuthService) {}

  intercept(req: HttpRequest<any>, next: HttpHandler) {
    const authToken = this.authService.getToken();
    
    if (authToken) {
      const authReq = req.clone({
        headers: req.headers.set('Authorization', `Bearer ${authToken}`)
      });
      return next.handle(authReq);
    }
    
    return next.handle(req);
  }
}
```

## 4. Feature Module - Products

### features/products/models/product.interface.ts
```typescript
export interface Product {
  id?: number;
  name: string;
  description: string;
  price: number;
  category: string;
  imageUrl?: string;
  stock: number;
  isActive: boolean;
  createdAt?: Date;
  updatedAt?: Date;
}

export interface ProductFilter {
  search?: string;
  category?: string;
  minPrice?: number;
  maxPrice?: number;
  isActive?: boolean;
  page?: number;
  limit?: number;
  sortBy?: string;
  sortOrder?: 'asc' | 'desc';
}

export interface CreateProductRequest {
  name: string;
  description: string;
  price: number;
  category: string;
  imageUrl?: string;
  stock: number;
}

export interface UpdateProductRequest extends Partial<CreateProductRequest> {
  isActive?: boolean;
}
```

### features/products/services/product.service.ts
```typescript
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { ApiService } from '../../../core/services/api.service';
import { ApiResponse, PaginatedResponse } from '../../../core/models/api-response.interface';
import { Product, ProductFilter, CreateProductRequest, UpdateProductRequest } from '../models/product.interface';

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private readonly endpoint = 'products';

  constructor(private apiService: ApiService) {}

  // GET - Listar produtos com filtros e paginação
  getProducts(filter?: ProductFilter): Observable<PaginatedResponse<Product>> {
    const params = this.buildFilterParams(filter);
    return this.apiService.get<ApiResponse<PaginatedResponse<Product>>>(
      this.endpoint, 
      { params }
    ).pipe(
      map(response => response.data)
    );
  }

  // GET - Buscar produto por ID
  getProductById(id: number): Observable<Product> {
    return this.apiService.get<ApiResponse<Product>>(`${this.endpoint}/${id}`)
      .pipe(
        map(response => response.data)
      );
  }

  // POST - Criar novo produto
  createProduct(product: CreateProductRequest): Observable<Product> {
    return this.apiService.post<ApiResponse<Product>>(this.endpoint, product)
      .pipe(
        map(response => response.data)
      );
  }

  // PUT - Atualizar produto completo
  updateProduct(id: number, product: UpdateProductRequest): Observable<Product> {
    return this.apiService.put<ApiResponse<Product>>(`${this.endpoint}/${id}`, product)
      .pipe(
        map(response => response.data)
      );
  }

  // PATCH - Atualizar parcialmente
  patchProduct(id: number, updates: Partial<UpdateProductRequest>): Observable<Product> {
    return this.apiService.patch<ApiResponse<Product>>(`${this.endpoint}/${id}`, updates)
      .pipe(
        map(response => response.data)
      );
  }

  // DELETE - Remover produto
  deleteProduct(id: number): Observable<void> {
    return this.apiService.delete<ApiResponse<void>>(`${this.endpoint}/${id}`)
      .pipe(
        map(() => void 0)
      );
  }

  // Métodos auxiliares
  toggleProductStatus(id: number): Observable<Product> {
    return this.apiService.patch<ApiResponse<Product>>(
      `${this.endpoint}/${id}/toggle-status`,
      {}
    ).pipe(
      map(response => response.data)
    );
  }

  getCategories(): Observable<string[]> {
    return this.apiService.get<ApiResponse<string[]>>(`${this.endpoint}/categories`)
      .pipe(
        map(response => response.data)
      );
  }

  private buildFilterParams(filter?: ProductFilter): any {
    if (!filter) return {};

    const params: any = {};
    
    if (filter.search) params.search = filter.search;
    if (filter.category) params.category = filter.category;
    if (filter.minPrice !== undefined) params.minPrice = filter.minPrice.toString();
    if (filter.maxPrice !== undefined) params.maxPrice = filter.maxPrice.toString();
    if (filter.isActive !== undefined) params.isActive = filter.isActive.toString();
    if (filter.page) params.page = filter.page.toString();
    if (filter.limit) params.limit = filter.limit.toString();
    if (filter.sortBy) params.sortBy = filter.sortBy;
    if (filter.sortOrder) params.sortOrder = filter.sortOrder;

    return params;
  }
}
```

## 5. Routing - products-routing.module.ts
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { ProductListComponent } from './pages/product-list/product-list.component';
import { ProductCreateComponent } from './pages/product-create/product-create.component';
import { ProductEditComponent } from './pages/product-edit/product-edit.component';
import { ProductDetailComponent } from './pages/product-detail/product-detail.component';
import { ProductResolver } from './resolvers/product.resolver';

const routes: Routes = [
  {
    path: '',
    component: ProductListComponent,
    data: { title: 'Lista de Produtos' }
  },
  {
    path: 'create',
    component: ProductCreateComponent,
    data: { title: 'Criar Produto' }
  },
  {
    path: ':id',
    component: ProductDetailComponent,
    resolve: { product: ProductResolver },
    data: { title: 'Detalhes do Produto' }
  },
  {
    path: ':id/edit',
    component: ProductEditComponent,
    resolve: { product: ProductResolver },
    data: { title: 'Editar Produto' }
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class ProductsRoutingModule { }
```

## 6. Resolver para Pre-loading
### features/products/resolvers/product.resolver.ts
```typescript
import { Injectable } from '@angular/core';
import { Router, Resolve, ActivatedRouteSnapshot } from '@angular/router';
import { Observable, of } from 'rxjs';
import { catchError } from 'rxjs/operators';
import { ProductService } from '../services/product.service';
import { Product } from '../models/product.interface';

@Injectable({
  providedIn: 'root'
})
export class ProductResolver implements Resolve<Product | null> {
  constructor(
    private productService: ProductService,
    private router: Router
  ) {}

  resolve(route: ActivatedRouteSnapshot): Observable<Product | null> {
    const id = Number(route.paramMap.get('id'));
    
    if (!id || isNaN(id)) {
      this.router.navigate(['/products']);
      return of(null);
    }

    return this.productService.getProductById(id).pipe(
      catchError(() => {
        this.router.navigate(['/products']);
        return of(null);
      })
    );
  }
}
```

## 7. Componentes das Pages

### features/products/pages/product-list/product-list.component.ts
```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Subject, debounceTime, distinctUntilChanged, takeUntil } from 'rxjs';
import { ProductService } from '../../services/product.service';
import { Product, ProductFilter } from '../../models/product.interface';
import { PaginatedResponse } from '../../../../core/models/api-response.interface';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.scss']
})
export class ProductListComponent implements OnInit, OnDestroy {
  products: Product[] = [];
  loading = false;
  error: string | null = null;
  
  // Filtros
  filter: ProductFilter = {
    page: 1,
    limit: 10,
    sortBy: 'name',
    sortOrder: 'asc'
  };
  
  // Paginação
  totalItems = 0;
  totalPages = 0;
  
  // Search
  searchTerm = '';
  private searchSubject = new Subject<string>();
  private destroy$ = new Subject<void>();

  // Categorias disponíveis
  categories: string[] = [];

  constructor(private productService: ProductService) {
    // Debounce na busca
    this.searchSubject.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      takeUntil(this.destroy$)
    ).subscribe(term => {
      this.filter.search = term || undefined;
      this.filter.page = 1;
      this.loadProducts();
    });
  }

  ngOnInit() {
    this.loadProducts();
    this.loadCategories();
  }

  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
  }

  loadProducts() {
    this.loading = true;
    this.error = null;

    this.productService.getProducts(this.filter).subscribe({
      next: (response: PaginatedResponse<Product>) => {
        this.products = response.data;
        this.totalItems = response.pagination.total;
        this.totalPages = response.pagination.totalPages;
        this.loading = false;
      },
      error: (error) => {
        this.error = error.message;
        this.loading = false;
        console.error('Erro ao carregar produtos:', error);
      }
    });
  }

  loadCategories() {
    this.productService.getCategories().subscribe({
      next: (categories) => {
        this.categories = categories;
      },
      error: (error) => {
        console.error('Erro ao carregar categorias:', error);
      }
    });
  }

  // Eventos de busca
  onSearchChange(term: string) {
    this.searchTerm = term;
    this.searchSubject.next(term);
  }

  // Eventos de filtro
  onCategoryChange(category: string) {
    this.filter.category = category || undefined;
    this.filter.page = 1;
    this.loadProducts();
  }

  onStatusChange(status: string) {
    this.filter.isActive = status === 'active' ? true : 
                          status === 'inactive' ? false : undefined;
    this.filter.page = 1;
    this.loadProducts();
  }

  // Eventos de ordenação
  onSortChange(sortBy: string) {
    if (this.filter.sortBy === sortBy) {
      this.filter.sortOrder = this.filter.sortOrder === 'asc' ? 'desc' : 'asc';
    } else {
      this.filter.sortBy = sortBy;
      this.filter.sortOrder = 'asc';
    }
    this.loadProducts();
  }

  // Eventos de paginação
  onPageChange(page: number) {
    this.filter.page = page;
    this.loadProducts();
  }

  onPageSizeChange(limit: number) {
    this.filter.limit = limit;
    this.filter.page = 1;
    this.loadProducts();
  }

  // Ações dos produtos
  toggleProductStatus(product: Product) {
    if (!product.id) return;

    this.productService.toggleProductStatus(product.id).subscribe({
      next: (updatedProduct) => {
        const index = this.products.findIndex(p => p.id === product.id);
        if (index !== -1) {
          this.products[index] = updatedProduct;
        }
      },
      error: (error) => {
        console.error('Erro ao alterar status:', error);
      }
    });
  }

  deleteProduct(product: Product) {
    if (!product.id || !confirm(`Deseja realmente excluir o produto "${product.name}"?`)) {
      return;
    }

    this.productService.deleteProduct(product.id).subscribe({
      next: () => {
        this.loadProducts(); // Recarrega a lista
      },
      error: (error) => {
        console.error('Erro ao excluir produto:', error);
      }
    });
  }

  // Limpar filtros
  clearFilters() {
    this.filter = {
      page: 1,
      limit: 10,
      sortBy: 'name',
      sortOrder: 'asc'
    };
    this.searchTerm = '';
    this.loadProducts();
  }
}
```

### features/products/pages/product-create/product-create.component.ts
```typescript
import { Component } from '@angular/core';
import { Router } from '@angular/router';
import { ProductService } from '../../services/product.service';
import { CreateProductRequest } from '../../models/product.interface';

@Component({
  selector: 'app-product-create',
  templateUrl: './product-create.component.html',
  styleUrls: ['./product-create.component.scss']
})
export class ProductCreateComponent {
  loading = false;
  error: string | null = null;

  constructor(
    private productService: ProductService,
    private router: Router
  ) {}

  onProductSubmit(productData: CreateProductRequest) {
    this.loading = true;
    this.error = null;

    this.productService.createProduct(productData).subscribe({
      next: (product) => {
        this.loading = false;
        this.router.navigate(['/products', product.id]);
      },
      error: (error) => {
        this.error = error.message;
        this.loading = false;
        console.error('Erro ao criar produto:', error);
      }
    });
  }

  onCancel() {
    this.router.navigate(['/products']);
  }
}
```

## 8. Roteamento Principal - app-routing.module.ts
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

const routes: Routes = [
  {
    path: '',
    redirectTo: '/dashboard',
    pathMatch: 'full'
  },
  {
    path: 'dashboard',
    loadChildren: () => import('./features/dashboard/dashboard.module').then(m => m.DashboardModule)
  },
  {
    path: 'products',
    loadChildren: () => import('./features/products/products.module').then(m => m.ProductsModule)
  },
  {
    path: '**',
    redirectTo: '/dashboard'
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## 9. Exemplo de URLs da API

### Endpoints esperados no backend:
```
GET    /api/v1/products                    # Lista com filtros e paginação
GET    /api/v1/products/:id               # Produto específico
POST   /api/v1/products                   # Criar produto
PUT    /api/v1/products/:id               # Atualizar produto completo
PATCH  /api/v1/products/:id               # Atualização parcial
DELETE /api/v1/products/:id               # Excluir produto
PATCH  /api/v1/products/:id/toggle-status # Alternar status
GET    /api/v1/products/categories        # Lista de categorias
```

### Exemplos de Parâmetros de Query:
```
GET /api/v1/products?search=notebook&category=electronics&page=1&limit=10&sortBy=price&sortOrder=desc&minPrice=100&maxPrice=1000&isActive=true
```