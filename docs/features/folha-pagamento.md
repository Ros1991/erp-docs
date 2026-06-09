# Feature: folha-pagamento

**Keywords:** folha, payroll, salário, holerite, inss, fgts, irrf, décimo terceiro, férias, fechamento, snapshot, recalcular
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/PayrollController.cs
  - erp-back/-2-Application/Services/PayrollService.cs
  - erp-back/-3-Infrastructure/Repositories/PayrollRepository.cs
  - erp-back/-1-Domain/Entities/payroll.cs
  - erp-back/-1-Domain/Entities/payrollEmployee.cs
  - erp-back/-1-Domain/Entities/payrollItem.cs
  - erp-front/src/services/payrollService.ts
  - erp-front/src/pages/payroll/
**Resumo:** Processamento da folha de pagamento por período: gera a folha a partir dos contratos, calcula proventos/descontos (INSS, FGTS, IRRF, benefícios, empréstimos), suporta 13º e férias, recalcula, fecha e reabre — com snapshot JSONB do cálculo.

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

API em `api/payroll` (a mais rica do sistema). Além do CRUD (`getAll`, `getPaged`, `GET {id}`, `GET {id}/details`, `create`, `PUT {id}`, `DELETE {id}`) e do `GET suggestion` (sugestão de período/empregados), tem ações de cálculo e ciclo: `POST {id}/recalculate`, `POST employee/{payrollEmployeeId}/recalculate`, `PUT item/{payrollItemId}`, `PUT employee/{payrollEmployeeId}/worked-units`, `POST item`, `POST/DELETE {id}/thirteenth-salary`, `POST {id}/vacation` + `DELETE {id}/vacation/{payrollEmployeeId}`, e o fechamento **`POST {id}/close`** (`payroll.canClose`) / **`POST {id}/reopen`** (`payroll.canReopen`). Demais ações sob `payroll.canEdit`.

Modelo: `Payroll` (período, totais bruto/descontos/líquido, `IsClosed`, **`Snapshot` JSONB**) → `PayrollEmployee` (por empregado: férias, dias, adiantamento de férias, totais) → `PayrollItem` (cada provento/desconto: `Type`, `Category`, `Amount`, `ReferenceId`, **`CalculationDetails` JSONB**, `CalculationBasis`). Puxa de [[empregados-contratos]] (contratos + encargos), [[emprestimos-adiantamentos]] (descontos) e usa parâmetros de [[empresas-multitenancy]] (dia de fechamento, horas semanais). Evoluída por várias migrations: `011` (worked units), `013` (INSS/FGTS), `014` (13º), `017` (campos de fechamento), `021` (impostos sobre adiantamento de férias).

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Snapshot JSONB do cálculo** (origem: estado pré-SPEC, 2026-06-08 20:15) — `Payroll.Snapshot` e `PayrollItem.CalculationDetails` guardam o detalhamento do cálculo como JSON, preservando a memória de cálculo mesmo se contratos mudarem depois.
- **Folha fechável/reabrível com permissões dedicadas** (origem: estado pré-SPEC, 2026-06-08 20:15) — `canClose`/`canReopen` separadas de `canEdit`; fechar congela a folha (`IsClosed`).

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Valores de contrato chegam em centavos (Int64)** (2026-06-08 20:15) — o cálculo parte de `Contract.Value`/`ContractBenefitDiscount.Amount` em centavos (migration `009`). Ver [[empregados-contratos]].
- **Campos JSONB com conversão identidade** (2026-06-08 20:15) — `Snapshot`/`CalculationDetails`/`CalculationBasis` são mapeados como `jsonb` com `HasConversion(v=>v, v=>v)` (string crua) no `ErpContext`; o JSON é montado/lido pela aplicação, não pelo EF.
- **Categoria de item já foi aumentada 2x** (2026-06-08 20:15) — migrations `012` e `016` aumentaram o tamanho de `PayrollItem.Category`; atenção ao truncamento ao adicionar categorias novas.

## Estado congelado (se houver)

_(nenhum)_
