# State — SPEC-20260609-1248

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-09 12:48

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-09 13:01
**Onde tô:** Onda 2 CONCLUÍDA e arquivada. Back+front buildam limpo, mergeados na main LOCAL (erp-back `c50fd96`, erp-front `1f556c5`; commits da feature `d9100bd`/`22aa1b7`). Sem push (só na última onda).
**Próximo passo:** Onda 3 — relatórios de ponto + banco de horas (consome os minutos previstos por jornada desta onda).
**Última decisão:** minutos por dia da semana (mon..sun) + intervalo único; tipo `Normal|Escala|Flexivel` (texto livre por ora). Folga = 0 minutos previstos é resultado válido.
**Bloqueio atual:** nenhum. Execução autônoma.
**Se retomar, ler:** este TL;DR + tabela de fases.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| J | WorkSchedule (entidade + CRUD + feature doc) | concluído | 2026-06-09 13:01 | `d9100bd`/`22aa1b7` |
| K | FK work_schedule_id no contrato + seletor | concluído | 2026-06-09 13:01 | `d9100bd`/`22aa1b7` |
| L | Destrave do ponto (GetExpectedDailyMinutes) | concluído | 2026-06-09 13:01 | `d9100bd` |

### Próximos passos

- [ ] Frontend jornadas (service + lista + form + rota + sidebar)
- [ ] Seletor de jornada no ContractForm + contractService
- [ ] Fechar onda (critérios + features + archive + merge na main local)

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-09 12:50] Migração `027_create_work_schedule.sql` aplicada na Railway: criada `tb_work_schedule` (company_id, name, type, minutes mon..sun, break, is_active + audit) + índice por company; `tb_contract` ganhou `work_schedule_id bigint NULL`. Confirmado por `--query`.
- [2026-06-09 12:55] Backend completo: `WorkSchedule` entity/DTOs/mapper/repo/service/controller (`api/work-schedule`, permissões `companySettings.*`), registrado em ErpContext/UnitOfWork/IoC. `Contract` + `WorkScheduleId` (entity/DTO/mapper). `TimeClockService.GetExpectedDailyMinutes(employeeId, companyId, date)` usa a jornada do contrato ativo. `dotnet build` 0 erros.

## Inferências prováveis

- [2026-06-09 12:48] Jornada usa permissões `companySettings.*` (mesmo critério de Feriados/Plano de contas — cadastro de configuração da empresa).

## Dúvidas em aberto

- [2026-06-09 12:48] `work_schedule_type` é só rótulo por ora (Normal/Escala/Flexivel) — sem lógica diferenciada. Escala real (ciclos 12x36 etc.) fica para iteração futura.

---

## Log cronológico (APPEND-ONLY)

## 2026-06-09 12:48 — [ativação]

Onda 2 ativada (programa 3/7, depende de SPEC-20260609-1212 concluída/mergeada). Branches `feature/onda-2-jornada` em erp-back e erp-front. Migração 027 escrita.

## 2026-06-09 12:55 — [MARCO] Backend da jornada concluído

027 aplicada na Railway + `erp.sql` atualizado. Back: `WorkSchedule` CRUD canônico (`api/work-schedule`) registrado; `Contract.WorkScheduleId` em entity/DTO/mapper; destrave do ponto em `GetExpectedDailyMinutes` (jornada do contrato ativo → minutos do dia − intervalo; fallback horas-semanais÷5 → 480). `dotnet build` 0 erros. Falta o frontend.

## 2026-06-09 13:01 — [conclusão] Onda 2 concluída e arquivada

Todos os critérios J,K,L + frontend marcados. Frontend: `workScheduleService`, telas `WorkSchedules`/`WorkScheduleForm` (horas por dia da semana, intervalo, tipo, total semanal, "igualar seg→sex"), rotas `/work-schedules` (+new/edit), item "Jornadas" no sidebar (Cadastros), seletor de jornada no `ContractForm`. `npm run build` ✓; `dotnet build` 0 erros. Features tocadas atualizadas (R.7): jornada-trabalho (nova), empregados-contratos (FK jornada), ponto-eletronico (minutos previstos por jornada). Commits — feature `d9100bd` (back) / `22aa1b7` (front); merge `feature/onda-2-jornada`→main LOCAL: erp-back `c50fd96` / erp-front `1f556c5` (sem push). `main.md` Status: done. Pasta movida `active/`→`archive/`.

**Entregue na Onda 2:** cadastro de jornadas de trabalho (escalas) com minutos por dia da semana + intervalo; vínculo da jornada ao contrato; ponto passa a calcular as horas previstas do dia a partir da jornada do contrato ativo (com fallback para a config da empresa).
