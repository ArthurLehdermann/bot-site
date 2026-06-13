# Hero — Mockup do WhatsApp Rotativo por Vertical Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make the WhatsApp mockup in `Hero.astro` rotate in sync with the existing typewriter, cycling through all 7 verticals instead of always showing the "oficina" scenario.

**Architecture:** Single-file change to `src/components/Hero.astro`. Add a `scenarios` data array (parallel to the existing `phrases` array), five small JS render-helper functions that build the chat HTML from a scenario object, and hook the existing `tick()` typewriter loop to trigger a crossfade swap of the chat container's `innerHTML` whenever the typewriter index advances. The Astro-rendered static markup (scenario 1 / "oficina") is updated to match the scenario-1 data exactly, so it remains a correct no-JS fallback and first-paint HTML.

**Tech Stack:** Astro 6, Tailwind CSS v4 (utility classes only, no new deps), vanilla JS (`<script>` tag, no framework).

---

## Context for the implementer

- Spec: `docs/superpowers/specs/2026-06-13-hero-mockup-rotation-design.md` — read it if anything below seems ambiguous, but this plan already incorporates every decision from it.
- Target file: `src/components/Hero.astro` (178 lines before this change).
- No test runner exists in this project. Verification = `npm run build` (must report "3 page(s) built") + manual check in a browser (`npm run dev`).
- The `.wpp-msg` CSS class (in `src/styles/global.css`, lines 138-149) gives each of the 7 chat children a **one-time** staggered entrance animation (`opacity: 0` → fade/slide in, with `animation-delay` from 0.4s to 6.0s per child). This class is correct for the Astro-rendered first paint (scenario 1), but **must NOT be used by the JS render helpers** — otherwise every scenario swap would make the new messages invisible for up to 6 seconds. The JS helpers below intentionally omit `wpp-msg` and use plain layout classes (`flex justify-end`, `flex gap-2`) instead. This is a deliberate deviation from "equivalent markup", required by spec section 4.
- Work directly on `main` (established project convention). Commit in Portuguese with `Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>`.

---

### Task 1: Align static markup with scenario-1 data and add hooks for JS

**Files:**
- Modify: `src/components/Hero.astro` (lines 96, 101, 122-123, 147, 156)

The static HTML (lines 95-161) is the SSR fallback and must represent scenario 1 exactly as defined in the spec, so there's no mismatch if a future reader compares the static markup to `scenarios[0]`. It also needs an `id` for JS to target, and a `wpp-card-label` class for the line-clamp CSS added in Task 2.

- [ ] **Step 1: Add `id="wpp-chat"` and resize the chat container**

In `src/components/Hero.astro`, find the chat area div (currently line 96):

```astro
          <div class="p-4 space-y-3" style="background: #0d1117; min-height: 380px;">
```

Replace with:

```astro
          <div id="wpp-chat" class="p-4 space-y-3" style="background: #0d1117; min-height: 380px; max-height: 460px; overflow: hidden; transition: opacity 0.25s ease;">
```

- [ ] **Step 2: Lowercase the client's opening message (organic phrasing)**

Find (currently line 101):

```astro
                <p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">Meu carro já ficou pronto?</p>
```

Replace with:

```astro
                <p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">meu carro já ficou pronto?</p>
```

- [ ] **Step 3: Update the status card label and body to match scenario 1**

Find (currently lines 122-123):

```astro
                <div style="font-size: 0.625rem; color: #6366f1; font-weight: 700; text-transform: uppercase; letter-spacing: 0.05em; margin-bottom: 6px;">OS #4872 — AMAROK</div>
                <p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">Serviço concluído. Veículo <strong>pronto para retirada</strong>.</p>
```

Replace with:

```astro
                <div class="wpp-card-label" style="font-size: 0.625rem; color: #6366f1; font-weight: 700; text-transform: uppercase; letter-spacing: 0.05em; margin-bottom: 6px;">OS #4872 — VEÍCULO</div>
                <p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">Serviço <strong>concluído</strong>.</p>
```

- [ ] **Step 4: Update the transcription text (organic phrasing)**

Find (currently line 147):

```astro
                <p style="font-size: 0.8125rem; color: #94a3b8; font-style: italic; line-height: 1.5;">"quero remarcar para terça-feira"</p>
```

Replace with:

```astro
                <p style="font-size: 0.8125rem; color: #94a3b8; font-style: italic; line-height: 1.5;">"quero remarcar pra terça-feira"</p>
```

- [ ] **Step 5: Update the final bot message to match scenario-1 `bot2` text exactly**

Find (currently line 156):

```astro
                <p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">Pré-agendamento criado para <strong>terça-feira</strong>. A equipe vai confirmar em breve.</p>
```

Replace with:

```astro
                <p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">Pré-agendamento criado para terça-feira. A equipe vai confirmar em breve.</p>
```

- [ ] **Step 6: Sanity check — run the build**

```bash
cd /Users/arthur/Projetos/bot-site && npm run build
```

Expected: build succeeds, "3 page(s) built" (or similar success message), no errors.

---

### Task 2: Add CSS for chat container transition and card-label clamp

**Files:**
- Modify: `src/components/Hero.astro` (`<style>` block, currently lines 178-188)

- [ ] **Step 1: Add the `.wpp-card-label` rule**

Find the `<style>` block (currently lines 178-188):

```astro
<style>
  @keyframes float {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-8px); }
  }
  @keyframes twBlink {
    0%, 100% { opacity: 1; }
    50% { opacity: 0; }
  }
  #tw-cursor { animation: twBlink 1s step-end infinite; }
</style>
```

Replace with:

```astro
<style>
  @keyframes float {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-8px); }
  }
  @keyframes twBlink {
    0%, 100% { opacity: 1; }
    50% { opacity: 0; }
  }
  #tw-cursor { animation: twBlink 1s step-end infinite; }

  .wpp-card-label {
    line-height: 1.3;
    min-height: 2.6em;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
    overflow: hidden;
  }
</style>
```

This guards the status card's label (`cardLabel`) against overflow if a future label runs longer than 2 lines — both the static markup (Task 1, Step 3) and `renderStatusCard` (Task 3) use the `wpp-card-label` class.

- [ ] **Step 2: Sanity check — run the build**

```bash
cd /Users/arthur/Projetos/bot-site && npm run build
```

Expected: build succeeds, no errors.

---

### Task 3: Add `scenarios` data, render helpers, and synced rotation logic

**Files:**
- Modify: `src/components/Hero.astro` (`<script>` block, currently lines 190-216)

This is the core of the feature. Replace the entire `<script>` block with the version below, which adds:
1. The `scenarios` array (7 entries, parallel to `phrases`).
2. Five render helpers: `renderClientBubble`, `renderBotBubble`, `renderStatusCard`, `renderAudioBubble`, `renderScenario`.
3. A randomized initial index `i` shared by the typewriter and the mockup, with an immediate (no-fade) initial scenario render when `i !== 0`.
4. A `swapScenario()` crossfade triggered 150ms after the typewriter advances to the next phrase, with the pause bumped from 2200ms to 2600ms.

- [ ] **Step 1: Replace the `<script>` block**

Find the entire current block (currently lines 190-216):

```astro
<script>
  const phrases = [
    'Para oficinas e auto centers',
    'Para cursos e mentorias',
    'Para clínicas e consultórios',
    'Para imobiliárias e incorporadoras',
    'Para assistência técnica',
    'Para academias e estúdios',
    'Para escolas e treinamentos',
  ];
  let i = 0, j = 0, deleting = false;
  const el = document.getElementById('tw-text');
  if (el) {
    function tick() {
      const phrase = phrases[i];
      if (!deleting) {
        el.textContent = phrase.slice(0, ++j);
        if (j === phrase.length) { deleting = true; return setTimeout(tick, 2200); }
      } else {
        el.textContent = phrase.slice(0, --j);
        if (j === 0) { deleting = false; i = (i + 1) % phrases.length; }
      }
      setTimeout(tick, deleting ? 28 : 58);
    }
    tick();
  }
</script>
```

Replace with:

```astro
<script>
  const phrases = [
    'Para oficinas e auto centers',
    'Para cursos e mentorias',
    'Para clínicas e consultórios',
    'Para imobiliárias e incorporadoras',
    'Para assistência técnica',
    'Para academias e estúdios',
    'Para escolas e treinamentos',
  ];

  const scenarios = [
    {
      client: 'meu carro já ficou pronto?',
      bot1: 'Localizando veículo no sistema...',
      cardLabel: 'OS #4872 — VEÍCULO',
      cardBody: 'Serviço <strong>concluído</strong>.',
      cardHighlight: 'Disponível a partir das 08h',
      audioDuration: '0:12',
      transcription: 'quero remarcar pra terça-feira',
      bot2: 'Pré-agendamento criado para terça-feira. A equipe vai confirmar em breve.',
      times: ['09:14', '09:14', '09:14', '09:15', '09:15'],
    },
    {
      client: 'minha inscrição no curso foi confirmada?',
      bot1: 'Consultando matrícula no sistema...',
      cardLabel: 'MATRÍCULA #1129 — CURSO AVANÇADO',
      cardBody: 'Vaga <strong>garantida</strong>',
      cardHighlight: 'Início da turma: segunda-feira',
      audioDuration: '0:09',
      transcription: 'quero mudar pro horário da noite',
      bot2: 'Solicitação de troca para turma noturna registrada.',
      times: ['10:32', '10:32', '10:32', '10:33', '10:33'],
    },
    {
      client: 'minha consulta tá confirmada?',
      bot1: 'Consultando agenda no sistema...',
      cardLabel: 'AGENDAMENTO #305 — DR. PAULO',
      cardBody: 'Consulta <strong>confirmada</strong>.',
      cardHighlight: 'Atendimento às 15h30',
      audioDuration: '0:11',
      transcription: 'preciso remarcar pra sexta',
      bot2: 'Pré-agendamento criado para sexta-feira. Aguardando confirmação da clínica.',
      times: ['14:05', '14:05', '14:05', '14:06', '14:06'],
    },
    {
      client: 'o apartamento 302 ainda tá disponível?',
      bot1: 'Consultando disponibilidade no sistema...',
      cardLabel: 'UNIDADE 302 — RESIDENCIAL AURORA',
      cardBody: 'Unidade <strong>disponível</strong>.',
      cardHighlight: 'Próxima visita: sábado às 10h',
      audioDuration: '0:14',
      transcription: 'quero agendar a visita pra sábado',
      bot2: 'Solicitação de visita registrada. Corretor responsável notificado',
      times: ['11:20', '11:20', '11:20', '11:21', '11:21'],
    },
    {
      client: 'meu celular já foi avaliado?',
      bot1: 'Consultando status do reparo...',
      cardLabel: 'OS #2231 — TROCA DE TELA',
      cardBody: 'Orçamento <strong>aprovado</strong>.',
      cardHighlight: 'Pronto até quinta-feira',
      audioDuration: '0:08',
      transcription: 'pode autorizar o reparo',
      bot2: 'Reparo autorizado. Equipe notificada para iniciar o serviço.',
      times: ['16:48', '16:48', '16:48', '16:49', '16:49'],
    },
    {
      client: 'posso remarcar minha avaliação física?',
      bot1: 'Consultando agenda de avaliações...',
      cardLabel: 'AVALIAÇÃO FÍSICA — PERSONAL RAFAEL',
      cardBody: 'Horário <strong>disponível</strong>.',
      cardHighlight: 'Quinta-feira às 18h',
      audioDuration: '0:10',
      transcription: 'pode marcar nesse horário',
      bot2: 'Reagendamento registrado para quinta-feira às 18h.',
      times: ['18:02', '18:02', '18:02', '18:03', '18:03'],
    },
    {
      client: 'meu filho já tá matriculado pro próximo semestre?',
      bot1: 'Consultando matrícula no sistema...',
      cardLabel: 'MATRÍCULA #884 — TURMA B',
      cardBody: 'Matrícula <strong>confirmada</strong>',
      cardHighlight: 'Início das aulas: 03/02',
      audioDuration: '0:13',
      transcription: 'preciso atualizar o endereço cadastrado',
      bot2: 'Atualização de cadastro registrada na secretaria.',
      times: ['08:15', '08:15', '08:15', '08:16', '08:16'],
    },
  ];

  function renderClientBubble(text, time) {
    return `
      <div class="flex justify-end">
        <div class="max-w-xs rounded-2xl rounded-tr-sm px-3.5 py-2.5" style="background: #1e3a5f;">
          <p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">${text}</p>
          <div style="font-size: 0.625rem; color: #94a3b8; text-align: right; margin-top: 4px; display: flex; align-items: center; justify-content: flex-end; gap: 3px;">
            ${time}
            <svg width="14" height="14" viewBox="0 0 24 24" fill="#06b6d4" aria-hidden="true"><path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41L9 16.17z"/></svg>
          </div>
        </div>
      </div>`;
  }

  function renderBotBubble(content, time, showAvatar = true) {
    const avatarClass = showAvatar ? '' : ' opacity-0';
    const avatarLabel = showAvatar ? 'B' : '';
    return `
      <div class="flex gap-2">
        <div class="w-6 h-6 rounded-full flex-shrink-0 flex items-center justify-center text-xs${avatarClass}" style="background: linear-gradient(135deg, #6366f1, #3b82f6); font-family: 'Plus Jakarta Sans'; font-weight: 700; color: white; margin-top: 2px;">${avatarLabel}</div>
        <div class="max-w-xs rounded-2xl rounded-tl-sm px-3.5 py-2.5" style="background: #1a1f2e;">
          ${content}
          <div style="font-size: 0.625rem; color: #64748b; margin-top: 4px;">${time}</div>
        </div>
      </div>`;
  }

  function renderStatusCard(label, body, highlight, time) {
    return `
      <div class="flex gap-2">
        <div class="w-6 h-6 rounded-full flex-shrink-0 flex items-center justify-center text-xs opacity-0" style="background: linear-gradient(135deg, #6366f1, #3b82f6);"></div>
        <div class="max-w-xs rounded-2xl rounded-tl-sm px-3.5 py-2.5" style="background: #1a1f2e; border: 1px solid rgba(99,102,241,0.2);">
          <div class="wpp-card-label" style="font-size: 0.625rem; color: #6366f1; font-weight: 700; text-transform: uppercase; letter-spacing: 0.05em; margin-bottom: 6px;">${label}</div>
          <p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">${body}</p>
          <div style="margin-top: 8px; padding: 6px 8px; border-radius: 6px; background: rgba(37,211,102,0.1); border: 1px solid rgba(37,211,102,0.2);">
            <p style="font-size: 0.6875rem; color: #25d366; font-weight: 600;">${highlight}</p>
          </div>
          <div style="font-size: 0.625rem; color: #64748b; margin-top: 4px;">${time}</div>
        </div>
      </div>`;
  }

  function renderAudioBubble(duration) {
    return `
      <div class="flex justify-end">
        <div class="max-w-xs rounded-2xl rounded-tr-sm px-3.5 py-2.5" style="background: #1e3a5f; display: flex; align-items: center; gap: 10px;">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="#06b6d4" aria-hidden="true"><path d="M12 1a3 3 0 0 0-3 3v8a3 3 0 0 0 6 0V4a3 3 0 0 0-3-3z"/><path d="M19 10v2a7 7 0 0 1-14 0v-2" fill="none" stroke="#06b6d4" stroke-width="2"/><line x1="12" y1="19" x2="12" y2="23" stroke="#06b6d4" stroke-width="2"/><line x1="8" y1="23" x2="16" y2="23" stroke="#06b6d4" stroke-width="2"/></svg>
          <div class="waveform">
            <span></span><span></span><span></span><span></span><span></span><span></span><span></span>
          </div>
          <span style="font-size: 0.6875rem; color: #94a3b8;">${duration}</span>
        </div>
      </div>`;
  }

  function renderScenario(s) {
    return [
      renderClientBubble(s.client, s.times[0]),
      renderBotBubble(`<p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">${s.bot1}</p>`, s.times[1]),
      renderStatusCard(s.cardLabel, s.cardBody, s.cardHighlight, s.times[2]),
      renderAudioBubble(s.audioDuration),
      renderBotBubble(`<div style="font-size: 0.625rem; color: #06b6d4; font-weight: 600; margin-bottom: 4px; text-transform: uppercase; letter-spacing: 0.04em;">Áudio transcrito</div><p style="font-size: 0.8125rem; color: #94a3b8; font-style: italic; line-height: 1.5;">"${s.transcription}"</p>`, s.times[3]),
      renderBotBubble(`<p style="font-size: 0.8125rem; color: #e2e8f0; line-height: 1.5;">${s.bot2}</p>`, s.times[4], false),
    ].join('');
  }

  let i = Math.floor(Math.random() * phrases.length), j = 0, deleting = false;
  const el = document.getElementById('tw-text');
  const chatEl = document.getElementById('wpp-chat');

  if (chatEl && i !== 0) {
    chatEl.innerHTML = renderScenario(scenarios[i]);
  }

  function swapScenario(newIndex) {
    if (!chatEl) return;
    chatEl.style.opacity = '0';
    setTimeout(() => {
      chatEl.innerHTML = renderScenario(scenarios[newIndex]);
      chatEl.style.opacity = '1';
    }, 250);
  }

  if (el) {
    function tick() {
      const phrase = phrases[i];
      if (!deleting) {
        el.textContent = phrase.slice(0, ++j);
        if (j === phrase.length) { deleting = true; return setTimeout(tick, 2600); }
      } else {
        el.textContent = phrase.slice(0, --j);
        if (j === 0) {
          deleting = false;
          i = (i + 1) % phrases.length;
          setTimeout(() => swapScenario(i), 150);
        }
      }
      setTimeout(tick, deleting ? 28 : 58);
    }
    tick();
  }
</script>
```

- [ ] **Step 2: Sanity check — run the build**

```bash
cd /Users/arthur/Projetos/bot-site && npm run build
```

Expected: build succeeds, "3 page(s) built" (or similar), no errors. Astro/TS may warn about implicit `any` on `s` in `renderScenario(s)` — if the build fails specifically on that, add `// @ts-nocheck` as the first line inside the `<script>` tag (this project has no existing type annotations in this script and no test/type-check step beyond `astro build`, so this is the minimal fix). Only add it if the build actually fails — don't add preemptively.

---

### Task 4: Manual verification in browser

**Files:** none (verification only)

- [ ] **Step 1: Start the dev server**

```bash
cd /Users/arthur/Projetos/bot-site && npm run dev
```

- [ ] **Step 2: Open the site and observe the Hero**

Open the local dev URL in a browser. Verify:
- On load, the typewriter phrase and the WhatsApp mockup correspond to the same vertical (e.g., if the subtitle reads "Para clínicas e consultórios", the mockup shows the clinic scenario — agendamento, Dr. Paulo, etc.). Reload a few times to confirm this holds for different random starting indices, including `i === 0` (oficina, the static markup).
- After ~4.5-5.5s (one typewriter cycle: type + 2600ms pause + delete), the chat area fades out briefly (~250ms) and a new scenario fades in, matching the next typewriter phrase. The fade should feel slightly offset/natural, not perfectly synced with the text change.
- The chat area does not visibly resize/jump ("layout shift") between scenarios — `max-height: 460px` with `overflow: hidden` should keep it stable. If a card label or message overflows visibly, note it (acceptable to tune `max-height` afterward).
- The status card's `cardLabel` (e.g., "AVALIAÇÃO FÍSICA — PERSONAL RAFAEL") never exceeds 2 lines.

- [ ] **Step 3: Check responsiveness**

Use browser dev tools to emulate a mobile viewport (e.g., iPhone SE / 375px width) and confirm the mockup still fits without overflow or clipping of the avatar/card/audio bubble.

- [ ] **Step 4: Stop the dev server**

Press `Ctrl+C` in the terminal running `npm run dev`.

---

### Task 5: Commit and push

**Files:**
- `src/components/Hero.astro`

- [ ] **Step 1: Review the diff**

```bash
cd /Users/arthur/Projetos/bot-site && git diff src/components/Hero.astro
```

- [ ] **Step 2: Stage and commit**

```bash
cd /Users/arthur/Projetos/bot-site && git add src/components/Hero.astro && git commit -m "$(cat <<'EOF'
feat: mockup do WhatsApp no Hero rotaciona por vertical, sincronizado com o typewriter

Cada troca de frase do subtítulo agora dispara uma troca de cenário no
mockup (cliente pergunta -> IA consulta sistema -> card de registro ->
áudio -> transcrição -> ação registrada), cobrindo os 7 segmentos em
vez de mostrar sempre a oficina.

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
EOF
)"
```

- [ ] **Step 3: Push**

```bash
cd /Users/arthur/Projetos/bot-site && git push
```

---

## Self-Review Notes

- **Spec coverage:** All 7 spec sections are covered — data array (Task 3), render helpers with exact names `renderClientBubble`/`renderBotBubble`/`renderStatusCard`/`renderAudioBubble`/`renderScenario` (Task 3), initial render & fallback alignment (Tasks 1 & 3), sync & crossfade with 150ms/250ms timings (Task 3), 2600ms pause (Task 3), layout stability via min/max-height + line-clamp (Tasks 1 & 2), organic variation in scenarios 2/4/7 (Task 3 data, no trailing period).
- **Helper signature note:** `renderBotBubble` takes a third optional `showAvatar` parameter (default `true`), used to render the "continuation" bot messages (status card and final action) with an invisible placeholder avatar — matching the current static markup's alignment. This is an additive, backward-compatible detail not explicitly spelled out in the spec's helper signatures but required to reproduce the existing visual rhythm.
- **`.wpp-msg` omission:** explained in "Context for the implementer" — required so scenario swaps don't inherit the up-to-6-second entrance stagger meant only for the first-paint Astro markup.
