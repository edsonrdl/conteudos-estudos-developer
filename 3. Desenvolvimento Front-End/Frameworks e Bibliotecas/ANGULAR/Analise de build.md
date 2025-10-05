# 📚 Guia Completo: Como Analisar e Otimizar Bundle Angular

## 🎯 Objetivo

Identificar o que está deixando sua aplicação Angular pesada e como otimizar o bundle para melhorar a performance.

---

## 📋 PARTE 1: Instalação e Preparação

### Passo 1: Instalar as Ferramentas de Análise

powershell

```powershell
# Instalar webpack-bundle-analyzer (análise visual)
npm install --save-dev webpack-bundle-analyzer

# Instalar source-map-explorer (alternativa mais simples)
npm install --save-dev source-map-explorer
```

### Passo 2: Adicionar Scripts no package.json

Abra o arquivo `package.json` e adicione esses scripts:

json

```json
{
  "scripts": {
    "build": "ng build",
    "build:prod": "ng build --configuration production",
    "build:stats": "ng build --stats-json",
    "analyze": "ng build --stats-json && webpack-bundle-analyzer dist/NOME-DO-SEU-PROJETO/stats.json",
    "analyze:source": "ng build --source-map && source-map-explorer dist/NOME-DO-SEU-PROJETO/browser/main*.js",
    "analyze:all": "ng build --source-map && source-map-explorer dist/NOME-DO-SEU-PROJETO/browser/*.js"
  }
}
```

⚠️ **Importante**: Substitua `NOME-DO-SEU-PROJETO` pelo nome real do seu projeto (ex: `generic-interface`)

---

## 🔍 PARTE 2: Gerando o Build com Estatísticas

### Passo 3: Build com Stats JSON

powershell

```powershell
# Gerar build com arquivo de estatísticas
ng build --stats-json
```

**O que isso faz?**

- Compila sua aplicação
- Gera um arquivo `stats.json` com informações detalhadas sobre o bundle
- Mostra um resumo no terminal

### Passo 4: Entender o Output do Terminal

Após o build, você verá algo assim:

```
Initial chunk files   | Names         | Raw size | Transfer size
chunk-ARULHAH5.js     | -             | 172.64 KB | 50.50 kB  ← 🔴 GRANDE
main-ITFL6AMO.js      | main          | 111.36 KB | 12.87 kB  ← 🔴 GRANDE
chunk-DIAJC3RH.js     | -             | 89.98 KB  | 22.66 kB  ← 🟡 MÉDIO
styles-DFNBWTVF.css   | styles        | 70.21 KB  | 12.14 kB  ← 🟡 MÉDIO
polyfills-B6TNHZQ6.js | polyfills     | 34.58 KB  | 11.32 kB  ← 🟢 OK

                      | Initial total | 564.97 kB | 134.46 kB

Lazy chunk files      | Names              | Raw size
chunk-IQX5MRWQ.js     | to-do-component    | 657.32 kB  ← OK (lazy)
```

#### 📊 Como Interpretar:

**Initial chunks** = Carregam IMEDIATAMENTE (impacta tempo de carregamento)

- 🔴 > 100 KB = PROBLEMA - precisa otimizar
- 🟡 50-100 KB = ATENÇÃO - pode melhorar
- 🟢 < 50 KB = BOM

**Lazy chunks** = Carregam SOB DEMANDA (não é problema crítico)

- Podem ser grandes sem afetar carregamento inicial

**Transfer size** = Tamanho real transferido (com compressão gzip)

- Esse é o tamanho que o usuário realmente baixa

---

## 🗺️ PARTE 3: Localizando o stats.json

### Passo 5: Encontrar o Arquivo stats.json

**Opção A - PowerShell (Windows):**

powershell

```powershell
# Procurar o arquivo stats.json
Get-ChildItem -Path .\dist -Recurse -Filter "stats.json"
```

**Opção B - Listar toda estrutura:**

powershell

```powershell
# Ver toda estrutura da pasta dist
ls dist -Recurse
```

**Opção C - CMD (Windows):**

cmd

```cmd
dir dist /s /b | findstr stats.json
```

**Opção D - Linux/Mac:**

bash

```bash
find ./dist -name "stats.json"
```

#### Resultado Esperado:

```
Diretório: C:\...\dist\generic-interface

Mode    LastWriteTime    Length  Name
----    -------------    ------  ----
-a----  02/10/2025       670071  stats.json  ← AQUI ESTÁ!
```

O caminho completo será algo como:

```
dist/generic-interface/stats.json
```

---

## 📊 PARTE 4: Análise Visual Detalhada

### Passo 6: Executar o webpack-bundle-analyzer

**Método 1 - Usando npm script (RECOMENDADO):**

powershell

```powershell
npm run analyze
```

**Método 2 - Comando direto:**

powershell

```powershell
# Use o caminho que você encontrou no Passo 5
npx webpack-bundle-analyzer dist/generic-interface/stats.json
```

**O que acontece:**

1. Abre automaticamente no navegador ([http://127.0.0.1:8888](http://127.0.0.1:8888))
2. Mostra um **mapa de calor visual** interativo
3. Cada bloco representa um arquivo/módulo
4. Tamanho do bloco = tamanho do arquivo

### Passo 7: Interpretar o Mapa Visual

#### 🎨 Como Ler o Gráfico:

```
┌─────────────────────────────────────────────┐
│  📦 main.js                                 │
│  ┌──────────────────────┐  ┌──────────┐   │
│  │   PrimeNG            │  │ RxJS     │   │
│  │   (150 KB) 🔴        │  │ (45 KB)  │   │
│  └──────────────────────┘  └──────────┘   │
│  ┌─────────┐  ┌─────────────────────────┐ │
│  │ @angular│  │  Seu código             │ │
│  │ (89 KB) │  │  (25 KB) 🟢             │ │
│  └─────────┘  └─────────────────────────┘ │
└─────────────────────────────────────────────┘
```

**Cores:**

- 🔴 Vermelho/Laranja = Módulos grandes (FOCAR AQUI)
- 🟡 Amarelo = Médio
- 🟢 Verde/Azul = Pequenos (OK)

#### 🔍 Interações:

- **Clique** em um bloco → mostra detalhes
- **Scroll** → zoom in/out
- **Hover** → mostra tamanho exato
- **Pesquisar** → use Ctrl+F no navegador

---

## 🎯 PARTE 5: Identificando os Culpados

### Passo 8: Checklist de Análise

Procure por estes padrões no mapa visual:

#### ✅ **O que procurar:**

1. **Bibliotecas grandes demais**

```
   🔴 primeng → 200+ KB
   🔴 chart.js → 150+ KB
   🔴 moment.js → 70+ KB (use date-fns)
```

2. **Módulos duplicados**

```
   ⚠️ lodash aparece 2x
   ⚠️ rxjs aparece em múltiplos chunks
```

3. **Imports desnecessários**

typescript

```typescript
   // ❌ RUIM - importa biblioteca inteira
   import * as _ from 'lodash';
   
   // ✅ BOM - importa só o necessário
   import { debounce } from 'lodash-es';
```

4. **CSS/Fonts grandes**

```
   🔴 primeicons.woff → 85 KB
   🔴 theme.css → 70 KB
```

### Passo 9: Análise Alternativa com Source Maps

Se o webpack-bundle-analyzer não funcionar, use:

powershell

```powershell
npm run analyze:source
```

Isso abre uma visualização similar, mas baseada em source maps.

---

## 🛠️ PARTE 6: Análise Manual (Backup)

### Passo 10: Análise Rápida com PowerShell

Crie um arquivo `analyze-manual.ps1`:

powershell

```powershell
# Ver tamanho dos arquivos JS ordenados
Write-Host "`n📦 Análise de Bundle - Initial Chunks`n" -ForegroundColor Cyan

Get-ChildItem -Path ".\dist\*\browser" -Recurse -Filter "*.js" | 
  Where-Object { $_.Name -notlike "chunk-*lazy*" } |
  Select-Object Name, @{Name="SizeKB";Expression={[math]::Round($_.Length/1KB, 2)}} | 
  Sort-Object SizeKB -Descending | 
  ForEach-Object {
    $icon = if ($_.SizeKB -gt 100) { "🔴" } elseif ($_.SizeKB -gt 50) { "🟡" } else { "🟢" }
    Write-Host "$icon $($_.Name.PadRight(30)) $($_.SizeKB) KB"
  }

Write-Host "`n📊 Total:" -ForegroundColor Cyan
$total = (Get-ChildItem -Path ".\dist\*\browser" -Recurse -Filter "*.js" | 
  Where-Object { $_.Name -notlike "*lazy*" } | 
  Measure-Object -Property Length -Sum).Sum / 1KB
Write-Host "$([math]::Round($total, 2)) KB`n" -ForegroundColor Yellow
```

Execute:

powershell

```powershell
.\analyze-manual.ps1
```

---

## 📈 PARTE 7: Relatório de Análise

### Passo 11: Documentar Achados

Crie um arquivo `BUNDLE-ANALYSIS.md`:

markdown

```markdown
# 📊 Análise de Bundle - [Data]

## Situação Atual
- **Total Initial**: 564.97 KB
- **Budget**: 500 KB
- **Excedente**: 64.97 KB (13% acima)

## 🔴 Principais Culpados

### 1. chunk-ARULHAH5.js (172.64 KB)
- **Contém**: PrimeNG components + themes
- **Problema**: Importando módulos não utilizados
- **Solução**: Import seletivo
- **Redução esperada**: -80 KB

### 2. main-ITFL6AMO.js (111.36 KB)
- **Contém**: Angular core + aplicação
- **Problema**: Sem lazy loading
- **Solução**: Lazy load de rotas
- **Redução esperada**: -40 KB

### 3. styles-DFNBWTVF.css (70.21 KB)
- **Contém**: PrimeNG themes + icons
- **Problema**: Tema completo sendo carregado
- **Solução**: CSS customizado
- **Redução esperada**: -30 KB

## 🎯 Meta
- **Novo total esperado**: ~415 KB
- **Redução total**: ~150 KB (26%)
```

---

## 🚀 PARTE 8: Plano de Otimização

### Passo 12: Priorização

**ALTA PRIORIDADE (Impacto > 50 KB):**

1. ✅ Lazy load de rotas principais
2. ✅ Otimizar imports do PrimeNG
3. ✅ Remover bibliotecas não utilizadas

**MÉDIA PRIORIDADE (Impacto 20-50 KB):** 4. ⚠️ Otimizar CSS/themes 5. ⚠️ Code splitting adicional 6. ⚠️ Substituir bibliotecas pesadas

**BAIXA PRIORIDADE (Impacto < 20 KB):** 7. 🔵 Minificação extra 8. 🔵 Tree shaking manual 9. 🔵 Compression avançada

---

## 🔄 PARTE 9: Monitoramento Contínuo

### Passo 13: Automatizar Análise

Adicione no `package.json`:

json

```json
{
  "scripts": {
    "build:check": "npm run build:stats && node check-bundle-size.js"
  }
}
```

Crie `check-bundle-size.js`:

javascript

```javascript
const fs = require('fs');
const path = require('path');

const BUDGET_KB = 500;
const distPath = './dist/generic-interface/browser';

function getFileSize(filePath) {
  const stats = fs.statSync(filePath);
  return stats.size / 1024;
}

console.log('\n🔍 Verificando Bundle Size...\n');

const files = fs.readdirSync(distPath);
let totalSize = 0;

files
  .filter(f => f.endsWith('.js') && !f.includes('lazy'))
  .forEach(file => {
    const size = getFileSize(path.join(distPath, file));
    totalSize += size;
    
    const icon = size > 100 ? '🔴' : size > 50 ? '🟡' : '🟢';
    console.log(`${icon} ${file.padEnd(30)} ${size.toFixed(2)} KB`);
  });

console.log(`\n📊 Total: ${totalSize.toFixed(2)} KB`);
console.log(`🎯 Budget: ${BUDGET_KB} KB`);

if (totalSize > BUDGET_KB) {
  const excess = totalSize - BUDGET_KB;
  console.log(`\n❌ EXCEDEU o budget em ${excess.toFixed(2)} KB (${((excess/BUDGET_KB)*100).toFixed(1)}%)\n`);
  process.exit(1);
} else {
  const remaining = BUDGET_KB - totalSize;
  console.log(`\n✅ Dentro do budget! Restam ${remaining.toFixed(2)} KB\n`);
}
```

Execute antes de cada commit:

bash

```bash
npm run build:check
```

---

## 📚 PARTE 10: Comandos de Referência Rápida

### Passo 14: Cheat Sheet

powershell

```powershell
# === ANÁLISE ===
npm run analyze              # Análise visual completa
npm run analyze:source       # Source map explorer
npm run build:stats          # Build com estatísticas

# === ENCONTRAR ARQUIVOS ===
Get-ChildItem -Path .\dist -Recurse -Filter "stats.json"
ls dist -Recurse

# === BUILD ===
ng build                     # Build padrão
ng build --configuration production  # Build produção
ng build --stats-json        # Com estatísticas

# === VERIFICAR DEPENDÊNCIAS ===
npm list --depth=0           # Ver todas dependências
npx depcheck                 # Encontrar não utilizadas
npx find-duplicate-dependencies  # Duplicatas
```

---

## ✅ Checklist Final

Após análise, você deve saber:

- [ ]  Qual o tamanho atual do bundle
- [ ]  Quais são os 3 maiores arquivos
- [ ]  Quais bibliotecas estão sendo usadas
- [ ]  Se há imports duplicados
- [ ]  Quais módulos podem ser lazy loaded
- [ ]  Qual a meta de redução
- [ ]  Plano de ação priorizado

---

## 🎯 Exemplo Prático Completo

powershell

```powershell
# 1. Preparar ambiente
npm install --save-dev webpack-bundle-analyzer source-map-explorer

# 2. Adicionar scripts no package.json (ver Passo 2)

# 3. Gerar análise
npm run analyze

# 4. Aguardar navegador abrir (http://127.0.0.1:8888)

# 5. Identificar culpados no mapa visual
#    - Procure blocos vermelhos/laranjas grandes
#    - Anote bibliotecas > 100 KB

# 6. Documentar no BUNDLE-ANALYSIS.md

# 7. Implementar otimizações (ver guia de otimização)

# 8. Verificar melhorias
npm run build:check

# 9. Repetir até atingir meta
```

---

## 📖 Resumo Executivo

**O que foi feito que funcionou:**

1. ✅ Instalou `webpack-bundle-analyzer`
2. ✅ Gerou build com `ng build --stats-json`
3. ✅ Localizou `stats.json` em `dist/generic-interface/stats.json`
4. ✅ Executou análise com caminho correto
5. ✅ Identificou que **chunk-ARULHAH5.js (172 KB)** é o maior culpado
6. ✅ Verificou que **to-do component (657 KB)** está lazy loaded (OK!)

**Próximos passos:**

- Otimizar imports do PrimeNG
- Implementar lazy loading de rotas
- Reduzir CSS/themes