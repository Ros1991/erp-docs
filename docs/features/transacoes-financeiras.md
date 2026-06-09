# Feature: transacoes-financeiras

**Keywords:** transação, financial transaction, movimentação, fluxo de caixa, lançamento, somente-leitura, rateio
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/FinancialTransactionController.cs
  - erp-back/-2-Application/Services/FinancialTransactionService.cs
  - erp-back/-3-Infrastructure/Repositories/FinancialTransactionRepository.cs
  - erp-back/-1-Domain/Entities/financialTransaction.cs
  - erp-back/-1-Domain/Entities/transactionCostCenter.cs
  - erp-front/src/services/financialTransactionService.ts
  - erp-front/src/pages/financial-transactions/
**Resumo:** Livro de movimentações financeiras (`FinancialTransaction`) — **somente leitura via API**; os lançamentos são gerados internamente pela baixa de contas a pagar/receber, ordens de compra e folha, com rateio opcional por centro de custo.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| — | — | — | _(nenhuma SPEC ainda — feature documentada por engenharia reversa do estado atual)_ |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/financial-transaction` expõe **apenas leitura** (`getAll`, `getPaged`, `GET {id}`) sob `financialTransaction.canView` — os endpoints de create/update/delete existem comentados no controller. `FinancialTransaction` tem `Type`, `Amount`, `TransactionDate`, `CompanyId` e FKs para `Account` (opcional), `AccountPayableReceivable` e `PurchaseOrder`, indicando a origem do lançamento. O rateio por centro de custo é registrado em `TransactionCostCenter`. É a fonte dos relatórios "Fluxo de Caixa", "Resumo Financeiro" e do dashboard financeiro em [[relatorios]].

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Transações são imutáveis pela API (somente leitura)** (origem: estado pré-SPEC, 2026-06-08 20:15) — nascem como efeito colateral de outras operações (baixa de título, processamento de ordem de compra, folha), nunca por digitação direta. Garante consistência do livro financeiro.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Não criar transação manualmente** (2026-06-08 20:15) — o CRUD está propositalmente desativado. Novas origens de lançamento devem gerar a `FinancialTransaction` dentro do service da feature originadora ([[contas-pagar-receber]], [[ordens-de-compra]], [[folha-pagamento]]).
- **FK para a conta é opcional** (2026-06-08 20:15) — `AccountId` pode ser nulo (migrations `003`/`005`); relatórios por conta devem tratar lançamentos sem conta.

## Estado congelado (se houver)

_(nenhum)_
