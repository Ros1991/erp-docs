# SPEC-20260609-1212: Onda 1 — Cadastros & dados

**Status:** done
**Criada:** 2026-06-09 12:12
**Ativada:** 2026-06-09 12:12
**Concluída:** 2026-06-09 12:40
**Pausada em:** —
**Commit final:** erp-back `4ef7c66` / erp-front `70a9e67`
**Keywords:** contrato, pix, banco, feriados, plano-de-contas, anexo, migração
**Features:** empregados-contratos, justificativas, feriados, plano-de-contas
**Branch:** feature/onda-1-cadastros-dados (erp-back + erp-front)
**Depende de:** SPEC-20260609-1111
**Bloqueia:** —
**Ordem no programa:** 2/7 — programa "Migração Fazenda → Meu Gestor"
**Origem:** comparativo [docs/comparativo-sistema-antigo.md](../../comparativo-sistema-antigo.md) (Tier 2); usuário em 2026-06-09 (mandato de execução autônoma)
**Resumo:** Cadastros e dados que faltavam no Meu Gestor — primeiras migrações ADITIVAS na Railway.

## Objetivo

Fechar lacunas de cadastro/dados do comparativo: dados bancários/PIX no contrato (requisito p/ pagar), feriados (cadastro), plano de contas (categorização financeira) e anexo de justificativa (atestado).

## Escopo

**DENTRO:**
- **F** — Dados bancários/PIX no `Contract` (migração aditiva + entidade/DTO/mapper + UI no ContractForm).
- **G** — Feriados: entidade `Holiday` + tabela + CRUD canônico + UI + feature doc nova.
- **H** — Plano de contas: `FinancialCategory` + FK nullable em `AccountPayableReceivable`/`FinancialTransaction` + seletor + feature doc nova.
- **I** — Anexo de justificativa (Base64 em `attachment_url`): upload no form + visualização (botão de câmera hoje morto).

**FORA:**
- Jornada/`WorkSchedule` (Onda 2); banco de horas (Onda 3); consumo de feriados no cálculo de ponto/folha (futuro).

## Implementação

Cada item: migração SQL numerada (`-1-Domain/database/migrations/`) aplicada na Railway via `node .migrate/apply.js` + `erp.sql` atualizado; entidade/DTO/mapper/repo/service/controller no back (padrão CostCenter); UI no front; `dotnet build` + `npm run build` limpos. Migrações só ADITIVAS (colunas/tabelas novas) — a prod só é deployada no push final.

## Critério de aceite

- [x] **F** — dados bancários/PIX no contrato (migração 023 + back + UI no ContractForm) (2026-06-09 12:40, commits `4c8793b`/`361fc4a`)
- [x] **G** — feriados (entidade + tabela + CRUD `api/holiday` + UI + feature doc) (2026-06-09 12:40, commits `a71fb83`/`a4c9df4`)
- [x] **H** — plano de contas (`FinancialCategory` + FK nullable + seletor nos formulários + feature doc) (2026-06-09 12:40, commits `0be4661`/`31e0f24`)
- [x] **I** — anexo de justificativa (upload Base64 + visualização) (2026-06-09 12:40, commits `4ef7c66`/`70a9e67`)
- [x] migrações aplicadas na Railway (023/024/025/026) + `erp.sql` atualizado (2026-06-09 12:40)
- [x] erp-back compila e erp-front builda (2026-06-09 12:40)
- [x] **Features tocadas (empregados-contratos, justificativas, feriados, plano-de-contas) atualizadas** com timestamp e referência a esta SPEC (2026-06-09 12:40)
- [x] `state.md` com entrada `[conclusão]` (2026-06-09 12:40)
- [x] `memory.md` com TL;DR final atualizado (2026-06-09 12:40)
