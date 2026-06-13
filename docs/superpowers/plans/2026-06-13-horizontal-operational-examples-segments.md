# Horizontalização operacional: Examples.astro + Segments.astro Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Expand `Examples.astro` from 3 to 6 examples (reducing oficina-bias from 3/3 to 2/6, adding a media-type badge and two new client message types) and rewrite `Segments.astro` from a flat pill list into a "segment → concrete operational case" card grid, reframing the section from "markets we serve" to "operation types".

**Architecture:** Pure content/presentational change to two existing Astro components. `Examples.astro` keeps its existing card-grid structure but gets a new `typeMeta` lookup (icon + label per `clientType`) used both for a new badge and for the existing "Cliente envia" preview row, plus two new `clientType` values (`audio`, `image`) replacing the unused `media` type. `Segments.astro` is rewritten to follow the exact `glass-card` icon+title+text pattern already used in `Security.astro`.

**Tech Stack:** Astro 6, Tailwind CSS v4 (utility classes + shared CSS classes `glass-card`, `section-tag`, `gradient-text`, `reveal` from `src/styles/global.css`). No test runner — verification is `npm run build` (must succeed).

Spec: `docs/superpowers/specs/2026-06-13-horizontal-operational-examples-segments-design.md`

---

### Task 1: Rewrite Examples.astro with 6 examples + media-type badge

**Files:**
- Modify (full rewrite): `src/components/Examples.astro`

- [ ] **Step 1: Replace the full file content**

Replace the entire content of `src/components/Examples.astro` with:

```astro
---
const examples = [
  {
    number: "01",
    title: "Consulta de status",
    client: "Minha Amarok já ficou pronta?",
    clientType: "text",
    steps: [
      { icon: "🔍", text: "Localiza o veículo no cadastro" },
      { icon: "📋", text: "Consulta a ordem de serviço" },
      { icon: "✅", text: "Responde com o status atual" },
    ],
    color: "#6366f1",
  },
  {
    number: "02",
    title: "Reagendamento de consulta",
    client: "Preciso remarcar minha consulta",
    clientType: "audio",
    steps: [
      { icon: "📅", text: "Consulta a agenda disponível" },
      { icon: "🔁", text: "Sugere novos horários" },
      { icon: "👩‍⚕️", text: "Encaminha o reagendamento para confirmação da equipe" },
    ],
    color: "#10b981",
  },
  {
    number: "03",
    title: "PDF para orçamento",
    client: "Gostaria de orçamento",
    clientType: "pdf",
    steps: [
      { icon: "📄", text: "Lê o documento enviado" },
      { icon: "📝", text: "Resume as informações relevantes" },
      { icon: "➡️", text: "Encaminha para análise de orçamento" },
    ],
    color: "#06b6d4",
  },
  {
    number: "04",
    title: "Acesso e suporte acadêmico",
    client: "Não consegui acessar as aulas",
    clientType: "text",
    steps: [
      { icon: "🎓", text: "Identifica a matrícula do aluno" },
      { icon: "📡", text: "Consulta status de acesso" },
      { icon: "📚", text: "Envia orientações ou encaminha para suporte acadêmico" },
    ],
    color: "#8b5cf6",
  },
  {
    number: "05",
    title: "Status do equipamento",
    client: "Meu notebook já ficou pronto?",
    clientType: "image",
    steps: [
      { icon: "🖼️", text: "Identifica o equipamento pela imagem enviada" },
      { icon: "📋", text: "Consulta o status do reparo" },
      { icon: "📅", text: "Informa previsão de entrega" },
    ],
    color: "#3b82f6",
  },
  {
    number: "06",
    title: "Negociação com encaminhamento",
    client: "Vocês fazem desconto para pagamento à vista?",
    clientType: "text",
    steps: [
      { icon: "🧠", text: "Identifica pedido de negociação" },
      { icon: "💬", text: "Responde dentro do script definido" },
      { icon: "👤", text: "Encaminha para atendente humano" },
    ],
    color: "#f59e0b",
  },
];

const typeMeta = {
  text: {
    label: "Texto",
    icon: `<svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="#94a3b8" stroke-width="2" aria-hidden="true"><path d="M21 15a2 2 0 01-2 2H7l-4 4V5a2 2 0 012-2h14a2 2 0 012 2z"/></svg>`,
  },
  audio: {
    label: "Áudio",
    icon: `<svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="#94a3b8" stroke-width="2" aria-hidden="true"><path d="M12 1a3 3 0 0 0-3 3v8a3 3 0 0 0 6 0V4a3 3 0 0 0-3-3Z"/><path d="M19 10v2a7 7 0 0 1-14 0v-2"/><line x1="12" y1="19" x2="12" y2="22"/></svg>`,
  },
  image: {
    label: "Imagem",
    icon: `<svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="#94a3b8" stroke-width="2" aria-hidden="true"><rect x="3" y="3" width="18" height="18" rx="2" ry="2"/><circle cx="8.5" cy="8.5" r="1.5"/><polyline points="21 15 16 10 5 21"/></svg>`,
  },
  pdf: {
    label: "PDF",
    icon: `<svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="#94a3b8" stroke-width="2" aria-hidden="true"><path d="M14 2H6a2 2 0 00-2 2v16a2 2 0 002 2h12a2 2 0 002-2V8z"/><polyline points="14,2 14,8 20,8"/></svg>`,
  },
};
---

<section id="exemplos" class="py-24 relative overflow-hidden">
  <div class="orb w-96 h-96 opacity-10" style="background: radial-gradient(circle, #3b82f6, transparent); bottom: 0; left: -10%;"></div>

  <div class="max-w-6xl mx-auto px-5">
    <div class="text-center mb-14">
      <div class="section-tag mb-5 mx-auto" style="width: fit-content;">Exemplos reais</div>
      <h2 style="font-family: 'Plus Jakarta Sans', sans-serif; font-weight: 800; font-size: clamp(1.5rem, 2.5vw, 2.125rem); letter-spacing: -0.03em; color: #f1f5f9; line-height: 1.15;">
        Veja como funciona
        <span class="gradient-text"> na prática.</span>
      </h2>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
      {examples.map((ex) => (
        <div class="glass-card p-6 flex flex-col gap-5 reveal">
          <!-- Media type badge -->
          <div class="flex items-center gap-1.5 px-2.5 py-1 rounded-full self-start" style="background: rgba(255,255,255,0.04); border: 1px solid rgba(255,255,255,0.08);">
            <Fragment set:html={typeMeta[ex.clientType].icon} />
            <span style="font-size: 0.6875rem; font-weight: 600; color: #94a3b8;">{typeMeta[ex.clientType].label}</span>
          </div>

          <div class="flex items-center justify-between">
            <span style={`font-family: 'Plus Jakarta Sans', sans-serif; font-size: 2.5rem; font-weight: 800; color: ${ex.color}; opacity: 0.3; line-height: 1;`}>{ex.number}</span>
            <span style={`font-family: 'Plus Jakarta Sans', sans-serif; font-weight: 700; font-size: 0.9375rem; color: ${ex.color};`}>{ex.title}</span>
          </div>

          <!-- Client message preview -->
          <div style="background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.06); border-radius: 0.75rem; padding: 0.875rem;">
            <div style="font-size: 0.6875rem; color: #475569; font-weight: 600; margin-bottom: 6px; text-transform: uppercase; letter-spacing: 0.05em;">Cliente envia</div>
            <div class="flex items-center gap-2">
              <Fragment set:html={typeMeta[ex.clientType].icon} />
              {ex.clientType === 'image' && (
                <span style="font-size: 0.875rem; color: #94a3b8;">Foto + "{ex.client}"</span>
              )}
              {ex.clientType === 'pdf' && (
                <span style="font-size: 0.875rem; color: #94a3b8;">PDF + "{ex.client}"</span>
              )}
              {(ex.clientType === 'text' || ex.clientType === 'audio') && (
                <span style="font-size: 0.875rem; color: #94a3b8; font-style: italic;">"{ex.client}"</span>
              )}
            </div>
          </div>

          <!-- Bot response steps -->
          <div class="flex flex-col gap-2.5">
            <div style="font-size: 0.6875rem; color: #475569; font-weight: 600; text-transform: uppercase; letter-spacing: 0.05em;">Bot executa</div>
            {ex.steps.map((step) => (
              <div class="flex items-start gap-3">
                <div class="w-6 h-6 rounded-md flex items-center justify-center flex-shrink-0 text-xs" style={`background: ${ex.color}14; border: 1px solid ${ex.color}22;`}>
                  <span aria-hidden="true">{step.icon}</span>
                </div>
                <span style="font-size: 0.875rem; color: #94a3b8; line-height: 1.5;">{step.text}</span>
              </div>
            ))}
          </div>
        </div>
      ))}
    </div>
  </div>
</section>
```

- [ ] **Step 2: Verify the build succeeds**

Run: `cd /Users/arthur/Projetos/bot-site && npm run build`
Expected: build completes with exit code 0, no errors referencing `Examples.astro`.

- [ ] **Step 3: Commit**

```bash
cd /Users/arthur/Projetos/bot-site
git add src/components/Examples.astro
git commit -m "Expand Examples section to 6 cases with media-type badges"
```

---

### Task 2: Rewrite Segments.astro as "operation type" card grid

**Files:**
- Modify (full rewrite): `src/components/Segments.astro`

- [ ] **Step 1: Replace the full file content**

Replace the entire content of `src/components/Segments.astro` with:

```astro
---
const items = [
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"/></svg>`,
    color: "#6366f1",
    title: "Oficinas e auto centers",
    text: "Consulta de ordens de serviço, aprovação de orçamento e atualização de status via WhatsApp.",
  },
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>`,
    color: "#10b981",
    title: "Clínicas e consultórios",
    text: "Confirmações, reagendamentos e encaminhamento operacional integrados à agenda da clínica.",
  },
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><rect x="2" y="3" width="20" height="14" rx="2"/><line x1="8" y1="21" x2="16" y2="21"/><line x1="12" y1="17" x2="12" y2="21"/></svg>`,
    color: "#3b82f6",
    title: "Assistência técnica",
    text: "Status de reparo, triagem e comunicação com clientes sem interromper a equipe técnica.",
  },
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><path d="M22 10 12 5 2 10l10 5 10-5Z"/><path d="M6 12v5c3 3 9 3 12 0v-5"/></svg>`,
    color: "#8b5cf6",
    title: "Escolas e cursos",
    text: "Dúvidas recorrentes, status de acesso a aulas/materiais e encaminhamento para suporte acadêmico.",
  },
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><rect x="8" y="2" width="8" height="4" rx="1" ry="1"/><path d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2"/><path d="M12 11h4"/><path d="M12 16h4"/><path d="M8 11h.01"/><path d="M8 16h.01"/></svg>`,
    color: "#f59e0b",
    title: "Prestadores de serviço",
    text: "Agendamento, confirmação e acompanhamento de serviços sem depender de atendimento manual constante.",
  },
  {
    icon: `<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"><path d="M3 9 12 2l9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2Z"/><polyline points="9 22 9 12 15 12 15 22"/></svg>`,
    color: "#06b6d4",
    title: "Imobiliárias",
    text: "Consulta de imóveis, agendamento de visitas e triagem inicial de atendimento via WhatsApp.",
  },
];
---

<section class="py-24 relative overflow-hidden">
  <div class="orb w-72 h-72 opacity-10" style="background: radial-gradient(circle, #8b5cf6, transparent); top: 10%; right: 5%;"></div>

  <div class="max-w-6xl mx-auto px-5">
    <div class="text-center mb-14">
      <div class="section-tag mb-5 mx-auto" style="width: fit-content;">Tipos de operação</div>
      <h2 style="font-family: 'Plus Jakarta Sans', sans-serif; font-weight: 800; font-size: clamp(1.5rem, 2.5vw, 2.125rem); letter-spacing: -0.03em; color: #f1f5f9; line-height: 1.15;">
        Operações com
        <span class="gradient-text"> atendimento recorrente.</span>
      </h2>
      <p style="color: #94a3b8; font-size: 1rem; line-height: 1.7; max-width: 560px; margin: 0.75rem auto 0;">
        Atendimento, consulta, triagem e execução operacional integrados aos sistemas da empresa.
      </p>
    </div>

    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
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
Expected: build completes with exit code 0, no errors referencing `Segments.astro`.

- [ ] **Step 3: Commit**

```bash
cd /Users/arthur/Projetos/bot-site
git add src/components/Segments.astro
git commit -m "Rewrite Segments section as operation-type card grid"
```

---

### Task 3: Visual verification and final build

**Files:** none (verification only)

- [ ] **Step 1: Run the dev server and visually verify**

Run: `cd /Users/arthur/Projetos/bot-site && npm run dev`

Open the local URL printed in the terminal (default `http://localhost:4321`) and check:
- "Exemplos reais" section shows 6 cards (2 rows of 3 on desktop), each with a small media-type badge ("Texto", "Áudio", "Imagem" or "PDF") at the top.
- Order matches: 1) Consulta de status (oficina), 2) Reagendamento de consulta (clínica), 3) PDF para orçamento (oficina), 4) Acesso e suporte acadêmico (escola), 5) Status do equipamento (assistência técnica), 6) Negociação com encaminhamento (comercial).
- "Tipos de operação" section (previously "Segmentos") shows 6 cards with icon, title and description, in order: Oficinas e auto centers, Clínicas e consultórios, Assistência técnica, Escolas e cursos, Prestadores de serviço, Imobiliárias.
- Section heading reads "Operações com atendimento recorrente." with subtitle "Atendimento, consulta, triagem e execução operacional integrados aos sistemas da empresa."

Stop the dev server (Ctrl+C) once verified.

- [ ] **Step 2: Run the production build**

Run: `cd /Users/arthur/Projetos/bot-site && npm run build`
Expected: build completes with exit code 0.
