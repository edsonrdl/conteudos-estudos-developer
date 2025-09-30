
## 📁 Estrutura de Pastas Recomendada

```
my-ecommerce-app/
├── src/
│   ├── app/
│   │   ├── core/                          # Singleton - Carregado UMA VEZ
│   │   │   ├── services/
│   │   │   │   ├── api.service.ts
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── logger.service.ts
│   │   │   │   └── storage.service.ts
│   │   │   ├── interceptors/
│   │   │   │   ├── auth.interceptor.ts
│   │   │   │   ├── error.interceptor.ts
│   │   │   │   └── loading.interceptor.ts
│   │   │   ├── guards/
│   │   │   │   ├── auth.guard.ts
│   │   │   │   └── role.guard.ts
│   │   │   ├── models/
│   │   │   │   ├── user.model.ts
│   │   │   │   └── api-response.model.ts
│   │   │   ├── constants/
│   │   │   │   └── app.constants.ts
│   │   │   └── core.module.ts
│   │   │
│   │   ├── shared/                        # Reutilizável - Importado MÚLTIPLAS VEZES
│   │   │   ├── components/
│   │   │   │   ├── button/
│   │   │   │   │   ├── button.component.ts
│   │   │   │   │   ├── button.component.html
│   │   │   │   │   ├── button.component.scss
│   │   │   │   │   └── button.component.spec.ts
│   │   │   │   ├── card/
│   │   │   │   ├── modal/
│   │   │   │   └── loading-spinner/
│   │   │   ├── directives/
│   │   │   │   ├── tooltip.directive.ts
│   │   │   │   └── highlight.directive.ts
│   │   │   ├── pipes/
│   │   │   │   ├── currency-br.pipe.ts
│   │   │   │   ├── truncate.pipe.ts
│   │   │   │   └── safe-html.pipe.ts
│   │   │   ├── validators/
│   │   │   │   └── cpf.validator.ts
│   │   │   └── shared.module.ts
│   │   │
│   │   ├── layout/                        # Layout da Aplicação
│   │   │   ├── components/
│   │   │   │   ├── header/
│   │   │   │   ├── footer/
│   │   │   │   ├── sidebar/
│   │   │   │   └── main-layout/
│   │   │   └── layout.module.ts
│   │   │
│   │   ├── features/                      # Funcionalidades - LAZY LOADING
│   │   │   ├── products/
│   │   │   │   ├── components/
│   │   │   │   │   ├── product-list/
│   │   │   │   │   ├── product-detail/
│   │   │   │   │   ├── product-card/
│   │   │   │   │   └── product-filter/
│   │   │   │   ├── services/
│   │   │   │   │   ├── product.service.ts
│   │   │   │   │   └── product-facade.service.ts
│   │   │   │   ├── models/
│   │   │   │   │   └── product.model.ts
│   │   │   │   ├── resolvers/
│   │   │   │   │   └── product.resolver.ts
│   │   │   │   ├── state/                 # NgRx (opcional)
│   │   │   │   │   ├── product.actions.ts
│   │   │   │   │   ├── product.reducer.ts
│   │   │   │   │   └── product.selectors.ts
│   │   │   │   ├── products-routing.module.ts
│   │   │   │   └── products.module.ts
│   │   │   │
│   │   │   ├── cart/
│   │   │   │   ├── components/
│   │   │   │   ├── services/
│   │   │   │   ├── cart-routing.module.ts
│   │   │   │   └── cart.module.ts
│   │   │   │
│   │   │   ├── user/
│   │   │   │   ├── components/
│   │   │   │   │   ├── profile/
│   │   │   │   │   ├── settings/
│   │   │   │   │   └── orders-history/
│   │   │   │   ├── services/
│   │   │   │   ├── user-routing.module.ts
│   │   │   │   └── user.module.ts
│   │   │   │
│   │   │   └── checkout/
│   │   │       ├── components/
│   │   │       ├── services/
│   │   │       ├── checkout-routing.module.ts
│   │   │       └── checkout.module.ts
│   │   │
│   │   ├── pages/                         # Páginas Simples - LAZY LOADING
│   │   │   ├── home/
│   │   │   │   ├── home.component.ts
│   │   │   │   ├── home.component.html
│   │   │   │   ├── home.component.scss
│   │   │   │   └── home.module.ts
│   │   │   ├── about/
│   │   │   ├── contact/
│   │   │   └── not-found/
│   │   │
│   │   ├── app-routing.module.ts
│   │   ├── app.component.ts
│   │   └── app.module.ts
│   │
│   ├── assets/
│   ├── environments/
│   └── main.ts
├── angular.json
└── package.json
```

---

## 🏗️ Camadas da Arquitetura

### 1. 🎯 CORE - Camada de Infraestrutura (Singleton)

**Propósito**: Serviços e funcionalidades que existem **uma única vez** na aplicação.

#### Características:

- ✅ **Importado apenas no AppModule**
- ✅ **Provê serviços globais** com `providedIn: 'root'`
- ✅ **Carregamento eager** (imediato)
- ❌ **Nunca importado em features ou shared**

#### Responsabilidades:

typescript

```typescript
// ✅ PERTENCE ao CORE
├── Autenticação e Autorização (AuthService)
├── Comunicação HTTP (ApiService, HttpClient)
├── Gerenciamento de Estado Global (Store, StateService)
├── Logging e Monitoramento (LoggerService)
├── Interceptors HTTP (token, errors, loading)
├── Guards de Rota (AuthGuard, RoleGuard)
├── Configurações Globais (AppConfig)
├── Tratamento de Erros Global (ErrorHandler)
└── Utilitários de Sistema (StorageService, CacheService)
```

#### Exemplo de Serviço Core:

typescript

```typescript
// core/services/auth.service.ts
@Injectable({ providedIn: 'root' })
export class AuthService {
  private currentUser$ = new BehaviorSubject<User | null>(null);
  
  login(credentials: Credentials): Observable<User> {
    return this.apiService.post('/auth/login', credentials)
      .pipe(
        tap(user => {
          this.storageService.set('token', user.token);
          this.currentUser$.next(user);
        })
      );
  }
  
  logout(): void {
    this.storageService.remove('token');
    this.currentUser$.next(null);
    this.router.navigate(['/login']);
  }
  
  getCurrentUser(): Observable<User | null> {
    return this.currentUser$.asObservable();
  }
}
```

#### ⚠️ Proteção contra Reimportação:

typescript

```typescript
// core/core.module.ts
@NgModule({
  providers: [
    // Guards, Interceptors, etc.
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

---

### 2. 🔄 SHARED - Camada de Componentes Reutilizáveis

**Propósito**: Componentes, pipes e diretivas **sem lógica de negócio** que são usados em **múltiplas features**.

#### Características:

- ✅ **Importado em múltiplos módulos**
- ✅ **Componentes "burros" (presentation components)**
- ✅ **Exporta tudo que será reutilizado**
- ❌ **Sem serviços** (exceto utilitários puros)
- ❌ **Sem dependências de features**

#### Responsabilidades:

typescript

```typescript
// ✅ PERTENCE ao SHARED
├── Componentes UI Reutilizáveis
│   ├── Botões, Cards, Modais
│   ├── Inputs customizados
│   ├── Tabelas, Paginação
│   └── Loading Spinners
├── Pipes de Formatação
│   ├── Datas, Moedas, Números
│   └── Texto (truncate, capitalize)
├── Diretivas de UI
│   ├── Tooltip, Dropdown
│   └── Drag and Drop
└── Validators Customizados
    ├── CPF, CNPJ, Email
    └── Validações de formulário
```

#### Exemplo de Componente Shared:

typescript

```typescript
// shared/components/button/button.component.ts
@Component({
  selector: 'app-button',
  template: `
    <button 
      [type]="type"
      [disabled]="disabled || loading"
      [class]="'btn btn-' + variant"
      (click)="handleClick()">
      <span *ngIf="loading" class="spinner"></span>
      <ng-content></ng-content>
    </button>
  `,
  styleUrls: ['./button.component.scss']
})
export class ButtonComponent {
  @Input() type: 'button' | 'submit' = 'button';
  @Input() variant: 'primary' | 'secondary' | 'danger' = 'primary';
  @Input() disabled = false;
  @Input() loading = false;
  @Output() clicked = new EventEmitter<void>();
  
  handleClick(): void {
    if (!this.disabled && !this.loading) {
      this.clicked.emit();
    }
  }
}
```

#### Características de Componentes Shared:

typescript

```typescript
// ✅ BOM - Componente "burro"
@Component({...})
export class CardComponent {
  @Input() title: string;
  @Input() image: string;
  @Output() cardClick = new EventEmitter<void>();
}

// ❌ RUIM - Componente com lógica de negócio
@Component({...})
export class ProductCardComponent {
  constructor(
    private productService: ProductService,  // ❌ Serviço de feature
    private cartService: CartService         // ❌ Lógica de negócio
  ) {}
  
  addToCart() {
    this.cartService.add(this.product);      // ❌ Não deveria estar aqui
  }
}
```

---

### 3. 🎨 LAYOUT - Camada de Estrutura Visual

**Propósito**: Componentes que definem a **estrutura e navegação** da aplicação.

#### Responsabilidades:

typescript

```typescript
// ✅ PERTENCE ao LAYOUT
├── Header/Navbar (com navegação)
├── Footer
├── Sidebar/Menu
├── Breadcrumbs
└── Main Layout (estrutura de páginas)
```

#### Exemplo:

typescript

```typescript
// layout/components/main-layout/main-layout.component.ts
@Component({
  selector: 'app-main-layout',
  template: `
    <app-header></app-header>
    <div class="container">
      <app-sidebar *ngIf="showSidebar"></app-sidebar>
      <main class="content">
        <router-outlet></router-outlet>
      </main>
    </div>
    <app-footer></app-footer>
  `
})
export class MainLayoutComponent {
  showSidebar$ = this.authService.isAuthenticated();
}
```

---

### 4. 🚀 FEATURES - Camada de Funcionalidades de Negócio

**Propósito**: Módulos que implementam **funcionalidades completas** da aplicação com **lógica de negócio**.

#### Características:

- ✅ **Lazy Loading** (carregado sob demanda)
- ✅ **Auto-contido** (tudo relacionado à feature fica junto)
- ✅ **Contém lógica de negócio**
- ✅ **Pode ter sub-módulos**
- ❌ **Não deve ser importado por outras features**

#### Responsabilidades:

typescript

```typescript
// ✅ PERTENCE a FEATURES
├── Componentes da Feature
│   ├── Smart Components (containers)
│   └── Presentation Components locais
├── Serviços da Feature
│   ├── Business Logic Services
│   └── Facade Services (opcional)
├── Modelos de Dados específicos
├── State Management (se usar NgRx)
├── Resolvers e Guards específicos
└── Rotas da Feature
```

#### Estrutura Interna de uma Feature:

typescript

```typescript
features/products/
├── components/
│   ├── product-list/          # Smart component
│   │   ├── product-list.component.ts
│   │   ├── product-list.component.html
│   │   └── product-list.component.scss
│   ├── product-detail/        # Smart component
│   └── product-card/          # Presentation (local da feature)
├── services/
│   ├── product.service.ts     # Lógica de negócio
│   └── product-facade.service.ts  # Camada de abstração
├── models/
│   └── product.model.ts       # Modelo específico
├── resolvers/
│   └── product.resolver.ts    # Pré-carregamento de dados
├── products-routing.module.ts
└── products.module.ts
```

#### Exemplo de Smart Component:

typescript

```typescript
// features/products/components/product-list/product-list.component.ts
@Component({
  selector: 'app-product-list',
  template: `
    <app-loading-spinner *ngIf="loading$ | async"></app-loading-spinner>
    
    <div class="products-grid">
      <app-card 
        *ngFor="let product of products$ | async"
        [title]="product.name"
        [image]="product.image"
        (cardClick)="viewProduct(product.id)">
        <p>{{ product.price | currency: 'BRL' }}</p>
        <app-button (clicked)="addToCart(product)">
          Adicionar
        </app-button>
      </app-card>
    </div>
  `
})
export class ProductListComponent implements OnInit {
  products$ = this.productService.getAll();
  loading$ = this.productService.loading$;
  
  constructor(
    private productService: ProductService,
    private cartService: CartService,
    private router: Router
  ) {}
  
  ngOnInit(): void {
    this.loadProducts();
  }
  
  loadProducts(): void {
    this.productService.loadAll().subscribe();
  }
  
  viewProduct(id: string): void {
    this.router.navigate(['/products', id]);
  }
  
  addToCart(product: Product): void {
    this.cartService.add(product);
  }
}
```

---

### 5. 📄 PAGES - Camada de Páginas Simples

**Propósito**: Páginas **sem lógica complexa**, geralmente estáticas ou com pouca interatividade.

#### Quando usar PAGES:

typescript

```typescript
// ✅ PERTENCE a PAGES (páginas simples)
├── Home (landing page)
├── About (sobre nós)
├── Contact (contato)
├── Terms of Service
├── Privacy Policy
└── 404 Not Found
```

#### Quando usar FEATURES:

typescript

```typescript
// ✅ PERTENCE a FEATURES (funcionalidades complexas)
├── Products (listagem, filtros, detalhes)
├── Cart (carrinho, cálculos)
├── Checkout (múltiplas etapas)
├── User Profile (edição, preferências)
└── Dashboard (gráficos, relatórios)
```

---

## 📊 Comparação Direta das Camadas

|Aspecto|CORE|SHARED|LAYOUT|FEATURES|PAGES|
|---|---|---|---|---|---|
|**Importado**|1x (AppModule)|Nx (vários módulos)|1x ou poucos|Lazy|Lazy|
|**Serviços**|✅ Globais|❌ Não|✅ Layout|✅ Feature-specific|❌ Poucos|
|**Lógica de Negócio**|✅ Infraestrutura|❌ Não|❌ Não|✅ Sim|❌ Mínima|
|**Reutilização**|N/A|✅ Alta|N/A|❌ Isolado|N/A|
|**Dependências**|Nenhuma|Nenhuma|Core|Core + Shared|Core + Shared|
|**Estado**|✅ Global|❌ Stateless|✅ UI State|✅ Feature State|❌ Pouco|

---

## 🎯 Regras de Ouro

### ✅ Fazer:

1. **Core**: Importar apenas no AppModule
2. **Shared**: Exportar tudo que for reutilizado
3. **Features**: Isolar lógica de negócio
4. **Lazy Loading**: Usar em features e pages
5. **Smart/Dumb Pattern**: Separar lógica de apresentação

### ❌ Não Fazer:

1. ❌ Importar Core em features
2. ❌ Colocar serviços no Shared
3. ❌ Criar dependências entre features
4. ❌ Colocar lógica de negócio no Shared
5. ❌ Misturar responsabilidades

---

## 🔄 Fluxo de Dados Recomendado

```
User Interaction
      ↓
Smart Component (Feature)
      ↓
Service (Feature ou Core)
      ↓
API Service (Core)
      ↓
HTTP Interceptor (Core)
      ↓
Backend
```

---

## 📦 Exemplo de Imports

typescript

```typescript
// app.module.ts
@NgModule({
  imports: [
    BrowserModule,
    CoreModule,          // ✅ Uma vez
    LayoutModule,        // ✅ Layout global
    AppRoutingModule
  ]
})
export class AppModule { }

// products.module.ts (Feature)
@NgModule({
  imports: [
    CommonModule,
    SharedModule,        // ✅ Importa shared
    ProductsRoutingModule
  ],
  declarations: [
    ProductListComponent,
    ProductDetailComponent
  ]
})
export class ProductsModule { }
```

---

## 🎓 Conclusão

Esta arquitetura promove:

- ✅ **Separação de Responsabilidades** (SRP)
- ✅ **Reutilização de Código** (DRY)
- ✅ **Manutenibilidade**
- ✅ **Testabilidade**
- ✅ **Escalabilidade**
- ✅ **Performance** (Lazy Loading)