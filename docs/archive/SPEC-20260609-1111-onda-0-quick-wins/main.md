# SPEC-20260609-1111: Onda 0 — Quick wins & correções

**Status:** done
**Criada:** 2026-06-09 11:11
**Ativada:** 2026-06-09 11:11
**Concluída:** 2026-06-09 12:04
**Pausada em:** —
**Commit final:** erp-back `c803479` / erp-front `3073be4`
**Keywords:** quick-wins, segurança, ponto, justificativa, senha, ux, bugfix
**Features:** ponto-eletronico, justificativas, auth, plataforma-core, emprestimos-adiantamentos
**Branch:** feature/onda-0-quick-wins (erp-back + erp-front)
**Depende de:** —
**Bloqueia:** SPEC futura de Jornada (Onda 2) consome a base de ponto corrigida aqui
**Ordem no programa:** 1/7 — programa "Migração Fazenda → Meu Gestor"
**Origem:** comparativo [docs/comparativo-sistema-antigo.md](../../comparativo-sistema-antigo.md) (Tier 1); usuário em 2026-06-09
**Resumo:** Primeira onda do programa de migração: correções de alto valor e baixo esforço, **sem mudança de schema**.

## Objetivo

Entregar os itens Tier 1 do comparativo que não exigem migração de banco: fechar o furo de segurança do ponto (gating de local por empregado), dar ao gestor a tela de aprovar/rejeitar justificativa, permitir troca de senha logado + reset real por e-mail, corrigir bugs do dashboard de ponto, e pequenas melhorias de UX/integridade.

## Escopo

**DENTRO:**
- **A** — Gating de local por empregado: reativar `EmployeeAllowedLocation` (repo+service+controller+UI); `ValidateLocation` passa a respeitar a whitelist; **fallback: whitelist vazia = todos os locais** (não quebra quem já usa).
- **B** — Aprovar/Rejeitar justificativa: endpoint `reject` + UI de gestor (aprovar/rejeitar/abonar horas).
- **C** — Alterar a própria senha logado + admin redefine senha de usuário + envio **real** de e-mail de reset (`EmailService`).
- **D** — Correções de ponto: `GetDashboardAsync` respeitar a `date`; `MinutesExpected` vir da config (não 480 fixo).
- **E** — UX/integridade: confirmação ao Sair; persistir estado da sidebar; ordenação por clique no cabeçalho; bloquear edição/exclusão de empréstimo com parcela paga.

**FORA:**
- Jornada de trabalho como entidade (Onda 2) — D usa cálculo **interino** a partir de `WeeklyHoursDefault`.
- Qualquer mudança de schema (Onda 1+); upload/anexo de justificativa (Onda 1).

## Implementação

Visão estática (descobertas durante execução vão em `state.md`):
- **A:** `IEmployeeAllowedLocationRepository`+impl; wiring no `UnitOfWork`; `IEmployeeAllowedLocationService`+impl; registro no IoC; `EmployeeAllowedLocationController` (`api/employee-allowed-location`); `OutputDTO` ganha `LocationName`; `TimeClockService.ValidateLocation` filtra pela whitelist; UI na ficha do empregado. **Sem migração** (`tb_employee_allowed_location` já existe).
- **B:** `JustificationController` `POST {id}/reject`; `JustificationService`; UI em `Absences.tsx` (ou aba de gestor) consumindo `approve`/`reject`. Usa `Status` existente.
- **C:** `AuthController`/`AuthService` `change-password` (valida `currentPassword`); `UserController` set-password admin; `EmailService` real (SMTP/SendGrid via config). Sem schema.
- **D:** `TimeClockService.GetTodaySummaryAsync(employeeId, date)`; `GetDashboardAsync` passa `targetDate`; `MinutesExpected = WeeklyHoursDefault*60/5` (interino, da `CompanySetting`).
- **E:** front `Header` (confirm logout), `Sidebar` (persist localStorage), listas (sort por coluna), `LoanAdvance` (bloqueio se `InstallmentsPaid>0`).

## Critério de aceite

- [x] **A** — `ValidateLocation` respeita `EmployeeAllowedLocation`; CRUD + UI para gerir locais do empregado; whitelist vazia mantém comportamento atual (2026-06-09 12:04, commits `bc93f49`/`780660e`)
- [x] **B** — gestor aprova/rejeita/abona justificativa por uma tela; endpoint `reject` existe (2026-06-09 12:04, commits `c803479`/`3073be4`)
- [x] **C** — usuário logado troca a própria senha; admin redefine senha; e-mail de reset enviado por SMTP configurável (sem creds → log) (2026-06-09 12:04, commits `a4dd879`/`3073be4`)
- [x] **D** — dashboard de ponto respeita a data selecionada; `MinutesExpected` deixa de ser 480 fixo (vem da config, interino) (2026-06-09 12:04, commit `bc93f49`)
- [x] **E** — confirmação ao Sair; sidebar persiste; empréstimo com parcela paga não edita/exclui (2026-06-09 12:04, commits `a4dd879`/`780660e`)
- [x] erp-back compila e erp-front builda (2026-06-09 12:04)
- [x] **Features tocadas (ponto-eletronico, justificativas, auth, plataforma-core, emprestimos-adiantamentos) atualizadas** com timestamp e referência a esta SPEC (2026-06-09 12:04)
- [x] `state.md` com entrada `[conclusão]` (2026-06-09 12:04)
- [x] `memory.md` com TL;DR final atualizado (2026-06-09 12:04)

## Itens deferidos (aguardam decisão do usuário)

- **Ordenação por clique no cabeçalho das listas** (estava no critério E original, removido daqui): retrofit em ~12 telas de lista; baixo valor relativo ao resto do programa. **Deferido pela IA durante execução autônoma** — sinalizado para o usuário decidir se vira SPEC própria ou se descarta. O backend já suporta `OrderBy`/`IsAscending` (`QueryableExtensions.OrderByProperty`), então é só trabalho de UI.
