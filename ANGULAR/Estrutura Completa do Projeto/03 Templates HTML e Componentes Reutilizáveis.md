
## 1. Template da Lista de Produtos
### features/products/pages/product-list/product-list.component.html
```html
<div class="product-list-container">
  <!-- Header da página -->
  <div class="page-header">
    <h1>Produtos</h1>
    <button 
      class="btn btn-primary"
      routerLink="/products/create"
      type="button">
      <i class="fas fa-plus"></i>
      Novo Produto
    </button>
  </div>

  <!-- Filtros -->
  <div class="filters-section">
    <div class="row">
      <!-- Busca -->
      <div class="col-md-4">
        <div class="form-group">
          <label for="search">Buscar</label>
          <input
            id="search"
            type="text"
            class="form-control"
            placeholder="Nome ou descrição..."
            [(ngModel)]="searchTerm"
            (input)="onSearchChange($event.target.value)">
        </div>
      </div>

      <!-- Categoria -->
      <div class="col-md-3">
        <div class="form-group">
          <label for="category">Categoria</label>
          <select
            id="category"
            class="form-control"
            [value]="filter.category || ''"
            (change)="onCategoryChange($event.target.value)">
            <option value="">Todas</option>
            <option *ngFor="let category of categories" [value]="category">
              {{ category }}
            </option>
          </select>
        </div>
      </div>

      <!-- Status -->
      <div class="col-md-3">
        <div class="form-group">
          <label for="status">Status</label>
          <select
            id="status"
            class="form-control"
            [value]="filter.isActive === true ? 'active' : filter.isActive === false ? 'inactive' : ''"
            (change)="onStatusChange($event.target.value)">
            <option value="">Todos</option>
            <option value="active">Ativo</option>
            <option value="inactive">Inativo</option>
          </select>
        </div>
      </div>

      <!-- Limpar filtros -->
      <div class="col-md-2">
        <div class="form-group">
          <label>&nbsp;</label>
          <button
            type="button"
            class="btn btn-outline-secondary btn-block"
            (click)="clearFilters()">
            Limpar
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- Loading -->
  <div *ngIf="loading" class="text-center py-4">
    <app-loading></app-loading>
  </div>

  <!-- Error -->
  <div *ngIf="error" class="alert alert-danger">
    {{ error }}
    <button type="button" class="btn btn-link" (click)="loadProducts()">
      Tentar novamente
    </button>
  </div>

  <!-- Lista de produtos -->
  <div *ngIf="!loading && !error">
    <!-- Header da tabela -->
    <div class="table-header">
      <span class="results-count">
        {{ totalItems }} produto(s) encontrado(s)
      </span>
      
      <!-- Ordenação -->
      <div class="sort-controls">
        <label>Ordenar por:</label>
        <select
          class="form-control form-control-sm ml-2"
          [value]="filter.sortBy"
          (change)="onSortChange($event.target.value)">
          <option value="name">Nome</option>
          <option value="price">Preço</option>
          <option value="category">Categoria</option>
          <option value="createdAt">Data de criação</option>
        </select>
        
        <button
          type="button"
          class="btn btn-sm btn-outline-secondary ml-1"
          (click)="onSortChange(filter.sortBy!)">
          <i [class]="'fas fa-sort-' + (filter.sortOrder === 'asc' ? 'up' : 'down')"></i>
        </button>
      </div>
    </div>

    <!-- Tabela -->
    <div class="table-responsive">
      <table class="table table-hover">
        <thead>
          <tr>
            <th>Produto</th>
            <th>Categoria</th>
            <th>Preço</th>
            <th>Estoque</th>
            <th>Status</th>
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr *ngFor="let product of products; trackBy: trackByProduct">
            <td>
              <div class="product-info">
                <img
                  *ngIf="product.imageUrl"
                  [src]="product.imageUrl"
                  [alt]="product.name"
                  class="product-thumbnail">
                <div>
                  <strong>{{ product.name }}</strong>
                  <br>
                  <small class="text-muted">{{ product.description | slice:0:50 }}...</small>
                </div>
              </div>
            </td>
            <td>{{ product.category }}</td>
            <td>{{ product.price | currency:'BRL':'symbol':'1.2-2' }}</td>
            <td>
              <span [class]="'badge badge-' + (product.stock > 10 ? 'success' : product.stock > 0 ? 'warning' : 'danger')">
                {{ product.stock }}
              </span>
            </td>
            <td>
              <button
                type="button"
                [class]="'btn btn-sm ' + (product.isActive ? 'btn-success' : 'btn-secondary')"
                (click)="toggleProductStatus(product)">
                {{ product.isActive ? 'Ativo' : 'Inativo'