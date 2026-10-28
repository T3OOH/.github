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

- **Node.js 22 LTS** (veja `.nvmrc`). Com o nvm: `nvm use`.
- Instale as dependências com `npm ci`, que respeita o `package-lock.json`.

## Segurança

Encontrou uma vulnerabilidade? **Não abra uma issue pública.** Siga o [SECURITY.md](SECURITY.md).
