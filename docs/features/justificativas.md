# Feature: justificativas

**Keywords:** justificativa, abono, ausência, atraso, aprovação, justification, ponto, horas
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/JustificationController.cs
  - erp-back/-2-Application/Services/JustificationService.cs
  - erp-back/-3-Infrastructure/Repositories/JustificationRepository.cs
  - erp-back/-1-Domain/Entities/justification.cs
  - erp-front/src/pages/time-clock/Absences.tsx
**Resumo:** Justificativas de ausência/atraso e abonos de ponto (`Justification`), com fluxo de aprovação por um gestor e concessão opcional de horas.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1111 | 2026-06-09 | `c803479` | Onda 0: endpoint `reject` + tela de gestor aprovar/rejeitar/abonar (Absences) |
| SPEC-20260609-1212 | 2026-06-09 | `4ef7c66` | Onda 1: anexo de justificativa (upload Base64 + visualização) |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/justification`: `GET getPaged` + `GET my` (próprias) + `GET {id}` (`justification.canView`), `create` (`canCreate`), `PUT {id}` (`canEdit`), `DELETE {id}` (`canDelete`) e **`POST {id}/approve`** (`justification.canManage`). `Justification` tem `EmployeeId`, `ReferenceDate`, `Reason`, `AttachmentUrl` (opcional), `HoursGranted` (horas abonadas), `UserIdApprover` e `Status`. Complementa o [[ponto-eletronico]]: registra por que faltou/atrasou e, quando aprovada, abona horas. A tela fica em `/time-clock/absences`.

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Aprovação separada por `canManage`** (origem: estado pré-SPEC, 2026-06-08 20:15) — criar/editar a própria justificativa usa `canCreate`/`canEdit`; aprovar/rejeitar/abonar as de outros exige `justification.canManage`.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **`HoursGranted` só vale após aprovação** (2026-06-08 20:15) — o abono de horas depende do fluxo `approve`; uma justificativa pendente não abona ponto.

## Estado congelado (se houver)

_(nenhum)_
