# Contexto para o Claude (continuar em qualquer computador)

> **Claude:** leia este arquivo inteiro antes de qualquer trabalho na T3 OOH / T3 LED e
> **atualize-o ao fim de cada tarefa** (seção "Em andamento" e "Histórico recente"), com
> commit e PR neste repositório. É assim que o trabalho continua do PC para o notebook e vice-versa.
> A memória do Claude fica só no computador onde ele roda; o que importa para continuar está aqui.

Atualizado em: 2026-11-06, fim da tarde (PC do Felipe).

## Em andamento

Nada em andamento. Última entrega: balão IA T3 (ver Histórico). Falta só configurar as variáveis no Render (Pendências).

## Pendências e perguntas abertas

- **IA T3 sem a chave**: o balão está no ar só com respostas prontas e encaminhamento. Para a IA responder perguntas
  livres, o usuário cria uma chave em console.anthropic.com e põe `ANTHROPIC_API_KEY` no Render (serviço da API).
  `SUPORTE_TECNICO_EMAIL` no Render define para onde vão os pedidos técnicos (hoje vão para os ADMIN).
  A chamada real ao Claude ainda não foi testada (nenhuma chave disponível no PC).
- **Webmail com a identidade T3 LED**: a Hostinger não deixa personalizar. Opções oferecidas ao usuário:
  (a) webmail próprio em webmail.t3led.com.br (Roundcube/SnappyMail) ou (b) aba de e-mails no T3 Gestor. Sem resposta.
- `RESEND_FROM` no Render: se tiver o nome antigo, sobrepõe o remetente padrão "T3 LED".
- T3 Gestor (AppT3): tela Indicadores e SLA e menu em grupos estão na `main`, mas só chegam aos usuários
  numa nova versão do app (última publicada: v1.3.0). A tela nova ainda não passou pela auditoria visual.
- Frontend: o `main` foi reescrito em 2026-11-06 (mesmo conteúdo, outros SHAs). Sempre criar branch do
  `origin/main` recém-buscado; em conflito estranho, compare os tips e use cherry-pick numa branch nova.

## Repositórios (organização github.com/T3OOH)

| Pasta local (`t3ooh/…`) | Repositório | O que é | Produção |
| --- | --- | --- | --- |
| `backend` | Backend | API em TypeScript + Express + Prisma | Render → **api.t3led.com.br** |
| `backend-js` | BackendJS | Mesma API em NestJS (JS) + Rust; espelho do Backend | ainda não publicado |
| `frontend` | Frontend | Site React + Vite (site, Gestão, CRM) | Hostinger → **t3led.com.br** |
| `desktop` | AppT3 | T3 Gestor (JavaFX) + T3 Launcher + `relatorios` (Python) | GitHub Releases (.msi) |
| `api-docs` | ApiDocs | Documentação da API (estático) | Hostinger → docs.t3led.com.br |
| `ambiente` | Ambiente | Docker do ambiente local (`scripts/preparar.ps1`) | — |
| `.github` | .github | MAPA.md, este CONTEXTO.md, workflow reutilizável | — |

Banco: Supabase (Postgres + Storage, projeto `obpzodeqatopkhatqwmk`). Local: Docker em 127.0.0.1:5433 (t3/t3dev, banco t3ooh).
Mapa completo do que existe: [MAPA.md](MAPA.md).

## Regras combinadas com o usuário (valem sempre)

- Responder sempre em **português do Brasil**.
- **Merge automático autorizado**: PRs que o Claude abrir na T3OOH podem ser mesclados (squash) com CI verde,
  apagando a branch. Merge na `main` do Backend/Frontend/ApiDocs **publica em produção** (workflow `Produção` confere).
- **Nunca force push.** Nunca contornar bloqueio do modo automático: explicar e pedir.
- Nunca digitar senhas, criar contas ou resolver CAPTCHA pelo usuário; nunca registrar tokens/senhas
  (`HOSTINGER_API_TOKEN`, `LAUNCHER_DOWNLOAD_PASSWORD` só no Render).
- `backend/.env` e `backend-js/.env` do PC apontam para o banco de **produção**: não criar dados com eles.
  Testar no banco local (Docker) ou no `demo/AuditoriaTelas` do AppT3.
- **Migração do Prisma**: o Render não roda migração. Aplicar com `npx prisma migrate deploy` (no `backend/`, com o
  `.env` de produção) **antes** de mesclar o código que usa a coluna nova (o Prisma lê todas as colunas).
- Rota nova na API: mudar **Backend e BackendJS** (código igual) e `api-docs/assets/endpoints.js`.
- Rota nova no React: incluir em `frontend/public/.htaccess`, senão vira 404 em produção.
- Telas do T3 Gestor: **sem texto cortado e sem barra de rolagem** (listas em páginas, telas longas em abas).
- Velocidade: home 100 no Lighthouse de celular. Não puxar libs pesadas para a home (TensorFlow só por import dinâmico).
- Cidade/UF sempre pela lista do IBGE (`CidadeInput` no site, `CampoCidade` no app, `padronizarLocal` na API).
- Schema de **edição** nunca herda `.default()` do cadastro (o `images: .default([])` já apagou as fotos dos 12
  painéis; restaurado do `https://t3led.com.br/panels.json`, que é a cópia de segurança dos painéis).
- Commits e PRs em português. Commit termina com `Co-Authored-By: Claude …`; PR termina com
  "🤖 Generated with [Claude Code](https://claude.com/claude-code)".

## Ferramentas e particularidades do PC

- Antivírus inspeciona HTTPS: `git -c http.sslBackend=schannel …`, `curl --ssl-no-revoke`,
  `NODE_EXTRA_CA_CERTS=<ambiente>/certificados/ca-extra.pem` para o npm.
- Node 24 no PC; repositórios pedem Node 22. Rust em `D:\Rust`; Maven offline em `D:\Tools\m2repo`; JDK 26.
- Merge via `gh api -X PUT repos/T3OOH/<repo>/pulls/<n>/merge -f merge_method=squash`.

## Histórico recente (mais novo primeiro)

- 2026-11-06 — **Chat IA T3 corrigido** (Frontend #73): o "Fechar" não funcionava (atributo `hidden` perdia para a
  classe `flex`); o balão foi para dentro do `MainLayout` (abaixo do cabeçalho z 500, por causa do `isolate`), o
  Leaflet ganhou `z-0`, posição calculada em `posicaoBalao` (no /mapa acima do zoom e da faixa de cards) e o chat
  aberto não cobre cabeçalho nem "Tela cheia". Merge do main na branch `fix/login-cadastro-sem-rolagem` (PR #54, verde).
- 2026-11-06 — **IA T3** (balão de dúvidas no site todo, menos Gestão/CRM/login): 6 caixas de assunto (com "Outro"),
  respostas prontas com dados do banco, pergunta livre no Claude `claude-opus-5-5` (esforço baixo, JSON, cache do
  contexto, `fallbacks: "default"`), encaminhamento: comercial = lead sem dono na fila do CRM; gestão = contato +
  e-mail ADMIN/MANAGER; técnico = contato + e-mail `SUPORTE_TECNICO_EMAIL`. Rotas `/assistente` (Backend #56,
  BackendJS #26, Frontend #72, ApiDocs #14). Código igual em `backend/src/lib/assistente.ts` e `backend-js/src/lib/assistente.js`.
- 2026-11-06 — **Enquadrar capa**: na foto de capa (cadastro e edição) o usuário arrasta um retângulo; os cards com
  moldura mostram o recorte (`imageFocus[url].corte`, `estiloFoco(..., 'recorte')` com CSS `scale`/`translate`).
  **Correção**: `thumb()` usa `resize=contain` (o Supabase cortava as laterais: 1080x1280 virava tira 480x1280).
  Botão "Refazer foco automático" na lista de Painéis; rodado em produção: 11 automáticos recalculados, 27 manuais
  do usuário intactos, 38 fotos iguais (Backend #55, BackendJS #25, Frontend #71, ApiDocs #13).
- 2026-11-06 — **Caixa de contexto** (este arquivo) + `CLAUDE.md` na raiz instalado pelo `ambiente/scripts/preparar`.
- 2026-11-06 — **Link do Google Maps** no cadastro de painéis: `GET /localidades/google-maps` (gestão) segue o link
  curto do Compartilhar só em domínios do Google e prefere o marcador `!3d!4d` (Backend #54, BackendJS #24, Frontend #70).
- 2026-11-06 — **Pino do telão** na edição: `imageFocus[url].pino`, a IA procura o telão em volta (Frontend #69, Backend #53).
- 2026-11-06 — **Incidente**: "Focar fotos" apagou as fotos (edição parcial com `.default([])`); corrigido (Backend #52,
  BackendJS #22) e restaurado. Backup em `ambiente/restauracao-fotos` (só no PC).
- 2026-11-06 — **Indicadores e SLA** (KPIs, SLA de 7 processos, lead time, tempo por comercial; `crm_deal_events` por
  gatilho no banco), menu em grupos no site e no T3 Gestor, **foco automático das fotos** (TF.js COCO-SSD + detector
  de telão) e botão "Focar fotos" (Backend #51, BackendJS #21, Frontend #67, ApiDocs #10, AppT3 #26).
- 2026-11-06 — Cards do /mapa voltaram ao formato anterior (Frontend #65, #66).
- 2026-11-05/06 — Velocidade no celular (home 100, mapa ~91–98), SEO "t3led", Search Console/Bing, MCP público,
  vulnerabilidades da Hostinger, cidade/UF pelo IBGE, e-mails com a identidade T3 LED.
