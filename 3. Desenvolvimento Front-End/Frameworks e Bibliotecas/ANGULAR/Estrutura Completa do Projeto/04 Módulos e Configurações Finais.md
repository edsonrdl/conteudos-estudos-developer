featu

## 1. Products Module
### features/products/products.module.ts
```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule, FormsModule } from '@angular/forms';

// Routing
import { ProductsRoutingModule } from './products-routing.module';

// Shared Module
import { SharedModule } from '../../shared/shared.module';

// Pages
import { ProductListComponent } from './pages/product-list/product-list.component';
import { ProductCreateComponent } from './pages/product-create/product-create.component';
import { ProductEditComponent } from './pages/product-edit/product-edit.component';
import { ProductDetailComponent } from './pages/product-detail/product-detail.component';

// Components
import { ProductFormComponent } from './components/product-form/product-form.component';
import { ProductCardComponent } from './components/product-card/product-card.component';

@NgModule({
  declarations: [
    // Pages
    ProductListComponent,
    ProductCreateComponent,
    ProductEditComponent,
    ProductDetailComponent,
    
    // Components
    ProductFormComponent,
    ProductCardComponent
  ],
  imports: [
    CommonModule,
    ReactiveFormsModule,
    FormsModule,
    ProductsRoutingModule,
    SharedModule
  ]
})
export class ProductsModule { }
```

## 2. Shared Module
### shared/shared.module.ts
```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router';
import { ReactiveFormsModule, FormsModule } from '@angular/forms';

// Components
import { LoadingComponent } from './components/loading/loading.component';
import { PaginationComponent } from './components/pagination/pagination.component';
import { ModalComponent } from './components/modal/modal.component';
import { ConfirmationDialogComponent } from './components/confirmation-dialog/confirmation-dialog.component';

// Pipes
import { CurrencyCustomPipe } from './pipes/currency-custom.pipe';
import { TruncatePipe } from './pipes/truncate.pipe';

// Directives
import { ClickOutsideDirective } from './directives/click-outside.directive';

@NgModule({
  declarations: [
    // Components
    LoadingComponent,
    PaginationComponent,
    ModalComponent,
    ConfirmationDialogComponent,
    
    // Pipes
    CurrencyCustomPipe,
    TruncatePipe,
    
    // Directives
    ClickOutsideDirective
  ],
  imports: [
    CommonModule,
    RouterModule,
    ReactiveFormsModule,
    FormsModule
  ],
  exports: [
    // Angular modules
    CommonModule,
    ReactiveFormsModule,
    FormsModule,
    RouterModule,
    
    // Components
    LoadingComponent,
    PaginationComponent,
    ModalComponent,
    ConfirmationDialogComponent,
    
    // Pipes
    CurrencyCustomPipe,
    TruncatePipe,
    
    // Directives
    ClickOutsideDirective
  ]
})
export class SharedModule { }
```

## 3. Core Module
### core/core.module.ts
```typescript
import { NgModule, Optional, SkipSelf } from '@angular/core';
import { CommonModule } from '@angular/common';
import { HTTP_INTERCEPTORS } from '@angular/common/http';

// Services
import { ApiService } from './services/api.service';
import { AuthService } from './services/auth.service';

// Guards
import { AuthGuard } from './guards/auth.guard';

// Interceptors
import { AuthInterceptor } from './interceptors/auth.interceptor';
import { ErrorInterceptor } from './interceptors/error.interceptor';
import { LoadingInterceptor } from './interceptors/loading.interceptor';

@NgModule({
  imports: [
    CommonModule
  ],
  providers: [
    // Services
    ApiService,
    AuthService,
    
    // Guards
    AuthGuard,
    
    // Interceptors
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
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
  // Evita que o CoreModule seja importado mais de uma vez
  constructor(@Optional() @SkipSelf() parentModule: CoreModule) {
    if (parentModule) {
      throw new Error('CoreModule já foi carregado. Importe apenas no AppModule.');
    }
  }
}
```

## 4. Error Interceptor
### core/interceptors/error.interceptor.ts
```typescript
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';
import { Router } from '@angular/router';

@Injectable()
export class ErrorInterceptor implements HttpInterceptor {
  constructor(private router: Router) {}

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<any> {
    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        if (error.status === 401) {
          // Redirecionar para login se não autorizado
          this.router.navigate(['/login']);
        } else if (error.status === 403) {
          // Redirecionar para página de acesso negado
          this.router.navigate(['/access-denied']);
        }
        
        return throwError(() => error);
      })
    );
  }
}
```

## 5. Loading Interceptor
### core/interceptors/loading.interceptor.ts
```typescript
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler } from '@angular/common/http';
import { Observable } from 'rxjs';
import { finalize } from 'rxjs/operators';
import { LoadingService } from '../services/loading.service';

@Injectable()
export class LoadingInterceptor implements HttpInterceptor {
  private activeRequests = 0;

  constructor(private loadingService: LoadingService) {}

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<any> {
    // Não mostrar loading para certas URLs (como upload de arquivos)
    if (req.url.includes('/upload') || req.headers.has('X-Skip-Loading')) {
      return next.handle(req);
    }

    this.activeRequests++;
    if (this.activeRequests === 1) {
      this.loadingService.setLoading(true);
    }

    return next.handle(req).pipe(
      finalize(() => {
        this.activeRequests--;
        if (this.activeRequests === 0) {
          this.loadingService.setLoading(false);
        }
      })
    );
  }
}
```

## 6. Loading Service
### core/services/loading.service.ts
```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class LoadingService {
  private loadingSubject = new BehaviorSubject<boolean>(false);
  public loading$: Observable<boolean> = this.loadingSubject.asObservable();

  setLoading(loading: boolean): void {
    this.loadingSubject.next(loading);
  }

  get isLoading(): boolean {
    return this.loadingSubject.value;
  }
}
```

## 7. Auth Service
### core/services/auth.service.ts
```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';
import { map, tap } from 'rxjs/operators';
import { ApiService } from './api.service';

export interface User {
  id: number;
  name: string;
  email: string;
  role: string;
}

export interface LoginRequest {
  email: string;
  password: string;
}

export interface AuthResponse {
  user: User;
  token: string;
  expiresIn: number;
}

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private currentUserSubject = new BehaviorSubject<User | null>(null);
  public currentUser$: Observable<User | null> = this.currentUserSubject.asObservable();

  constructor(private apiService: ApiService) {
    this.loadStoredUser();
  }

  login(credentials: LoginRequest): Observable<AuthResponse> {
    return this.apiService.post<AuthResponse>('auth/login', credentials)
      .pipe(
        tap(response => {
          this.storeAuthData(response);
          this.currentUserSubject.next(response.user);
        })
      );
  }

  logout(): void {
    localStorage.removeItem('authToken');
    localStorage.removeItem('currentUser');
    this.currentUserSubject.next(null);
  }

  getToken(): string | null {
    return localStorage.getItem('authToken');
  }

  isAuthenticated(): boolean {
    return !!this.getToken();
  }

  getCurrentUser(): User | null {
    return this.currentUserSubject.value;
  }

  private loadStoredUser(): void {
    const storedUser = localStorage.getItem('currentUser');
    if (storedUser) {
      try {
        const user = JSON.parse(storedUser);
        this.currentUserSubject.next(user);
      } catch (error) {
        console.error('Erro ao carregar usuário:', error);
        this.logout();
      }
    }
  }

  private storeAuthData(authResponse: AuthResponse): void {
    localStorage.setItem('authToken', authResponse.token);
    localStorage.setItem('currentUser', JSON.stringify(authResponse.user));
  }
}
```

## 8. Auth Guard
### core/guards/auth.guard.ts
```typescript
import { Injectable } from '@angular/core';
import { CanActivate, Router, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
import { AuthService } from '../services/auth.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(
    private authService: AuthService,
    private router: Router
  ) {}

  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean {
    if (this.authService.isAuthenticated()) {
      return true;
    }

    // Redirecionar para login, preservando a URL de destino
    this.router.navigate(['/login'], { 
      queryParams: { returnUrl: state.url } 
    });
    return false;
  }
}
```

## 9. App Module
### app.module.ts
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';

// Routing
import { AppRoutingModule } from './app-routing.module';

// Core Module (importado apenas uma vez)
import { CoreModule } from './core/core.module';

// Layout Components
import { AppComponent } from './app.component';
import { MainLayoutComponent } from './layout/main-layout/main-layout.component';
import { HeaderComponent } from './layout/header/header.component';
import { SidebarComponent } from './layout/sidebar/sidebar.component';

@NgModule({
  declarations: [
    AppComponent,
    MainLayoutComponent,
    HeaderComponent,
    SidebarComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule,
    AppRoutingModule,
    CoreModule // Importado apenas no AppModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## 10. Pipes Customizados
### shared/pipes/currency-custom.pipe.ts
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'currencyCustom'
})
export class CurrencyCustomPipe implements PipeTransform {
  transform(value: number, showSymbol: boolean = true, decimals: number = 2): string {
    if (value == null || isNaN(value)) {
      return '';
    }

    const formatted = value.toLocaleString('pt-BR', {
      minimumFractionDigits: decimals,
      maximumFractionDigits: decimals
    });

    return showSymbol ? `R$ ${formatted}` : formatted;
  }
}
```

### shared/pipes/truncate.pipe.ts
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'truncate'
})
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 50, trail: string = '...'): string {
    if (!value) return '';
    
    return value.length > limit ? value.substring(0, limit) + trail : value;
  }
}
```

## 11. Diretiva Click Outside
### shared/directives/click-outside.directive.ts
```typescript
import { Directive, ElementRef, EventEmitter, HostListener, Output } from '@angular/core';

@Directive({
  selector: '[appClickOutside]'
})
export class ClickOutsideDirective {
  @Output() clickOutside = new EventEmitter<void>();

  constructor(private elementRef: ElementRef) {}

  @HostListener('document:click', ['$event.target'])
  public onClick(target: any): void {
    const clickedInside = this.elementRef.nativeElement.contains(target);
    if (!clickedInside) {
      this.clickOutside.emit();
    }
  }
}
```

## 12. Modal Component
### shared/components/modal/modal.component.ts
```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-modal',
  templateUrl: './modal.component.html',
  styleUrls: ['./modal.component.scss']
})
export class ModalComponent {
  @Input() isOpen = false;
  @Input() title = '';
  @Input() size: 'sm' | 'md' | 'lg' | 'xl' = 'md';
  @Input() closable = true;
  
  @Output() close = new EventEmitter<void>();
  @Output() confirm = new EventEmitter<void>();

  onClose(): void {
    if (this.closable) {
      this.close.emit();
    }
  }

  onConfirm(): void {
    this.confirm.emit();
  }

  onBackdropClick(event: Event): void {
    if (event.target === event.currentTarget && this.closable) {
      this.onClose();
    }
  }
}
```

### shared/components/modal/modal.component.html
```html
<div class="modal-overlay" *ngIf="isOpen" (click)="onBackdropClick($event)">
  <div class="modal-container" [class]="'modal-' + size">
    <div class="modal-header" *ngIf="title || closable">
      <h5 class="modal-title" *ngIf="title">{{ title }}</h5>
      <button
        *ngIf="closable"
        type="button"
        class="btn-close"
        (click)="onClose()">
        <i class="fas fa-times"></i>
      </button>
    </div>
    
    <div class="modal-body">
      <ng-content></ng-content>
    </div>
    
    <div class="modal-footer" *ngIf="closable">
      <ng-content select="[slot=footer]"></ng-content>
    </div>
  </div>
</div>
```

## 13. Confirmation Dialog
### shared/components/confirmation-dialog/confirmation-dialog.component.ts
```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-confirmation-dialog',
  templateUrl: './confirmation-dialog.component.html',
  styleUrls: ['./confirmation-dialog.component.scss']
})
export class ConfirmationDialogComponent {
  @Input() isOpen = false;
  @Input() title = 'Confirmar ação';
  @Input() message = 'Tem certeza que deseja continuar?';
  @Input() confirmText = 'Confirmar';
  @Input() cancelText = 'Cancelar';
  @Input() type: 'danger' | 'warning' | 'info' = 'warning';
  
  @Output() confirm = new EventEmitter<void>();
  @Output() cancel = new EventEmitter<void>();

  onConfirm(): void {
    this.confirm.emit();
  }

  onCancel(): void {
    this.cancel.emit();
  }
}
```

## 14. Exemplo de Uso no Template
### Exemplo de uso do Modal e Confirmation Dialog
```html
<!-- No component -->
<app-confirmation-dialog
  [isOpen]="showDeleteDialog"
  title="Excluir Produto"
  message="Tem certeza que deseja excluir este produto? Esta ação não pode ser desfeita."
  confirmText="Excluir"
  cancelText="Cancelar"
  type="danger"
  (confirm)="confirmDelete()"
  (cancel)="showDeleteDialog = false">
</app-confirmation-dialog>

<!-- Usando Modal customizado -->
<app-modal
  [isOpen]="showProductModal"
  title="Detalhes do Produto"
  size="lg"
  (close)="showProductModal = false">
  
  <!-- Conteúdo do modal -->
  <div>
    <p>Conteúdo do produto aqui...</p>
  </div>
  
  <!-- Footer customizado -->
  <div slot="footer">
    <button type="button" class="btn btn-secondary" (click)="showProductModal = false">
      Fechar
    </button>
    <button type="button" class="btn btn-primary" (click)="saveProduct()">
      Salvar
    </button>
  </div>
</app-modal>
```

## 15. Configuração de Ambiente para Produção
### environment.prod.ts
```typescript
export const environment = {
  production: true,
  apiUrl: 'https://api.meusite.com/api',
  apiVersion: 'v1'
};
```

## 16. Exemplo de trackBy para Performance
### Função trackBy no component
```typescript
// No ProductListComponent
trackByProduct(index: number, product: Product): any {
  return product.id || index;
}
```

Esse exemplo completo inclui:

✅ **Arquitetura modular** seguindo as melhores práticas do Angular
✅ **CRUD completo** com todas as operações HTTP
✅ **Roteamento com lazy loading** para otimização
✅ **Interceptors** para autenticação, erro e loading
✅ **Componentes reutilizáveis** (loading, paginação, modal)
✅ **Resolvers** para pre-loading de dados
✅ **Guards** para proteção de rotas  
✅ **Pipes customizados** para formatação
✅ **Formulários reativos** com validação
✅ **Paginação e filtros** avançados
✅ **Tratamento de erros** centralizado
✅ **TypeScript interfaces** para tipagem forte