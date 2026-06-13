# Hero — mockup do WhatsApp rotativo por vertical

## Contexto

O Hero (`Hero.astro`) já tem um efeito typewriter no subtítulo que cicla por 7 frases ("Para oficinas e auto centers", "Para clínicas e consultórios", etc. — array `phrases`, linhas 191-199). Porém o mockup de WhatsApp ao lado é uma conversa estática, sempre do segmento "oficina" (carro, OS #4872, retirada de veículo).

Isso deixa o Hero enviesado para um único segmento, mesmo após a horizontalização do restante do site (Examples.astro, Segments.astro). O objetivo desta mudança é fazer o mockup acompanhar o typewriter: a cada troca de frase, a conversa no celular troca de cenário para o segmento correspondente, mantendo sempre a mesma "gramática operacional":

1. cliente pergunta sobre algo
2. IA consulta o sistema
3. sistema retorna um registro/contexto (card com destaque)
4. cliente pede uma ação por áudio
5. IA transcreve o áudio
6. IA registra a ação no sistema

Essa repetição estrutural é o que comunica "conversa + operação integrada" — não é um chatbot genérico, é um sistema que consulta e age.

## Escopo

Único arquivo: `src/components/Hero.astro`. Sem novas dependências, sem novos arquivos.

### 1. Dados — array `scenarios`

Adicionar ao `<script>` existente (junto de `phrases`) um array `scenarios` com 7 objetos, no mesmo índice de `phrases`. Cada objeto:

```js
{
  client: string,        // mensagem do cliente (texto)
  bot1: string,           // "Consultando/Localizando ... no sistema..."
  cardLabel: string,       // título do card (ex: "OS #4872 — VEÍCULO")
  cardBody: string,         // 1 frase curta, status em <strong> — "X <strong>status</strong>."
  cardHighlight: string,    // 1 informação de prazo/disponibilidade
  audioDuration: string,    // "0:08" a "0:14", varia por cenário
  transcription: string,    // texto transcrito do áudio do cliente
  bot2: string,             // mensagem final de "ação registrada"
  times: [string, string, string, string, string], // 5 horários (client, bot1, card, transcription, bot2)
}
```

Conteúdo final dos 7 cenários:

**1. Oficinas e auto centers**
- client: "meu carro já ficou pronto?"
- bot1: "Localizando veículo no sistema..."
- cardLabel: "OS #4872 — VEÍCULO"
- cardBody: `Serviço <strong>concluído</strong>.`
- cardHighlight: "Disponível a partir das 08h"
- audioDuration: "0:12"
- transcription: "quero remarcar pra terça-feira"
- bot2: "Pré-agendamento criado para terça-feira. A equipe vai confirmar em breve."
- times: ["09:14","09:14","09:14","09:15","09:15"]

**2. Cursos e mentorias**
- client: "minha inscrição no curso foi confirmada?"
- bot1: "Consultando matrícula no sistema..."
- cardLabel: "MATRÍCULA #1129 — CURSO AVANÇADO"
- cardBody: `Vaga <strong>garantida</strong>.`
- cardHighlight: "Início da turma: segunda-feira"
- audioDuration: "0:09"
- transcription: "quero mudar pro horário da noite"
- bot2: "Solicitação de troca para turma noturna registrada."
- times: ["10:32","10:32","10:32","10:33","10:33"]

**3. Clínicas e consultórios**
- client: "minha consulta tá confirmada?"
- bot1: "Consultando agenda no sistema..."
- cardLabel: "AGENDAMENTO #305 — DR. PAULO"
- cardBody: `Consulta <strong>confirmada</strong>.`
- cardHighlight: "Atendimento às 15h30"
- audioDuration: "0:11"
- transcription: "preciso remarcar pra sexta"
- bot2: "Pré-agendamento criado para sexta-feira. Aguardando confirmação da clínica."
- times: ["14:05","14:05","14:05","14:06","14:06"]

**4. Imobiliárias e incorporadoras**
- client: "o apartamento 302 ainda tá disponível?"
- bot1: "Consultando disponibilidade no sistema..."
- cardLabel: "UNIDADE 302 — RESIDENCIAL AURORA"
- cardBody: `Unidade <strong>disponível</strong>.`
- cardHighlight: "Próxima visita: sábado às 10h"
- audioDuration: "0:14"
- transcription: "quero agendar a visita pra sábado"
- bot2: "Solicitação de visita registrada. Corretor responsável notificado."
- times: ["11:20","11:20","11:20","11:21","11:21"]

**5. Assistência técnica**
- client: "meu celular já foi avaliado?"
- bot1: "Consultando status do reparo..."
- cardLabel: "OS #2231 — TROCA DE TELA"
- cardBody: `Orçamento <strong>aprovado</strong>.`
- cardHighlight: "Pronto até quinta-feira"
- audioDuration: "0:08"
- transcription: "pode autorizar o reparo"
- bot2: "Reparo autorizado. Equipe notificada para iniciar o serviço."
- times: ["16:48","16:48","16:48","16:49","16:49"]

**6. Academias e estúdios**
- client: "posso remarcar minha avaliação física?"
- bot1: "Consultando agenda de avaliações..."
- cardLabel: "AVALIAÇÃO FÍSICA — PERSONAL RAFAEL"
- cardBody: `Horário <strong>disponível</strong>.`
- cardHighlight: "Quinta-feira às 18h"
- audioDuration: "0:10"
- transcription: "pode marcar nesse horário"
- bot2: "Reagendamento registrado para quinta-feira às 18h."
- times: ["18:02","18:02","18:02","18:03","18:03"]

**7. Escolas e treinamentos**
- client: "meu filho já tá matriculado pro próximo semestre?"
- bot1: "Consultando matrícula no sistema..."
- cardLabel: "MATRÍCULA #884 — TURMA B"
- cardBody: `Matrícula <strong>confirmada</strong>.`
- cardHighlight: "Início das aulas: 03/02"
- audioDuration: "0:13"
- transcription: "preciso atualizar o endereço cadastrado"
- bot2: "Atualização de cadastro registrada na secretaria."
- times: ["08:15","08:15","08:15","08:16","08:16"]

Regra de consistência aplicada a todos os 7: `cardBody` é sempre uma frase curta "Sujeito + status em `<strong>`", e `cardHighlight` carrega exatamente um dado de prazo/disponibilidade. Isso garante leitura rápida e visual de "dashboard", e evita que um cenário pareça mais/menos detalhado que outro.

### 2. Renderização — helpers JS pequenos

Em vez de um template literal gigante, criar funções pequenas no `<script>`, uma por tipo de bolha:

- `renderClientBubble(text, time)` — bolha do cliente (fundo `#1e3a5f`, alinhada à direita, com ícone de "lido").
- `renderBotBubble(text, time)` — bolha do bot (fundo `#1a1f2e`, avatar "B" com gradiente).
- `renderStatusCard(label, body, highlight, time)` — o card de "registro encontrado" (borda azul, label uppercase, body, destaque verde).
- `renderAudioBubble(duration)` — bolha de áudio do cliente (waveform + duração, sem timestamp).
- `renderScenario(s)` — monta a sequência completa (client → bot1 → card → audio → transcription(bot) → bot2) chamando os helpers acima, retornando o HTML completo da área de chat.

O markup/estilos gerados devem ser equivalentes ao HTML estático atual (linhas 98-160), só parametrizados. Manter os helpers pequenos facilita ajustes futuros de espaçamento, avatar, timestamp ou tipografia sem precisar tocar em strings gigantes.

### 3. Render inicial e fallback sem JS

O markup estático do Astro continua sendo o cenário 1 (oficina) — funciona como fallback sem JS e como o HTML do primeiro paint.

No `<script>`, ao carregar:
- `i` começa em um índice aleatório: `Math.floor(Math.random() * phrases.length)`. Esse mesmo `i` é a única fonte de verdade tanto para `phrases[i]` (o typewriter sempre começa do zero, digitando essa frase) quanto para `scenarios[i]` (o mockup) — os dois nascem alinhados antes do primeiro render, nunca há um frame com frase e mockup de índices diferentes.
- Se `i !== 0`, o conteúdo do chat é substituído por `renderScenario(scenarios[i])` imediatamente (sem fade, é o estado inicial).

### 4. Sincronização e crossfade

No `tick()` do typewriter, o ponto em que `i` é incrementado (`j === 0` após apagar a frase) passa a também disparar a troca de cenário do mockup, com um pequeno deslocamento de tempo para não parecer perfeitamente sincronizado/robótico:

- `setTimeout(() => swapScenario(i), 150)` — 150ms de offset entre a troca do texto e a troca do mockup.
- `swapScenario(newIndex)`:
  1. Container do chat recebe `opacity: 0` (transição CSS `opacity 0.25s ease`, já declarada no `<style>`).
  2. Após 250ms, `innerHTML` do container é substituído por `renderScenario(scenarios[newIndex])`.
  3. `opacity` volta para `1`.
- As novas mensagens **não** reproduzem o stagger de entrada (`.wpp-msg` com delays 0.4s-6.0s) — aparecem todas juntas no fade-in do container. Esse stagger continua existindo apenas no carregamento inicial da página (cenário renderizado pelo Astro).

### 5. Timing do typewriter

A pausa após digitar a frase completa aumenta de `2200ms` para `2600ms`, dando ~4.5-5.5s de "tempo de leitura" por cenário (frases mais longas naturalmente ficam um pouco mais, frases curtas um pouco menos — dentro da faixa de 4-5s pedida).

### 6. Layout estável (sem layout shift)

- A área de chat (`.p-4.space-y-3`, hoje `min-height: 380px`) passa a usar `min-height` + `max-height` (em vez de uma altura fixa única), com `overflow: hidden` — evita que o frame do celular "pule" de tamanho entre cenários, mas sem travar um valor "mágico" desktop-only que quebre em mobile/zoom/fontes diferentes. Valores ajustados visualmente durante a implementação (referência inicial: `min-height: 380px; max-height: 460px`), testando especialmente em mobile real (Safari iOS) com a página parcialmente rolada — telas de desktop tendem a mascarar overflow que aparece só em mobile.
- `cardLabel` (o título uppercase do card, ex: "AVALIAÇÃO FÍSICA — PERSONAL RAFAEL" vs "OS #4872 — VEÍCULO") recebe `min-height` para 2 linhas **e** `-webkit-line-clamp: 2` (`display: -webkit-box; -webkit-box-orient: vertical; overflow: hidden`) — garante que nem o comprimento atual nem um label maior no futuro estourem o card.

### 7. Variação orgânica no texto

Para evitar que os 7 cenários pareçam "templated" demais, nem todo `cardBody`/`bot2` termina em ponto final ou segue exatamente a mesma cadência. Ajustes:
- Cenário 2 (Cursos): `cardBody` → `Vaga <strong>garantida</strong>` (sem ponto final).
- Cenário 4 (Imobiliárias): `bot2` → `Solicitação de visita registrada. Corretor responsável notificado` (sem ponto final).
- Cenário 7 (Escolas): `cardBody` → `Matrícula <strong>confirmada</strong>` (sem ponto final).

Os demais cenários (1, 3, 5, 6) mantêm pontuação completa como especificado na seção 1 — a variação é sutil, não um padrão novo a seguir.

## Fora de escopo

- Mockup de dashboard/painel operacional, GIF/vídeo do fluxo completo — itens já sinalizados pelo usuário como trabalho futuro, não fazem parte desta mudança.
- Alterações de preços/planos.
- Revisão visual geral do Hero (orbs, glow, gradientes) — à medida que o produto amadurece, a estética pode evoluir para um tom mais "software operacional" e menos "startup neon", mas isso é uma discussão de identidade visual separada, não faz parte desta mudança.

## Notas de implementação

- Sem novas dependências, sem estado complexo, sem framework JS — o Hero deve continuar leve.
- O fallback estático (Astro, cenário 1) continua sendo o HTML real do primeiro paint — bom para SEO/CLS. O JS não deve deixar o Hero "vazio até hidratar".
