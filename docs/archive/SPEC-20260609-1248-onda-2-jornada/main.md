# SPEC-20260609-1248: Onda 2 — Jornada de trabalho

**Status:** done
**Criada:** 2026-06-09 12:48
**Ativada:** 2026-06-09 12:48
**Concluída:** 2026-06-09 13:01
**Pausada em:** —
**Commit final:** erp-back `d9100bd` / erp-front `22aa1b7`
**Keywords:** jornada, escala, work-schedule, contrato, ponto, minutos-previstos, migração
**Features:** jornada-trabalho, empregados-contratos, ponto-eletronico
**Branch:** feature/onda-2-jornada (erp-back + erp-front)
**Depende de:** SPEC-20260609-1212
**Bloqueia:** SPEC Onda 3 (relatórios de ponto + banco de horas — depende de minutos previstos por jornada)
**Ordem no programa:** 3/7 — programa "Migração Fazenda → Meu Gestor"
**Origem:** comparativo [docs/comparativo-sistema-antigo.md](../../comparativo-sistema-antigo.md) (Tier 1 — jornada/escala); usuário em 2026-06-09 (mandato de execução autônoma)
**Resumo:** Cadastro de jornadas de trabalho (escalas) e vínculo ao contrato, destravando o cálculo de minutos previstos no ponto (remove o fallback fixo de 8h).

## Objetivo

Permitir cadastrar jornadas (minutos por dia da semana + intervalo) e associar uma jornada ao contrato do empregado, de modo que o ponto eletrônico calcule os **minutos previstos** por dia a partir da jornada real (e não mais de um valor fixo/horas-semanais÷5). Fundação para banco de horas (Onda 3).

## Escopo

**DENTRO:**
- **J** — Entidade `WorkSchedule` + tabela `tb_work_schedule` + CRUD canônico `api/work-schedule` (permissões `companySettings.*`) + feature doc nova `jornada-trabalho`.
- **K** — FK `work_schedule_id` nullable em `Contract` (entidade/DTO/mapper) + seletor de jornada no `ContractForm`.
- **L** — Destrave do ponto: `TimeClockService.GetExpectedDailyMinutes` usa a jornada do contrato ativo (minutos do dia da semana − intervalo); fallbacks horas-semanais÷5 e 480.
- Frontend: tela de lista + formulário de jornadas + rota `/work-schedules` + item no sidebar.

**FORA:**
- Banco de horas / saldo acumulado (Onda 3); consumo de feriados no cálculo do previsto (futuro); validação de escala vs. registros (futuro); tolerância/arredondamento de ponto (futuro).

## Implementação

Migração SQL numerada (027) aplicada na Railway via `node .migrate/apply.js` + `erp.sql` atualizado; entidade/DTO/mapper/repo/service/controller no back (padrão CostCenter/Holiday/FinancialCategory) + registro em ErpContext/UnitOfWork/IoC; UI no front; `dotnet build` + `npm run build` limpos. Migração só ADITIVA (tabela + coluna nova nullable) — prod só é deployada no push final.

## Critério de aceite

- [x] **J** — `WorkSchedule` (entidade + `tb_work_schedule` + CRUD `api/work-schedule` + feature doc) (2026-06-09 12:55)
- [x] **K** — FK `work_schedule_id` no contrato (back + seletor no ContractForm) (2026-06-09 13:01, commits `d9100bd`/`22aa1b7`)
- [x] **L** — destrave do ponto (`GetExpectedDailyMinutes` por jornada do contrato ativo) (2026-06-09 13:01, commit `d9100bd`)
- [x] frontend: lista + form de jornadas + rota `/work-schedules` + sidebar (2026-06-09 13:01, commit `22aa1b7`)
- [x] migração 027 aplicada na Railway + `erp.sql` atualizado (2026-06-09 12:50)
- [x] erp-back compila e erp-front builda (2026-06-09 13:01)
- [x] **Features tocadas (jornada-trabalho, empregados-contratos, ponto-eletronico) atualizadas** com timestamp e referência a esta SPEC (2026-06-09 13:01)
- [x] `state.md` com entrada `[conclusão]` (2026-06-09 13:01)
- [x] `memory.md` com TL;DR final atualizado (2026-06-09 13:01)
