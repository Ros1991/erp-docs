# Memory — SPEC-20260609-1516 (Onda 5 — Pessoal)

> Main: [main.md](./main.md) · State: [state.md](./state.md)

## TL;DR final

**Onda 5 entregue (2026-06-09 15:33).** 4 recursos de Pessoal/RH:
- **Resumo de empréstimos** (`GET api/loan-advance/employee/{id}/summary`): buckets Mês/13º/Férias derivados de `LoanAdvance.DiscountSource`, dívida total, parcela mensal, salário do contrato ativo, % comprometido e alerta ≥30%. Tela `LoanSummary`. **Sem migração** (dados já existiam).
- **Benefício percentual**: `ContractBenefitDiscount.IsPercentage` + `.Percentage`; a folha calcula `% × salário base` (aditivo, **default-off** = cálculo existente intacto). UI: toggle "Percentual" no `BenefitDiscountList`.
- **Abonos** (Dias/Horas): `tb_employee_abono` + CRUD `api/employee-abono` + tela `Abonos`.
- **Período de férias**: `tb_vacation_period` (aquisitivo + gozo + dias) + CRUD `api/vacation-period` + tela `VacationPeriods`.
Migração 030 (2 colunas + 2 tabelas). Commits feature `34f5246`/`efd91b4`; main local `0b0b9fa`/`f1a4ab8` (sem push).

## Contexto / decisões

- **Buckets do empréstimo = `DiscountSource`** (`TODOS`/`SALARIO`/`13SAL`/`FERIAS`) — já existia; o resumo só agrega `remaining = Amount − (Amount/Installments × InstallmentsPaid)` por bucket. Parcela mensal só conta buckets mensais (`SALARIO`/`TODOS`/vazio). % comprometido = parcela mensal ÷ salário do contrato ativo. Alerta default 30%.
- **Benefício % é seguro** — `IsPercentage` default false; o laço de benefícios da folha só usa o caminho % quando marcado, então payrolls existentes não mudam. Base do % = `salaryAmount` (salário base já calculado no método).
- **Abono/Férias = cadastro** — espelham os cadastros standalone do antigo (`Empregados_Abonos`, `Lancar Ferias`). Consumo automático no ponto/folha deferido (documentado no main.md).
- **Permissões**: resumo → `loanAdvance.canView`; abono/férias → `employee.canView`/`canEdit`. Guarda multi-tenant no resumo (`employee.CompanyId == companyId`).

## Armadilhas

- **Não tocar no motor de folha além do mínimo** — a folha é o "crown jewel"; a única mudança foi o cálculo de benefício % (additive/default-off). "Mês Admissão" exigiria filtro período-aware → deferido.
- Repos novos seguem o padrão lax de `GetOneByIdAsync(FindAsync)` sem checagem de empresa (consistente com WorkSchedule/TimeBankAdjustment); as leituras de lista são company-scoped.

## Referências

- Feature docs: [emprestimos-adiantamentos](../../features/emprestimos-adiantamentos.md), [empregados-contratos](../../features/empregados-contratos.md), [folha-pagamento](../../features/folha-pagamento.md), [abonos-ferias](../../features/abonos-ferias.md).
- Sistema antigo: `05 - Pessoal e RH/` (Emprestimos - Resumo, Imposto ou Beneficio, Abonos e Ferias, Lancar Ferias).
