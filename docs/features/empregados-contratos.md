# Feature: empregados-contratos

**Keywords:** empregado, funcionário, employee, contrato, contract, salário, benefício, desconto, gerente, associar usuário
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/EmployeeController.cs
  - erp-back/-4-WebApi/Controllers/ContractController.cs
  - erp-back/-2-Application/Services/EmployeeService.cs
  - erp-back/-2-Application/Services/ContractService.cs
  - erp-back/-1-Domain/Entities/employee.cs
  - erp-back/-1-Domain/Entities/contract.cs
  - erp-back/-1-Domain/Entities/contractBenefitDiscount.cs
  - erp-back/-1-Domain/Entities/contractCostCenter.cs
  - erp-front/src/pages/employees/
  - erp-front/src/pages/contracts/
**Resumo:** Cadastro de empregados, sua hierarquia (gerente), a associação opcional com um `User` de login, e os contratos de trabalho (valor, tipo, encargos, benefícios/descontos e rateio por centro de custo) que alimentam a folha.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1212 | 2026-06-09 | `4c8793b` | Onda 1: dados bancários/PIX no contrato (chave PIX, banco, agência, conta, operação) |
| SPEC-20260609-1248 | 2026-06-09 | `d9100bd` | Onda 2: vínculo de jornada (`work_schedule_id`) no contrato + seletor de jornada no ContractForm. Ver [jornada-trabalho](jornada-trabalho.md) |
| SPEC-20260609-1516 | 2026-06-09 | `34f5246` | Onda 5: benefício do contrato como **percentual do salário** (`IsPercentage`/`Percentage`) + UI no BenefitDiscountList. Ver [folha-pagamento](folha-pagamento.md) |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

**Empregados** — `api/employee` (CRUD sob `employee.*`) + `GET me` (empregado do usuário logado) + um conjunto de endpoints para vincular empregado↔usuário: `searchUser`, `associateUser`, `createAndAssociateUser`, `disassociateUser` (todos sob `employee.canEdit`). `Employee` tem `CompanyId`, `UserId` (opcional), `EmployeeIdManager` (auto-relacionamento de hierarquia), dados pessoais (`FullName`, `Nickname`, `Email`, `Phone`, `Cpf`).

**Contratos** — `api/contract` indexado por empregado (`employee/{employeeId}/all`, `employee/{employeeId}/active`, `GET {contractId}`, create, `PUT {contractId}`, `DELETE {contractId}`) — usa as permissões de `employee.*`. `Contract` tem `Type`, `Value`, flags de encargos (`IsPayroll`, `HasInss`, `HasIrrf`, `HasFgts`), `StartDate`/`EndDate`, `WeeklyHours`, `IsActive`. Benefícios/descontos recorrentes ficam em `ContractBenefitDiscount`; o rateio do custo do contrato em `ContractCostCenter` (ver [[centros-de-custo]]). É a base do cálculo de [[folha-pagamento]].

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Empregado e usuário são entidades separadas, vinculáveis** (origem: estado pré-SPEC, 2026-06-08 20:15) — `Employee.UserId` é opcional; nem todo empregado tem login. Endpoints dedicados criam/associam/desassociam o `User`. Ver [[usuarios]].
- **Contratos sob a permissão `employee.*`** (origem: estado pré-SPEC, 2026-06-08 20:15) — não há módulo de permissão separado para contrato; quem pode ver/editar empregado pode ver/editar contratos.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **`Contract.Value` e `ContractBenefitDiscount.Amount` são em CENTAVOS (Int64)** (2026-06-08 20:15) — a migration `009_contract_values_to_cents` converteu os valores monetários para inteiro em centavos; no `ErpContext` o tipo é `Int64`, não `decimal`. Dividir por 100 ao exibir.
- **Excluir empregado cascateia** (2026-06-08 20:15) — migration `006_add_cascade_delete_employee`: apagar `Employee` apaga contratos, alocações, ponto, empréstimos, folha-do-empregado e justificativas (CASCADE). Ver `-1-Domain/database/migrations/README_CASCADE_DELETE.md`.

## Estado congelado (se houver)

_(nenhum)_
