# DESIGN.md — Redesign do Dashboard

Referência visual: **Twisty Dashboard** (layout) + **Mischa's Bakery** (identidade/paleta).

---

## Princípios de Design

| Princípio | Descrição |
|-----------|-----------|
| **Clean & Airy** | Fundo claro `#faf5f0`, cards brancos com sombras suaves, espaçamento generoso |
| **Rounded Surfaces** | Todos os cards com `border-radius: 16–20px`, cantos arredondados em botões e inputs |
| **Subtle Depth** | `box-shadow: 0 2px 12px rgba(0,0,0,0.06)` nos cards, sem bordas pesadas |
| **Data-forward** | Números grandes e proeminentes, labels discretos em uppercase 10px |
| **Mischa Brand** | Vermelho crimson `#c41230` como cor de marca, dourado `#d4a843` como accent/CTA — identidade visual da padaria |

---

## Layout Geral

Inspirado no grid 2-colunas da referência Twisty:

```
┌──────────────────────────────────────────────────────────┐
│  HEADER — logo + nav tabs + search + avatar              │
├──────────────────────┬───────────────────────────────────┤
│                      │                                   │
│   COLUNA PRINCIPAL   │        COLUNA LATERAL             │
│   (≈60% width)      │        (≈40% width)               │
│                      │                                   │
│   • Income Tracker   │   • Your Recent Projects          │
│     equivalente →    │     equivalente →                  │
│     PRODUÇÃO         │     BREAKDOWN POR ETAPA            │
│     (gráfico +       │     (lista de etapas com           │
│      métricas)       │      tempo e status)               │
│                      │                                   │
├──────────┬───────────┼───────────────────────────────────┤
│ CONNECT  │ PREMIUM   │   PROPOSAL PROGRESS               │
│ equiv →  │ equiv →   │   equivalente →                    │
│ EQUIPE   │ CONFIG    │   GANTT / TURNO ESTIMADO           │
│          │ RÁPIDO    │                                    │
└──────────┴───────────┴───────────────────────────────────┘
```

---

## Componentes — Mapeamento Referência → Mischa

### 1. Header / Nav Bar

**Referência:** Logo "TWISTY" + nav links (Home, Messages, Discover, Wallet, Projects) + search bar + avatar

**Mischa:**
- Logo à esquerda: **Mischa's Bakery** + subtitle `fluxo de produção`
- Nav tabs: `Fluxo` · `Dashboard` · `Config` (mantém estilo inline, text tabs sublinhadas)
- À direita: **Flavor toggle** (Trad/DDL/Mix) + botão limpar tempos + indicador salvo
- Search bar **não se aplica** — remover
- Avatar **não se aplica** — remover

**CSS novo:**
- `background: #ffffff` com `box-shadow: 0 1px 4px rgba(0,0,0,0.05)` em vez de `border-bottom`
- Nav tabs com hover suave e indicador `border-bottom: 2.5px solid var(--amber)` com `border-radius`
- Padding `16px 32px`

---

### 2. Card Principal — "Income Tracker" → **Painel de Produção**

**Referência:** Card grande com gráfico de barras verticais, tooltip `$2,567`, indicador `+20%`, seletor de período (Week), dias da semana `S M T W T F S`

**Mischa — equivalente:**
- Card grande ocupando ≈60% da coluna esquerda
- Título: **Produção** com ícone 📊
- Seletor de período: **dropdown "Semana"** → muda para `Formas: 40` (input editável inline)
- **Mini gráfico de barras verticais**: barras representando tempo por ciclo (manual vs. forno), com tooltip mostrando o tempo exato
- **KPI destaque grande**: `+20%` → no Mischa: **duração estimada** em formato grande (ex: `4h 00min`)
- Subtexto: "gargalo: forno" ou "gargalo: preparo manual"
- **Indicadores da semana**: S M T W T F S → substituir por indicadores de **ciclo** (`C1 C2 C3 ... C8`) com o ciclo atual em destaque

**Estilo:**
```css
.production-hero {
  background: #ffffff;
  border-radius: 20px;
  padding: 28px 32px;
  box-shadow: 0 2px 16px rgba(0,0,0,0.05);
}
.hero-value {
  font-family: 'Syne', sans-serif;
  font-size: 42px;
  font-weight: 700;
  color: var(--text);
}
.hero-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  background: var(--green-light);
  color: var(--green);
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 500;
}
```

---

### 3. Sidebar — "Your Recent Projects" → **Breakdown por Área**

**Referência:** Lista de projetos com ícone colorido, nome, badge (Paid/Not Paid), valor/hora, tags (Remote, Part-time), descrição, local + tempo

**Mischa — equivalente:**
- Título: **Etapas do Dia**  com link "Ver todas →"
- Lista de áreas como "projetos":
  - **🔵 Produção** — badge `8 ciclos` em azul, `${fmtM(wallClock)}` como valor
    - Tags: `Manual: ${manualTotal}` · `Forno: ${fornoTotal}`
    - Descrição curta: "Beatriz + Enzo · gargalo: forno"
    - Indicador: `Hoje · ${formas} formas`
  - **🟢 Pós-Produção** — badge `${turnoPos}` em verde
    - `Theo · ${manualPos} min manual`
  - **🟠 Pré-Produção** — badge `${turnoPre}` em amber
    - `Theo · ${manualPre} min manual`
- Cada item com **chevron de expand** `∨` (ao expandir → mostra breakdown table das etapas)

**Estilo:**
```css
.area-card {
  background: #ffffff;
  border-radius: 14px;
  padding: 18px 20px;
  margin-bottom: 12px;
  box-shadow: 0 1px 8px rgba(0,0,0,0.04);
  cursor: pointer;
  transition: box-shadow 0.2s;
}
.area-card:hover {
  box-shadow: 0 4px 16px rgba(0,0,0,0.08);
}
.area-icon {
  width: 40px;
  height: 40px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 18px;
}
.status-badge {
  padding: 3px 10px;
  border-radius: 12px;
  font-size: 11px;
  font-weight: 500;
}
.status-badge.ok   { background: #e6f4ea; color: #1a7431; }
.status-badge.warn { background: #fef3e0; color: #b86e1a; }
```

---

### 4. "Let's Connect" → **Equipe**

**Referência:** Lista de perfis com avatar, nome, badge (Senior/Middle), cargo, botão `+`

**Mischa — equivalente:**
- Card **Equipe Disponível**
- Dois itens:
  - **Beatriz + Enzo** — badge `Produção` em azul — controle `−` `2` `+`
  - **Theo** — badge `Pós + Pré` em amber — controle `−` `1` `+`
- Visual: ícone de avatar genérico (círculo com inicial), nome, badge, stepper

**Estilo:** Seguir exatamente o padrão "Let's Connect" — cards arredondados por pessoa, botões circulares

---

### 5. "Unlock Premium Features" → **Alerta / Dica Rápida**

**Referência:** Card com fundo pattern (pontos/grid), texto promocional, botão "Upgrade now →"

**Mischa — equivalente:**
- Card com **padrão de background decorativo** (dots ou subtle grid)
- Conteúdo dinâmico:
  - Se **bottleneck**: "⚠ Preparo manual excede o ciclo de forno. Considere mais uma pessoa."
  - Se **OK**: "✓ Seu fluxo cabe dentro do ciclo de forno. Tudo no ritmo."
  - Se **dados incompletos**: "📝 Preencha os tempos na aba Fluxo para ver os cálculos."
- Botão: "Ir para Fluxo →" ou "Ajustar equipe →"

**Estilo:**
```css
.insight-card {
  background: linear-gradient(135deg, #faf8f5 0%, #f0ece4 100%);
  border-radius: 16px;
  padding: 24px 28px;
  position: relative;
  overflow: hidden;
}
.insight-card::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image: radial-gradient(circle, var(--border) 1px, transparent 1px);
  background-size: 16px 16px;
  opacity: 0.5;
}
.insight-btn {
  background: var(--surface);
  border: none;
  padding: 10px 20px;
  border-radius: 24px;
  font-weight: 500;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
}
```

---

### 6. "Proposal Progress" → **Gantt / Turno Estimado**

**Referência:** Card com data selecionável, 3 KPIs grandes (Proposals sent: 64, Interviews: 12, Hires: 10) e mini gráfico de barras verticais vermelhas

**Mischa — equivalente:**
- Título: **Turno Estimado** com seletor de data (não funcional agora, pode ser `Hoje`)
- **3 KPIs horizontais** (seguir exatamente o layout da referência):
  - **Produção**: valor grande (ex: `4h`) — label discreto
  - **Pós-Produção**: valor grande (ex: `2h 30min`)
  - **Pré-Produção**: valor grande (ex: `45min`)
- Abaixo dos KPIs: **mini Gantt** com barras coloridas (blue/green/amber) — simplificado, horizontal
- Separador com linhas verticais finas tipo o gráfico de barras da referência

**Estilo:**
```css
.turno-kpi {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1px;
  background: var(--border);
  border-radius: 12px;
  overflow: hidden;
}
.turno-kpi-item {
  background: var(--surface);
  padding: 20px 16px;
  text-align: center;
}
.turno-kpi-label {
  font-size: 11px;
  color: var(--text-muted);
  letter-spacing: 0.06em;
  margin-bottom: 8px;
}
.turno-kpi-value {
  font-size: 28px;
  font-weight: 600;
  color: var(--text);
}
```

---

## Paleta de Cores — Mischa's Bakery

| Token | Hex | Uso |
|-------|-----|-----|
| `--bg` | `#faf5f0` | Fundo global — off-white quente |
| `--surface` | `#ffffff` | Cards e superfícies |
| `--surface2` | `#f5f0ea` | Backgrounds secundários, inputs |
| `--border` | `#e8e0d4` | Bordas leves, separadores |
| `--shadow` | `rgba(0,0,0,0.05)` | Sombras dos cards |
| `--crimson` | `#c41230` | **Cor da marca** — header, logo, destaques primários |
| `--crimson-light` | `rgba(196,18,48,0.08)` | Backgrounds leves da marca |
| `--gold` | `#d4a843` | **Accent/CTA** — botões primários, valores destacados |
| `--gold-light` | `rgba(212,168,67,0.12)` | Hover em gold, badges |
| `--gold-dim` | `#c49a38` | Hover escuro do gold |
| `--blue` | `#2a6ea0` | Produção (área) |
| `--blue-light` | `rgba(42,110,160,0.10)` | Background area Produção |
| `--green` | `#4a8c22` | Pós-produção (área) |
| `--green-light` | `rgba(74,140,34,0.10)` | Background área Pós |
| `--amber` | `#b86e1a` | Pré-produção (área) |
| `--amber-light` | `rgba(184,110,26,0.10)` | Background área Pré |
| `--purple` | `#8a4a8a` | DDL / paralelo |
| `--text` | `#2a2218` | Texto principal |
| `--text-muted` | `#7a6e5e` | Labels, subtexto |
| `--text-dim` | `#b0a898` | Placeholders, metadados |

---

## Design System — Mischa OS

### Tipografia

Fonte carregada via Google Fonts com `display=swap`.

| Classe | Família | Uso | Pesos |
|--------|---------|-----|-------|
| `--font-sans` | Inter | UI geral, textos, botões, labels | 400, 500, 600, 700 |
| `--font-serif` | Source Serif 4 | Títulos editoriais, logo, KPIs grandes | 400, 600, 700 |
| `--font-mono` | JetBrains Mono | Código, dados técnicos, inputs, valores numéricos | 400, 500, 600 |

---

### Paleta de Cores (tokens HSL)

**Light Mode**

| Token | HSL | Uso |
|-------|-----|-----|
| `--background` | `0 0% 100%` | Fundo principal |
| `--foreground` | `0 0% 10.55%` | Texto principal |
| `--primary` | `41.19 84.13% 66.47%` | Cor de destaque (dourado/âmbar) |
| `--primary-foreground` | `0 0% 0%` | Texto sobre primary |
| `--secondary` | `234.62 14.04% 95.29%` | Fundo secundário (azul acinzentado claro) |
| `--secondary-foreground` | `235.71 33.92% 25.49%` | Texto sobre secondary |
| `--muted` | `231.43 10.2% 97.65%` | Fundos sutis |
| `--muted-foreground` | `234.78 24.03% 46%` | Texto de apoio/desabilitado |
| `--accent` | `79.41 100% 95.88%` | Destaque verde claro |
| `--accent-foreground` | `41.85 84.66% 35.1%` | Texto sobre accent |
| `--destructive` | `17.86 87.85% 65.49%` | Ações destrutivas (laranja-vermelho) |
| `--destructive-foreground` | `0 0% 100%` | Texto sobre destructive |
| `--border` | `234.55 24.24% 91.76%` | Bordas |
| `--input` | `234.55 24.24% 91.76%` | Bordas de inputs |
| `--ring` | `41.19 84.13% 66.47%` | Anel de foco |

**Dark Mode** (classe `.dark` no `<html>`)

| Token | HSL |
|-------|-----|
| `--background` | `0 0% 8.04%` |
| `--foreground` | `0 0% 91.18%` |
| `--primary` | `41.19 84.13% 66.47%` |
| `--secondary` | `0 0% 10.55%` |
| `--muted` | `0 0% 9.41%` |
| `--muted-foreground` | `0 0% 70.59%` |
| `--accent` | `41.85 84.66% 35.1%` |
| `--border` | `0 0% 14.51%` |

**Cores de Gráficos**

| Token | HSL | Cor visual |
|-------|-----|-----------|
| `--chart-1` | `142 71% 45%` | Verde |
| `--chart-2` | `217 91% 60%` | Azul |
| `--chart-3` | `43 96% 56%` | Amarelo |
| `--chart-4` | `220 9% 46%` | Cinza |
| `--chart-5` | `0 84% 60%` | Vermelho |

**Sidebar**

| Token | Light | Dark |
|-------|-------|------|
| `--sidebar-background` | `231.43 10.2% 97.65%` | `0 0% 6.59%` |
| `--sidebar-foreground` | `0 0% 10.55%` | `0 0% 91.18%` |
| `--sidebar-primary` | `41.19 84.13% 66.47%` | `41.19 84.13% 66.47%` |
| `--sidebar-accent` | `79.41 100% 95.88%` | `41.85 84.66% 35.1%` |
| `--sidebar-border` | `234.55 24.24% 91.76%` | `0 0% 14.51%` |

---

### Border Radius

| Token | Valor | Uso |
|-------|-------|-----|
| `--radius` | `0.375rem` (6px) | Base — buttons, inputs, badges |
| `--radius-sm` | `0.25rem` (4px) | Tags, chips tiny |
| `--radius-md` | `0.75rem` (12px) | Small cards, popovers |
| `--radius-lg` | `1rem` (16px) | Cards |
| `--radius-xl` | `1.25rem` (20px) | Hero cards, lane cards |

---

### Identidade Visual

| Item | Valor |
|------|-------|
| Cor da marca | `--brand: #d1193a` (vermelho Mischa's) |
| Header | Fundo `var(--brand)`, texto branco |
| Tema padrão | Light mode (dark mode via classe `.dark`) |

---

### Regras de Uso

- Nunca usar cores hardcoded em componentes — sempre usar tokens semânticos (`var(--background)`, `var(--foreground)`, `var(--muted-foreground)`, etc.)
- Todas as cores definidas como `hsl()` completo no `index.html`
- Suporte a dark mode via classe `.dark` no elemento `<html>`
- Títulos (`h1`–`h6`) usam `var(--font-serif)`; body e UI usam `var(--font-sans)`; dados e valores usam `var(--font-mono)`

---

## Espaçamento & Sizing

| Elemento | Valor |
|----------|-------|
| Card border-radius | `16px` (grande), `10px` (badge/botão) |
| Card padding | `24px 28px` |
| Grid gap | `16px` |
| Section gap | `32px` |
| Header height | `≈64px` |
| Sidebar column | `min 340px`, `≈38% do viewport` |

---

## Micro-animações

| Trigger | Efeito |
|---------|--------|
| Card hover | `box-shadow` cresce suavemente, `transform: translateY(-1px)` |
| Tab switch | Conteúdo faz `fade-in` com `opacity 0→1` em `200ms` |
| KPI update | Números fazem `counter animation` (valor antigo → novo) |
| Badge status | Cor transiciona suavemente com `transition: background 0.3s` |
| Gantt bars | `width` anima com `transition: width 0.4s ease-out` |
| Card expand | `max-height` animado para abrir/fechar breakdown tables |
| Save indicator | Flash de verde com `fade` de 1.8s |

---

## Responsividade

| Breakpoint | Comportamento |
|------------|---------------|
| `≥ 1024px` | Layout 2-colunas (60/40) — com sidebar |
| `768–1023px` | Colunas empilham, sidebar vira seção abaixo |
| `< 768px` | Coluna única, cards full-width, KPIs empilham |

---

## Decisões de Implementação

1. **Tudo permanece em `index.html`** — sem build step, sem framework
2. **CSS Grid** para o layout 2-colunas do dashboard (substitui o `max-width: 960px` atual)
3. **Mantém a lógica JS existente** — `calc()`, `renderDash()`, `S`, `STEPS` — apenas muda o HTML que `renderDash()` gera
4. **Breakdowns viram accordions** — clicáveis nos cards da sidebar, evitam scroll excessivo
5. **As 3 abas (Fluxo, Dashboard, Config) continuam** — o redesign afeta primariamente a aba Dashboard
6. **Cores semânticas mantidas** — amber/blue/green são core da identidade visual da padaria
