# Feature: usuarios

**Keywords:** usuário, user, conta, identidade, cpf, e-mail, telefone, cadastro
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/UserController.cs
  - erp-back/-2-Application/Services/userService.cs
  - erp-back/-3-Infrastructure/Repositories/userRepository.cs
  - erp-back/-1-Domain/Entities/user.cs
  - erp-front/src/services/userService.ts
  - erp-front/src/pages/users/
**Resumo:** Cadastro global de usuários (identidade: e-mail, telefone, CPF, hash de senha) — a entidade `User` é a identidade que faz login; o vínculo com empresa e cargo vive em `CompanyUser` ([[empresas-multitenancy]]).

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260611-1249 | 2026-06-11 | — | Onda 8: lista de usuários mostra o **apelido do empregado associado** (`CompanyUserOutputDTO.EmployeeNickname`, lookup por `UserId` no service) |
| SPEC-20260612-1215 | 2026-06-12 | `9dd7de7`/`715020e` | Onda 10: criar usuário pela tela de funcionários **não apaga mais o form** em erro de duplicidade e **oferece associar o usuário existente** (busca via `POST api/employee/searchUserByData`) |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/user` seguindo o padrão CRUD canônico (`getAll`, `getPaged`, `GET {userId}`, `create`, `PUT {userId}`, `DELETE {userId}`) sob permissões `user.canView/canCreate/canEdit/canDelete`. `User` carrega a identidade (`Email`, `Phone`, `Cpf`, `PasswordHash`) e os campos de reset de senha (`ResetToken`, `ResetTokenExpiresAt`). É entidade **global** (não tem `CompanyId`) — um mesmo usuário pode pertencer a várias empresas via `CompanyUser`.

Na prática, as telas de "usuários" do frontend (`/users`) operam sobre `CompanyUser` (vínculo usuário↔empresa↔cargo) e usam as permissões `user.*`; o CRUD de `User` puro é a base de identidade reutilizada por [[auth]], [[empregados-contratos]] (associação empregado↔usuário) e [[empresas-multitenancy]].

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **`User` é identidade global sem `CompanyId`** (origem: estado pré-SPEC, 2026-06-08 20:15) — multi-tenancy é modelada por `CompanyUser`, permitindo um login pertencer a N empresas com cargos distintos.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Permissões usam o módulo `user`, mas a tela edita `CompanyUser`** (2026-06-08 20:15) — `UserController` e `CompanyUserController` compartilham as permissões `user.*`; o gerenciamento de acesso à empresa é feito por `CompanyUser`, não por `User`. Ver [[empresas-multitenancy]].

## Estado congelado (se houver)

_(nenhum)_
