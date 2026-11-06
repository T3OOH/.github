<div align="center">

# T3 OOH | T3 LED | T3 SOLUTION

Painéis de LED e mídia exterior em Goiânia e no interior de Goiás.

[t3ooh.com](https://t3ooh.com) · [Frontend](https://github.com/T3OOH/Frontend) · [Backend](https://github.com/T3OOH/Backend)

</div>

---

A T3 tem duas linhas de negócio, e a plataforma atende as duas:

- **T3 OOH**: aluguel de espaço publicitário nos telões de LED da rede. O anunciante escolhe os pontos no mapa ou no catálogo, define o prazo e recebe o orçamento.
- **T3 Solution**: venda, instalação e suporte de painéis de LED sob medida para casas, condomínios, empresas, comércio e empreendimentos.

Os pedidos das duas linhas caem no mesmo CRM, separados por linha, e a equipe acompanha tudo do primeiro contato até o pós-venda.

## O que tem na plataforma

### Site público

- Tela de entrada para escolher entre T3 OOH e T3 Solution, cada lado com o próprio vídeo.
- Mapa de painéis com filtros por estado e cidade, detalhes de cada ponto e carrinho de orçamento. No celular, atalhos por cidade, lista completa dos painéis e cartão do telão selecionado.
- Catálogo com os mesmos painéis em lista, para montar pacotes.
- Página da T3 Solution com formulário de orçamento que abre um negócio no CRM.
- Desconto por prazo calculado no servidor: 15% em 3 meses, 20% em 6 e 30% a partir de 12.
- Páginas públicas pré-renderizadas no build, com dados estruturados (schema.org), `sitemap.xml`, `robots.txt` e `llms.txt`, para buscadores e assistentes de IA lerem o conteúdo sem executar JavaScript.

### Conta do cliente e da equipe

- Cadastro e login com e-mail e senha ou com o Google.
- Sessão mantida por aparelho, com aviso por e-mail quando a conta entra em um aparelho novo e o botão "Não fui eu" para desconectar tudo e trocar a senha.
- Perfil para todos os papéis: dados, orçamentos com a etapa de cada um (clientes), aparência do site e segurança (aparelhos conectados, troca de senha, atividade recente).
- Em qualquer erro, o botão "Acionar suporte" abre o WhatsApp da equipe já com a mensagem e o código do registro.

### Gestão (administradores e gestores)

- Painéis: cadastro, fotos, preço, situação e posição no mapa.
- Pedidos do catálogo, usuários e papéis, portfólio da T3 Solution e página Sobre.
- Tráfego e leads: visitas por dia, origem de cada visita e de cada lead (Google, Instagram, assistentes de IA, campanhas), páginas mais vistas, exportação em CSV e gerador de links de campanha.
- Relatórios: vendas e negócios fechados no período, funil por etapa, previsão de receita ponderada pela probabilidade, receita dos últimos 12 meses, origem dos negócios e ocupação de cada painel mês a mês.
- Registro de auditoria: quem alterou o quê no sistema (com antes e depois), alterações feitas direto no banco, histórico dos repositórios e erros da API e do navegador.
- Busca rápida (Ctrl+K ou Cmd+K) em qualquer tela interna: páginas, clientes, negócios e painéis.

### CRM comercial

- Fila de atendimento com os pedidos do site que ainda não têm consultor.
- Funil em colunas, filtrado por linha de negócio, com etapas, ganhos e perdidos.
- Negócios da T3 Solution com o projeto: aplicação, ambiente, medidas, pixel pitch, serviços e valor.
- Clientes, propostas em PDF, agenda, cupons de desconto e chat em tempo real.

## Repositórios

| Repositório | O que é | Tecnologias | Publicação |
| --- | --- | --- | --- |
| [Frontend](https://github.com/T3OOH/Frontend) | Site, mapa, conta do cliente, gestão e CRM | React 19, Vite, TypeScript, Tailwind | Hostinger · `t3led.com.br` |
| [Backend](https://github.com/T3OOH/Backend) | API REST e WebSocket (produção) | Node.js, Express 5, Prisma 7, Socket.IO, Zod | Render · `api.t3led.com.br` |
| [BackendJS](https://github.com/T3OOH/BackendJS) | API nova (substituirá o Backend) + serviço de eventos | NestJS em JavaScript, Prisma, Rust | Render (`render.yaml`, ainda não publicado) |
| [AppT3](https://github.com/T3OOH/AppT3) | App desktop **T3 Gestor**, **T3 Launcher** e serviço de relatórios | Java 25, JavaFX, Python (FastAPI) | GitHub Releases (`.msi`) |
| [ApiDocs](https://github.com/T3OOH/ApiDocs) | Documentação da API | HTML, CSS e JS sem build | Hostinger · `docs.t3led.com.br` |
| [Ambiente](https://github.com/T3OOH/Ambiente) | Ambiente de desenvolvimento local | Docker Compose | — |

Banco de dados PostgreSQL e armazenamento de arquivos no Supabase. E-mails pelo Resend, de `nao-responda@t3led.com.br`.
O que já foi feito, o que falta e como o código chega à produção: [MAPA.md](https://github.com/T3OOH/.github/blob/main/MAPA.md).

## Arquitetura

```mermaid
flowchart LR
    V[Visitantes e clientes] --> FE
    E[Equipe T3] --> FE
    E --> APP[T3 Gestor<br/>app desktop]
    FE[Frontend<br/>Hostinger] -- REST com JWT --> BE[Backend<br/>Render]
    FE -- WebSocket --> BE
    APP -- REST --> BE
    APP --> REL[Relatórios<br/>Python local]
    BE --> DB[(PostgreSQL<br/>Supabase)]
    BE --> ST[(Arquivos<br/>Supabase Storage)]
    BE --> RS[Resend<br/>e-mails]
    BE --> GO[Google<br/>login e reCAPTCHA]
    BE --> HG[Hostinger API<br/>caixas de e-mail]
```

O caminho de um lead, do site ao fechamento:

```mermaid
flowchart LR
    S[Visita ao site<br/>origem registrada] --> F{Formulário}
    F -- mapa ou catálogo --> Q[Fila do CRM<br/>linha T3 OOH]
    F -- página Solution --> QS[Fila do CRM<br/>linha T3 Solution]
    Q --> C[Consultor assume]
    QS --> C
    C --> P[Funil: contato, negociação,<br/>proposta, pós-venda]
    P --> G[Ganho ou perdido]
```

## Papéis de acesso

| Papel | Pode acessar |
| --- | --- |
| Administrador | Tudo, incluindo usuários e papéis |
| Gestor | Gestão (painéis, pedidos, tráfego, conteúdo) e todo o CRM |
| Comercial | CRM, restrito aos próprios clientes e negócios |
| Cliente e empresa | Área do cliente e orçamentos |

## Segurança

- Token de acesso de 15 minutos e refresh token por aparelho, trocado a cada uso. O banco guarda só o hash.
- Limite de tentativas por IP e por conta, reCAPTCHA no login e no cadastro.
- Row Level Security ligado em todas as tabelas do Supabase.
- Toda alteração fica no registro de auditoria, inclusive as feitas fora da API: um gatilho no próprio Postgres grava mudanças vindas do painel do Supabase ou de ferramentas externas.
- Estatísticas de visita próprias e anônimas, sem guardar IP, e opção "Só o essencial" no aviso de privacidade.

A arquitetura de sessões, os e-mails de alerta e o roteiro de resposta a incidentes estão em [`Backend/docs/SEGURANCA.md`](https://github.com/T3OOH/Backend/blob/main/docs/SEGURANCA.md). Para reportar uma vulnerabilidade, veja a [política de segurança](https://github.com/T3OOH/.github/blob/main/SECURITY.md).

## Como contribuir

1. Crie uma branch a partir da `main` atualizada (`feat/...`, `fix/...`, `docs/...`).
2. Siga o README de cada repositório para rodar localmente.
3. Abra um Pull Request explicando o que mudou e como testar. O CI precisa passar.
4. Mudanças no banco entram como migração do Prisma e são aplicadas antes do deploy do backend.
5. Nunca faça commit de `.env`, chaves ou senhas.

O guia completo está em [CONTRIBUTING.md](https://github.com/T3OOH/.github/blob/main/CONTRIBUTING.md).

---

<div align="center">
<sub>T3 LED Mídia e Tecnologia · CNPJ 43.773.494/0001-50 · Goiânia, GO</sub>
</div>
