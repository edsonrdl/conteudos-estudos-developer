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
│   │   │   └── models/
│   │   │       ├── api-response.interface.ts
│   │   │       └── pagination.interface.ts
│   │   │
│   │   ├── shared/
│   │   │   ├── components/
│   │   │   │   ├── loading/
│   │   │   │   │   ├── loading.component.html
│   │   │   │   │   ├── loading.component.css
│   │   │   │   │   └── loading.component.ts
│   │   │   │   ├── modal/
│   │   │   │   │   ├── modal.component.html
│   │   │   │   │   ├── modal.component.css
│   │   │   │   │   └── modal.component.ts
│   │   │   │   ├── confirmation-dialog/
│   │   │   │   │   ├── confirmation-dialog.component.html
│   │   │   │   │   ├── confirmation-dialog.component.css
│   │   │   │   │   └── confirmation-dialog.component.ts
│   │   │   │   └── pagination/
│   │   │   │       ├── pagination.component.html
│   │   │   │       ├── pagination.component.css
│   │   │   │       └── pagination.component.ts
│   │   │   ├── pipes/
│   │   │   │   └── currency.pipe.ts
│   │   │   └── validators/
│   │   │       └── custom-validators.ts
│   │   │
│   │   ├── features/
│   │   │   ├── products/
│   │   │   │   ├── pages/
│   │   │   │   │   ├── product-list/
│   │   │   │   │   │   ├── product-list.component.html
│   │   │   │   │   │   ├── product-list.component.css
│   │   │   │   │   │   └── product-list.component.ts
│   │   │   │   │   ├── product-create/
│   │   │   │   │   │   ├── product-create.component.html
│   │   │   │   │   │   ├── product-create.component.css
│   │   │   │   │   │   └── product-create.component.ts
│   │   │   │   │   ├── product-edit/
│   │   │   │   │   │   ├── product-edit.component.html
│   │   │   │   │   │   ├── product-edit.component.css
│   │   │   │   │   │   └── product-edit.component.ts
│   │   │   │   │   └── product-detail/
│   │   │   │   │       ├── product-detail.component.html
│   │   │   │   │       ├── product-detail.component.css
│   │   │   │   │       └── product-detail.component.ts
│   │   │   │   ├── components/
│   │   │   │   │   ├── product-form/
│   │   │   │   │   │   ├── product-form.component.html
│   │   │   │   │   │   ├── product-form.component.css
│   │   │   │   │   │   └── product-form.component.ts
│   │   │   │   │   └── product-card/
│   │   │   │   │       ├── product-card.component.html
│   │   │   │   │       ├── product-card.component.css
│   │   │   │   │       └── product-card.component.ts
│   │   │   │   ├── services/
│   │   │   │   │   └── product.service.ts
│   │   │   │   ├── models/
│   │   │   │   │   └── product.interface.ts
│   │   │   │   └── resolvers/
│   │   │   │       └── product.resolver.ts
│   │   │   │
│   │   │   ├── usuarios/
│   │   │   │   ├── pages/
│   │   │   │   │   ├── usuario-list/
│   │   │   │   │   ├── usuario-create/
│   │   │   │   │   ├── usuario-edit/
│   │   │   │   │   └── usuario-detail/
│   │   │   │   ├── components/
│   │   │   │   │   ├── usuario-form/
│   │   │   │   │   └── usuario-card/
│   │   │   │   ├── services/
│   │   │   │   │   └── usuario.service.ts
│   │   │   │   ├── models/
│   │   │   │   │   └── usuario.interface.ts
│   │   │   │   └── resolvers/
│   │   │   │       └── usuario.resolver.ts
│   │   │   │
│   │   │   └── dashboard/
│   │   │       ├── dashboard.component.html
│   │   │       ├── dashboard.component.css
│   │   │       └── dashboard.component.ts
│   │   │
│   │   ├── layout/
│   │   │   ├── header/
│   │   │   │   ├── header.component.html
│   │   │   │   ├── header.component.css
│   │   │   │   └── header.component.ts
│   │   │   ├── sidebar/
│   │   │   │   ├── sidebar.component.html
│   │   │   │   ├── sidebar.component.css
│   │   │   │   └── sidebar.component.ts
│   │   │   └── main-layout/
│   │   │       ├── main-layout.component.html
│   │   │       ├── main-layout.component.css
│   │   │       └── main-layout.component.ts
│   │   │
│   │   ├── app.routes.ts
│   │   ├── app.config.ts
│   │   ├── app.component.html
│   │   ├── app.component.css
│   │   └── app.component.ts
│   │
│   ├── environments/
│   │   ├── environment.ts
│   │   └── environment.prod.ts
│   │
│   ├── main.ts
│   └── index.html
```

## 2. Configuração Principal

### main.ts

typescript

```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { appConfig } from './app/app.config';

bootstrapApplication(AppComponent, appConfig)
  .catch(err => console.error(err));
```

### app.config.ts

typescript

```typescript
import { ApplicationConfig } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { routes } from './app.routes';
import { authInterceptor } from './core/interceptors/auth.interceptor';
import { errorInterceptor } from './core/interceptors/error.interceptor';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideHttpClient(
      withInterceptors([authInterceptor, errorInterceptor])
    )
  ]
};
```

### app.routes.ts

typescript

```typescript
import { Routes } from '@angular/router';
import { authGuard } from './core/guards/auth.guard';

export const routes: Routes = [
  {
    path: '',
    redirectTo: '/dashboard',
    pathMatch: 'full'
  },
  {
    path: 'dashboard',
    loadComponent: () => 
      import('./features/dashboard/dashboard.component')
        .then(c => c.DashboardComponent),
    canActivate: [authGuard]
  },
  {
    path: 'products',
    loadChildren: () => import('./features/products/product.routes')
      .then(r => r.productRoutes)
  },
  {
    path: 'usuarios',
    loadChildren: () => import('./features/usuarios/usuario.routes')
      .then(r => r.usuarioRoutes)
  },
  {
    path: '**',
    redirectTo: '/dashboard'
  }
];
```

## 3. Environment (Igual ao exemplo)

### environment.ts

typescript

```typescript
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000/api',
  apiVersion: 'v1'
};
```

## 4. Core Services (Standalone)

### core/services/api.service.ts

typescript

```typescript
import { Injectable, inject } from '@angular/core';
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
  private http = inject(HttpClient);
  private baseUrl = `${environment.apiUrl}/${environment.apiVersion}`;

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

### core/interceptors/auth.interceptor.ts

typescript

```typescript
import { HttpInterceptorFn } from '@angular/common/http';
import { inject } from '@angular/core';
import { AuthService } from '../services/auth.service';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authService = inject(AuthService);
  const authToken = authService.getToken();
  
  if (authToken) {
    const authReq = req.clone({
      headers: req.headers.set('Authorization', `Bearer ${authToken}`)
    });
    return next(authReq);
  }
  
  return next(req);
};
```

## 5. Feature Products - Standalone

### features/products/product.routes.ts

typescript

```typescript
import { Routes } from '@angular/router';
import { productResolver } from './resolvers/product.resolver';

export const productRoutes: Routes = [
  {
    path: '',
    loadComponent: () => 
      import('./pages/product-list/product-list.component')
        .then(c => c.ProductListComponent),
    data: { title: 'Lista de Produtos' }
  },
  {
    path: 'create',
    loadComponent: () => 
      import('./pages/product-create/product-create.component')
        .then(c => c.ProductCreateComponent),
    data: { title: 'Criar Produto' }
  },
  {
    path: ':id',
    loadComponent: () => 
      import('./pages/product-detail/product-detail.component')
        .then(c => c.ProductDetailComponent),
    resolve: { product: productResolver },
    data: { title: 'Detalhes do Produto' }
  },
  {
    path: ':id/edit',
    loadComponent: () => 
      import('./pages/product-edit/product-edit.component')
        .then(c => c.ProductEditComponent),
    resolve: { product: productResolver },
    data: { title: 'Editar Produto' }
  }
];
```

### features/products/models/product.interface.ts (Igual ao exemplo)

typescript

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

### features/products/services/product.service.ts (Adaptado para Standalone)

typescript

```typescript
import { Injectable, inject } from '@angular/core';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { ApiService } from '../../../core/services/api.service';
import { ApiResponse, PaginatedResponse } from '../../../core/models/api-response.interface';
import { Product, ProductFilter, CreateProductRequest, UpdateProductRequest } from '../models/product.interface';

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private apiService = inject(ApiService);
  private readonly endpoint = 'products';

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

### features/products/resolvers/product.resolver.ts

typescript

```typescript
import { inject } from '@angular/core';
import { Router, ResolveFn, ActivatedRouteSnapshot } from '@angular/router';
import { Observable, of } from 'rxjs';
import { catchError } from 'rxjs/operators';
import { ProductService } from '../services/product.service';
import { Product } from '../models/product.interface';

export const productResolver: ResolveFn<Product | null> = (
  route: ActivatedRouteSnapshot
): Observable<Product | null> => {
  const productService = inject(ProductService);
  const router = inject(Router);
  
  const id = Number(route.paramMap.get('id'));
  
  if (!id || isNaN(id)) {
    router.navigate(['/products']);
    return of(null);
  }

  return productService.getProductById(id).pipe(
    catchError(() => {
      router.navigate(['/products']);
      return of(null);
    })
  );
};
```

## 6. Page Components - Standalone

### features/products/pages/product-list/product-list.component.ts

typescript

```typescript
import { Component, OnInit, OnDestroy, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { RouterLink } from '@angular/router';
import { Subject, debounceTime, distinctUntilChanged, takeUntil } from 'rxjs';

// Shared Components
import { LoadingComponent } from '../../../../shared/components/loading/loading.component';
import { PaginationComponent } from '../../../../shared/components/pagination/pagination.component';

// Feature Components
import { ProductCardComponent } from '../../components/product-card/product-card.component';

// Services & Models
import { ProductService } from '../../services/product.service';
import { Product, ProductFilter } from '../../models/product.interface';
import { PaginatedResponse } from '../../../../core/models/api-response.interface';

@Component({
  selector: 'app-product-list',
  standalone: true,
  imports: [
    CommonModule,
    FormsModule,
    RouterLink,
    LoadingComponent,
    PaginationComponent,
    ProductCardComponent
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
export class ProductListComponent implements OnInit, OnDestroy {
  private productService = inject(ProductService);
  
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

  constructor() {
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

typescript

```typescript
import { Component, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { Router } from '@angular/router';

// Feature Components
import { ProductFormComponent } from '../../components/product-form/product-form.component';

// Services & Models
import { ProductService } from '../../services/product.service';
import { CreateProductRequest } from '../../models/product.interface';

@Component({
  selector: 'app-product-create',
  standalone: true,
  imports: [
    CommonModule,
    ProductFormComponent
  ],
  templateUrl: './product-create.component.html',
  styleUrl: './product-create.component.css'
})
export class ProductCreateComponent {
  private productService = inject(ProductService);
  private router = inject(Router);
  
  loading = false;
  error: string | null = null;

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

## 7. Shared Components - Standalone

### shared/components/loading/loading.component.ts

typescript

```typescript
import { Component, Input } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-loading',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div class="loading-container" *ngIf="show">
      <div class="spinner"></div>
      <p *ngIf="message">{{ message }}</p>
    </div>
  `,
  styleUrl: './loading.component.css'
})
export class LoadingComponent {
  @Input() show = true;
  @Input() message = 'Carregando...';
}
```

### shared/components/pagination/pagination.component.ts

typescript

```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-pagination',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './pagination.component.html',
  styleUrl: './pagination.component.css'
})
export class PaginationComponent {
  @Input() currentPage = 1;
  @Input() totalPages = 1;
  @Input() totalItems = 0;
  @Input() pageSize = 10;

  @Output() pageChange = new EventEmitter<number>();
  @Output() pageSizeChange = new EventEmitter<number>();

  onPageChange(page: number) {
    if (page >= 1 && page <= this.totalPages && page !== this.currentPage) {
      this.pageChange.emit(page);
    }
  }

  onPageSizeChange(event: Event) {
    const select = event.target as HTMLSelectElement;
    this.pageSizeChange.emit(Number(select.value));
  }

  get pages(): number[] {
    const pages: number[] = [];
    const start = Math.max(1, this.currentPage - 2);
    const end = Math.min(this.totalPages, this.currentPage + 2);

    for (let i = start; i <= end; i++) {
      pages.push(i);
    }
    return pages;
  }
}
```

## 8. Layout Components - Standalone

### layout/main-layout/main-layout.component.ts

typescript

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterOutlet } from '@angular/router';

import { HeaderComponent } from '../header/header.component';
import { SidebarComponent } from '../sidebar/sidebar.component';

@Component({
  selector: 'app-main-layout',
  standalone: true,
  imports: [
    CommonModule,
    RouterOutlet,
    HeaderComponent,
    SidebarComponent
  ],
  template: `
    <div class="layout">
      <app-header></app-header>
      <div class="layout-body">
        <app-sidebar></app-sidebar>
        <main class="main-content">
          <router-outlet></router-outlet>
        </main>
      </div>
    </div>
  `,
  styleUrl: './main-layout.component.css'
})
export class MainLayoutComponent {}
```

### layout/header/header.component.ts

typescript

```typescript
import { Component, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterLink, RouterLinkActive } from '@angular/router';
import { AuthService } from '../../core/services/auth.service';

@Component({
  selector: 'app-header',
  standalone: true,
  imports: [
    CommonModule,
    RouterLink,
    RouterLinkActive
  ],
  template: `
    <header class="header">
      <div class="header-brand">
        <h1>Sistema CRUD</h1>
      </div>
      <nav class="header-nav">
        <a routerLink="/dashboard" routerLinkActive="active">Dashboard</a>
        <a routerLink="/products" routerLinkActive="active">Produtos</a>
        <a routerLink="/usuarios" routerLinkActive="active">Usuários</a>
      </nav>
      <div class="header-actions">
        <button (click)="logout()" class="btn-logout">Sair</button>
      </div>
    </header>
  `,
  styleUrl: './header.component.css'
})
export class HeaderComponent {
  private authService = inject(AuthService);

  logout() {
    this.authService.logout();
  }
}
```

### layout/sidebar/sidebar.component.ts

typescript

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterLink, RouterLinkActive } from '@angular/router';

interface MenuItem {
  icon: string;
  label: string;
  route: string;
  children?: MenuItem[];
}

@Component({
  selector: 'app-sidebar',
  standalone: true,
  imports: [
    CommonModule,
    RouterLink,
    RouterLinkActive
  ],
  template: `
    <aside class="sidebar">
      <nav class="sidebar-nav">
        <ul class="menu-list">
          <li *ngFor="let item of menuItems" class="menu-item">
            <a 
              [routerLink]="item.route" 
              routerLinkActive="active"
              class="menu-link"
            >
              <i [class]="item.icon"></i>
              <span>{{ item.label }}</span>
            </a>
            
            <ul *ngIf="item.children" class="submenu">
              <li *ngFor="let child of item.children" class="submenu-item">
                <a 
                  [routerLink]="child.route" 
                  routerLinkActive="active"
                  class="submenu-link"
                >
                  <i [class]="child.icon"></i>
                  <span>{{ child.label }}</span>
                </a>
              </li>
            </ul>
          </li>
        </ul>
      </nav>
    </aside>
  `,
  styleUrl: './sidebar.component.css'
})
export class SidebarComponent {
  menuItems: MenuItem[] = [
    {
      icon: 'fas fa-home',
      label: 'Dashboard',
      route: '/dashboard'
    },
    {
      icon: 'fas fa-box',
      label: 'Produtos',
      route: '/products',
      children: [
        {
          icon: 'fas fa-list',
          label: 'Listar',
          route: '/products'
        },
        {
          icon: 'fas fa-plus',
          label: 'Criar',
          route: '/products/create'
        }
      ]
    },
    {
      icon: 'fas fa-users',
      label: 'Usuários',
      route: '/usuarios',
      children: [
        {
          icon: 'fas fa-list',
          label: 'Listar',
          route: '/usuarios'
        },
        {
          icon: 'fas fa-plus',
          label: 'Criar',
          route: '/usuarios/create'
        }
      ]
    }
  ];
}
```

## 9. Feature Components - Standalone

### features/products/components/product-form/product-form.component.ts

typescript

```typescript
import { Component, Input, Output, EventEmitter, OnInit, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormBuilder, FormGroup, Validators, ReactiveFormsModule } from '@angular/forms';

import { Product, CreateProductRequest, UpdateProductRequest } from '../../models/product.interface';
import { ProductService } from '../../services/product.service';

@Component({
  selector: 'app-product-form',
  standalone: true,
  imports: [
    CommonModule,
    ReactiveFormsModule
  ],
  template: `
    <form [formGroup]="productForm" (ngSubmit)="onSubmit()" class="product-form">
      <div class="form-group">
        <label for="name">Nome *</label>
        <input 
          id="name"
          type="text" 
          formControlName="name"
          class="form-control"
          [class.error]="isFieldInvalid('name')"
        >
        <div *ngIf="isFieldInvalid('name')" class="error-message">
          Nome é obrigatório
        </div>
      </div>

      <div class="form-group">
        <label for="description">Descrição *</label>
        <textarea 
          id="description"
          formControlName="description"
          class="form-control"
          rows="3"
          [class.error]="isFieldInvalid('description')"
        ></textarea>
        <div *ngIf="isFieldInvalid('description')" class="error-message">
          Descrição é obrigatória
        </div>
      </div>

      <div class="form-row">
        <div class="form-group">
          <label for="price">Preço *</label>
          <input 
            id="price"
            type="number" 
            formControlName="price"
            step="0.01"
            min="0"
            class="form-control"
            [class.error]="isFieldInvalid('price')"
          >
          <div *ngIf="isFieldInvalid('price')" class="error-message">
            Preço deve ser maior que zero
          </div>
        </div>

        <div class="form-group">
          <label for="stock">Estoque *</label>
          <input 
            id="stock"
            type="number" 
            formControlName="stock"
            min="0"
            class="form-control"
            [class.error]="isFieldInvalid('stock')"
          >
          <div *ngIf="isFieldInvalid('stock')" class="error-message">
            Estoque deve ser maior ou igual a zero
          </div>
        </div>
      </div>

      <div class="form-group">
        <label for="category">Categoria *</label>
        <select 
          id="category"
          formControlName="category"
          class="form-control"
          [class.error]="isFieldInvalid('category')"
        >
          <option value="">Selecione uma categoria</option>
          <option *ngFor="let category of categories" [value]="category">
            {{ category }}
          </option>
        </select>
        <div *ngIf="isFieldInvalid('category')" class="error-message">
          Categoria é obrigatória
        </div>
      </div>

      <div class="form-group">
        <label for="imageUrl">URL da Imagem</label>
        <input 
          id="imageUrl"
          type="url" 
          formControlName="imageUrl"
          class="form-control"
          placeholder="https://exemplo.com/imagem.jpg"
        >
      </div>

      <div class="form-group" *ngIf="isEditMode">
        <label class="checkbox-label">
          <input 
            type="checkbox" 
            formControlName="isActive"
          >
          <span>Produto ativo</span>
        </label>
      </div>

      <div class="form-actions">
        <button 
          type="button" 
          (click)="onCancel()" 
          class="btn btn-secondary"
        >
          Cancelar
        </button>
        <button 
          type="submit" 
          [disabled]="productForm.invalid || loading"
          class="btn btn-primary"
        >
          {{ loading ? 'Salvando...' : (isEditMode ? 'Atualizar' : 'Criar') }}
        </button>
      </div>
    </form>
  `,
  styleUrl: './product-form.component.css'
})
export class ProductFormComponent implements OnInit {
  private fb = inject(FormBuilder);
  private productService = inject(ProductService);

  @Input() product: Product | null = null;
  @Input() loading = false;
  
  @Output() submit = new EventEmitter<CreateProductRequest | UpdateProductRequest>();
  @Output() cancel = new EventEmitter<void>();

  productForm!: FormGroup;
  categories: string[] = [];

  get isEditMode(): boolean {
    return !!this.product?.id;
  }

  ngOnInit() {
    this.createForm();
    this.loadCategories();
    
    if (this.product) {
      this.populateForm();
    }
  }

  createForm() {
    this.productForm = this.fb.group({
      name: ['', [Validators.required, Validators.minLength(2)]],
      description: ['', [Validators.required, Validators.minLength(10)]],
      price: [0, [Validators.required, Validators.min(0.01)]],
      stock: [0, [Validators.required, Validators.min(0)]],
      category: ['', Validators.required],
      imageUrl: [''],
      isActive: [true]
    });
  }

  populateForm() {
    if (this.product) {
      this.productForm.patchValue({
        name: this.product.name,
        description: this.product.description,
        price: this.product.price,
        stock: this.product.stock,
        category: this.product.category,
        imageUrl: this.product.imageUrl || '',
        isActive: this.product.isActive
      });
    }
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

  isFieldInvalid(fieldName: string): boolean {
    const field = this.productForm.get(fieldName);
    return !!(field && field.invalid && (field.dirty || field.touched));
  }

  onSubmit() {
    if (this.productForm.valid) {
      const formValue = this.productForm.value;
      
      // Remove isActive se não estiver em modo de edição
      if (!this.isEditMode) {
        delete formValue.isActive;
      }

      this.submit.emit(formValue);
    } else {
      this.markFormGroupTouched();
    }
  }

  onCancel() {
    this.cancel.emit();
  }

  private markFormGroupTouched() {
    Object.keys(this.productForm.controls).forEach(key => {
      const control = this.productForm.get(key);
      if (control) {
        control.markAsTouched();
      }
    });
  }
}
```

### features/products/components/product-card/product-card.component.ts

typescript

```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterLink } from '@angular/router';

import { Product } from '../../models/product.interface';

@Component({
  selector: 'app-product-card',
  standalone: true,
  imports: [
    CommonModule,
    RouterLink
  ],
  template: `
    <div class="product-card" [class.inactive]="!product.isActive">
      <div class="product-image">
        <img 
          [src]="product.imageUrl || '/assets/images/no-image.png'" 
          [alt]="product.name"
          (error)="onImageError($event)"
        >
        <div class="product-status" [class]="product.isActive ? 'active' : 'inactive'">
          {{ product.isActive ? 'Ativo' : 'Inativo' }}
        </div>
      </div>

      <div class="product-content">
        <h3 class="product-name">{{ product.name }}</h3>
        <p class="product-description">{{ product.description | slice:0:100 }}...</p>
        
        <div class="product-details">
          <span class="product-category">{{ product.category }}</span>
          <span class="product-price">{{ product.price | currency:'BRL':'symbol':'1.2-2' }}</span>
        </div>

        <div class="product-stock">
          <span [class]="getStockClass()">
            Estoque: {{ product.stock }}
          </span>
        </div>
      </div>

      <div class="product-actions">
        <a 
          [routerLink]="['/products', product.id]" 
          class="btn btn-sm btn-outline"
        >
          Ver
        </a>
        <a 
          [routerLink]="['/products', product.id, 'edit']" 
          class="btn btn-sm btn-primary"
        >
          Editar
        </a>
        <button 
          (click)="onToggleStatus()" 
          class="btn btn-sm"
          [class]="product.isActive ? 'btn-warning' : 'btn-success'"
        >
          {{ product.isActive ? 'Desativar' : 'Ativar' }}
        </button>
        <button 
          (click)="onDelete()" 
          class="btn btn-sm btn-danger"
        >
          Excluir
        </button>
      </div>
    </div>
  `,
  styleUrl: './product-card.component.css'
})
export class ProductCardComponent {
  @Input() product!: Product;
  
  @Output() toggleStatus = new EventEmitter<Product>();
  @Output() delete = new EventEmitter<Product>();

  onImageError(event: Event) {
    const target = event.target as HTMLImageElement;
    target.src = '/assets/images/no-image.png';
  }

  getStockClass(): string {
    if (this.product.stock === 0) return 'stock-zero';
    if (this.product.stock < 10) return 'stock-low';
    return 'stock-ok';
  }

  onToggleStatus() {
    this.toggleStatus.emit(this.product);
  }

  onDelete() {
    this.delete.emit(this.product);
  }
}
```

## 10. Feature Usuarios - Rotas e Estrutura

### features/usuarios/usuario.routes.ts

typescript

```typescript
import { Routes } from '@angular/router';
import { usuarioResolver } from './resolvers/usuario.resolver';

export const usuarioRoutes: Routes = [
  {
    path: '',
    loadComponent: () => 
      import('./pages/usuario-list/usuario-list.component')
        .then(c => c.UsuarioListComponent),
    data: { title: 'Lista de Usuários' }
  },
  {
    path: 'create',
    loadComponent: () => 
      import('./pages/usuario-create/usuario-create.component')
        .then(c => c.UsuarioCreateComponent),
    data: { title: 'Criar Usuário' }
  },
  {
    path: ':id',
    loadComponent: () => 
      import('./pages/usuario-detail/usuario-detail.component')
        .then(c => c.UsuarioDetailComponent),
    resolve: { usuario: usuarioResolver },
    data: { title: 'Detalhes do Usuário' }
  },
  {
    path: ':id/edit',
    loadComponent: () => 
      import('./pages/usuario-edit/usuario-edit.component')
        .then(c => c.UsuarioEditComponent),
    resolve: { usuario: usuarioResolver },
    data: { title: 'Editar Usuário' }
  }
];
```

### features/usuarios/models/usuario.interface.ts

typescript

```typescript
export interface Usuario {
  id?: number;
  nome: string;
  email: string;
  telefone?: string;
  cargo: string;
  departamento: string;
  ativo: boolean;
  dataAdmissao?: Date;
  createdAt?: Date;
  updatedAt?: Date;
}

export interface UsuarioFilter {
  search?: string;
  cargo?: string;
  departamento?: string;
  ativo?: boolean;
  page?: number;
  limit?: number;
  sortBy?: string;
  sortOrder?: 'asc' | 'desc';
}

export interface CreateUsuarioRequest {
  nome: string;
  email: string;
  telefone?: string;
  cargo: string;
  departamento: string;
  dataAdmissao?: Date;
}

export interface UpdateUsuarioRequest extends Partial<CreateUsuarioRequest> {
  ativo?: boolean;
}
```

### features/usuarios/services/usuario.service.ts

typescript

```typescript
import { Injectable, inject } from '@angular/core';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { ApiService } from '../../../core/services/api.service';
import { ApiResponse, PaginatedResponse } from '../../../core/models/api-response.interface';
import { Usuario, UsuarioFilter, CreateUsuarioRequest, UpdateUsuarioRequest } from '../models/usuario.interface';

@Injectable({
  providedIn: 'root'
})
export class UsuarioService {
  private apiService = inject(ApiService);
  private readonly endpoint = 'usuarios';

  // GET - Listar usuários com filtros e paginação
  getUsuarios(filter?: UsuarioFilter): Observable<PaginatedResponse<Usuario>> {
    const params = this.buildFilterParams(filter);
    return this.apiService.get<ApiResponse<PaginatedResponse<Usuario>>>(
      this.endpoint, 
      { params }
    ).pipe(
      map(response => response.data)
    );
  }

  // GET - Buscar usuário por ID
  getUsuarioById(id: number): Observable<Usuario> {
    return this.apiService.get<ApiResponse<Usuario>>(`${this.endpoint}/${id}`)
      .pipe(
        map(response => response.data)
      );
  }

  // POST - Criar novo usuário
  createUsuario(usuario: CreateUsuarioRequest): Observable<Usuario> {
    return this.apiService.post<ApiResponse<Usuario>>(this.endpoint, usuario)
      .pipe(
        map(response => response.data)
      );
  }

  // PUT - Atualizar usuário completo
  updateUsuario(id: number, usuario: UpdateUsuarioRequest): Observable<Usuario> {
    return this.apiService.put<ApiResponse<Usuario>>(`${this.endpoint}/${id}`, usuario)
      .pipe(
        map(response => response.data)
      );
  }

  // DELETE - Remover usuário
  deleteUsuario(id: number): Observable<void> {
    return this.apiService.delete<ApiResponse<void>>(`${this.endpoint}/${id}`)
      .pipe(
        map(() => void 0)
      );
  }

  // Métodos auxiliares
  toggleUsuarioStatus(id: number): Observable<Usuario> {
    return this.apiService.patch<ApiResponse<Usuario>>(
      `${this.endpoint}/${id}/toggle-status`,
      {}
    ).pipe(
      map(response => response.data)
    );
  }

  getCargos(): Observable<string[]> {
    return this.apiService.get<ApiResponse<string[]>>(`${this.endpoint}/cargos`)
      .pipe(
        map(response => response.data)
      );
  }

  getDepartamentos(): Observable<string[]> {
    return this.apiService.get<ApiResponse<string[]>>(`${this.endpoint}/departamentos`)
      .pipe(
        map(response => response.data)
      );
  }

  private buildFilterParams(filter?: UsuarioFilter): any {
    if (!filter) return {};

    const params: any = {};
    
    if (filter.search) params.search = filter.search;
    if (filter.cargo) params.cargo = filter.cargo;
    if (filter.departamento) params.departamento = filter.departamento;
    if (filter.ativo !== undefined) params.ativo = filter.ativo.toString();
    if (filter.page) params.page = filter.page.toString();
    if (filter.limit) params.limit = filter.limit.toString();
    if (filter.sortBy) params.sortBy = filter.sortBy;
    if (filter.sortOrder) params.sortOrder = filter.sortOrder;

    return params;
  }
}
```

## 11. Core Models (API Response)

### core/models/api-response.interface.ts

typescript

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

export interface ErrorResponse {
  success: false;
  message: string;
  errors?: string[];
  statusCode: number;
}
```

## 12. Guards - Standalone

### core/guards/auth.guard.ts

typescript

```typescript
import { inject } from '@angular/core';
import { Router, CanActivateFn } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const authGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (authService.isAuthenticated()) {
    return true;
  }

  router.navigate(['/login']);
  return false;
};
```

### core/services/auth.service.ts

typescript

```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

interface User {
  id: number;
  name: string;
  email: string;
  role: string;
}

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private currentUserSubject = new BehaviorSubject<User | null>(null);
  public currentUser$ = this.currentUserSubject.asObservable();

  constructor() {
    this.loadUserFromStorage();
  }

  isAuthenticated(): boolean {
    return !!this.getToken();
  }

  getToken(): string | null {
    return localStorage.getItem('authToken');
  }

  getCurrentUser(): User | null {
    return this.currentUserSubject.value;
  }

  logout() {
    localStorage.removeItem('authToken');
    localStorage.removeItem('currentUser');
    this.currentUserSubject.next(null);
  }

  private loadUserFromStorage() {
    const userJson = localStorage.getItem('currentUser');
    if (userJson) {
      const user = JSON.parse(userJson);
      this.currentUserSubject.next(user);
    }
  }
}
```

## Resumo da Estrutura Standalone

### **Principais Diferenças:**

1. **Sem Módulos**: Eliminamos todos os `.module.ts`
2. **Lazy Loading**: `loadComponent` e `loadChildren` para rotas
3. **Imports Diretos**: Cada componente gerencia suas dependências
4. **Interceptors**: Função ao invés de classe
5. **Guards**: Função `CanActivateFn` ao invés de classe
6. **Resolvers**: Função `ResolveFn` ao invés de classe

### **Vantagens:**

- **Performance**: Tree-shaking automático
- **Simplicidade**: Menos boilerplate
- **Flexibilidade**: Componentes verdadeiramente independentes
- **Modernidade**: Padrão Angular 17+

### **Estrutura de URLs da API:**

```
GET    /api/v1/products                    # Lista com filtros
GET    /api/v1/products/:id               # Produto específico
POST   /api/v1/products                   # Criar produto
PUT    /api/v1/products/:id               # Atualizar produto
DELETE /api/v1/products/:id               # Excluir produto

GET    /api/v1/usuarios                   # Lista de usuários
GET    /api/v1/usuarios/:id               # Usuário específico
POST   /api/v1/usuarios                   # Criar usuário
PUT    /api/v1/usuarios/:id               # Atualizar usuário
DELETE /api/v1/usuarios/:id               # Excluir usuário
```

Esta estrutura standalone oferece máxima flexibilidade e performance, mantendo a organização e escalabilidade do projeto!