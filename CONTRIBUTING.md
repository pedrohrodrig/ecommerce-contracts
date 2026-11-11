# Contribuindo

## Branches (git flow)

| Branch                | Papel                                                                               |
| --------------------- | ----------------------------------------------------------------------------------- |
| `main`                | Versão publicada do contrato. Só recebe merges de `release/*` (e `hotfix/*`).       |
| `develop`             | Integração. Base de todo trabalho novo.                                             |
| `feature/<descricao>` | Uma mudança no contrato. Sai de `develop` e volta por PR.                           |
| `release/<versao>`    | Preparação de uma versão. Sai de `develop`, entra em `main` e volta para `develop`. |

## Commits

Seguimos [Conventional Commits](https://www.conventionalcommits.org/pt-br/).
Mudança que quebra consumidores (remover campo, renomear, mudar tipo) usa `!`:

```text
feat(catalog): adiciona GET /v1/store/products
fix(catalog): corrige tipo do campo price
feat(catalog)!: renomeia sku para code
```

## Fluxo de uma mudança no contrato

1. Edite os arquivos em `openapi/`.
2. `pnpm lint` até não haver erros.
3. `pnpm generate` e commite os arquivos de `generated/` junto.
4. `pnpm format`.

O CI repete essas verificações e falha se `generated/` estiver desatualizado.

## Estrutura do projeto

Pastas são criadas **somente quando recebem o primeiro arquivo** (sem
`.gitkeep`). Estrutura alvo:

```text
openapi/
├── openapi.yaml               # raiz: info, servers, tags e lista de paths
├── paths/
│   ├── health.yaml            # rotas de sistema ficam na raiz de paths/
│   └── <dominio>/             # auth, catalog, inventory, orders, customers
│       └── <recurso>.yaml     # um arquivo por caminho, ex.: products_{id}.yaml
└── components/
    ├── schemas/               # um schema por arquivo, nome em PascalCase
    ├── responses/             # respostas reutilizáveis (erros, paginação)
    └── parameters/            # parâmetros reutilizáveis (paginação, filtros)
generated/
├── typescript/                # openapi-typescript
└── go/                        # oapi-codegen (avaliar na etapa 5)
```

Convenções:

- Rotas da loja em `/v1/store/...`, do admin em `/v1/admin/...`.
- Todo erro responde `application/problem+json` com o schema `Problem`.
- Todo `operationId` em camelCase e único (vira nome de função nos clientes).
- Mudou a estrutura alvo? Atualize esta seção no mesmo PR.

## Decisões de arquitetura

Decisões relevantes viram um ADR na base
[ADRs do Notion do projeto](https://app.notion.com/p/415cc60b2a2980b6b738f80c72276480).
