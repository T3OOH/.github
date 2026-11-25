# Erros do registro da Gestão — análise e correções

Fonte: **Gestão > Registros** (`/dashboard/registros`, tabela `audit_logs`), últimos 365 dias, lidos em 2026-11-25.
Total: **155 erros** e **157 avisos**, agrupados em 35 tipos. O log local do T3 Gestor
(`%USERPROFILE%\.t3-gestor\sessao.log`) foi conferido e não tem erros (só login, renovação e encerramento).

Situação: **Corrigido** = código mudado neste trabalho; **Resolvido** = já não acontece (causa removida antes);
**Ruído** = vem de fora do site (navegador do Instagram, extensão) e passa a ser ignorado; **Esperado** = comportamento certo.

## Resumo

| # | Erro | Qtde | Período | Situação |
| --- | --- | ---: | --- | --- |
| 1 | `EMAXCONNSESSION max clients reached` em `GET /meetings` | 114 | 04–06/11 | Resolvido (VPS) |
| 2 | Tabela `public.meetings` não existe (`GET`/`POST /meetings`) | 12 | 04/11 | Resolvido |
| 3 | "Server has closed the connection" (`/banners`, `/solution/portfolio`) | 2 | 04/11 | Resolvido (VPS) |
| 4 | Avisos "direto no banco" feitos pela própria API (`Supavisor · postgres`) | 104 | 30/10–18/11 | Resolvido (VPS) |
| 5 | Avisos "direto no banco" de manutenção (painel do Supabase, `psql`, `mgmt-api`) | 53 | 05–19/11 | Esperado |
| 6 | `Error invoking postMessage: Java object is gone` | 8 | 06–24/11 | Ruído → **Corrigido** (filtro) |
| 7 | `window.ethereum.selectedAddress = undefined` | 3 | 06–08/11 | Ruído → **Corrigido** (filtro) |
| 8 | `Failed to fetch dynamically imported module` (Home, Register) | 5 | 05–20/11 | **Corrigido** |
| 9 | `'text/html' is not a valid JavaScript MIME type` / `Cross-origin script load denied` | 2 | 05–06/11 | **Corrigido** (mesma causa do 8) |
| 10 | `Cannot read properties of undefined (reading '_leaflet_pos')` | 1 | 03/11 | Resolvido (Frontend #43) |
| 11 | `Erro 500 em POST /health: Unexpected end of JSON input` | 1 | 06/11 | **Corrigido** |
| 12 | `Erro 503` em `GET /downloads/desktop` e `POST /downloads/launcher` | 7 | 04/11 | Resolvido (configuração) |
| 13 | "Senha errada no download do launcher" | 2 | 04/11 | Esperado |

## Detalhes

### 1. `EMAXCONNSESSION` em `GET /meetings` (114)
- **Causa:** a API no Render abria até 20 conexões (`DATABASE_POOL_MAX`, padrão 20), mas o pooler do Supabase em
  modo sessão aceitava só 15 (`pool_size: 15`). Acima disso, a consulta falhava. Quase todos os registros caíram em
  `/meetings` porque é a chamada mais frequente: cada T3 Gestor aberto recarrega a lista de reuniões a cada 2 min
  e a cada evento do socket (`ServicoDeReunioes.iniciar`).
- **Correção:** a API saiu do Render e do pooler em 2026-11-19 e conecta direto ao Postgres da VPS. Nenhum registro
  desde 06/11. Se um dia voltar a ter pooler, manter `DATABASE_POOL_MAX` abaixo do `pool_size` dele.

### 2. Tabela `meetings` não existe (12)
- **Causa:** o código de Reuniões foi publicado antes da migração do Prisma ser aplicada no banco.
- **Correção:** migração aplicada no mesmo dia. A regra "aplicar a migração **antes** de publicar o código" já está no `CONTEXTO.md`.

### 3. "Server has closed the connection" (2)
- **Causa:** o pooler do Supabase fechou conexões no dia 04/11 (mesma época do item 1).
- **Correção:** sem pooler desde a migração para a VPS.

### 4. Avisos "direto no banco" que eram da API (104)
`crm_deal_items` (37), `users` (34, preferências de tema e cor), `panels` (21), `order_items` (2), `system_settings` (1).
- **Causa:** o gatilho `audit_direct_change` ignora o que vem com `application_name = 't3-api'`. O Supavisor (pooler do
  Supabase) trocava o nome para `Supavisor`, então alterações feitas pela API apareciam como feitas "direto no banco".
  Itens criados dentro de outro registro (itens do negócio e do pedido) nunca tinham a cópia apagada pela limpeza da API.
- **Correção:** na VPS a API conecta direto e o nome `t3-api` chega ao gatilho. Nenhum aviso `Supavisor` depois de 18/11.

### 5. Avisos "direto no banco" de manutenção (53)
Exclusões de testes pelo painel do Supabase (05/11), foco das fotos (06/11) e a migração para a VPS
(`psql`, 19/11: reescrita das URLs das fotos em `panels`, `institutional`, `solution_portfolio`).
- **Correção:** nenhuma; é para isso que o gatilho existe. Registros conferidos, todos esperados.

### 6. `Java object is gone` (8)
- **Causa:** script do navegador interno do Instagram no Android (`iabjs://navigation_performance_logger_android`),
  que falha quando a pessoa fecha o navegador do app. Não é código do site.
- **Correção:** `frontend/src/lib/errorReporting.ts` passa a ignorar erros cuja pilha vem de `iabjs://`
  e a mensagem `Java object is gone`.

### 7. `window.ethereum.selectedAddress` (3)
- **Causa:** extensão de carteira de criptomoeda no Safari/iOS tentando escrever em `window.ethereum`. O site não usa isso.
- **Correção:** ignorado no mesmo filtro.

### 8 e 9. Arquivo de versão antiga do site (7)
- **Causa:** página aberta antes de um deploy pede um arquivo (`Home-xxxx.js`) que não existe mais. O `ErrorBoundary`
  recarregava a página **uma vez por aba**: a marca no `sessionStorage` nunca era apagada, então no deploy seguinte
  a pessoa via a tela "Algo deu errado". E o recarregamento podia reaproveitar o HTML antigo do cache do navegador
  interno do Instagram/Facebook (os casos de 16/11 e 20/11 vieram de links com `fbclid`).
  O item 9 é o mesmo problema no domínio antigo `t3ooh.com`: o arquivo inexistente voltava como HTML.
- **Correção:** recarregamento automático guardado com horário (nova tentativa depois de 1 min, não uma vez por aba),
  com parâmetro na URL para furar o cache, que some da barra logo depois de carregar; o evento `vite:preloadError`
  (falha ao pré-carregar um arquivo) também recarrega. O erro só é registrado se o recarregamento não resolver.

### 10. `_leaflet_pos` (1)
- **Causa:** no Leaflet 1.9, toda animação de zoom agenda o fim com um `setTimeout` de 250 ms que o `map.remove()`
  não cancela. Ao salvar o painel e voltar para a lista no meio da animação do mapa do cadastro, o fim chegava
  com o mapa já destruído.
- **Correção:** já feita no mesmo dia, depois deste registro: `src/features/map/leaflet-guards.ts` (Frontend #43) ignora
  o fim atrasado quando o mapa não existe mais. Nenhuma ocorrência depois de 03/11.

### 11. `POST /health` com JSON vazio → 500 (1)
- **Causa:** algum monitor externo mandou `Content-Type: application/json` sem corpo. O Express não conseguiu ler o JSON
  e o tratador de erros do Backend respondia 500 e gravava erro. O BackendJS já respondia 400.
- **Correção:** o tratador de erros do Backend (`src/app.ts`) responde **400 "JSON inválido."** e **413** para corpo
  grande demais, como o BackendJS, sem gravar erro.

### 12. `503` nos downloads (7)
- **Causa:** em 04/11, `LAUNCHER_DOWNLOAD_PASSWORD` e o acesso aos Releases ainda não estavam configurados na API.
  O 503 é a resposta prevista para isso.
- **Correção:** configurado no mesmo dia; nenhum 503 depois.

### 13. Senha errada no download do launcher (2)
- Aviso de segurança previsto (tentativa com senha errada). Nada a corrigir.

## Onde ficaram as correções

- Frontend: `src/lib/errorReporting.ts` e `src/components/ErrorBoundary.tsx`.
- Backend: `src/app.ts` (tratador de erros). BackendJS já estava certo.
