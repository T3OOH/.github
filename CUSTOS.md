# Custos mensais do sistema T3 LED / T3 OOH

Levantamento de 2026-11-17. Dólar de referência: **R$ 5,15** (set/2026; conferir o PTAX do dia).
"Verificado" = lido na conta do serviço. "Estimado" = preço público do plano provável, falta confirmar na fatura.

## Hoje

| Serviço | Para que serve | Plano | R$/mês | Status |
|---|---|---|---|---|
| Supabase (org "T3 Led") | Banco Postgres + fotos (Storage) | Pro, US$ 25 (inclui US$ 10 de compute, cobre a Micro) | **~129** | Verificado |
| Hostinger – hospedagem | Site t3led.com.br e docs | Plano pago, vence 2026-12-05 | **~24 a 49** | Estimado (plano exato não lido) |
| Hostinger – e-mail | Caixas @t3led.com.br | Business Starter (teste desde 04/11, renova anual) | **~2,50 a 5 por caixa** | Estimado (nº de caixas não lido) |
| Domínio t3led.com.br | | registro.br, R$ 40/ano | **~3,33** | Estimado |
| Domínio t3ooh.com | Redireciona para t3led.com.br | .com, ~US$ 10-15/ano | **~4 a 6** | Estimado |
| Render | API api.t3led.com.br | Free (dorme após 15 min, Oregon) | 0 | Verificado |
| Vercel (FBR_Dev) | Só o redirecionamento do t3ooh.com | Hobby | 0 | Verificado (sem cobranças) |
| GitHub (org T3OOH) | Código, CI, releases do T3 Gestor | Free | 0 | Verificado |
| Resend | E-mails do sistema | Free (3.000/mês, 100/dia) | 0 | Provável, conferir painel |
| Anthropic (IA T3) | Perguntas livres no chat | Sem chave ainda | 0 | Verificado |
| Google login, reCAPTCHA, STUN do Google, OpenStreetMap | | Gratuitos | 0 | |

**Total fixo hoje: ~R$ 170 a 200 por mês** (Supabase é ~70% disso).

## Custos variáveis e próximos

| Item | Quanto | Quando entra |
|---|---|---|
| IA T3 (Claude Opus 5.5) | ~US$ 0,017 por mensagem = **~R$ 88 a cada 1.000 mensagens** | Ao pôr `ANTHROPIC_API_KEY` no Render |
| Render Starter (API sem dormir) | US$ 7 = **~R$ 36/mês** | Recomendado: hoje a 1ª visita após 15 min espera ~50 s |
| Resend Pro | US$ 20 = **~R$ 103/mês** (50 mil e-mails) | Se passar de 100 e-mails/dia ou o Free não aceitar 2 domínios |
| Supabase acima do Pro | US$ 0,125/GB de banco acima de 8 GB; egress acima de 250 GB | Com muitas fotos/visitas |
| Certificado de assinatura do T3 Gestor | ~R$ 1.500 a 3.000/ano | Opcional, para o Windows não alertar no instalador |

**Com a recomendação (Render Starter): ~R$ 210 a 235 por mês**, mais a IA conforme o uso.

## Riscos e decisões

- **Renovações de dezembro**: hospedagem Hostinger vence 2026-12-05 e o teste do e-mail acaba ~04/12 (renova anual).
  A Black Friday da Hostinger vai até 14/12, então dá para renovar por mais tempo e mais barato.
- **Render Free dorme** e fica em Oregon (latência maior que São Paulo/Virginia). É o único ponto lento do sistema.
- **Resend Free permite 1 domínio**; t3led.com.br e t3ooh.com foram verificados. Conferir o plano no painel.
- **Vercel Hobby é só para uso não comercial**. Como ali só há um redirecionamento, dá para mover para o DNS/Hostinger e zerar o risco.
- **Tiles do OpenStreetMap** têm política de uso justo; com tráfego alto, trocar por um provedor (MapTiler/Stadia, plano grátis).

## Fontes

[Supabase](https://supabase.com/pricing) · [Hostinger e-mail](https://www.hostinger.com/br/business-email) ·
[Hostinger Black Friday](https://cybernews.com/br/black-friday/ofertas-de-black-friday-do-hostinger-2026/) ·
[Resend](https://resend.com/pricing) · [Render](https://render.com/articles/how-much-does-cloud-application-hosting-cost-for-small-businesses) ·
[registro.br](https://www.homehost.com.br/blog/registro-de-dominios/quanto-custa-um-dominio) ·
[Dólar](https://www.meelion.com/indicadores-financeiros/dolar-hoje/)

Para fechar os valores "estimados": faturas da Hostinger (plano e nº de caixas) e o painel do Resend.
