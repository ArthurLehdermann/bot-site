# FAQ Rewording + "Automação com controle operacional" Section Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rewrite two defensive FAQ answers and add a new "Automação com controle operacional" section (4 feature cards: políticas, aprovação humana, auditoria, isolamento por empresa) to the bot-site landing page, replacing the small inline security note in `Comparison.astro`.

**Architecture:** Pure content/presentational change to the Astro static site (`bot-site`). One existing component (`FAQ.astro`) gets text edits, one existing component (`Comparison.astro`) loses its inline security note, one new component (`Security.astro`) is created following the exact card-grid pattern already used in `Features.astro`, and `index.astro` wires the new component in between `Comparison` and `Plans`.

**Tech Stack:** Astro 6, Tailwind CSS v4 (utility classes + shared CSS classes `glass-card`, `section-tag`, `gradient-text`, `reveal` from `src/styles/global.css`). No test runner in this project — verification is `npm run build` (must succeed) plus a visual check via `npm run dev`.

Spec: `docs/superpowers/specs/2026-06-13-faq-security-section-design.md`

---

### Task 1: Reword the two defensive FAQ answers

**Files:**
- Modify: `src/components/FAQ.astro:36-38` and `src/components/FAQ.astro:55-58`

- [ ] **Step 1: Update "A IA responde qualquer pergunta?" answer**

In `src/components/FAQ.astro`, find this entry (around line 35-38):

```js
  {
    q: "A IA responde qualquer pergunta?",
    a: "Não. O sistema é configurado para atuar dentro do contexto da empresa, evitando respostas fora do escopo operacional.",
  },
```

Replace the `a:` value with:

```js
  {
    q: "A IA responde qualquer pergunta?",
    a: "O sistema atua dentro das políticas, processos e informações definidos pela empresa, mantendo o atendimento alinhado à operação do negócio.",
  },
```

- [ ] **Step 2: Update "O bot sabe quando acionar um humano?" answer**

In the same file, find this entry (around line 55-58):

```js
  {
    q: "O bot sabe quando acionar um humano?",
    a: "Sim. Quando o cliente solicita negociação de preço, condições de pagamento, ou qualquer situação fora do escopo configurado, o bot identifica automaticamente e transfere o atendimento para um humano disponível.",
  },
```

Replace the `a:` value with:

```js
  {
    q: "O bot sabe quando acionar um humano?",
    a: "Sim. O sistema identifica situações que exigem validação humana, como negociações, exceções operacionais ou solicitações fora das políticas definidas pela empresa, e encaminha automaticamente para a equipe responsável.",
  },
```

- [ ] **Step 3: Verify the build still succeeds**

Run: `cd /Users/arthur/Projetos/bot-site && npm run build`
Expected: build completes with exit code 0, no errors referencing `FAQ.astro`.

- [ ] **Step 4: Commit**

```bash
cd /Users/arthur/Projetos/bot-site
git add src/components/FAQ.astro
git commit -m "Reword defensive FAQ answers to sound less limiting"
```

---

### Task 2: Remove the inline security note from Comparison.astro

**Files:**
- Modify: `src/components/Comparison.astro:71-80`

- [ ] **Step 1: Remove the "Security note" block**

In `src/components/Comparison.astro`, the section currently ends like this (lines ~69-82):

```astro
    </div>

    <!-- Security note -->
    <div class="mt-10 max-w-3xl mx-auto glass-card p-6 flex items-start gap-4" style="border-color: rgba(245,158,11,0.15);">
      <div class="w-10 h-10 rounded-lg flex items-center justify-center flex-shrink-0" style="background: rgba(245,158,11,0.1); border: 1px solid rgba(245,158,11,0.2);">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#f59e0b" stroke-width="1.75" aria-hidden="true"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/></svg>
      </div>
      <div>
        <h3 style="font-family: 'Plus Jakarta Sans', sans-serif; font-weight: 700; font-size: 0.9375rem; color: #f59e0b; margin-bottom: 0.375rem;">Segurança e controle</h3>
        <p style="color: #94a3b8; font-size: 0.9rem; line-height: 1.65;">A empresa mantém controle sobre aprovações e ações críticas. Agendamentos, alterações e processos sensíveis podem exigir confirmação humana antes da execução.</p>
      </div>
    </div>
  </div>
</section>
```

Remove the `<!-- Security note -->` comment and the `<div class="mt-10 max-w-3xl mx-auto glass-card ...">...</div>` block entirely, leaving:

```astro
    </div>
  </div>
</section>
```

- [ ] **Step 2: Verify the build still succeeds**

Run: `cd /Users/arthur/Projetos/bot-site && npm run build`
Expected: build completes with exit code 0, no errors referencing `Comparison.astro`.

- [ ] **Step 3: Commit**

```bash
cd /Users/arthur/Projetos/bot-site
git add src/components/Comparison.astro
git commit -m "Remove inline security note from Comparison section"
```

---

### Task 3: Create the Security.astro component

**Files:**
- Create: `src/components/Security.astro`

- [ ] **Step 1: Write the new component**

Create `src/components/Security.astro` with the following content. This follows the same card-grid pattern as `src/components/Features.astro` (icon + title + text, `glass-card`, `reveal`), with 4 cards in the order defined by the spec (políticas → aprovação → auditoria → isolamento):

```astro
---
const items = [
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><line x1="6" y1="3" x2="6" y2="15"/><circle cx="18" cy="6" r="3"/><circle cx="6" cy="18" r="3"/><path d="M18 9a9 9 0 0 1-9 9"/></svg>`,
    color: "#6366f1",
    title: "Políticas de execução configuráveis",
    text: "A empresa define quais processos podem ser automatizados, supervisionados ou executados apenas com aprovação humana.",
  },
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><rect x="8" y="2" width="8" height="4" rx="1" ry="1"/><path d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2"/><path d="m9 14 2 2 4-4"/></svg>`,
    color: "#10b981",
    title: "Aprovação humana em ações críticas",
    text: "A empresa define quais ações exigem confirmação antes da execução, como agendamentos, alterações ou aprovações operacionais.",
  },
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/></svg>`,
    color: "#06b6d4",
    title: "Auditoria e rastreabilidade",
    text: "Todas as interações, consultas e ações executadas pelo sistema ficam registradas para acompanhamento e auditoria operacional.",
  },
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><polygon points="12 2 2 7 12 12 22 7 12 2"/><polyline points="2 17 12 22 22 17"/><polyline points="2 12 12 17 22 12"/></svg>`,
    color: "#8b5cf6",
    title: "Ambiente separado por empresa",
    text: "Dados, contexto, conversas e integrações operam de forma independente para cada empresa.",
  },
];
---

<section class="py-24 relative">
  <div class="max-w-6xl mx-auto px-5">
    <div class="text-center mb-14">
      <div class="section-tag mb-5 mx-auto" style="width: fit-content;">Controle operacional</div>
      <h2 style="font-family: 'Plus Jakarta Sans', sans-serif; font-weight: 800; font-size: clamp(1.5rem, 2.5vw, 2.125rem); letter-spacing: -0.03em; color: #f1f5f9; line-height: 1.15;">
        Automação com
        <span class="gradient-text"> controle operacional.</span>
      </h2>
      <p style="color: #94a3b8; font-size: 1rem; line-height: 1.7; max-width: 560px; margin: 0.75rem auto 0;">
        A automação funciona dentro das regras da empresa, com rastreabilidade, aprovações e supervisão humana.
      </p>
    </div>

    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
      {items.map((item) => (
        <div class="glass-card p-5 flex flex-col gap-3 reveal" style="transition: border-color 0.3s, transform 0.3s;" onmouseover="this.style.transform='translateY(-2px)'" onmouseout="this.style.transform='translateY(0)'">
          <div class="w-10 h-10 rounded-lg flex items-center justify-center" style={`background: ${item.color}18; color: ${item.color}; border: 1px solid ${item.color}22;`}>
            <Fragment set:html={item.icon} />
          </div>
          <h3 style={`font-family: 'Plus Jakarta Sans', sans-serif; font-weight: 700; font-size: 0.9375rem; color: #f1f5f9;`}>{item.title}</h3>
          <p style="color: #64748b; font-size: 0.875rem; line-height: 1.65;">{item.text}</p>
        </div>
      ))}
    </div>
  </div>
</section>
```

- [ ] **Step 2: Verify the build succeeds**

Run: `cd /Users/arthur/Projetos/bot-site && npm run build`
Expected: build completes with exit code 0. `Security.astro` is not yet imported anywhere, so it shouldn't affect output, but this confirms there's no syntax error in the new file (Astro typechecks all files in `src` during build).

- [ ] **Step 3: Commit**

```bash
cd /Users/arthur/Projetos/bot-site
git add src/components/Security.astro
git commit -m "Add Security.astro: controle operacional section"
```

---

### Task 4: Wire Security.astro into the page and visually verify

**Files:**
- Modify: `src/pages/index.astro:12-31`

- [ ] **Step 1: Import and place the new section**

In `src/pages/index.astro`, add the import next to the `Comparison` import (around line 12):

```astro
import Comparison from '../components/Comparison.astro';
import Security from '../components/Security.astro';
```

Then place `<Security />` right after `<Comparison />` (around line 31):

```astro
    <Comparison />
    <Security />
    <Plans />
```

- [ ] **Step 2: Run the dev server and visually verify**

Run: `cd /Users/arthur/Projetos/bot-site && npm run dev`

Open the local URL printed in the terminal (default `http://localhost:4321`) and check:
- The new "Automação com controle operacional" section appears between the "Diferenciais" (Comparison) section and the "Planos" (Plans) section.
- All 4 cards render with icon, title and description, in the order: Políticas de execução configuráveis, Aprovação humana em ações críticas, Auditoria e rastreabilidade, Ambiente separado por empresa.
- The old "Segurança e controle" note no longer appears inside the "Diferenciais" section.
- The two updated FAQ answers ("A IA responde qualquer pergunta?" and "O bot sabe quando acionar um humano?") show the new wording.

Stop the dev server (Ctrl+C) once verified.

- [ ] **Step 3: Run the production build**

Run: `cd /Users/arthur/Projetos/bot-site && npm run build`
Expected: build completes with exit code 0.

- [ ] **Step 4: Commit**

```bash
cd /Users/arthur/Projetos/bot-site
git add src/pages/index.astro
git commit -m "Add Automação com controle operacional section to landing page"
```
