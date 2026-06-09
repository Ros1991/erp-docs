# Feature: centros-de-custo

**Keywords:** centro de custo, cost center, rateio, distribuição, alocação, percentual
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/CostCenterController.cs
  - erp-back/-2-Application/Services/CostCenterService.cs
  - erp-back/-3-Infrastructure/Repositories/CostCenterRepository.cs
  - erp-back/-1-Domain/Entities/costCenter.cs
  - erp-back/-1-Domain/Entities/defaultCostCenterDistribution.cs
  - erp-back/-1-Domain/Entities/transactionCostCenter.cs
  - erp-front/src/services/costCenterService.ts
  - erp-front/src/pages/cost-centers/
**Resumo:** Centros de custo (`CostCenter`) e o rateio de valores entre eles — usados para alocar transações financeiras, títulos a pagar/receber e contratos, com distribuições default por empresa.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1538 | 2026-06-09 | `385b415` | Onda 6: template de rateio nomeado (`CostCenterAllocationTemplate` + linhas) — conjuntos reutilizáveis de centro de custo + % (valida soma 100%); `api/cost-center-allocation-template` |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/cost-center` (CRUD canônico sob `costCenter.*`) — esta é a fatia vertical **de referência** do padrão do projeto (ver [[plataforma-core]]). `CostCenter` tem `Name`, `Description`, `IsActive`, `CompanyId`. O rateio aparece em três lugares: `TransactionCostCenter` (rateio de [[transacoes-financeiras]]), `AccountPayableReceivableCostCenter` (rateio de [[contas-pagar-receber]]), `ContractCostCenter` (rateio de [[empregados-contratos]]). `DefaultCostCenterDistribution` guarda a distribuição padrão da empresa (exposta via `api/company-setting/default-distributions` — ver [[empresas-multitenancy]]). Base do "Relatório por Centro de Custo" e "Centro de Custo Mensal" em [[relatorios]].

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Rateio modelado por tabela de associação por contexto** (origem: estado pré-SPEC, 2026-06-08 20:15) — em vez de uma tabela genérica, cada origem (transação, título, contrato) tem sua própria tabela de rateio com `Percentage`/`Amount`.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Distribuição default por empresa vive em outra feature** (2026-06-08 20:15) — `DefaultCostCenterDistribution` é gerenciada via configurações da empresa, não pelo controller de centro de custo.

## Estado congelado (se houver)

_(nenhum)_
