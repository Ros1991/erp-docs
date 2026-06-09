# Feature: ponto-eletronico

**Keywords:** ponto, time clock, bater ponto, time entry, geolocalização, local, raio, dashboard, entrada, saída
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/TimeClockController.cs
  - erp-back/-4-WebApi/Controllers/LocationController.cs
  - erp-back/-2-Application/Services/TimeClockService.cs
  - erp-back/-2-Application/Services/LocationService.cs
  - erp-back/-1-Domain/Entities/timeEntry.cs
  - erp-back/-1-Domain/Entities/location.cs
  - erp-back/-1-Domain/Entities/employeeAllowedLocation.cs
  - erp-front/src/pages/time-clock/
  - erp-front/src/services/timeClockService.ts
**Resumo:** Registro de ponto eletrônico com geolocalização: o funcionário bate ponto (`punch`) validado contra os locais permitidos (raio em metros), e gestores acompanham via dashboard.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1111 | 2026-06-09 | `bc93f49` | Onda 0: gating de local por empregado (EmployeeAllowedLocation ativo) + correções do dashboard (respeita a data; MinutesExpected da config, não 480 fixo) |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

**Ponto** — `api/time-clock`: `POST punch` (`timeClock.canPunch`), `GET today` + `GET today/summary` (`timeClock.canView`), `DELETE {id}` (`timeClock.canDelete`), `GET dashboard` (`timeClock.canManage`, visão de todos os funcionários). `TimeEntry` registra `Type` (entrada/saída/etc.), `Timestamp` (string), geolocalização (`Latitude`/`Longitude`) e o `LocationId`. A migration `022_add_task_and_timeclock_fields` ampliou os campos.

**Locais** — `api/location` (CRUD sob `location.*`). `Location` tem coordenadas (`Latitude`/`Longitude`), `RadiusMeters` e `IsActive`. `EmployeeAllowedLocation` define em quais locais cada empregado pode bater ponto; o `punch` valida a posição contra o raio do local permitido. Tolerância e regras de jornada vêm das configurações da empresa ([[empresas-multitenancy]]). Ausências/atrasos são tratados em [[justificativas]].

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Ponto validado por geocerca (raio do local)** (origem: estado pré-SPEC, 2026-06-08 20:15) — o `punch` exige posição dentro do `RadiusMeters` de um `Location` permitido ao empregado (`EmployeeAllowedLocation`).
- **Permissões granulares de ponto** (origem: estado pré-SPEC, 2026-06-08 20:15) — `canPunch` (bater), `canView` (ver os próprios), `canManage` (dashboard de todos), `canDelete`.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **`TimeEntry.Timestamp` é string** (2026-06-08 20:15) — modelado como `string` no `ErpContext` (não `DateTime`); cuidado ao ordenar/filtrar por data.
- **`canManage` é a permissão do dashboard** (2026-06-08 20:15) — a rota de dashboard do frontend (`/time-clock/dashboard`) e o endpoint exigem `timeClock.canManage`, não `canView`.

## Estado congelado (se houver)

_(nenhum)_
