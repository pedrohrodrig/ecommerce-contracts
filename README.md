# ecommerce-contracts

Contratos HTTP oficiais (OpenAPI 3.1) entre os frontends do e-commerce da ótica
(loja e admin) e o backend. O contrato é a fonte da verdade: tipos TypeScript (e
futuramente Go) são gerados a partir dele.

> **Fase atual:** configuração base. O contrato tem só `GET /health` e o schema
> de erro padrão (`Problem`, RFC 9457). Os endpoints reais entram na etapa 5.

## Requisitos

| Ferramenta                     | Versão                          | Como instalar                      |
| ------------------------------ | ------------------------------- | ---------------------------------- |
| [Node.js](https://nodejs.org/) | 24 LTS (ver `.nvmrc`)           | instalador oficial, `nvm` ou `fnm` |
| [pnpm](https://pnpm.io/)       | 12 (fixado em `packageManager`) | `npm install -g pnpm@12`           |

## Comandos

```bash
pnpm install          # instala as dependências
pnpm lint             # valida o contrato (Redocly)
pnpm generate         # gera generated/typescript/schema.d.ts
pnpm check:generated  # falha se os tipos gerados estiverem desatualizados
pnpm typecheck        # compila os tipos gerados
pnpm format           # formata com Prettier
pnpm format:check     # verifica a formatação
pnpm bundle           # junta o contrato em um arquivo: dist/openapi.yaml
pnpm build:docs       # documentação HTML navegável: dist/docs.html
```

## Ferramentas

- **[Redocly CLI](https://redocly.com/docs/cli/)**: valida o contrato com o
  conjunto de regras `recommended` (ver [`redocly.yaml`](redocly.yaml)), junta
  os arquivos em um só e gera a documentação HTML. Exceções pontuais às regras
  ficam em [`.redocly.lint-ignore.yaml`](.redocly.lint-ignore.yaml) (hoje só o
  `/health`, que não tem resposta 4xx).
- **[openapi-typescript](https://openapi-ts.dev/)**: gera os tipos TypeScript
  que os frontends vão consumir. Configurado em `redocly.yaml`
  (`x-openapi-ts.output`). Exige TypeScript 5.x, por isso o projeto não usa o 7.
- **[Prettier](https://prettier.io/)**: formata YAML, JSON e Markdown, com as
  mesmas regras da Mistral UI. O Redocly não verifica formatação, então os dois
  não conflitam.

## VS Code

O VS Code sugere as extensões de
[`.vscode/extensions.json`](.vscode/extensions.json):

- **Prettier** (`esbenp.prettier-vscode`): formata ao salvar, usando a versão e
  a configuração do projeto.
- **Redocly OpenAPI** (`redocly.openapi-vs-code`): autocompletar, navegação por
  `$ref`, pré-visualização e lint do contrato no editor.
- **EditorConfig** (`editorconfig.editorconfig`).

A pasta `generated/` abre como somente leitura no editor.

## Estrutura

```text
openapi/
├── openapi.yaml       # raiz do contrato
├── paths/             # um arquivo por caminho
└── components/
    ├── schemas/
    └── responses/
generated/
└── typescript/        # tipos gerados (não editar)
```

Novas pastas são criadas quando recebem o primeiro arquivo. A estrutura alvo e
as convenções estão em [CONTRIBUTING.md](CONTRIBUTING.md#estrutura-do-projeto).

## Distribuição

Os frontends ainda não consomem este pacote. A forma de distribuição (pacote
npm versionado ou tag Git) será decidida na etapa 5 e registrada como ADR no
Notion do projeto.

## CI

[`.github/workflows/ci.yml`](.github/workflows/ci.yml) roda em PRs e pushes para
`develop` e `main`: lint do contrato e formatação; tipos gerados atualizados,
typecheck e bundle.
