# Memory — SPEC-20260609-1111

> Main: [main.md](./main.md)
> State: [state.md](./state.md)
> Criado: 2026-06-09 11:11

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-09 12:04 (sessão #1)
**Onde tô:** **SPEC CONCLUÍDA** — Onda 0 implementada (A,B,C,D,E), arquivada; merge na main LOCAL de erp-back e erp-front (sem push).
**Próximo passo:** programa segue na Onda 1 (cadastros & dados) numa nova SPEC/branch.
**Última decisão:** E-sort (ordenação por coluna) DEFERIDO — ver main.md "Itens deferidos" (flag p/ usuário). whitelist vazia = todos os locais; `MinutesExpected` interino da `CompanySetting`.
**Bloqueio atual:** nenhum.
**Se retomar, ler:** main.md (critérios + itens deferidos) + state.md [conclusão].

---

## Contexto ativo

### O que está sendo feito AGORA

Onda 0 do programa de migração Fazenda Castelo → Meu Gestor. Implementando os 5 itens Tier 1 sem schema. Começando por A (fechar furo de segurança: `ValidateLocation` ignora a whitelist `EmployeeAllowedLocation`, deixando qualquer empregado bater ponto em qualquer local da empresa no raio). A entidade/DTOs/mapper já existem; falta repo+service+controller+UI e usar a whitelist na validação.

### Hipóteses em jogo

- **Whitelist vazia = todos os locais** (status: decidida) — preserva o comportamento atual para quem não configurou nada.

### Decisões recentes que importam pra continuar

- [2026-06-09 11:11] Padrão do projeto: Controller(BaseController, [RequirePermissions]) → IService → IUnitOfWork.Repo → ErpContext; mapper estático; BaseResponse. Seguir CostCenter/Location como modelo.
- [2026-06-09 11:11] Item D usa MinutesExpected interino de `CompanySetting.WeeklyHoursDefault` (jornada real é Onda 2).

### Respostas-chave do usuário

- [2026-06-09 11:11] Usuário: "Voce escreve, você aplica e lembra sempre de editar o arquivo principal ... erp.sql ... pode executar sem medo" (migrações na Railway autorizadas; manter erp.sql; deixar arquivos de migration para conferência).
- [2026-06-09 11:11] Usuário escolheu: começar pela Onda 0; rastrear com SPEC por onda.

### Tentativas que falharam (para NÃO repetir)

- _(nenhuma ainda)_

### Arquivos ativamente sendo tocados

- `erp-back/-2-Application/Services/TimeClockService.cs` (ValidateLocation + item D)
- novos: repo/service/controller de EmployeeAllowedLocation
- `erp-back/-3-Infrastructure/UnitOfWork/ErpUnitOfWork.cs` + `IUnitOfWork.cs` + `ServiceConfiguration.cs`

### Onde parei exatamente

SPEC criada; prestes a escrever os arquivos do item A.

---

## Histórico de sessões

| # | Início | Duração | Tipo | Sumário 1 linha |
|---|--------|---------|------|-----------------|
| 1 | 2026-06-09 11:11 | em curso | ativação | SPEC criada; item A em implementação |
