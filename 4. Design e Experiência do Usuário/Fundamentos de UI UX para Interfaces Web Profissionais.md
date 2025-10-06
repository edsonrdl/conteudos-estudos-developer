## Introdução

Design de interface não é sobre fazer algo "bonito" - é sobre criar sistemas visuais que comuniquem hierarquia, facilitem a navegação e reduzam a carga cognitiva do usuário. Este guia explica os princípios fundamentais e como aplicá-los corretamente.

---

## 1. Sistema de Espaçamento: O Grid de 8 Pontos

### Por que existe

O cérebro humano processa padrões melhor que aleatoriedade. Quando espaçamentos são consistentes, os usuários conseguem prever onde encontrar elementos e processar informação mais rapidamente. O sistema de 8pt é usado porque:

- 8 é divisível por 2, 4 e 8 (escalável)
- Telas modernas têm densidade de pixels que se alinham bem com múltiplos de 8
- Cria ritmo visual consistente

### Como funciona

Você define uma escala base onde cada valor é múltiplo de 8 pixels:

- 8px (0.5rem) - espaçamento mínimo
- 16px (1rem) - espaçamento padrão
- 24px (1.5rem) - espaçamento médio
- 32px (2rem) - espaçamento grande
- 48px (3rem) - separação de seções

### Aplicação prática

**Errado:**

```
padding: 13px 19px;
margin-bottom: 27px;
```

Por que está errado: Valores arbitrários criam inconsistência visual. O olho humano percebe que algo está "fora do lugar" mesmo sem saber o porquê.

**Correto:**

```
padding: 16px 24px;  /* ou var(--space-4) var(--space-6) */
margin-bottom: 32px;
```

Por que está correto: Valores previsíveis criam ritmo. Se todos os cards têm padding de 24px, o usuário inconscientemente reconhece o padrão.

### Regra de ouro

- Elementos relacionados: 8-16px de distância
- Grupos diferentes: 24-32px de distância
- Seções de página: 48-64px de distância

---

## 2. Tipografia: Hierarquia e Legibilidade

### O problema fundamental

Texto é 95% do conteúdo web. Se o usuário não consegue ler confortavelmente, você falhou. A tipografia tem três objetivos:

1. Ser legível (caracteres distinguíveis)
2. Ser confortável de ler (não cansa os olhos)
3. Comunicar hierarquia (o que é mais importante)

### Escala tipográfica

Use uma escala matemática, não tamanhos aleatórios. A escala "Major Third" (1.250) é popular porque cria diferenças perceptíveis sem saltos abruptos:

```
12px → 15px → 19px → 24px → 30px → 37px → 46px
```

**Por que não escolher tamanhos aleatórios:**

Se você usa 14px, 17px, 22px, 29px, o cérebro não identifica um padrão. Com uma escala consistente, mesmo que o usuário não saiba matemática, ele percebe que há ordem.

### Tamanho de fonte base

**Nunca menos que 16px para texto corrido.**

Isso não é preferência estética - é ergonomia. Em monitores modernos, 14px força o usuário a se aproximar da tela. 16px permite leitura confortável a 50-70cm de distância.

### Line-height (altura de linha)

A fórmula geral:

- Títulos curtos: 1.2 - 1.3 (120% - 130% do tamanho da fonte)
- Texto corrido: 1.5 - 1.6 (150% - 160%)
- Texto denso: 1.7 - 1.8

**Por que isso importa:**

Line-height muito apertado: o olho "pula" para a linha errada ao voltar da margem direita.

Line-height muito solto: parece que são parágrafos separados, quebrando a coesão.

### Hierarquia visual

O usuário deve identificar em 1 segundo o que é:

- Título principal (maior, mais pesado)
- Subtítulos (tamanho intermediário)
- Corpo de texto (tamanho base)
- Texto secundário (menor, cor mais clara)

**Exemplo errado:**

```
Título: 18px, weight 400
Parágrafo: 16px, weight 400
```

Problema: A diferença é mínima. O usuário precisa processar conscientemente.

**Exemplo correto:**

```
Título: 32px, weight 700
Parágrafo: 16px, weight 400
```

A diferença é óbvia. O processamento é automático.

---

## 3. Cores: Contraste e Significado

### WCAG e contraste

As diretrizes WCAG não são arbitrárias - são baseadas em como o olho humano funciona. As recomendações:

- Texto normal: mínimo 4.5:1
- Texto grande (18pt+): mínimo 3:1
- Elementos UI: mínimo 3:1

**O que isso significa na prática:**

Texto cinza claro (#999) sobre fundo branco (#FFF) tem contraste de 2.85:1 - **FALHA**. Pessoas com baixa visão ou em ambientes com luz forte não conseguem ler.

Texto cinza escuro (#333) sobre fundo branco tem contraste de 12.6:1 - **PASSA** com folga.

Use ferramentas como WebAIM Contrast Checker - não confie no seu olho.

### Paleta de cores

**Erro comum:** Escolher cores aleatórias do Dribbble.

**Abordagem correta:**

1. Defina a cor primária (identidade da marca)
2. Gere 9 variações (50, 100, 200... 900)
3. Use ferramenta como Coolors ou Paletton para garantir harmonia
4. Teste contraste de CADA combinação usada

### Uso semântico

Cores comunicam significado através de convenção cultural:

- Verde: sucesso, confirmação, positivo
- Vermelho: erro, ação destrutiva, alerta
- Amarelo/Laranja: atenção, aviso
- Azul: informação, neutralidade

**Nunca use vermelho para "Confirmar" ou verde para "Cancelar"** - você estará lutando contra décadas de condicionamento.

---

## 4. Anatomia de Componentes

### Botões: Área de toque e hierarquia

**Regra mínima:** 44x44 pixels de área clicável.

Isso vem das diretrizes da Apple baseadas em ergonomia do dedo humano. Um botão de 32px de altura não é "design minimalista" - é design ruim que força precisão desnecessária.

**Hierarquia de botões:**

Em qualquer interface, deve haver apenas UM botão primário visível por seção:

```
[Cancelar]  [Confirmar]
  ↑ ghost    ↑ primary
```

Se tudo é importante, nada é importante. O botão primário deve ser visualmente dominante.

**Estados visuais obrigatórios:**

1. Default (repouso)
2. Hover (passar o mouse)
3. Active (clique pressionado)
4. Focus (navegação por teclado)
5. Disabled (não disponível)

Omitir qualquer um desses estados é erro de acessibilidade.

### Inputs: Feedback e validação

**Altura mínima:** 40-48px (mesma lógica dos botões)

**Estados necessários:**

1. Empty (vazio)
2. Focus (em edição)
3. Filled (preenchido)
4. Error (erro de validação)
5. Disabled (não editável)
6. Success (validado com sucesso)

**Erro comum:** Mostrar erro só após envio do formulário.

**Correto:** Validação inline progressiva - conforme o usuário digita ou sai do campo, já mostra se está correto. Isso reduz frustração.

---

## 5. Layout: Header e Footer

### Header

**Altura recomendada:** 64-72px no desktop, 56-64px no mobile.

**Por que não mais baixo:** Headers muito baixos (40px) parecem "espremidos" e dificultam áreas de toque em mobile.

**Por que não mais alto:** Headers muito altos (100px+) desperdiçam espaço vertical precioso, especialmente em laptops.

**Comportamento de scroll:**

Três abordagens válidas:

1. **Sticky (fixo):** Header sempre visível. Bom para navegação frequente.
2. **Static (estático):** Header sai com scroll. Maximiza espaço de conteúdo.
3. **Hide on scroll down:** Esconde ao rolar para baixo, mostra ao rolar para cima. Combina o melhor dos dois.

Não há "certo absoluto" - depende do seu caso de uso.

### Footer

**Altura mínima:** Conteúdo + 48px de padding vertical.

**Estrutura típica:**

```
[Logo]           [Links]         [Newsletter]
[Copyright]      [Políticas]     [Social]
```

O footer é onde usuários procuram informações secundárias - contato, políticas, links institucionais. Se está vazio, parece site abandonado.

---

## 6. Responsividade: Mobile-First

### Por que mobile-first

Não é tendência - é pragmatismo. É mais fácil expandir um layout simples (mobile) para complexo (desktop) do que o contrário.

**Abordagem errada:**

css

```css
/* Desktop primeiro */
.card { width: 300px; }

@media (max-width: 768px) {
  .card { width: 100%; }
}
```

**Abordagem correta:**

css

```css
/* Mobile primeiro */
.card { width: 100%; }

@media (min-width: 768px) {
  .card { width: 300px; }
}
```

### Breakpoints recomendados

```
640px  (sm) - Telefones maiores, landscape
768px  (md) - Tablets portrait
1024px (lg) - Tablets landscape, laptops pequenos
1280px (xl) - Desktops
1536px (2xl) - Telas grandes
```

**Erro comum:** Testar só no Chrome DevTools.

**Correto:** Testar em dispositivos reais. Simuladores não capturam performance, comportamento de toque e problemas de viewport.

---

## 7. Acessibilidade: Não é opcional

### Focus states

Todo elemento interativo DEVE ter indicador visual quando focado via teclado.

**Nunca faça isto:**

css

```css
*:focus { outline: none; }
```

Você acabou de quebrar a navegação para usuários de teclado.

**Correto:**

css

```css
:focus-visible {
  outline: 3px solid blue;
  outline-offset: 2px;
}
```

### Hierarquia semântica

**Errado:**

html

```html
<div class="title">Título da Página</div>
```

**Correto:**

html

```html
<h1>Título da Página</h1>
```

Screen readers dependem de HTML semântico. Uma div com estilo de título não é reconhecida como título.

### Texto alternativo

**Errado:**

html

```html
<img src="foto.jpg">
<img src="foto.jpg" alt="foto">
```

**Correto:**

html

```html
<img src="foto.jpg" alt="Mulher apresentando relatório em reunião">
```

Descreva o conteúdo e contexto, não o fato de que é uma imagem.

---

## 8. Performance e Percepção

### Skeleton screens

Quando algo carrega, não mostre loading spinner genérico. Mostre a "forma" do conteúdo que virá.

**Por que funciona:** Reduz percepção de tempo de espera. O cérebro interpreta que "algo está acontecendo" em vez de "travou".

### Animações: O menos é mais

**Regra:** Animações devem ter propósito, não ser decoração.

Propósitos válidos:

- Indicar mudança de estado
- Mostrar relação espacial (drawer abrindo)
- Chamar atenção para feedback importante

**Timing:**

- Micro-interações: 150-250ms
- Transições de página: 300-400ms
- Nunca mais que 500ms

Animações longas frustram usuários frequentes que já conhecem a interface.

---

## Conclusão: Princípios Fundamentais

1. **Consistência:** Use os mesmos padrões repetidamente
2. **Hierarquia:** Deixe óbvio o que é mais importante
3. **Affordance:** Elementos devem parecer o que são (botões parecem clicáveis)
4. **Feedback:** Toda ação deve ter reação visual
5. **Simplicidade:** Quando em dúvida, remova, não adicione

Design de interface não é arte - é engenharia de comunicação visual. Cada decisão deve ter raciocínio defensável, não ser "achismo" ou imitação cega de tendências.