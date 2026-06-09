# erp-docs

Repositório de **documentação SPEC-driven (v3)** do ERP **Meu Gestor**.

Faz par com os repositórios de código (mesma org [`Ros1991`](https://github.com/Ros1991)):

- [`erp-back`](https://github.com/Ros1991/erp-back) — API .NET 9 (Clean Architecture/DDD, EF Core + Npgsql, PostgreSQL)
- [`erp-front`](https://github.com/Ros1991/erp-front) — SPA React 19 + Vite + TypeScript + Tailwind

> No workspace local os três convivem em `D:\Projetos\Meu Gestor`. **`erp-back/` e `erp-front/` têm o próprio git e NÃO são versionados aqui** (ver [`.gitignore`](.gitignore)).

## Por onde começar

1. [`CLAUDE.md`](CLAUDE.md) — orientação para IA + primeira ação obrigatória de cada sessão
2. [`docs/RULES.md`](docs/RULES.md) — processo SPEC-driven completo (fonte da verdade)
3. [`docs/INDEX.md`](docs/INDEX.md) — mapa das features (gerado pelo CI, não editar à mão)
4. [`docs/features/`](docs/features) — estado vivo de cada área do sistema (18 features)

## Estrutura

```
docs/
├── RULES.md            processo (imperativo, fonte da verdade)
├── INDEX.md            índice de features (gerado pelo CI)
├── features/           estado vivo por área
├── active/             SPECs em execução (vazio na main — vivem em branches)
├── future/             SPECs planejadas
├── archive/            SPECs concluídas
└── discard/            SPECs descartadas
scripts/
├── generate-index.sh   regenera docs/INDEX.md
├── lint-docs.sh        valida o formato dos arquivos de controle
└── audit-docs.sh       auditoria + gate R.7 (modo --pr)
```

## CI

Workflows em [`.github/workflows/`](.github/workflows):

| Workflow | Gatilho | O que faz |
|---|---|---|
| `docs-index.yml` | push na `main` que toca `docs/features/**` | roda `generate-index.sh` e commita o `INDEX.md` regenerado (`[skip ci]`) |
| `docs-lint-audit.yml` | PR e push na `main` | `lint-docs.sh` (formato) + `audit-docs.sh --pr` (gate R.7: features atualizadas ao arquivar SPEC) |

## Convenção de nome

Segue o padrão dos repos irmãos: `erp-back`, `erp-front`, **`erp-docs`**.
