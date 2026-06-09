# Feature: plano-de-contas

**Keywords:** plano de contas, categoria, receita, despesa, financial-category, dre
**Arquivos principais:**
  - erp-back/-1-Domain/Entities/financialCategory.cs
  - erp-back/-4-WebApi/Controllers/FinancialCategoryController.cs
  - erp-back/-2-Application/Services/FinancialCategoryService.cs
  - erp-back/-1-Domain/database/migrations/025_create_financial_category.sql
  - erp-front/src/services/financialCategoryService.ts
  - erp-front/src/pages/financial-categories/
**Resumo:** Categorização contábil (plano de contas) de Receita/Despesa, vinculável a títulos a pagar/receber (e, futuramente, transações), habilitando relatórios por categoria.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1212 | 2026-06-09 | `0be4661` | Onda 1: FinancialCategory + FK em conta a pagar/receber + seletor |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

`FinancialCategory` (tabela `tb_financial_category`, por `CompanyId`): `Code`, `Name`, `Type` (Receita/Despesa), `IsActive`. API `api/financial-category` CRUD canônico (permissões `accountPayableReceivable.*`). FK `financial_category_id` (nullable) em `tb_account_payable_receivable` (wired: entidade/DTO/mapper + seletor no `AccountPayableReceivableForm`) e em `tb_financial_transaction` (coluna criada, ainda **não** wired na entidade — transação é gerada internamente; categoria será derivada do título no futuro). Front: lista `FinancialCategories` + `FinancialCategoryForm` em `/financial-categories`, item no menu Cadastros.

> Última atualização: 2026-06-09 (SPEC-20260609-1212)

## Decisões arquiteturais ativas

- **Permissões reusam `accountPayableReceivable.*`** (origem: SPEC-20260609-1212, 2026-06-09) — evita criar módulo de permissão novo; categoria é parte do fluxo financeiro de títulos.
- **FK em FinancialTransaction só no banco por ora** (origem: SPEC-20260609-1212, 2026-06-09) — a transação é read-only/gerada internamente; a categoria será copiada do título quando a geração for ajustada.

## Alternativas consideradas e rejeitadas

- _(nenhuma registrada ainda)_

## Gotchas

- **Sem relatório por categoria ainda** (2026-06-09, SPEC-20260609-1212) — a categorização existe; o relatório/DRE por categoria fica para uma onda futura.

## Estado congelado (se houver)

_(nenhum)_
