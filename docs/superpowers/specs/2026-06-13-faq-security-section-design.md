# FAQ rewording + seção "Controle e segurança operacional"

## Contexto

Revisão de copywriting do site identificou que (1) duas respostas do FAQ soam defensivas/limitadoras e (2) falta uma seção dedicada que comunique governança operacional (aprovação humana, auditoria, isolamento por empresa, políticas configuráveis) — hoje só existe um aviso pequeno dentro de `Comparison.astro`.

Recursos reais do bot que sustentam a nova seção: Policy Engine (políticas de execução/aprovação), audit trail por ação, trust score (reputação por contato), isolamento multi-tenant (dados/conversas por empresa).

## Escopo

### 1. FAQ.astro — reescrever 2 respostas

**"A IA responde qualquer pergunta?"**
Nova resposta:
> "O sistema atua dentro das políticas, processos e informações definidos pela empresa, mantendo o atendimento alinhado à operação do negócio."

**"O bot sabe quando acionar um humano?"**
Nova resposta:
> "Sim. O sistema identifica situações que exigem validação humana, como negociações, exceções operacionais ou solicitações fora das políticas definidas pela empresa, e encaminha automaticamente para a equipe responsável."

Demais FAQs não mudam.

### 2. Novo componente `Security.astro`

Nova seção entre `Comparison` e `Plans` no `index.astro`.

**Título:** "Controle e segurança operacional"
**Subtítulo:** "A automação funciona dentro das regras da empresa, com rastreabilidade, aprovações e supervisão humana quando necessário."

**4 cards** (grid, mesmo estilo visual de `glass-card` usado em `Comparison.astro`):

1. **Aprovação humana em ações críticas**
   "A empresa define quais ações exigem confirmação antes da execução, como agendamentos, alterações ou aprovações operacionais."

2. **Auditoria e rastreabilidade**
   "Todas as interações, consultas e ações executadas pelo sistema ficam registradas para acompanhamento e auditoria operacional."

3. **Ambiente separado por empresa**
   "Dados, contexto, conversas e integrações operam de forma independente para cada empresa."

4. **Políticas de execução configuráveis**
   "A empresa define quais processos podem ser automatizados, supervisionados ou executados apenas com aprovação humana."

**Ícones (um por card, linguagem operacional, evitar cadeado/cybersecurity):**
1. Aprovação humana → check/aprovação (ex: balão com check, ou clipboard-check)
2. Auditoria e rastreabilidade → log/atividade (ex: lista com timeline/activity)
3. Ambiente separado por empresa → building/layers (separação de ambientes, não "cofre")
4. Políticas de execução configuráveis → workflow/branching (fluxo com decisão)

**Estilo:** `section-tag` + heading com `gradient-text` no padrão das outras seções (ver `Comparison.astro`, `FAQ.astro` para referência de markup).

### 3. Comparison.astro — remoção

Remove o bloco "Segurança e controle" (linhas ~71-80, o `<div class="mt-10 ...">` com o aviso de segurança), já que a nova seção dedicada o substitui com mais força e evita redundância.

## Fora de escopo

- Mudança de CTA/funil (self-service → consultivo) — frente B, pendente de confirmação
- Landing pages verticais (`/oficinas` etc.) — frente C, escopo maior
- Alteração de preços dos planos
- Menções a LGPD, criptografia, compliance, ISO, SOC2 — evitar por ora (poluiria o marketing principal; usar só com ICP mais enterprise/processo consultivo)
