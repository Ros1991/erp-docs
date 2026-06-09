# Feature: contas-correntes

**Keywords:** conta, account, conta corrente, saldo, caixa, banco
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/AccountController.cs
  - erp-back/-2-Application/Services/accountService.cs
  - erp-back/-3-Infrastructure/Repositories/accountRepository.cs
  - erp-back/-1-Domain/Entities/account.cs
  - erp-front/src/services/accountService.ts
  - erp-front/src/pages/accounts/
**Resumo:** Cadastro de contas correntes/caixas da empresa (`Account`: nome, tipo, saldo inicial), sobre as quais as transações financeiras movimentam valores.

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

API em `api/account` (CRUD canônico sob `account.*`). `Account` pertence à empresa (`CompanyId`) e tem `Name`, `Type` e `InitialBalance`. É o destino/origem das movimentações em [[transacoes-financeiras]] (`FinancialTransaction.AccountId`) e base do "Relatório de Conta" em [[relatorios]]. O saldo corrente é derivado do saldo inicial + transações (não armazenado como campo mutável na própria conta).

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Saldo inicial fixo + movimentações** (origem: estado pré-SPEC, 2026-06-08 20:15) — a conta guarda só o `InitialBalance`; o saldo atual é calculado a partir das transações vinculadas.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **`AccountId` é opcional em transações** (2026-06-08 20:15) — migrations `003`/`005` tornaram a FK nullable; nem toda movimentação financeira está amarrada a uma conta corrente. Ver [[transacoes-financeiras]].

## Estado congelado (se houver)

_(nenhum)_
