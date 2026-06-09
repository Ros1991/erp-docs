# Feature: notificacoes

**Keywords:** notificação, sino, header, alerta, lida, não lida, pendência
**Arquivos principais:**
  - erp-back/-1-Domain/Entities/notification.cs
  - erp-back/-4-WebApi/Controllers/NotificationController.cs
  - erp-back/-2-Application/Services/NotificationService.cs
  - erp-back/-1-Domain/database/migrations/031_onda6_final.sql
  - erp-front/src/components/layout/NotificationBell.tsx
  - erp-front/src/services/notificationService.ts
**Resumo:** Módulo de notificações por usuário (sino no header com contador e dropdown), com marcar lida/todas e endpoint de criação para gatilhos do sistema.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1538 | 2026-06-09 | `385b415` | Onda 6: módulo de notificações + sino no header |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | Gatilhos automáticos | Gerar notificação ao criar justificativa / atribuir tarefa / pendências de ponto |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

`Notification` (tabela `tb_notification`, por `CompanyId`+`UserId`): `Title`, `Message`, `Type`, `Link`, `IsRead` + auditoria. API `api/notification` (apenas `[Authorize]`, sem permissão de módulo — recurso pessoal):
- `GET mine?onlyUnread=&take=` · `GET unread-count` · `POST create` · `POST {id}/read` · `POST read-all` · `DELETE {id}`.

Front: `NotificationBell` no `Header` — ícone com badge de não lidas (poll a cada 60s), dropdown com as últimas 20, clique marca como lida e navega pelo `Link`, e "Marcar todas como lidas".

> Última atualização: 2026-06-09 (SPEC-20260609-1538)

## Decisões arquiteturais ativas

- **Notificação é pessoal** (origem: SPEC-20260609-1538, 2026-06-09) — escopada por `UserId`+`CompanyId`; sem `RequirePermissions` (só autenticação). Cada usuário vê e gerencia apenas as suas.
- **Criação exposta para o sistema** (origem: SPEC-20260609-1538) — `POST create` permite que gatilhos internos gerem notificações; os gatilhos automáticos (ponto/justificativa/tarefa) ficam para iteração futura.

## Alternativas consideradas e rejeitadas

- **WebSocket/push em tempo real** (2026-06-09, SPEC-20260609-1538) — adiado: o poll de 60s no sino é suficiente para o ciclo atual; tempo real é evolução futura.

## Gotchas

- **Sem gatilhos automáticos ainda** (2026-06-09, SPEC-20260609-1538) — a infra (criar/listar/marcar) está pronta, mas nada gera notificações automaticamente; é preciso chamar `create` nos pontos de interesse.

## Estado congelado (se houver)

_(nenhum)_
