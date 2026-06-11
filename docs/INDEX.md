# Index — features do projeto Meu Gestor

> **GERADO AUTOMATICAMENTE.** Edição manual PROIBIDA.
> Regenerado por `scripts/generate-index.sh` a cada push na `main`.
>
> Última regeneração: 2026-06-11 12:48 (commit `cede9b3`)

---

## Features

### abonos-ferias

- **Keywords:** abono, dias, horas, férias, período aquisitivo, gozo, vacation, Empregados_Abonos
- **Resumo:** Cadastro de abonos (Dias/Horas) e de períodos de férias (aquisitivo + gozo) por empregado — registros de RH portados do sistema antigo.
- **Arquivos principais:**
    - erp-back/-1-Domain/Entities/employeeAbono.cs
    - erp-back/-1-Domain/Entities/vacationPeriod.cs
    - erp-back/-4-WebApi/Controllers/EmployeeAbonoController.cs
    - erp-back/-4-WebApi/Controllers/VacationPeriodController.cs
    - erp-back/-2-Application/Services/EmployeeAbonoService.cs
    - erp-back/-2-Application/Services/VacationPeriodService.cs
    - erp-back/-1-Domain/database/migrations/030_onda5_pessoal.sql
    - erp-front/src/pages/personnel/Abonos.tsx
    - erp-front/src/pages/personnel/VacationPeriods.tsx
- → [features/abonos-ferias.md](features/abonos-ferias.md)

### auth

- **Keywords:** autenticação, login, jwt, token, refresh-token, senha, reset-password, sessão
- **Resumo:** Autenticação por JWT (login por e-mail/telefone/CPF + senha), emissão/renovação de tokens, fluxo de recuperação de senha, e o endpoint que entrega o mapa de permissões do usuário na empresa atual.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/AuthController.cs
    - erp-back/-2-Application/Services/authService.cs
    - erp-back/-4-WebApi/Middlewares/JwtMiddleware.cs
    - erp-back/-5-CrossCutting/IoC/JwtConfiguration.cs
    - erp-back/-5-CrossCutting/Services/TokenService.cs
    - erp-back/-5-CrossCutting/Services/PasswordHashService.cs
    - erp-back/-1-Domain/Entities/userToken.cs
    - erp-front/src/services/authService.ts
    - erp-front/src/contexts/AuthContext.tsx
    - erp-front/src/pages/auth/
- → [features/auth.md](features/auth.md)

### banco-de-horas

- **Keywords:** banco de horas, saldo de horas, hora extra, acerto, lançamento manual, crédito, débito, espelho de ponto
- **Resumo:** Saldo de horas (banco de horas) por empregado, formado por lançamentos manuais assinados (crédito/débito/acerto) e visualizado no espelho de ponto junto ao saldo computado do período.
- **Arquivos principais:**
    - erp-back/-1-Domain/Entities/timeBankAdjustment.cs
    - erp-back/-4-WebApi/Controllers/TimeBankAdjustmentController.cs
    - erp-back/-2-Application/Services/TimeBankAdjustmentService.cs
    - erp-back/-2-Application/Services/TimeClockService.cs (GetTimeMirrorAsync / GetPeriodSummaryAsync)
    - erp-back/-1-Domain/database/migrations/028_create_time_bank_adjustment.sql
    - erp-front/src/services/timeBankAdjustmentService.ts
    - erp-front/src/pages/time-clock/TimeMirror.tsx
- → [features/banco-de-horas.md](features/banco-de-horas.md)

### cargos-permissoes

- **Keywords:** cargo, role, permissão, rbac, modules-configuration, RequirePermissions, autorização, isAdmin
- **Resumo:** Controle de acesso por papéis (RBAC): cargos (`Role`) guardam um mapa de permissões em JSONB; o atributo `[RequirePermissions("module.action")]` protege endpoints e o frontend espelha as mesmas regras para esconder/bloquear telas.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/RoleController.cs
    - erp-back/-4-WebApi/Controllers/ModuleConfigurationController.cs
    - erp-back/-4-WebApi/Attributes/RequirePermissionsAttribute.cs
    - erp-back/-5-CrossCutting/Services/PermissionService.cs
    - erp-back/-5-CrossCutting/Services/ModuleConfigurationService.cs
    - erp-back/-4-WebApi/Configuration/modules-configuration.json
    - erp-back/-1-Domain/Entities/role.cs
    - erp-front/src/contexts/PermissionContext.tsx
    - erp-front/src/components/permissions/PermissionProtectedRoute.tsx
    - erp-front/src/pages/roles/
- → [features/cargos-permissoes.md](features/cargos-permissoes.md)

### centros-de-custo

- **Keywords:** centro de custo, cost center, rateio, distribuição, alocação, percentual
- **Resumo:** Centros de custo (`CostCenter`) e o rateio de valores entre eles — usados para alocar transações financeiras, títulos a pagar/receber e contratos, com distribuições default por empresa.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/CostCenterController.cs
    - erp-back/-2-Application/Services/CostCenterService.cs
    - erp-back/-3-Infrastructure/Repositories/CostCenterRepository.cs
    - erp-back/-1-Domain/Entities/costCenter.cs
    - erp-back/-1-Domain/Entities/defaultCostCenterDistribution.cs
    - erp-back/-1-Domain/Entities/transactionCostCenter.cs
    - erp-front/src/services/costCenterService.ts
    - erp-front/src/pages/cost-centers/
- → [features/centros-de-custo.md](features/centros-de-custo.md)

### contas-correntes

- **Keywords:** conta, account, conta corrente, saldo, caixa, banco
- **Resumo:** Cadastro de contas correntes/caixas da empresa (`Account`: nome, tipo, saldo inicial), sobre as quais as transações financeiras movimentam valores.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/AccountController.cs
    - erp-back/-2-Application/Services/accountService.cs
    - erp-back/-3-Infrastructure/Repositories/accountRepository.cs
    - erp-back/-1-Domain/Entities/account.cs
    - erp-front/src/services/accountService.ts
    - erp-front/src/pages/accounts/
- → [features/contas-correntes.md](features/contas-correntes.md)

### contas-pagar-receber

- **Keywords:** contas a pagar, contas a receber, payable, receivable, vencimento, baixa, pagamento, rateio
- **Resumo:** Títulos a pagar e a receber (`AccountPayableReceivable`: descrição, tipo, valor, vencimento, pago/não-pago), com rateio opcional por centro de custo; a baixa gera movimentação em [[transacoes-financeiras]].
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/AccountPayableReceivableController.cs
    - erp-back/-2-Application/Services/AccountPayableReceivableService.cs
    - erp-back/-3-Infrastructure/Repositories/AccountPayableReceivableRepository.cs
    - erp-back/-1-Domain/Entities/accountPayableReceivable.cs
    - erp-back/-1-Domain/Entities/accountPayableReceivableCostCenter.cs
    - erp-front/src/services/accountPayableReceivableService.ts
    - erp-front/src/pages/account-payable-receivable/
- → [features/contas-pagar-receber.md](features/contas-pagar-receber.md)

### empregados-contratos

- **Keywords:** empregado, funcionário, employee, contrato, contract, salário, benefício, desconto, gerente, associar usuário
- **Resumo:** Cadastro de empregados, sua hierarquia (gerente), a associação opcional com um `User` de login, e os contratos de trabalho (valor, tipo, encargos, benefícios/descontos e rateio por centro de custo) que alimentam a folha.
- **Arquivos principais:**
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
- → [features/empregados-contratos.md](features/empregados-contratos.md)

### empresas-multitenancy

- **Keywords:** empresa, company, multi-tenant, x-company-id, companyuser, vínculo, configurações, company-setting
- **Resumo:** Isolamento multi-tenant do ERP: empresas (`Company`), o vínculo usuário↔empresa↔cargo (`CompanyUser`), as configurações operacionais da empresa (`CompanySetting`) e o middleware que exige/valida o header `X-Company-Id` em toda requisição autenticada.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/CompanyController.cs
    - erp-back/-4-WebApi/Controllers/CompanyUserController.cs
    - erp-back/-4-WebApi/Controllers/CompanySettingController.cs
    - erp-back/-4-WebApi/Middlewares/CompanyContextMiddleware.cs
    - erp-back/-5-CrossCutting/Services/PermissionService.cs
    - erp-back/-1-Domain/Entities/company.cs
    - erp-back/-1-Domain/Entities/companyUser.cs
    - erp-back/-1-Domain/Entities/companySetting.cs
    - erp-front/src/pages/companies/CompanySelect.tsx
    - erp-front/src/pages/company-settings/
- → [features/empresas-multitenancy.md](features/empresas-multitenancy.md)

### emprestimos-adiantamentos

- **Keywords:** empréstimo, adiantamento, loan, advance, parcela, desconto em folha, vale
- **Resumo:** Empréstimos e adiantamentos a empregados (`LoanAdvance`: valor, parcelas, origem do desconto), descontados na folha de pagamento ao longo das parcelas.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/LoanAdvanceController.cs
    - erp-back/-2-Application/Services/LoanAdvanceService.cs
    - erp-back/-3-Infrastructure/Repositories/LoanAdvanceRepository.cs
    - erp-back/-1-Domain/Entities/loanAdvance.cs
    - erp-front/src/services/loanAdvanceService.ts
    - erp-front/src/pages/loan-advances/
- → [features/emprestimos-adiantamentos.md](features/emprestimos-adiantamentos.md)

### feriados

- **Keywords:** feriado, holiday, calendário, dia útil, recorrente, anual
- **Resumo:** Cadastro de feriados por empresa (descrição, data, recorrente/anual). Base para futuras regras de dia útil / jornada / folha.
- **Arquivos principais:**
    - erp-back/-1-Domain/Entities/holiday.cs
    - erp-back/-4-WebApi/Controllers/HolidayController.cs
    - erp-back/-2-Application/Services/HolidayService.cs
    - erp-back/-1-Domain/database/migrations/024_create_holiday.sql
    - erp-front/src/services/holidayService.ts
    - erp-front/src/pages/holidays/
- → [features/feriados.md](features/feriados.md)

### folha-pagamento

- **Keywords:** folha, payroll, salário, holerite, inss, fgts, irrf, décimo terceiro, férias, fechamento, snapshot, recalcular
- **Resumo:** Processamento da folha de pagamento por período: gera a folha a partir dos contratos, calcula proventos/descontos (INSS, FGTS, IRRF, benefícios, empréstimos), suporta 13º e férias, recalcula, fecha e reabre — com snapshot JSONB do cálculo.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/PayrollController.cs
    - erp-back/-2-Application/Services/PayrollService.cs
    - erp-back/-3-Infrastructure/Repositories/PayrollRepository.cs
    - erp-back/-1-Domain/Entities/payroll.cs
    - erp-back/-1-Domain/Entities/payrollEmployee.cs
    - erp-back/-1-Domain/Entities/payrollItem.cs
    - erp-front/src/services/payrollService.ts
    - erp-front/src/pages/payroll/
- → [features/folha-pagamento.md](features/folha-pagamento.md)

### fornecedores-clientes

- **Keywords:** fornecedor, cliente, supplier, customer, parceiro, cadastro
- **Resumo:** Cadastro unificado de fornecedores e clientes (`SupplierCustomer`) da empresa, referenciado por compras, títulos a pagar/receber e relatórios.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/SupplierCustomerController.cs
    - erp-back/-2-Application/Services/SupplierCustomerService.cs
    - erp-back/-3-Infrastructure/Repositories/SupplierCustomerRepository.cs
    - erp-back/-1-Domain/Entities/supplierCustomer.cs
    - erp-front/src/services/supplierCustomerService.ts
    - erp-front/src/pages/supplier-customers/
- → [features/fornecedores-clientes.md](features/fornecedores-clientes.md)

### jornada-trabalho

- **Keywords:** jornada, escala, work-schedule, minutos previstos, intervalo, horas por dia, ponto
- **Resumo:** Cadastro de jornadas de trabalho (escalas) com minutos por dia da semana + intervalo, vinculável ao contrato do empregado. O ponto calcula os minutos previstos do dia a partir da jornada do contrato ativo.
- **Arquivos principais:**
    - erp-back/-1-Domain/Entities/workSchedule.cs
    - erp-back/-4-WebApi/Controllers/WorkScheduleController.cs
    - erp-back/-2-Application/Services/WorkScheduleService.cs
    - erp-back/-2-Application/Services/TimeClockService.cs (GetExpectedDailyMinutes)
    - erp-back/-1-Domain/database/migrations/027_create_work_schedule.sql
    - erp-front/src/services/workScheduleService.ts
    - erp-front/src/pages/work-schedules/
- → [features/jornada-trabalho.md](features/jornada-trabalho.md)

### justificativas

- **Keywords:** justificativa, abono, ausência, atraso, aprovação, justification, ponto, horas
- **Resumo:** Justificativas de ausência/atraso e abonos de ponto (`Justification`), com fluxo de aprovação por um gestor e concessão opcional de horas.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/JustificationController.cs
    - erp-back/-2-Application/Services/JustificationService.cs
    - erp-back/-3-Infrastructure/Repositories/JustificationRepository.cs
    - erp-back/-1-Domain/Entities/justification.cs
    - erp-front/src/pages/time-clock/Absences.tsx
- → [features/justificativas.md](features/justificativas.md)

### notificacoes

- **Keywords:** notificação, sino, header, alerta, lida, não lida, pendência
- **Resumo:** Módulo de notificações por usuário (sino no header com contador e dropdown), com marcar lida/todas e endpoint de criação para gatilhos do sistema.
- **Arquivos principais:**
    - erp-back/-1-Domain/Entities/notification.cs
    - erp-back/-4-WebApi/Controllers/NotificationController.cs
    - erp-back/-2-Application/Services/NotificationService.cs
    - erp-back/-1-Domain/database/migrations/031_onda6_final.sql
    - erp-front/src/components/layout/NotificationBell.tsx
    - erp-front/src/services/notificationService.ts
- → [features/notificacoes.md](features/notificacoes.md)

### ordens-de-compra

- **Keywords:** ordem de compra, purchase order, solicitação, aprovação, requisitante, aprovador, canProcess, status
- **Resumo:** Solicitações de compra com fluxo de aprovação (`PurchaseOrder`: requisitante, aprovador, valor, status), com a ação `process` (aprovar/rejeitar) gated por permissão própria `purchaseOrder.canProcess`.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/PurchaseOrderController.cs
    - erp-back/-2-Application/Services/PurchaseOrderService.cs
    - erp-back/-3-Infrastructure/Repositories/PurchaseOrderRepository.cs
    - erp-back/-1-Domain/Entities/purchaseOrder.cs
    - erp-front/src/services/purchaseOrderService.ts
    - erp-front/src/pages/purchase-orders/
- → [features/ordens-de-compra.md](features/ordens-de-compra.md)

### plano-de-contas

- **Keywords:** plano de contas, categoria, receita, despesa, financial-category, dre
- **Resumo:** Categorização contábil (plano de contas) de Receita/Despesa, vinculável a títulos a pagar/receber (e, futuramente, transações), habilitando relatórios por categoria.
- **Arquivos principais:**
    - erp-back/-1-Domain/Entities/financialCategory.cs
    - erp-back/-4-WebApi/Controllers/FinancialCategoryController.cs
    - erp-back/-2-Application/Services/FinancialCategoryService.cs
    - erp-back/-1-Domain/database/migrations/025_create_financial_category.sql
    - erp-front/src/services/financialCategoryService.ts
    - erp-front/src/pages/financial-categories/
- → [features/plano-de-contas.md](features/plano-de-contas.md)

### plataforma-core

- **Keywords:** arquitetura, clean-architecture, baseresponse, basecontroller, unitofwork, efcore, npgsql, exception-filter, swagger, docker, railway, axios
- **Resumo:** O esqueleto compartilhado por todas as features: Clean Architecture em camadas, padrão Controller→Service→UnitOfWork→Repository→ErpContext, envelope `BaseResponse<T>`, tratamento global de exceções, paginação, e a fundação do frontend (axios + interceptors + guardas de rota + UI).
- **Arquivos principais:**
    - erp-back/Program.cs
    - erp-back/-4-WebApi/Controllers/Base/BaseController.cs
    - erp-back/-5-CrossCutting/Filters/GlobalExceptionFilter.cs
    - erp-back/-5-CrossCutting/Exceptions/DomainExceptions.cs
    - erp-back/-3-Infrastructure/UnitOfWork/ErpUnitOfWork.cs
    - erp-back/-3-Infrastructure/Data/ErpContext.cs
    - erp-back/-3-Infrastructure/Extensions/QueryableExtensions.cs
    - erp-back/-5-CrossCutting/IoC/ServiceConfiguration.cs
    - erp-front/src/services/api.ts
    - erp-front/src/contexts/ToastContext.tsx
    - erp-front/src/routes/index.tsx
    - erp-front/src/components/ui/
- → [features/plataforma-core.md](features/plataforma-core.md)

### ponto-eletronico

- **Keywords:** ponto, time clock, bater ponto, time entry, geolocalização, local, raio, dashboard, entrada, saída
- **Resumo:** Registro de ponto eletrônico com geolocalização: o funcionário bate ponto (`punch`) validado contra os locais permitidos (raio em metros), e gestores acompanham via dashboard.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/TimeClockController.cs
    - erp-back/-4-WebApi/Controllers/LocationController.cs
    - erp-back/-2-Application/Services/TimeClockService.cs
    - erp-back/-2-Application/Services/LocationService.cs
    - erp-back/-1-Domain/Entities/timeEntry.cs
    - erp-back/-1-Domain/Entities/location.cs
    - erp-back/-1-Domain/Entities/employeeAllowedLocation.cs
    - erp-front/src/pages/time-clock/
    - erp-front/src/services/timeClockService.ts
- → [features/ponto-eletronico.md](features/ponto-eletronico.md)

### relatorios

- **Keywords:** relatório, report, dashboard, fluxo de caixa, previsão, resumo financeiro, centro de custo, conta, somente-leitura
- **Resumo:** Relatórios e dashboards agregados (somente leitura) sobre os dados financeiros e operacionais — resumo financeiro, fluxo de caixa, previsão, por centro de custo, por conta, por fornecedor/cliente, contas a pagar/receber e conta do empregado.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/ReportController.cs
    - erp-back/-2-Application/Services/ReportService.cs
    - erp-front/src/services/reportService.ts
    - erp-front/src/pages/reports/
    - erp-front/src/pages/dashboard/Dashboard.tsx
- → [features/relatorios.md](features/relatorios.md)

### tarefas

- **Keywords:** tarefa, task, atribuição, execução, pausa, cronômetro, recorrência, status, imagem, comentário, minhas-tarefas
- **Resumo:** Gestão de tarefas com atribuição por empregado e ciclo de execução cronometrado (iniciar/pausar/retomar/parar/concluir/reabrir), recorrência por dias da semana, dependências entre tarefas, e anexos de imagem/comentários.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/TaskController.cs
    - erp-back/-2-Application/Services/TaskService.cs
    - erp-back/-1-Domain/Entities/task.cs
    - erp-back/-1-Domain/Entities/taskEmployee.cs
    - erp-back/-1-Domain/Entities/taskStatusHistory.cs
    - erp-back/-1-Domain/Entities/taskPause.cs
    - erp-back/-1-Domain/Entities/taskImage.cs
    - erp-back/-1-Domain/Entities/taskComment.cs
    - erp-front/src/pages/tasks/
    - erp-front/src/pages/my-tasks/
- → [features/tarefas.md](features/tarefas.md)

### transacoes-financeiras

- **Keywords:** transação, financial transaction, movimentação, fluxo de caixa, lançamento, somente-leitura, rateio
- **Resumo:** Livro de movimentações financeiras (`FinancialTransaction`) — **somente leitura via API**; os lançamentos são gerados internamente pela baixa de contas a pagar/receber, ordens de compra e folha, com rateio opcional por centro de custo.
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/FinancialTransactionController.cs
    - erp-back/-2-Application/Services/FinancialTransactionService.cs
    - erp-back/-3-Infrastructure/Repositories/FinancialTransactionRepository.cs
    - erp-back/-1-Domain/Entities/financialTransaction.cs
    - erp-back/-1-Domain/Entities/transactionCostCenter.cs
    - erp-front/src/services/financialTransactionService.ts
    - erp-front/src/pages/financial-transactions/
- → [features/transacoes-financeiras.md](features/transacoes-financeiras.md)

### usuarios

- **Keywords:** usuário, user, conta, identidade, cpf, e-mail, telefone, cadastro
- **Resumo:** Cadastro global de usuários (identidade: e-mail, telefone, CPF, hash de senha) — a entidade `User` é a identidade que faz login; o vínculo com empresa e cargo vive em `CompanyUser` ([[empresas-multitenancy]]).
- **Arquivos principais:**
    - erp-back/-4-WebApi/Controllers/UserController.cs
    - erp-back/-2-Application/Services/userService.cs
    - erp-back/-3-Infrastructure/Repositories/userRepository.cs
    - erp-back/-1-Domain/Entities/user.cs
    - erp-front/src/services/userService.ts
    - erp-front/src/pages/users/
- → [features/usuarios.md](features/usuarios.md)

