# State — SPEC-20260609-1111

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-09 11:11

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-09 11:59
**Onde tô:** **Toda a Onda 0 implementada** (exceto E-sort, deferido). erp-back build limpo; erp-front `npm run build` limpo. Commits: back bc93f49/a4dd879/c803479, front 780660e/3073be4.
**Próximo passo:** (autônomo) FECHAR a Onda 0 — marcar critérios A-D no main.md, rescopar E (tirar column-sort → "Itens deferidos") e marcar; atualizar as 5 features (R.7); [conclusão] + memory TL;DR; mover SPEC para archive; `scripts/lint-docs.sh`; merge `feature/onda-0-quick-wins` na **main local** (erp-back + erp-front). Depois iniciar Onda 1.
**Última decisão:** column-sort (E-sort) DIFERIDO — flag para o usuário; será movido para "Itens deferidos" do main.md (não marcar como done).
**Bloqueio atual:** nenhum. Execução autônoma.
**Se retomar, ler:** este TL;DR + tabela de fases + log de 2026-06-09 11:59.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| A | Gating de local por empregado (backend) | concluído | 2026-06-09 11:19 | `bc93f49` |
| A-ui | UI dos locais permitidos na ficha do empregado | concluído | 2026-06-09 11:51 | `780660e` |
| B-be | Endpoint reject de justificativa | concluído | 2026-06-09 11:51 | `c803479` |
| B-ui | Tela de gestor: aprovar/rejeitar/abonar | concluído | 2026-06-09 11:59 | `3073be4` |
| C | Trocar senha logado + admin-reset + e-mail SMTP (backend) | concluído | 2026-06-09 11:43 | `a4dd879` |
| C-ui | Form de trocar senha + botão admin-reset | concluído | 2026-06-09 11:59 | `3073be4` |
| D | Correções de ponto (data + MinutesExpected) — backend | concluído | 2026-06-09 11:19 | `bc93f49` |
| E | Trava empréstimo c/ parcela paga (backend) | concluído | 2026-06-09 11:43 | `a4dd879` |
| E-ui | Confirmar Sair + persistir sidebar | concluído | 2026-06-09 11:51 | `780660e` |
| E-sort | Ordenação por clique no cabeçalho (DIFERIDO — flag p/ usuário) | pendente | 2026-06-09 11:51 | — |

### Próximos passos

- [x] Implementar backend item A + ajustar ValidateLocation (2026-06-09 11:19, commit `bc93f49`)
- [x] Item D no TimeClockService (data + MinutesExpected) (2026-06-09 11:19, commit `bc93f49`)
- [x] Build erp-back (clean: 0 erros) (2026-06-09 11:19)
- [ ] UI item A na ficha do empregado (EmployeeForm)
- [ ] Verificar front do dashboard de ponto envia a data
- [ ] Itens B (justificativa), C (senha), E (UX)

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-09 11:11] `tb_employee_allowed_location` já existe (entidade mapeada no ErpContext + migration 006 cascade). Fonte: ErpContext.cs, employeeAllowedLocation.cs. → item A NÃO precisa de migração.
- [2026-06-09 11:11] `TimeClockService.ValidateLocation` itera TODOS os locais da empresa, ignorando a whitelist. Fonte: TimeClockService.cs:138-160.
- [2026-06-09 11:11] `GetDashboardAsync` calcula `targetDate` mas chama `GetTodaySummaryAsync` que usa `DateTime.UtcNow.Date` fixo. `MinutesExpected = 480` fixo. Fonte: TimeClockService.cs:96,108-121.
- [2026-06-09 11:11] `ICompanySettingRepository.GetByCompanyIdAsync` existe; `CompanySetting.WeeklyHoursDefault` disponível. → base do MinutesExpected interino.
- [2026-06-09 11:11] Build de baseline do erp-back OK (exit 0).

## Inferências prováveis

- [2026-06-09 11:11] Permissão de gestão da whitelist = `employee.canView`/`canEdit` (config por empregado). Validar com UX da ficha do empregado.

## Dúvidas em aberto

- [2026-06-09 11:11] MinutesExpected interino: dividir WeeklyHoursDefault por 5 ou considerar AllowWeekendWork? Próxima ação: usar /5 por ora; jornada real fica para Onda 2.

---

## Log cronológico (APPEND-ONLY — NUNCA editar entradas antigas)

## 2026-06-09 11:11 — [ativação]

Programa "Migração Fazenda → Meu Gestor" iniciado. Onda 0 (Tier 1, sem schema) ativada após confirmação do usuário (começar pela Onda 0; migrações = eu escrevo+aplico na Railway e mantenho erp.sql; rastrear com SPEC por onda). Branches `feature/onda-0-quick-wins` criadas em erp-back e erp-front. Plano: A (gating local) → D (correções ponto) → build → UI A → B/C/E. Arquivos relevantes lidos: TimeClockService, employeeAllowedLocation (entidade+DTOs+mapper), ErpUnitOfWork, LocationRepository/ILocationRepository, ICompanySettingRepository.

## 2026-06-09 11:19 — [MARCO] Itens A e D (backend) concluídos

**A — gating de local por empregado:** criados `IEmployeeAllowedLocationRepository`+impl, `IEmployeeAllowedLocationService`+impl, `EmployeeAllowedLocationController` (`api/employee-allowed-location`: GET/PUT por empregado), `SetEmployeeAllowedLocationsDTO`; `OutputDTO` ganhou `LocationName`; wiring em `IUnitOfWork`/`ErpUnitOfWork`/`ServiceConfiguration`. `TimeClockService.ValidateLocation` agora filtra pela whitelist do empregado (vazia = todos os locais). Sem migração (tabela já existia).

**D — correções de ponto:** `GetTodaySummaryAsync(employeeId, date?)` parametrizado; `GetDashboardAsync` passa `targetDate` (corrige o bug de ignorar a data); novo `GetExpectedDailyMinutes(companyId)` substitui o `480` fixo por `WeeklyHoursDefault*60/5` (interino — jornada real é Onda 2). Tempo-até-agora só soma se a data for hoje.

**Descoberta [tentativa→correção]:** bare `Task` (não-genérico) colide com a entidade `ERP.Domain.Entities.Task` → erro CS0104. Resolvido qualificando `System.Threading.Tasks.Task` no método void-async. Lição: nesta base, métodos async sem retorno DEVEM qualificar `System.Threading.Tasks.Task` (ou retornar genérico).

Build verificado limpo via output alternativo (`bin/_verify_onda0`): 0 erros, 0 avisos. Commit `bc93f49` na branch `feature/onda-0-quick-wins` (erp-back). Obs.: a DLL estava travada porque o backend do usuário estava em execução (debug) — precisa reiniciar para pegar as mudanças.

## 2026-06-09 11:43 — [MARCO] Itens C e E (backend) concluídos — backend da Onda 0 completo

Usuário deu mandato de **execução autônoma de todas as ondas** (branch+SPEC por onda, merge na main **local** por onda, **push só no fim da última onda**; e-mail = SMTP config+log; **sem Equipamentos**; **sem reconhecimento facial** — adiado). ⚠️ Usuário colou credenciais AWS reais no chat → NÃO armazenadas; recomendei rotacionar.

**E:** `LoanAdvanceService.UpdateByIdAsync`/`DeleteByIdAsync` agora bloqueiam quando `InstallmentsPaid > 0`.

**C:** `AuthService.ChangePasswordAsync` + `POST api/auth/change-password` ([Authorize], valida senha atual); `UserService.SetPasswordAsync` + `PUT api/user/{id}/password` ([RequirePermissions user.canEdit], admin-reset); `EmailService` reescrito com SMTP por config (`Smtp:Host/Port/User/Password/From`, `App:FrontendUrl`) + fallback de log. `UserService` ganhou `IPasswordHashService`.

Build limpo (0 erros). Commit `a4dd879`. **Backend da Onda 0 = A+C+D+E prontos.** Falta: B-backend (reject) + frontend de A/B/C/E.

## 2026-06-09 11:43 — [nota] Continuação autônoma agendada

Agendado wakeup para retomar e implementar o frontend da Onda 0 + B-backend, fechar a Onda 0 (merge na main local) e seguir para a Onda 1. Próximo foco: ler `erp-front` (EmployeeForm, Absences, Header, Sidebar, services) e implementar a UI, verificando com `npm run build`.

## 2026-06-09 11:51 — [MARCO] B-backend + frontend de A e E

**B-be:** `JustificationService.RejectAsync` + `POST api/justification/{id}/reject` (`justification.canManage`), `JustificationRejectDTO`. Build erp-back limpo. Commit `c803479`.

**A-ui:** `employeeAllowedLocationService.ts` (GET/PUT) + seção "Locais de ponto permitidos" na `EmployeeForm` (só em edição): checkboxes dos locais ativos, vazia = todos. **E-ui:** `Header` confirma ao Sair (`window.confirm`); `Sidebar` persiste `expandedMenus` em `localStorage` (init lazy + useEffect). `tsc -b` limpo. Commit `780660e` (erp-front).

**Decisão [R.6.2]:** `E-sort` (ordenação por clique no cabeçalho) DIFERIDO — retrofit em ~12 listas é um pass de UX próprio, baixo valor relativo ao resto do programa; **flag para o usuário** decidir, sem marcar o critério E como completo. Não bloqueia.

Falta para fechar a Onda 0: C-ui (alterar senha + admin-reset) e B-ui (tela de aprovação). Continuação autônoma agendada.

## 2026-06-09 11:59 — [MARCO] Frontend C-ui e B-ui concluídos — Onda 0 implementada

**C-ui:** `ChangePassword.tsx` (POST auth/change-password) + rota `/change-password` + link "Alterar Senha" no Header; botão "Redefinir senha" na `EditUser` (PUT user/{id}/password). `authService.changePassword`, `userService.setPassword`. **B-ui:** `Absences.tsx` — gestor (`justification.canManage`) aprova (modal com horas abonadas + observação) / rejeita (motivo) justificativas pendentes; `timeClockService.rejectJustification`. erp-front `npm run build` limpo (✓ built, 2499 módulos). Commit `3073be4` (erp-front).

Onda 0 está 100% implementada salvo **E-sort** (ordenação por coluna), deferido. Próximo: fechar a onda (rescopar/marcar critérios, R.7 nas 5 features, archive, merge na main local) e iniciar Onda 1.

## 2026-06-09 12:04 — [conclusão] Onda 0 concluída e arquivada

Todos os critérios A–E marcados. E **rescopado**: a "ordenação por clique no cabeçalho" foi removida do critério E e movida para a seção "Itens deferidos" do main.md (flag para o usuário decidir; backend já suporta OrderBy). As 5 features tocadas (ponto-eletronico, justificativas, auth, plataforma-core, emprestimos-adiantamentos) ganharam linha em "Concluídas" (R.7). `main.md` Status: done; Commit final erp-back `c803479` / erp-front `3073be4`. Pasta movida `active/` → `archive/`. Merge `feature/onda-0-quick-wins` → **main LOCAL** em erp-back e erp-front (sem push, conforme mandato).

**Entregue na Onda 0:** gating de local por empregado (fecha furo de segurança), endpoint reject + tela de aprovar/rejeitar/abonar justificativa, alterar a própria senha + admin-reset + e-mail SMTP real, dashboard de ponto respeita a data + previsto da config (não 480 fixo), confirmação ao Sair + sidebar persistida, trava de edição/exclusão de empréstimo com parcela paga.
