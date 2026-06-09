# Comparativo funcional — Fazenda Castelo (antigo) × Meu Gestor (novo)

> Análise tela-a-tela das **92 telas** do ERP antigo (Fazenda Castelo — ASP.NET WebForms low-code Gvinci, PWA) contra o **Meu Gestor** (.NET 9 + React, multi-tenant), para decidir o que portar. Cada afirmação de "existe / não existe no novo" foi **verificada no código** (grep/leitura em `erp-back`/`erp-front`), não só nas docs.
>
> Gerado em 2026-06-09 a partir de análise multi-agente (11 áreas + revisão de completude). 68 pares de comparação cobrindo as 92 telas.

---

## 1. Veredito geral

O Meu Gestor **já é igual ou superior ao antigo** no núcleo de várias áreas — e em algumas é **muito** superior:

- **Folha de pagamento**: o novo tem motor real de INSS/IRRF/FGTS por faixas (tabela 2024), 13º, férias com 1/3 + adiantamento, snapshot JSONB, e **fechamento que gera lançamentos financeiros reais** (transação por funcionário + INSS + FGTS, rateio por centro de custo, abate parcelas de empréstimo, cria adiantamento se líquido negativo) — tudo **reversível** ao reabrir. O antigo só armazenava valores numa classe opaca.
- **Empréstimos/Adiantamentos**: unificados em `LoanAdvance` com quitação automática e **fracionária** na folha.
- **Login / segurança**: credencial unificada (e-mail/telefone/CPF), JWT com refresh-token revogável, hash de senha real.
- **Controle de acesso**: RBAC granular por módulo×ação (18 módulos) vs. o **flag binário "Admin"** do antigo.
- **Multi-tenant**: um login → N empresas; o antigo é mono-fazenda.
- **Geocerca de ponto**: Haversine + raio por local; relatórios **financeiros** completos (recharts) que o antigo não tinha.

Mas o antigo tem **lacunas reais e portáveis**, e o novo tem **bugs e features pela metade**. Estes são o foco deste documento.

---

## 2. Correções importantes (o que parecia gap mas não é, e vice-versa)

1. **Extrato / Conta Corrente do empregado JÁ EXISTE no novo.** Há `ReportService.GetEmployeeAccountReportAsync` + a tela `EmployeeAccountReport.tsx` (saldo anterior + extrato do período a partir de empréstimos e folhas). **Não é lacuna** — no máximo pode ganhar os "buckets" de dívida (Mês/13º/Férias) do antigo.
2. **O novo TEM upload de imagem — via Base64**, não multipart. Foto de perfil do empregado (`ProfileImageBase64`) e imagem de tarefa (`TaskImageInputDTO`) usam Base64 + `<input type=file>`. A lacuna real não é "não tem upload", é **não ter um gerenciador de anexos genérico** (ver/baixar/remover) e o **anexo de justificativa estar sem UI** (botão de câmera morto).
3. **`EmployeeAllowedLocation` é um furo de segurança, não só "feature pela metade".** A entidade + DTOs + Mapper existem, mas **sem repository/service/controller** e o `TimeClockService.ValidateLocation` **a ignora** — percorre TODOS os locais da empresa e aceita o ponto se estiver no raio de qualquer um. Na prática **qualquer empregado bate ponto em qualquer local**.

### Bugs confirmados no novo (independentes do antigo)
- **`GetDashboardAsync(companyId, date)` ignora o parâmetro `date`** — sempre chama `GetTodaySummaryAsync` (hoje). O dashboard de ponto do gestor **não respeita o filtro de data**.
- **`MinutesExpected` está fixo em 480 (8h)** no `TimeClockService:96` (há um `TODO` no código pedindo "vir do contrato/jornada"). Compromete previsto×trabalhado, atraso e qualquer banco de horas.
- **`EmailService` é stub** — o "esqueci a senha" gera o token mas **não envia e-mail** (só loga). O fluxo está quebrado em produção.
- **Tela de Justificativas (`Absences.tsx`)** só cria/exclui; o botão de câmera do anexo **não tem `onClick`**; e **não existe UI de gestor para aprovar/rejeitar** (o endpoint `approve` existe; não há "Rejeitar").
- **Dashboard de boas-vindas (`Dashboard.tsx`)** tem números **hardcoded** (ex.: R$ 125.420).

---

## 3. Matriz de priorização (a ferramenta de decisão)

Legenda: **Valor** = ganho para o negócio · **Esforço** = custo de implementar · 🐞 = corrige bug existente.

### Tier 1 — Alto valor, baixo esforço (fazer primeiro)
| # | O que portar/corrigir | Área | Valor | Esforço |
|---|---|---|---|---|
| 1 | **Reativar `EmployeeAllowedLocation`** (gating de local por empregado) — fecha furo de segurança; entidade/DTO/mapper já existem, falta repo+service+controller+1 chamada no `ValidateLocation`+UI | Ponto/Locais | Alto | Baixo-Médio |
| 2 | **Tela de gestor "Resolver justificativas"** (aprovar/rejeitar/abonar) — backend `approve` já existe; falta UI + caminho de "Rejeitar" | Ponto/Justif. | Alto | Baixo-Médio |
| 3 | **Implementar envio real de e-mail** (SendGrid/SMTP no `EmailService`) — sem isso "esqueci a senha" não funciona 🐞 | Auth | Alto | Baixo |
| 4 | **Alterar a própria senha logado** + **admin redefine senha de funcionário** (campo sem e-mail) | Auth | Alto | Baixo |
| 5 | **Dados bancários / PIX no contrato** (chave Pix, banco, agência, conta) — requisito para pagar | Empregados | Alto | Baixo |
| 6 | **Corrigir bug da data no dashboard de ponto** + **trocar 480 fixo** pela jornada/tolerância 🐞 | Ponto | Alto | Baixo |
| 7 | **Bloquear edição/exclusão de empréstimo com parcela já paga** (integridade vs folha fechada) | Empréstimos | Médio-Alto | Baixo |
| 8 | **Confirmação ao "Sair"** + **persistir estado da sidebar** (localStorage) | UX/Layout | Médio | Baixo |
| 9 | **Anexo de justificativa**: ligar o upload (Base64 já é o padrão) + visualização (botão câmera hoje é morto) | Ponto/Justif. | Alto | Baixo-Médio |

### Tier 2 — Alto valor, esforço médio (planejar como SPECs)
| # | O que portar | Área | Valor | Esforço |
|---|---|---|---|---|
| 10 | **Jornada de Trabalho como entidade** (Normal/Escala/Flexível, horas+intervalo por dia) → resolve o `MinutesExpected` e destrava ponto/banco de horas. **Tema transversal nº 1** | Ponto/RH | Alto | Médio-Alto |
| 11 | **Relatórios de ponto: Semanal, Mensal e Resumo do Período** (consolidado por funcionário + banco de horas dia-a-dia) — hoje só há painel diário | Relatórios/Ponto | Alto | Médio |
| 12 | **Plano de contas** (categorias de Receita/Despesa) — habilita DRE e relatório por categoria; hoje só há descrição em texto livre | Financeiro | Alto | Médio |
| 13 | **Resumo de empréstimos por empregado** (dívida total + Mês/13º/Férias + alerta de % do salário comprometido) | Empréstimos | Alto | Médio |
| 14 | **Tarefas — recursos operacionais**: geração automática da próxima tarefa recorrente, "uma tarefa em andamento por usuário", **editar hora** manual, **pausa geral** | Tarefas | Alto | Médio |
| 15 | **Fluxo de desligamento/desativação** (redirecionar tarefas e subordinados antes de tirar acesso; soft-delete do `CompanyUser`) — evita órfãos | Sistema | Alto | Médio |
| 16 | **Feriados** (cadastro + consumir em ponto/folha) | RH/Ponto | Médio | Baixo |

### Tier 3 — Estratégico / maior esforço (decidir conforme o uso real)
| # | O que portar | Quando vale | Esforço |
|---|---|---|---|
| 17 | **Banco de Horas / Acerto de horas extras** (saldo acumulado + lançamento manual) | Se fecha horas mensalmente — depende de Jornada (#10) | Médio-Alto |
| 18 | **Sistema de Notificações** (módulo transversal: pendências de ponto/justificativa, sino no header) | Para fechar o ciclo de ponto e os atalhos do header | Alto |
| 19 | **Reconhecimento facial no ponto** (login/ponto por rosto, modo totem) | Só se ponto em campo/totem entrar no escopo | Alto |
| 20 | **Mapa visual (Leaflet)** — ver geocerca, escolher lat/long no `LocationForm`, mapa do ponto | Habilita 3-4 telas de uma vez | Baixo-Médio |
| 21 | **Equipamentos + manutenção preventiva** (a FK `task_equipment_id` já existe órfã) | Se opera frota/máquinas | Alto |
| 22 | **Template de rateio nomeado** (aplicar rateio com 1 clique) | Produtividade no lançamento financeiro | Médio |
| 23 | **Gerenciador de anexos genérico** (ver/baixar/remover; decidir Base64 vs storage/S3) | Decisão de arquitetura única para todas as áreas | Médio-Alto |
| 24 | **Modo "Percentual"** no benefício/desconto + opção "Mês Admissão"; **período aquisitivo de férias** | Refinamentos de RH/folha | Médio |

---

## 4. Comparação detalhada por área

### 4.1 Acesso, Login e Autenticação
**Novo já é superior no básico** (credencial unificada e-mail/telefone/CPF, "lembrar de mim", JWT+refresh revogável, registro, esqueci-a-senha). Lacunas:
- **Reconhecimento facial** (login/ponto por rosto, modo totem com captura automática): só-antigo, inexistente no novo. Portar só se ponto em totem/campo entrar no escopo (Tier 3, #19).
- **Alterar a própria senha logado**: o antigo tinha; o novo só troca via token de reset → **portar** (Tier 1, #4).
- **E-mail de reset é stub** → implementar de verdade (Tier 1, #3).
- Telas técnicas (Autenticando, BlankPage, ErrorPage): **não portar** (o SPA resolve).

### 4.2 Início, Dashboard, Relatórios, Mapas e Multimídia
- **Relatórios financeiros**: novo é muito superior (fluxo de caixa, previsão, centro de custo, conta, fornecedor/cliente) — nada a portar.
- **Relatórios de PONTO (Semanal, Mensal, Resumo do Período)**: **ausentes** no novo → portar (Tier 2, #11). Dependem de Jornada.
- **Dashboard diário de ponto**: existe (`TimeClockDashboard`) mas **com bug de data** e **480 fixo**, e **não embute tarefas do empregado** nem banco de horas → corrigir + enriquecer (Tier 1, #6).
- **Mapa / Mapa do Ponto**: não há nenhuma lib de mapa → portar com Leaflet (Tier 3, #20). A regra de geocerca já existe e é melhor; falta a camada visual e o "criar local a partir da batida".
- **Multimídia**: ver correção §2.2 — Base64 existe; falta gerenciador de anexos (Tier 3, #23).
- **Seletor de data**: `<input type=date>` nativo já basta (novo até suporta intervalo). Não portar.

### 4.3 Cadastros — Financeiros e Operacionais
- **Fornecedores e Clientes**: equivalente e o novo é melhor (CPF/CNPJ, e-mail, ativo/inativo). Único port: **flags Fornecedor/Cliente** (papel) para filtrar por tipo (baixo esforço).
- **Receitas e Despesas (plano de contas)**: **ausente** — maior buraco financeiro. Portar como `FinancialCategory` (Tier 2, #12).
- **Bancos** (catálogo FEBRABAN): baixo valor; no máximo um campo `BankName` em `Account`. **Não** criar CRUD.
- **Atividades**: já = `CostCenter` (superior). Não portar.
- **Rateios**: novo tem rateio por contexto + default da empresa, mas **não** tem template nomeado reutilizável → portar conceito (Tier 3, #22).
- **Equipamentos**: só-fazenda, mas a FK `task_equipment_id` já existe órfã → portar se opera frota (Tier 3, #21).

### 4.4 Cadastros — Jornada, Locais, Feriados, Banco de Horas, Animais
- **Jornada de Trabalho**: **ausente** como entidade (só um número `WeeklyHours`). É o **tema transversal nº 1** (Tier 2, #10).
- **Locais Permitidos**: `Location` está completo e é melhor. Mas a **restrição empregado×local está morta** (`EmployeeAllowedLocation`) → **prioridade 1** (Tier 1, #1).
- **Feriados**: **ausente** (zero no código) → portar, barato (Tier 2, #16).
- **Banco de Horas**: **ausente** (só o teto `MaxOvertimeHoursPerMonth`) → Tier 3, #17 (depende de Jornada).
- **Raças / Classificação de Animais**: pecuária pura → **não portar**.

### 4.5 Tarefas
Núcleo (criar, atribuir, cronômetro start/pause/resume/stop/complete/reopen, histórico, pausas) **já é equivalente ou superior** (o novo tem subtarefas/bloqueio). Lacunas a portar (Tier 2, #14):
- **Geração automática da próxima ocorrência** de tarefa recorrente (os campos existem; nenhuma lógica gera) — meia-feature hoje.
- **"Uma tarefa em andamento por usuário"** (pausar/finalizar a atual ao iniciar outra).
- **Editar hora** manual de início/término/pausa (correção de esquecimentos) — maior lacuna operacional.
- **Pausa geral** (pausar toda a equipe de uma tarefa de uma vez).
- **Imagens**: backend só guarda URL e **não há UI**; portar upload (câmera no mobile) + galeria.
- Motivo de pausa: o backend já é estruturado; falta o **modal de motivo** no front ao pausar/parar.

### 4.6 Pessoal — Empregados e Contratos
Núcleo equivalente/superior (empregado e usuário **desacoplados**, N contratos por empregado, benefícios inline, rateio com default). Lacunas:
- **Dados bancários/PIX** no contrato: **ausentes** → portar (Tier 1, #5).
- **Configuração de ponto por empregado** (bate-ponto sim/não, jornada individual, locais por empregado): no novo é só por empresa → o item acionável é reativar `EmployeeAllowedLocation` (Tier 1, #1).
- **Conta corrente/extrato do empregado**: **JÁ EXISTE** (`EmployeeAccountReport`) — ver §2.1. Opcional: adicionar buckets de dívida.
- **Não** portar: reconhecimento facial, percentuais de encargo fixos no contrato (o novo centraliza na folha), rateio por atividade agrícola (o novo usa centro de custo).

### 4.7 Pessoal — Folha de Pagamento
**Novo é claramente superior** (ver Veredito). As 3 telas antigas têm equivalente melhor. Portar só refinamentos (baixo esforço):
- **Duas datas de pagamento** (salários vs INSS/FGTS) no fechamento (o novo usa uma só).
- **Pré-preencher** `InssAmount`/`FgtsAmount` no fechar com os totais já calculados.
- Helper **"quantidade × valor unitário"** no lançamento manual de débito/crédito (diárias).
- Flag **"Incide FGTS/INSS"** por item manual (hoje um provento manual nunca entra na base de encargos).
- O botão "Banco de Horas" do antigo **era morto** — não é port, é a feature de Banco de Horas (Tier 3).

### 4.8 Pessoal — Adiantamentos, Empréstimos, Abonos e Férias
Novo unificou e automatizou (quitação fracionária, gera transação financeira). Lacunas:
- **Resumo de empréstimos por empregado** (dívida/comprometimento): **ausente** → portar (Tier 2, #13).
- **Bloqueio de edição com parcela paga**: portar (Tier 1, #7).
- **Modo Percentual** e **"Mês Admissão"** no benefício/desconto: portar (Tier 3, #24).
- **Período aquisitivo de férias** (dias de direito): ausente → Tier 3, #24.
- **Abono em DIAS** (o novo só abona horas): adicionar tipo (baixo esforço).
- "Reembolsar para" (adiantamento): confirmar se ainda usam antes de portar.

### 4.9 Ponto e Justificativas
Paridade só no básico (bater ponto GPS+geocerca, criar/listar justificativa própria com voz). Lacunas grandes:
- **Tela de gestor para aprovar/rejeitar/abonar** justificativa: backend pronto, **front ausente** + falta "Rejeitar" → Tier 1, #2 (altíssimo ROI).
- **Upload/visualização de anexo** (atestado): campo existe, botão câmera morto → Tier 1, #9.
- **Acerto de horas extras** (pagar/descontar minutos): **ausente** → Tier 3, #17.
- **Notificações**: módulo **inexistente** → Tier 3, #18.
- Previsto real por jornada (hoje 480 fixo), nome do local na batida, excluir última batida na própria tela: corrigir/portar (Tier 1, #6).

### 4.10 Sistema, Configurações e Controle de Acesso
- **Configurações (Parâmetros)**: o `CompanySetting` já cobre o núcleo de RH/ponto/folha (e é multi-tenant). Os campos extras do antigo são (a) financeiro de baixo nível → vive em centro de custo/transações no novo, (b) específico de fazenda, (c) AWS/face, (d) geo → migrou para `Location`. Portar pontualmente só tolerância min/máx e regras de folha que o novo for honrar.
- **Controle de acesso (grupos)**: RBAC do novo é **estritamente superior** ao flag binário do antigo → **não portar** (só mapear na migração de dados: grupo Admin → `isAdmin=true`).
- **Usuários**: novo (identidade global + `CompanyUser`) supera. Avaliar **admin redefine senha** (Tier 1, #4).
- **Desativar Usuário**: o antigo redireciona tarefas/subordinados antes de desligar → **melhor candidato da área** (Tier 2, #15).
- **Configuração do Banco de Dados**: artefato on-premise → **não portar** (SaaS gerenciado).

### 4.11 Componentes, Navegação e Padrões
Novo supera (sidebar por permissão, guardas de rota em camadas, seletor de empresa multi-tenant, responsivo). Pequenas regressões a portar (baixo esforço): **confirmação ao Sair**, **persistir estado da sidebar**, **ordenação por clique no cabeçalho** (backend já suporta `OrderBy`), opção **"Cadastrar outro"** após salvar inclusão. **Footer** antigo é andaime vazio — não portar. Confirmado: **não há PWA/manifest/service worker** nem **Web Share** no novo (avaliar PWA se ponto no campo importar).

---

## 5. O que NÃO portar (e por quê)
- **Raças, Classificação de Animais, Tipo de Animal** — pecuária; fora do escopo do Meu Gestor.
- **Catálogo de Bancos (FEBRABAN)** — no máximo um campo de texto em `Account`.
- **Atividades** — já coberto por `CostCenter` (superior).
- **Controle de acesso binário** — o RBAC do novo é melhor.
- **Configuração do Banco de Dados / connection string** — SaaS gerenciado; expor isso seria retrocesso de segurança.
- **Footer, Autenticando, BlankPage, ErrorPage** — andaime/artefatos de WebForms.
- **Percentuais de encargo fixos no contrato** e **rateio por atividade agrícola** — o novo já resolve melhor (flags + centro de custo).
- **Login automático por dispositivo (TEMPDEVICE/FCM)** — paradigma de PWA do antigo.

## 6. Onde o novo JÁ é superior (não precisa portar nada)
Login/segurança · JWT+refresh · **folha de pagamento** (motor de cálculo + integração financeira) · empréstimo/adiantamento unificado com quitação automática · **férias na folha** (1/3, INSS/IRRF, adiantamento) · **RBAC granular** · **multi-tenancy** · geocerca Haversine · relatórios **financeiros** · **extrato/conta corrente do empregado**.

## 7. Temas transversais (resolver estes destrava várias telas)
1. **Jornada / horas esperadas** (`MinutesExpected=480` fixo): destrava ponto, dashboard, banco de horas, acerto de horas, relatórios semanal/mensal/resumo. **Base de pelo menos 5 telas.**
2. **Banco de horas / saldo de horas**: ausente; atravessa Resumo do Período, Acerto de Horas e o botão morto da folha.
3. **Anexos/Multimídia**: decidir Base64 vs storage uma vez; atravessa Tarefas, Justificativas, perfil.
4. **Notificações**: módulo inexistente; pré-requisito dos atalhos contextuais do header.
5. **Geo/Mapa visual** (Leaflet): habilita Mapa, Mapa do Ponto, escolher lat/long e ver geocerca.
6. **Relatórios de ponto** (vs. só financeiros): bloco coeso, dependente de Jornada.
