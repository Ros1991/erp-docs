# Memory — SPEC-20260612-1256

> Main: [main.md](./main.md)
> State: [state.md](./state.md)
> Criado: 2026-06-12 12:56

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-12 13:15 (sessão #1)
**Onde tô:** SPEC CONCLUÍDA em 2026-06-12 13:15, commits `0a78108` (erp-back) + `ce56767` (erp-front), merges na main pushed.
**Próximo passo:** nenhum (arquivada). Operacional: aplicar migração `036` no Postgres Railway.
**Última decisão:** criação navega p/ edição para liberar a grade de manutenções (espelha o sistema antigo).
**Bloqueio atual:** nenhum
**Se retomar, ler:** state.md, entradas de 2026-06-12 13:05 em diante.

---

## Contexto ativo

### O que está sendo feito AGORA

Nada — módulo entregue em sessão única: levantamento no sistema antigo → backend (fatia vertical + permissões `equipment.*` + migração 036) → frontend (lista/form/manutenções/sidebar) → builds → revisão adversarial (zero achados) → publicação → docs.

### Hipóteses em jogo

- **Vida útil em anos** (status: assumida; UI rotula "Vida Útil (anos)"; sem impacto até existir cálculo de depreciação). 2026-06-12 13:15

### Decisões recentes que importam pra continuar

- [2026-06-12 13:05] `EquipmentService` verifica `CompanyId` em todo acesso unitário (GetOwned*) — mais rígido que o padrão Holiday; seguir esse modelo em módulos novos.
- [2026-06-12 13:05] Sem FK em `tb_task.task_equipment_id`; vincular tarefa↔equipamento é candidato natural a SPEC futura.

### Respostas-chave do usuário

- [2026-06-12 12:10] Usuário: "Criar cadastro de equipamentos (olhar a documentação do sistema antigo 'D:\Projetos\Documentacao Sistema Antigo' para ver informações que utiliza para equipamento."
- [2026-06-12 12:14] Usuário: aprovou feature nova `equipamentos` e recorte em SPEC separada.

### Tentativas que falharam (para NÃO repetir)

- _(nenhuma nesta SPEC; lição herdada da Onda 10 aplicada: `OrderDirection` em vez de `IsAscending` na query string)_

### Arquivos ativamente sendo tocados

- _(nenhum — concluída)_

### Onde parei exatamente

SPEC concluída e arquivada. Migração `036` pendente de aplicação manual no banco.

---

## Histórico de sessões

| # | Início | Duração | Tipo | Sumário 1 linha |
|---|--------|---------|------|-----------------|
| 1 | 2026-06-12 12:56 | ~20min | ativação+conclusão | Módulo de equipamentos completo (back+front), revisado e publicado |
