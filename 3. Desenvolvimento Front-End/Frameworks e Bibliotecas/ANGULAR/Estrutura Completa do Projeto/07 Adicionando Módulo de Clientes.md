# Exemplo Prático: Adicionando Módulo de Clientes

## 1. Passo a Passo Completo

### Comandos Executados em Sequência
```bash
# 1. Criar módulo de clientes com roteamento
ng generate module features/customers --routing

# 2. Criar páginas principais
ng generate component features/customers/pages/customers-list --module=features/customers
ng generate component features/customers/pages/customer-detail --module=features/customers  
ng generate component features/customers/pages/customer-create --module=features/customers
ng generate component features/customers/pages/customer-edit --module=features/customers

# 3. Criar componentes reutilizáveis
ng generate component features/customers/components/customer-card --module=features/customers
ng generate component features/customers/components/customer-form --module=features/customers

# 4. Criar serviços
ng generate service features/customers/services/customers-api
ng generate service features/customers/services/customers-facade

# 5. Criar modelos
ng generate interface features/customers/models/customer
ng generate interface features/customers/models/customer-filter

# 6. Criar resolver
ng generate resolver features/customers/resolvers/customer
```

## 2. Estrutura Criada
```
src/app/features/customers/
├── components/
│   ├── customer-card/
│   │   ├── customer-card.component.html
│   │   ├── customer-card.component.scss  
│   │   ├── customer-card.component.ts
│   │   └── customer-card.component.spec.ts
│   └── customer-form/
│       ├── customer-form.component.html
│       ├── customer-form.component.scss
│       ├── customer-form.component.ts
│       └── customer-form.component.spec.ts
├── models/
│   ├── customer.ts
│   └── customer-filter.ts
├── pages/
│   ├── customer-create/
│   ├── customer-detail/
│   ├── customer-edit/
│   └── customers-list/
├── resolvers/
│   ├── customer.resolver.ts
│   └── customer.resolver.spec.ts
├── services/
│   ├── customers-api.service.ts
│   ├── customers-api.service.spec.ts
│   ├── customers-facade.service.ts
│   └── customers-facade.service.spec.ts
├── customers-routing.module.ts
├── customers.module.ts
```

## 3. Implementação dos Arquivos Criados

### models/customer.ts
```typescript
export interface Customer {
  id?: number;
  name: string;
  email: string;
  phone: string;
  document: string; // CPF/CNPJ
  address: CustomerAddress;
  isActive: boolean;
  customerType: 'individual' | 'company';
  createdAt?: Date;
  updatedAt?: Date;
}

export interface CustomerAddress {
  street: string;
  number: string;
  complement?: string;
  neighborhood: string;
  city: string;
  state: string;
  zipCode: string;
  country: string;
}

export interface CreateCustomerRequest {
  name: string;
  email: string;
  phone: string;
  document: string;
  address: CustomerAddress;
  customerType: 'individual' | 'company';
}

export interface UpdateCustomerRequest extends Partial<CreateCustomerRequest> {
  isActive?: boolean;
}
```

### models/customer-filter.ts
```typescript
export interface CustomerFilter {
  search?: string;
  customerType?: 'individual' | 'company';
  isActive?: boolean;
  state?: string;
  city?: string;
  page?: number;
  limit?: number;
  sortBy?: 'name' | 'email' | 'createdAt';
  sortOrder?: 'asc' | 'desc';
}

export interface CustomersResponse {
  data: Customer[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}
```

### services/customers-api.service.ts
```typescript
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { BaseApiService } from '../../../core/services/base-api.service';
import { environment } from '../../../../environments/environment';
import { Customer, CreateCustomerRequest, UpdateCustomerRequest, CustomerFilter, CustomersResponse } from '../models/customer';

@Injectable({
  providedIn: 'root'
})
export class CustomersApiService extends BaseApiService {
  protected apiConfig = environment.apis.customers || environment.apis.users; // Fallback para users API

  protected getApiIdentifier(): string {
    return 'CUSTOMERS-API';
  }

  getCustomers(filter?: CustomerFilter): Observable<CustomersResponse> {
    const options = {
      params: this.buildFilterParams(filter),
      timeout: 30000
    };
    
    return this.get<CustomersResponse>('customers', options);
  }

  getCustomerById(id: number): Observable<Customer> {
    return this.get<{ data: Customer }>(`customers/${id}`)
      .pipe(map(response => response.data));
  }

  createCustomer(customer: CreateCustomerRequest): Observable<Customer> {
    return this.post<{ data: Customer }>('customers', customer)
      .pipe(map(response => response.data));
  }

  updateCustomer(id: number, customer: UpdateCustomerRequest): Observable<Customer> {
    return this.put<{ data: Customer }>(`customers/${id}`, customer)
      .pipe(map(response => response.data));
  }

  deleteCustomer(id: number): Observable<void> {
    return this.delete<void>(`customers/${id}`);
  }

  toggleCustomerStatus(id: number): Observable<Customer> {
    return this.patch<{ data: Customer }>(`customers/${id}/toggle-status`, {})
      .pipe(map(response => response.data));
  }

  searchCustomers(query: string): Observable<Customer[]> {
    const options = {
      params: { q: query, limit: 10 },
      timeout: 15000
    };
    
    return this.get<{ data: Customer[] }>('customers/search', options)
      .pipe(map(response => response.data));
  }

  getCustomerOrders(customerId: number): Observable<any[]> {
    return this.get<{ data: any[] }>(`customers/${customerId}/orders`)
      .pipe(map(response => response.data));
  }

  private buildFilterParams(filter?: CustomerFilter): any {
    if (!filter) return {};

    const params: any = {};
    
    if (filter.search) params.search = filter.search;
    if (filter.customerType) params.customerType = filter.customerType;
    if (filter.isActive !== undefined) params.isActive = filter.isActive.toString();
    if (filter.state) params.state = filter.state;
    if (filter.city) params.city = filter.city;
    if (filter.page) params.page = filter.page.toString();
    if (filter.limit) params.limit = filter.limit.toString();
    if (filter.sortBy) params.sortBy = filter.sortBy;
    if (filter.sortOrder) params.sortOrder = filter.sortOrder;

    return params;
  }
}
```

### services/customers-facade.service.ts
```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';
import { map, tap, shareReplay, finalize } from 'rxjs/operators';
import { CustomersApiService } from './customers-api.service';
import { Customer, CustomerFilter, CustomersResponse, CreateCustomerRequest, UpdateCustomerRequest } from '../models/customer';
import { LoadingService } from '../../../core/services/loading.service';
import { NotificationService } from '../../../core/services/notification.service';

export interface CustomersState {
  customers: Customer[];
  loading: boolean;
  error: string | null;
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
  filters: CustomerFilter;
}

@Injectable()
export class CustomersFacadeService {
  private stateSubject = new BehaviorSubject<CustomersState>({
    customers: [],
    loading: false,
    error: null,
    pagination: { page: 1, limit: 10, total: 0, totalPages: 0 },
    filters: {}
  });

  public state$ = this.stateSubject.asObservable();
  public customers$ = this.state$.pipe(map(state => state.customers));
  public loading$ = this.state$.pipe(map(state => state.loading));
  public error$ = this.state$.pipe(map(state => state.error));
  public pagination$ = this.state$.pipe(map(state => state.pagination));

  constructor(
    private customersApi: CustomersApiService,
    private loadingService: LoadingService,
    private notificationService: NotificationService
  ) {}

  get currentState(): CustomersState {
    return this.stateSubject.value;
  }

  loadCustomers(filters?: CustomerFilter): Observable<CustomersResponse> {
    this.updateState({ loading: true, error: null });

    return this.customersApi.getCustomers(filters).pipe(
      tap(response => {
        this.updateState({
          customers: response.data,
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

  getCustomer(id: number): Observable<Customer> {
    return this.customersApi.getCustomerById(id).pipe(
      shareReplay(1)
    );
  }

  createCustomer(customer: CreateCustomerRequest): Observable<Customer> {
    this.updateState({ loading: true, error: null });

    return this.customersApi.createCustomer(customer).pipe(
      tap(newCustomer => {
        const currentCustomers = [...this.currentState.customers, newCustomer];
        this.updateState({ 
          customers: currentCustomers,
          loading: false 
        });

        this.notificationService.success('Cliente criado com sucesso!');
      }),
      finalize(() => this.updateState({ loading: false }))
    );
  }

  updateCustomer(id: number, customer: UpdateCustomerRequest): Observable<Customer> {
    this.updateState({ loading: true, error: null });

    return this.customersApi.updateCustomer(id, customer).pipe(
      tap(updatedCustomer => {
        const currentCustomers = this.currentState.customers.map(c => 
          c.id === id ? updatedCustomer : c
        );
        
        this.updateState({ 
          customers: currentCustomers,
          loading: false 
        });

        this.notificationService.success('Cliente atualizado com sucesso!');
      }),
      finalize(() => this.updateState({ loading: false }))
    );
  }

  deleteCustomer(id: number): Observable<void> {
    this.updateState({ loading: true, error: null });

    return this.customersApi.deleteCustomer(id).pipe(
      tap(() => {
        const currentCustomers = this.currentState.customers.filter(c => c.id !== id);
        this.updateState({ 
          customers: currentCustomers,
          loading: false 
        });

        this.notificationService.success('Cliente excluído com sucesso!');
      }),
      finalize(() => this.updateState({ loading: false }))
    );
  }

  toggleCustomerStatus(id: number): Observable<Customer> {
    return this.customersApi.toggleCustomerStatus(id).pipe(
      tap(updatedCustomer => {
        const currentCustomers = this.currentState.customers.map(c => 
          c.id === id ? updatedCustomer : c
        );
        
        this.updateState({ customers: currentCustomers });
      })
    );
  }

  searchCustomers(query: string): Observable<Customer[]> {
    return this.customersApi.searchCustomers(query);
  }

  applyFilters(filters: CustomerFilter): void {
    this.updateState({ filters });
    this.loadCustomers(filters).subscribe();
  }

  clearFilters(): void {
    this.updateState({ filters: {} });
    this.loadCustomers().subscribe();
  }

  private updateState(partialState: Partial<CustomersState>): void {
    const currentState = this.currentState;
    const newState = { ...currentState, ...partialState };
    this.stateSubject.next(newState);
  }

  clearState(): void {
    this.stateSubject.next({
      customers: [],
      loading: false,
      error: null,
      pagination: { page: 1, limit: 10, total: 0, totalPages: 0 },
      filters: {}
    });
  }
}
```

### customers-routing.module.ts
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

// Guards
import { AuthGuard } from '../../core/guards/auth.guard';
import { PermissionGuard } from '../../core/guards/permission.guard';
import { CanDeactivateGuard } from '../../core/guards/can-deactivate.guard';

// Pages
import { CustomersListComponent } from './pages/customers-list/customers-list.component';
import { CustomerCreateComponent } from './pages/customer-create/customer-create.component';
import { CustomerEditComponent } from './pages/customer-edit/customer-edit.component';
import { CustomerDetailComponent } from './pages/customer-detail/customer-detail.component';

// Resolvers
import { CustomerResolver } from './resolvers/customer.resolver';

const routes: Routes = [
  {
    path: '',
    component: CustomersListComponent,
    canActivate: [AuthGuard, PermissionGuard],
    data: { 
      title: 'Clientes',
      breadcrumb: 'Lista',
      permissions: ['customers.read']
    }
  },
  {
    path: 'create',
    component: CustomerCreateComponent,
    canActivate: [AuthGuard, PermissionGuard],
    canDeactivate: [CanDeactivateGuard],
    data: { 
      title: 'Criar Cliente',
      breadcrumb: 'Criar',
      permissions: ['customers.create']
    }
  },
  {
    path: ':id',
    component: CustomerDetailComponent,
    canActivate: [AuthGuard, PermissionGuard],
    resolve: {
      customer: CustomerResolver
    },
    data: { 
      title: 'Detalhes do Cliente',
      breadcrumb: 'Detalhes',
      permissions: ['customers.read']
    }
  },
  {
    path: ':id/edit',
    component: CustomerEditComponent,
    canActivate: [AuthGuard, PermissionGuard],
    canDeactivate: [CanDeactivateGuard],
    resolve: {
      customer: CustomerResolver
    },
    data: { 
      title: 'Editar Cliente',
      breadcrumb: 'Editar',
      permissions: ['customers.update']
    }
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class CustomersRoutingModule { }
```

### customers.module.ts
```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule, FormsModule } from '@angular/forms';

// Routing
import { CustomersRoutingModule } from './customers-routing.module';

// Shared
import { SharedModule } from '../../shared/shared.module';

// Services
import { CustomersFacadeService } from './services/customers-facade.service';

// Pages
import { CustomersListComponent } from './pages/customers-list/customers-list.component';
import { CustomerCreateComponent } from './pages/customer-create/customer-create.component';
import { CustomerEditComponent } from './pages/customer-edit/customer-edit.component';
import { CustomerDetailComponent } from './pages/customer-detail/customer-detail.component';

// Components
import { CustomerFormComponent } from './components/customer-form/customer-form.component';
import { CustomerCardComponent } from './components/customer-card/customer-card.component';

// Resolvers
import { CustomerResolver } from './resolvers/customer.resolver';

@NgModule({
  declarations: [
    // Pages
    CustomersListComponent,
    CustomerCreateComponent,
    CustomerEditComponent,
    CustomerDetailComponent,
    
    // Components
    CustomerFormComponent,
    CustomerCardComponent
  ],
  imports: [
    CommonModule,
    ReactiveFormsModule,
    FormsModule,
    CustomersRoutingModule,
    SharedModule
  ],
  providers: [
    CustomersFacadeService,
    CustomerResolver
  ]
})
export class CustomersModule { }
```

### resolvers/customer.resolver.ts
```typescript
import { Injectable } from '@angular/core';
import { Router, Resolve, ActivatedRouteSnapshot } from '@angular/router';
import