# Horizontalização operacional: Examples.astro + Segments.astro

## Contexto

A landing page hoje está num meio-termo ruim: não é vertical, mas os exemplos reais (`Examples.astro`) são todos de oficina (Amarok, ordem de serviço, orçamento), e a seção de segmentos (`Segments.astro`) é uma lista superficial de "mercados atendidos" (ícone + nome) sem mostrar nenhum caso operacional concreto.

O objetivo desta mudança é reposicionar a percepção de "bot para oficina" para "camada operacional conversacional aplicável em múltiplos contextos", sem pivotar para landing pages verticais (fora de escopo — decisão estratégica registrada abaixo).

Isso é feito por:
1. Diversificar os exemplos reais em `Examples.astro` (de 3 para 6, reduzindo o peso de oficina de 3/3 para 2/6).
2. Reescrever `Segments.astro` para mostrar "segmento → caso operacional concreto" em vez de "lista de mercados".

## Escopo

### 1. Examples.astro — expandir para 6 exemplos

**Novos tipos de mensagem do cliente:** o componente hoje suporta `text`, `media` (ícone de áudio + imagem combinados, atualmente não usado) e `pdf`. Substituir `media` por dois tipos novos e mais específicos:
- `audio`: ícone de microfone (lucide "mic"): `<path d="M12 1a3 3 0 0 0-3 3v8a3 3 0 0 0 6 0V4a3 3 0 0 0-3-3Z"/><path d="M19 10v2a7 7 0 0 1-14 0v-2"/><line x1="12" y1="19" x2="12" y2="22"/>`
- `image`: ícone de imagem (lucide "image"): `<rect x="3" y="3" width="18" height="18" rx="2" ry="2"/><circle cx="8.5" cy="8.5" r="1.5"/><polyline points="21 15 16 10 5 21"/>`

(`pdf` e `text` mantêm os ícones atuais.)

**Badge de mídia:** cada card recebe um pequeno badge no topo (acima do número/título), estilo pill neutro (`background: rgba(255,255,255,0.04); border: 1px solid rgba(255,255,255,0.08); color: #94a3b8;`), com o ícone do tipo + label: "Texto", "Áudio", "Imagem" ou "PDF". Objetivo: comunicar multimodalidade rapidamente (escaneável), sem aumentar densidade de texto.

**Grid:** `md:grid-cols-3` com 6 itens → 2 linhas de 3 colunas no desktop, 1 coluna no mobile (mesmo padrão de stacking já usado em outras seções — sem carrossel).

**Conteúdo final (ordem final dos 6 cards):**

| # | Título | Vertical | Cliente envia | Tipo | `color` | Bot executa (steps) |
|---|--------|----------|---------------|------|---------|----------------------|
| 1 | Consulta de status | Oficina | "Minha Amarok já ficou pronta?" | `text` | `#6366f1` | 🔍 Localiza o veículo no cadastro / 📋 Consulta a ordem de serviço / ✅ Responde com o status atual |
| 2 | Reagendamento de consulta | Clínica | "Preciso remarcar minha consulta" | `audio` | `#10b981` | 📅 Consulta a agenda disponível / 🔁 Sugere novos horários / 👩‍⚕️ Encaminha confirmação para a equipe |
| 3 | PDF para orçamento | Oficina | "Gostaria de orçamento" (+ PDF anexo) | `pdf` | `#06b6d4` | 📄 Lê o documento enviado / 📝 Resume as informações relevantes / ➡️ Encaminha para análise de orçamento |
| 4 | Acesso e suporte acadêmico | Escola/curso | "Não consegui acessar as aulas" | `text` | `#8b5cf6` | 🎓 Identifica a matrícula do aluno / 📡 Consulta status de acesso / 📚 Envia orientações ou encaminha para suporte acadêmico |
| 5 | Status do equipamento | Assistência técnica | "Meu notebook já ficou pronto?" (+ foto do aparelho) | `image` | `#3b82f6` | 🖼️ Identifica o equipamento pela imagem enviada / 📋 Consulta o status do reparo / 📅 Informa previsão de entrega |
| 6 | Negociação e handoff | Comercial (genérico) | "Vocês fazem desconto para pagamento à vista?" | `text` | `#f59e0b` | 🧠 Identifica pedido de negociação / 💬 Responde dentro do script definido / 👤 Encaminha para atendente humano |

Cards 1, 3 e 6 são os 3 exemplos atuais (1=#01 atual, 3=#03 atual com o último step reescrito, 6=#02 atual recategorizado como "Comercial" — conteúdo igual ao atual). Cards 2, 4, 5 são novos. `number` de cada card passa a ser "01" a "06" na nova ordem da tabela.

### 2. Segments.astro — de "lista de mercados" para "segmento → caso operacional"

**section-tag:** "Operações" (era "Segmentos")

**h2:** "Feito para operações com <span class=\"gradient-text\">atendimento recorrente.</span>" (ajuste do atual "Ideal para qualquer empresa com atendimento recorrente.", removendo o "qualquer empresa" genérico)

**subtítulo (novo):** "Atendimento, consulta, triagem e execução operacional integrados aos sistemas da empresa."

**Cards:** de 8 pills (ícone emoji + nome) para 6 cards no padrão visual do `Security.astro` (`glass-card`, ícone SVG outline + título + descrição, grid `sm:grid-cols-2 lg:grid-cols-3`, classe `reveal`). Remove "Academias e estúdios"; consolida "Cursos e mentorias" + "Escolas e treinamentos" em "Escolas e cursos".

| Segmento | `color` | Ícone (lucide) | Descrição (caso operacional) |
|----------|---------|-----------------|-------------------------------|
| Oficinas e auto centers | `#6366f1` | wrench: `<path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"/>` | Consulta de ordens de serviço, aprovação de orçamento e atualização de status via WhatsApp. |
| Clínicas e consultórios | `#10b981` | calendar: `<rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/>` | Confirmações, reagendamentos e encaminhamento operacional integrados à agenda da clínica. |
| Assistência técnica | `#3b82f6` | monitor: `<rect x="2" y="3" width="20" height="14" rx="2"/><line x1="8" y1="21" x2="16" y2="21"/><line x1="12" y1="17" x2="12" y2="21"/>` | Status de reparo, triagem e comunicação com clientes sem interromper a equipe técnica. |
| Escolas e cursos | `#8b5cf6` | graduation-cap: `<path d="M22 10 12 5 2 10l10 5 10-5Z"/><path d="M6 12v5c3 3 9 3 12 0v-5"/>` | Dúvidas recorrentes, status de acesso a aulas/materiais e encaminhamento para suporte acadêmico. |
| Prestadores de serviço | `#f59e0b` | clipboard-list: `<rect x="8" y="2" width="8" height="4" rx="1" ry="1"/><path d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2"/><path d="M12 11h4"/><path d="M12 16h4"/><path d="M8 11h.01"/><path d="M8 16h.01"/>` | Agendamento, confirmação e acompanhamento de visitas e atendimentos integrados à operação. |
| Imobiliárias | `#06b6d4` | home: `<path d="M3 9 12 2l9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2Z"/><polyline points="9 22 9 12 15 12 15 22"/>` | Consulta de imóveis disponíveis, agendamento de visitas e atualização de status de propostas. |

A paleta de cores é a mesma usada em `Examples.astro` (mesma associação por vertical: Oficinas=indigo, Clínicas=verde, Assistência técnica=azul, Escolas=violeta), reforçando identidade visual cruzada entre as duas seções sem criar "identidade por vertical" isolada.

## Fora de escopo

- Landing pages verticais (`/oficinas`, `/clinicas` etc.) — decisão estratégica: manter posicionamento horizontal operacional, evitar fragmentação de branding/SEO/manutenção neste estágio.
- Mudanças no `Hero.astro` — já está com framing operacional/horizontal adequado ("IA integrada à operação da empresa", "Seu WhatsApp respondendo clientes, consultando sistemas e executando ações"), não precisa de ajuste.
- Páginas leves `/segmentos/oficinas` etc. para expansão semântica de SEO — possível no futuro, não agora.
- Front B (CTA/funil self-service → consultivo) — pendente, será tratado separadamente.

## Notas de implementação

- Manter o padrão visual e classes compartilhadas (`glass-card`, `gradient-text`, `section-tag`, `reveal`) já usados no restante do site.
- Os ícones SVG do `Segments.astro` seguem o padrão do `Security.astro`: `width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75"`, container `w-10 h-10 rounded-lg` com `background: ${color}18`, `border: 1px solid ${color}22`, `color: ${color}`.
- Os ícones de badge em `Examples.astro` usam tamanho pequeno (14x14), consistente com os ícones de "Cliente envia" já existentes no componente.
