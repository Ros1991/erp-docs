# Feature: contas-pagar-receber

**Keywords:** contas a pagar, contas a receber, payable, receivable, vencimento, baixa, pagamento, rateio
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/AccountPayableReceivableController.cs
  - erp-back/-2-Application/Services/AccountPayableReceivableService.cs
  - erp-back/-3-Infrastructure/Repositories/AccountPayableReceivableRepository.cs
  - erp-back/-1-Domain/Entities/accountPayableReceivable.cs
  - erp-back/-1-Domain/Entities/accountPayableReceivableCostCenter.cs
  - erp-front/src/services/accountPayableReceivableService.ts
  - erp-front/src/pages/account-payable-receivable/
**Resumo:** Títulos a pagar e a receber (`AccountPayableReceivable`: descrição, tipo, valor, vencimento, pago/não-pago), com rateio opcional por centro de custo; a baixa gera movimentação em [[transacoes-financeiras]].

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

API em `api/account-payable-receivable` (CRUD canônico sob `accountPayableReceivable.*`). A entidade tem `Type` (pagar/receber), `Amount`, `DueDate`, `IsPaid` e `CompanyId`. O rateio por centro de custo é feito por `AccountPayableReceivableCostCenter` (introduzido na migration `015`). Ao ser baixado/pago, o título se conecta a `FinancialTransaction` (`FinancialTransaction.AccountPayableReceivableId`) — ver [[transacoes-financeiras]]. Alimenta os relatórios "Contas a Pagar/Receber", "Fluxo de Caixa" e "Previsão Financeira" em [[relatorios]].

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Rateio por centro de custo em tabela dedicada** (origem: estado pré-SPEC, 2026-06-08 20:15) — `AccountPayableReceivableCostCenter` permite distribuir um título entre múltiplos centros de custo. Ver [[centros-de-custo]].

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Baixa cria transação financeira (read-only via API)** (2026-06-08 20:15) — a movimentação correspondente é gerada internamente; `FinancialTransaction` não tem CRUD aberto. Ver [[transacoes-financeiras]].

## Estado congelado (se houver)

_(nenhum)_
