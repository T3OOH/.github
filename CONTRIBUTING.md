# Guia de contribuição — T3 OOH

Este guia vale para todos os repositórios da organização [T3OOH](https://github.com/T3OOH).

## Fluxo de trabalho

1. **Nunca faça commit direto na `main`.** Ela é protegida: toda mudança entra por Pull Request com o CI passando.
2. Crie uma branch a partir da `main` atualizada:

   ```bash
   git switch main && git pull
   git switch -c feat/mapa-filtro-por-bairro
   ```

3. Faça commits pequenos e descritivos (veja *Commits* abaixo).
4. Rode localmente antes de abrir o PR:

   | Repositório | Comandos |
   | --- | --- |
   | Frontend | `npm run lint` · `npm run typecheck` · `npm run build` |
   | Backend | `npm run typecheck` · `npm run build` |
   | BackendJS | `npm run lint` · `npm test -- --runInBand` · `npm run test:e2e` · `npm run build` · em `rust/eventos`: `cargo fmt --check` · `cargo clippy` · `cargo test` |
   | AppT3 | em `app/`: `mvn -B verify` · em `relatorios/`: `ruff check .` · `pytest -q` |
   | ApiDocs | `node scripts/check.mjs` |
   | Ambiente | `docker compose config -q` |

5. Abra o PR para a `main`, preencha o template e aguarde o CI ficar verde.
6. O merge é feito com **Squash and merge**. A branch é apagada automaticamente.

## Nome das branches

`<tipo>/<descricao-curta-em-kebab-case>`

| Tipo | Uso |
| --- | --- |
| `feat/` | Nova funcionalidade |
| `fix/` | Correção de bug |
| `chore/` | Configuração, dependências, tarefas internas |
| `docs/` | Apenas documentação |
| `refactor/` | Mudança de código sem alterar comportamento |
| `hotfix/` | Correção urgente em produção |

## Commits

Seguimos o padrão [Conventional Commits](https://www.conventionalcommits.org/pt-br/), em português:

```
<tipo>(escopo opcional): resumo no imperativo, em minúsculas

Corpo opcional explicando o porquê da mudança.
```

Exemplos:

```
feat(crm): adiciona filtro por vendedor no pipeline
fix(mapa): corrige marcadores que ignoravam os filtros
chore(deps): atualiza prisma para 7.9
```

Tipos aceitos: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.

## Pull Requests

- Um PR resolve **um** assunto. Mudanças grandes devem ser quebradas.
- O título segue o mesmo padrão dos commits (vira a mensagem do squash).
- Descreva **o que mudou, por quê e como testar**. Inclua prints em mudanças visuais.
- **Mudanças de contrato da API** (rotas, payloads, respostas) precisam ser sinalizadas, com o PR correspondente no outro repositório.

## Padrões de código

- **TypeScript** em modo `strict`. Evite `any`; quando inevitável, justifique em comentário.
- **Formatação:** siga o `.editorconfig` (4 espaços, LF, UTF-8).
- **Textos da interface** em português do Brasil.
- **Regras de negócio e preços** sempre validados no backend. O frontend nunca é fonte de verdade.
- **Rotas novas no backend** devem declarar `authMiddleware` e `requireRoles(...)`, exceto as explicitamente públicas.

## Segredos e variáveis de ambiente

- **Nunca** faça commit de `.env`, chaves, tokens ou senhas.
- Toda variável nova entra no `.env.example` do repositório, com descrição.
- Variáveis `VITE_*` são públicas (vão para o navegador). Não coloque segredos nelas.

## Ambiente

- O jeito mais rápido de ter tudo rodando (banco, API, site, eventos e relatórios) é o
  [T3OOH/Ambiente](https://github.com/T3OOH/Ambiente): `scripts/preparar.ps1` (Windows) ou `scripts/preparar.sh`.
  Ele usa um banco local com dados de exemplo; **nunca** desenvolva contra o banco de produção.
- `scripts/verificar` no Ambiente confere as ferramentas instaladas (Node 22, Java 25, Python 3.14, Rust, Docker).
- **Node.js 22 LTS** (veja `.nvmrc`). Com o nvm: `nvm use`. Instale as dependências com `npm ci`.

## Pipeline

Todo repositório segue o mesmo caminho até a produção (detalhes no [MAPA.md](MAPA.md)):

1. **PR** → workflow `CI` (lint, testes e build). Precisa ficar verde.
2. **Squash and merge** na `main` (a branch é apagada).
3. **Deploy automático**: Render (Backend) e Hostinger (Frontend, ApiDocs) publicam a `main` sozinhos.
4. **Produção**: o workflow `Produção` espera o deploy e confere as URLs. Se falhar, o GitHub avisa por e-mail.
5. **App desktop**: versão nova = tag `vX.Y.Z` e release no AppT3 com o `.msi`.

## Padrão dos repositórios

Todo repositório tem: `README.md`, `.editorconfig` e `.gitattributes` iguais, `.gitignore`, `.github/CODEOWNERS`,
`.github/dependabot.yml`, workflow `CI` (PR e push na `main`, com `concurrency`) e `atribuir-issues.yml`.
Os que publicam algo também têm o workflow `Produção`. Configuração do GitHub: só *squash merge* e apagar a branch após o merge.

## Segurança

Encontrou uma vulnerabilidade? **Não abra uma issue pública.** Siga o [SECURITY.md](SECURITY.md).
