# Contexto para o Claude (continuar em qualquer computador)

> **Claude:** leia este arquivo inteiro antes de qualquer trabalho na T3 OOH / T3 LED e
> **atualize-o ao fim de cada tarefa** (seção "Em andamento" e "Histórico recente"), com
> commit e PR neste repositório. É assim que o trabalho continua do PC para o notebook e vice-versa.
> A memória do Claude fica só no computador onde ele roda; o que importa para continuar está aqui.

Atualizado em: 2026-11-25 (PC do Felipe).

## Em andamento

Nada em andamento. Última entrega: erros do registro (ERROS-GESTOR.md, Frontend #81, Backend #59 com deploy
da API pelo `deploy-api.sh`). Antes: segurança e LGPD (2026-11-25), tudo publicado: API na VPS (commit e333566,
`/health` ok), Frontend #79 e #80 no ar, ApiDocs #15 e BackendJS #31 mesclados. No BackendJS ainda faltam
exportar/excluir conta e a rotina de retenção (só no Backend).

## Pendências e perguntas abertas

- **Deploy do Frontend pela Hostinger**: a conexão com o GitHub é uma só para a conta inteira. O app da Hostinger
  precisa ter acesso à organização T3OOH (github.com/settings/installations > Hostinger). Nunca usar "Trocar GitHub
  conta" no hPanel: substitui a conexão e para as implantações dos outros sites.
- **Desligar Render e Supabase cloud**: o serviço `t3-led-ooh-plataforma` está **suspenso** (não apagado) e o projeto
  Supabase `obpzodeqatopkhatqwmk` está intacto, para rollback. Só apagar com OK do usuário, depois de alguns dias estável.

- **IA T3 sem a chave**: o balão está no ar só com respostas prontas e encaminhamento. Para a IA responder perguntas
  livres, o usuário cria uma chave em console.anthropic.com e põe `ANTHROPIC_API_KEY` em `/opt/t3led/api.env` na VPS.
  `SUPORTE_TECNICO_EMAIL` (mesmo arquivo) define para onde vão os pedidos técnicos (hoje vão para os ADMIN).
  A chamada real ao Claude ainda não foi testada (nenhuma chave disponível no PC).
- **Custos** (`CUSTOS.md`): ~R$ 170-200/mês hoje. Falta confirmar o plano da Hostinger, o nº de caixas de e-mail
  e o plano do Resend. Hospedagem vence 2026-12-05 e o teste do e-mail acaba ~04/12. Render e Supabase saem da conta
  depois de desligados (API, banco e Storage agora na VPS).
- **Webmail com a identidade T3 LED**: a Hostinger não deixa personalizar. Opções oferecidas ao usuário:
  (a) webmail próprio em webmail.t3led.com.br (Roundcube/SnappyMail) ou (b) aba de e-mails no T3 Gestor. Sem resposta.
- `RESEND_FROM` não está definido em produção: vale o remetente padrão "T3 LED".
- T3 Gestor (AppT3): tela Indicadores e SLA e menu em grupos estão na `main`, mas só chegam aos usuários
  numa nova versão do app (última publicada: v1.3.0). A tela nova ainda não passou pela auditoria visual.
- Frontend: o `main` foi reescrito em 2026-11-06 (mesmo conteúdo, outros SHAs). Sempre criar branch do
  `origin/main` recém-buscado; em conflito estranho, compare os tips e use cherry-pick numa branch nova.

## Repositórios (organização github.com/T3OOH)

| Pasta local (`t3ooh/…`) | Repositório | O que é | Produção |
| --- | --- | --- | --- |
| `backend` | Backend | API em TypeScript + Express + Prisma | VPS (T3Led) → **api.t3led.com.br** |
| `backend-js` | BackendJS | Mesma API em NestJS (JS) + Rust; espelho do Backend | ainda não publicado |
| `frontend` | Frontend | Site React + Vite (site, Gestão, CRM) | Hostinger → **t3led.com.br** |
| `desktop` | AppT3 | T3 Gestor (JavaFX) + T3 Launcher + `relatorios` (Python) | GitHub Releases (.msi) |
| `api-docs` | ApiDocs | Documentação da API (estático) | Hostinger → docs.t3led.com.br |
| `ambiente` | Ambiente | Docker do ambiente local (`scripts/preparar.ps1`) | — |
| `.github` | .github | MAPA.md, este CONTEXTO.md, workflow reutilizável | — |

Produção (desde 2026-11-19): VPS 200.141.21.97, projeto **T3Led** em `/opt/t3led` (Postgres + Storage do Supabase
self-hosted + a API), versionado em FBR-Dev-Technology/Backend `infra/t3led` (roteiro em `infra/t3led/migracao/README.md`).
`api.t3led.com.br` serve a API e o Storage (`/storage/v1`); segredos da API em `/opt/t3led/api.env`.
Banco pelo painel painel.fbrdev.com.br (Banco de dados > T3Led). Local: Docker em 127.0.0.1:5433 (t3/t3dev, banco t3ooh).
Mapa completo do que existe: [MAPA.md](MAPA.md).

## Regras combinadas com o usuário (valem sempre)

- Responder sempre em **português do Brasil**.
- **Merge automático autorizado**: PRs que o Claude abrir na T3OOH podem ser mesclados (squash) com CI verde,
  apagando a branch. Merge na `main` do Frontend/ApiDocs **publica em produção** (Hostinger; workflow `Produção` confere).
  **Backend não publica sozinho desde a migração**: depois do merge, rodar `sh infra/t3led/deploy-api.sh <clone do Backend>`
  (repo FBR-Dev-Technology/Backend); o workflow `Produção` do Backend confere o commit em `/health`.
- **Nunca force push.** Nunca contornar bloqueio do modo automático: explicar e pedir.
- Nunca digitar senhas, criar contas ou resolver CAPTCHA pelo usuário; nunca registrar tokens/senhas
  (`HOSTINGER_API_TOKEN` e demais só em `/opt/t3led/api.env` na VPS).
- `backend/.env` e `backend-js/.env` do PC ainda apontam para o Supabase cloud (antigo, sem uso em produção desde
  2026-11-19): não criar dados com eles. Testar no banco local (Docker) ou no `demo/AuditoriaTelas` do AppT3.
- **Migração do Prisma**: o deploy não roda migração. Aplicar no T3Led (Postgres sem porta pública: pelo container
  `t3led-api` na VPS ou pelo SQL do painel) **antes** de publicar o código que usa a coluna nova.- Rota nova na API: mudar **Backend e BackendJS** (código igual) e `api-docs/assets/endpoints.js`.
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

- 2026-11-25 — **Revisão de segurança + LGPD** (sem achado crítico/alto). Backend #58: chat do CRM restrito à carteira
  do COMERCIAL (antes via clientes de outros vendedores), socket recusa sessão encerrada, `mediaUrl` só https, logout
  confere assinatura, login sem enumeração por tempo, `GET /profile/export` e `DELETE /profile` (anonimiza; usa
  `prismaSemAuditoria` para não guardar o "antes"), retenção diária (`services/retencao.service.ts`: eventos de
  segurança 1 ano, analytics sem IP e até 25 meses, IP de contatos 6 meses). Frontend #79: estatística só após
  "Entendi", "Preferências de cookies" no rodapé, `AvisoPrivacidade` nos formulários, aviso de IA no chat, política com
  CNPJ, terceiros, transferência internacional e prazos (revisão jurídica recomendada). Frontend #80 (publicado):
  painel sem foto ou com link quebrado mostra a logo da T3 em fundo preto ("Imagem ainda não adicionada").
  Ficaram de fora: refresh token em localStorage (ideal: cookie HttpOnly), CSP de scripts e HSTS no `.htaccess`,
  `/crm/site-clients` entrega CPF/CNPJ de todos os clientes ao COMERCIAL (regra de negócio), máscara de CPF/telefone
  no `audit_logs`, ROPA/RIPD/DPAs (jurídico). RLS conferido só no Supabase antigo; o Postgres da VPS não foi revisado.

- 2026-11-19 — **Migração Supabase cloud + Render → VPS (T3Led)**. Cópia conferida linha a linha (contagem + md5
  por tabela, estrutura, 72 objetos / 24,3 MB do Storage), recarga final na janela com o Render suspenso, 16 URLs de
  foto reescritas de `*.supabase.co` para `api.t3led.com.br/storage/v1`, DNS `api` trocado de CNAME (Render) para
  A 200.141.21.97. Miniaturas (`/render/image`) por imgproxy, iguais às do cloud. Frontend #76: `thumb()` aceita
  `api.t3led.com.br`. O deploy da Hostinger estava sem GitHub desde 2026-11-06; o usuário deu ao app da Hostinger
  acesso à T3OOH, o site foi reconectado a T3OOH/Frontend e republicado (miniaturas conferidas no /mapa).

- 2026-11-17 — **Levantamento de custos mensais** em `CUSTOS.md` (Supabase Pro verificado; Render, Vercel e GitHub
  grátis; Hostinger estimada). `/graphify` ficou incompleto: AST pronto em `graphify-out/`, faltam os 2 lotes de docs.

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
