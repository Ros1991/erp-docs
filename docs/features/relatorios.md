# Feature: relatorios

**Keywords:** relatório, report, dashboard, fluxo de caixa, previsão, resumo financeiro, centro de custo, conta, somente-leitura
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/ReportController.cs
  - erp-back/-2-Application/Services/ReportService.cs
  - erp-front/src/services/reportService.ts
  - erp-front/src/pages/reports/
  - erp-front/src/pages/dashboard/Dashboard.tsx
**Resumo:** Relatórios e dashboards agregados (somente leitura) sobre os dados financeiros e operacionais — resumo financeiro, fluxo de caixa, previsão, por centro de custo, por conta, por fornecedor/cliente, contas a pagar/receber e conta do empregado.

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

API em `api/Report` (todos `GET`, `report.canView`): `financial-summary`, `cost-center`, `cost-center-monthly`, `account`, `supplier-customer`, `cash-flow`, `accounts-payable-receivable`, `financial-forecast`, `employee-account`. `ReportService` agrega dados das demais features sem mutar nada. No frontend, as telas em `pages/reports/` (`FinancialDashboard`, `CostCenterReport`, `AccountReport`, `SupplierCustomerReport`, `CashFlowReport`, `AccountsPayableReceivableReport`, `FinancialForecastReport`, `EmployeeAccountReport`) e o `Dashboard` consomem esses endpoints, com gráficos via `recharts`. As rotas de relatório no frontend exigem empresa selecionada mas **não** estão envoltas em `PermissionProtectedRoute` (o gate é o `report.canView` no backend).

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Relatórios são leitura agregada, permissão única** (origem: estado pré-SPEC, 2026-06-08 20:15) — todos os endpoints sob `report.canView`; nenhuma escrita. Os números saem de [[transacoes-financeiras]], [[contas-pagar-receber]], [[centros-de-custo]], [[contas-correntes]], [[fornecedores-clientes]] e [[empregados-contratos]].

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Rotas de relatório no frontend não têm guarda de permissão** (2026-06-08 20:15) — diferente das outras telas, `pages/reports/*` não usam `PermissionProtectedRoute`; a autorização depende exclusivamente do `report.canView` no backend. Endurecer no front se necessário.

## Estado congelado (se houver)

_(nenhum)_
