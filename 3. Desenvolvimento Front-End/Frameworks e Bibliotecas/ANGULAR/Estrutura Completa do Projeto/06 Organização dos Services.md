

### 1. **Core Services** (Globais/Singleton)

typescript

```typescript
// src/app/core/services/
├── api.service.ts        // Service base para HTTP
├── auth.service.ts       // Autenticação global
├── notification.service.ts // Notificações globais
└── storage.service.ts    // LocalStorage/SessionStorage
```

### 2. **Feature Services** (Por módulo/feature)

typescript

```typescript
// src/app/features/usuarios/services/
├── usuarios.service.ts   // CRUD completo de usuários
└── usuarios-api.service.ts // Opcional: separar lógica de API

// src/app/features/tarefas/services/
├── tarefas.service.ts    // CRUD completo de tarefas
└── tarefas-api.service.ts
```

## Exemplo de implementação:

### **usuarios.service.ts**

typescript

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { environment } from '../../../environments/environment';

export interface Usuario {
  id?: number;
  nome: string;
  email: string;
  ativo: boolean;
}

@Injectable({
  providedIn: 'root'
})
export class UsuariosService {
  private apiUrl = `${environment.apis.users.baseUrl}/${environment.apis.users.version}/usuarios`;

  constructor(private http: HttpClient) {}

  // READ - Listar todos
  getAll(): Observable<Usuario[]> {
    return this.http.get<Usuario[]>(this.apiUrl);
  }

  // READ - Buscar por ID
  getById(id: number): Observable<Usuario> {
    return this.http.get<Usuario>(`${this.apiUrl}/${id}`);
  }

  // CREATE - Criar novo
  create(usuario: Usuario): Observable<Usuario> {
    return this.http.post<Usuario>(this.apiUrl, usuario);
  }

  // UPDATE - Atualizar
  update(id: number, usuario: Usuario): Observable<Usuario> {
    return this.http.put<Usuario>(`${this.apiUrl}/${id}`, usuario);
  }

  // DELETE - Deletar
  delete(id: number): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

### **usuarios.component.ts** (usando o service)

typescript

```typescript
import { Component, OnInit } from '@angular/core';
import { UsuariosService, Usuario } from './services/usuarios.service';

@Component({
  selector: 'app-usuarios',
  templateUrl: './usuarios.component.html'
})
export class UsuariosComponent implements OnInit {
  usuarios: Usuario[] = [];
  usuarioSelecionado: Usuario | null = null;
  modoEdicao = false;

  constructor(private usuariosService: UsuariosService) {}

  ngOnInit() {
    this.carregarUsuarios();
  }

  carregarUsuarios() {
    this.usuariosService.getAll().subscribe({
      next: (usuarios) => this.usuarios = usuarios,
      error: (error) => console.error('Erro ao carregar usuários:', error)
    });
  }

  salvarUsuario(usuario: Usuario) {
    if (usuario.id) {
      // UPDATE
      this.usuariosService.update(usuario.id, usuario).subscribe({
        next: () => this.carregarUsuarios(),
        error: (error) => console.error('Erro ao atualizar:', error)
      });
    } else {
      // CREATE
      this.usuariosService.create(usuario).subscribe({
        next: () => this.carregarUsuarios(),
        error: (error) => console.error('Erro ao criar:', error)
      });
    }
  }

  deletarUsuario(id: number) {
    this.usuariosService.delete(id).subscribe({
      next: () => this.carregarUsuarios(),
      error: (error) => console.error('Erro ao deletar:', error)
    });
  }
}
```

## Sua estrutura ficaria assim:

```
features/
├── usuarios/
│   ├── components/
│   ├── services/
│   │   └── usuarios.service.ts
│   ├── usuarios.component.ts
│   ├── usuarios.component.html
│   └── usuarios.module.ts
└── tarefas/
    ├── components/
    ├── services/
    │   └── tarefas.service.ts
    ├── tarefas.component.ts
    ├── tarefas.component.html
    └── tarefas.module.ts
```

**Resumo:** Um service por feature/módulo, não por página. Cada service contém todo o CRUD da sua entidade!