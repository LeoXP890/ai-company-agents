# Plano — AI Company Agents (empresa de agentes de IA)

**Origem:** cópia do AI Commerce feita em 01/11/2026 (sem histórico, sem segredos e sem documentos do piloto).
O AI Commerce continua preservado no repositório privado original.

## 1. Objetivo

Transformar o time de 13 agentes, hoje preso a um único negócio (dropshipping no Mercado Livre), em uma
**empresa de agentes de uso geral**:

1. os agentes **pesquisam e propõem ideias de negócio**, ou trabalham no que o dono mandar;
2. o dono **aprova ou recusa** cada ideia;
3. depois da aprovação, os agentes **executam a ideia** dentro de um orçamento, de um prazo e de uma regra de parada;
4. o sistema **mede o resultado** e propõe continuar, ajustar ou encerrar.

Os 13 agentes mantêm as mesmas funções (pesquisa, finanças, conteúdo, compliance, etc.), mas deixam de ser
"de e-commerce". Cada negócio vira um **plugin** (playbook + conectores), e o AI Commerce é só o primeiro.

## 2. Lições do AI Commerce, que viram regras do sistema

| O que aconteceu no AI Commerce | Regra no sistema novo |
|---|---|
| Um mês construindo antes de provar que existia uma venda lucrativa | Toda iniciativa nasce com **hipótese, meta mínima e prazo de validação**. Nada de construir funcionalidade antes de validar a hipótese. |
| Margem calculada sem todos os custos (tarifa fixa, frete do fornecedor) | O **business case** exige cada custo com fonte. Custo desconhecido bloqueia a proposta. |
| Sugestões que violavam regras de plataforma (Workana: links e trabalho grátis) | A **Clara confere as regras oficiais da plataforma**, citando a cláusula, antes de qualquer proposta chegar ao dono. |
| Assinatura paga (Dropi) antes da validação | **Gasto só com orçamento aprovado**, reservado no ledger, e com regra de cancelamento definida antes. |
| Caças repetidas sem resultado, sem critério de parada | **Regra de parada** obrigatória ("se em N dias não acontecer X, encerrar") avaliada automaticamente. |

## 3. Ciclo de vida de uma iniciativa

```
DIRETRIZ do dono ──► IDEIA ──► PESQUISA ──► BUSINESS CASE ──► [APROVAÇÃO HUMANA] ──► EXECUÇÃO ──► REVISÃO
  ("ache formas de      (Luna)    (Luna,        (Dante, Finn,          (dono)           (Atlas      (Orion:
   ganhar com IA")                Nexus)        Clara)                                 + time)     meta × real)
                                                                                                    │
                                              ENCERRAR ◄── AJUSTAR ◄── CONTINUAR ◄─────────────────┘
```

- **Diretriz:** instrução livre do dono ("pesquisem negócios de afiliados", "não usem nada que exija CNPJ",
  "orçamento máximo R$ 200"). Diretrizes ficam salvas e todos os agentes as respeitam.
- **Ideia:** título, hipótese, por que agora, fontes.
- **Business case:** custos com fonte, receita esperada (cenários pessimista, base e otimista), tempo do dono
  por semana, riscos, **regras das plataformas envolvidas (com link e cláusula)**, meta mínima, prazo e regra
  de parada.
- **Aprovação:** reusa a caixa de Aprovações atual (payload com hash, segregação de funções, reserva de orçamento).
- **Execução:** o Atlas quebra a iniciativa em missões e tarefas por agente. Toda ação externa continua passando
  por `governance.propose` e pela aprovação.
- **Revisão:** rotina periódica compara a meta com o real e propõe continuar, ajustar ou encerrar (o dono decide).

## 4. Os 13 agentes, com papéis genéricos

| Agente | Hoje (AI Commerce) | Na empresa de agentes |
|---|---|---|
| Atlas | Direção e missões | **Direção:** transforma diretrizes em iniciativas e iniciativas aprovadas em missões |
| Luna | Pesquisa de mercado e tendências | **Pesquisa de oportunidades:** mercados, tendências, demanda, concorrência |
| Dante | Modelagem comercial de dropshipping | **Modelagem de negócio:** business case, unit economics, cenários |
| Nexus | Fornecedores e ofertas | **Parceiros e recursos:** fornecedores, plataformas, ferramentas, custos de terceiros |
| Iris | Criativos e conteúdo | **Conteúdo:** textos, posts, propostas, materiais |
| Pixel | Mídia paga | **Aquisição:** anúncios e canais de tráfego (sempre com aprovação) |
| Nova | Loja e canais | **Canais e operação:** publicar e operar nos canais aprovados |
| Finn | Finanças e ledger | **Finanças:** orçamento por iniciativa, ledger, custo de IA, resultado |
| Eva | Atendimento | **Relacionamento:** clientes, compradores, respostas |
| Orion | Analytics e experimentos | **Métricas:** metas, experimentos, revisão das iniciativas |
| Clara | Compliance jurídico-fiscal | **Compliance e regras de plataforma:** termos, políticas, legislação, sempre citando a fonte |
| Logan | Logística e SLAs | **Entregas e prazos:** cronogramas, SLAs, prazos prometidos |
| Vera | Qualidade e conversão | **Qualidade:** revisa as entregas antes de saírem |

Implementação: `workforce/registry.py` passa a ter os papéis acima. As ferramentas de cada agente deixam de ser
fixas no código e passam a ser **concedidas por plugin** (a iniciativa de afiliados dá à Nova as ferramentas de
afiliados, por exemplo).

## 5. O que reaproveitar, generalizar ou mover

**Reaproveitar sem mudança (núcleo):**
- Identidade, RBAC, MFA, recuperação de acesso (`identity/`).
- Governança: propostas, aprovações com hash, executor com outbox, kill switch e auditoria (`governance/`).
- Workforce: agentes, tarefas, missões e rotinas (`workforce/`).
- Runtime de IA: adapter LLM, registro de ferramentas com permissão por agente, cache, limites diários,
  teto de custo e auditoria de cada chamada (`ai/llm.py`, `ai/tools/__init__.py`, `ai/runtime.py`, `ai/agent_audit.py`).
- Pesquisa web com trilha e teto (`ai/web_research.py`, `ai/research_guard.py`).
- Ledger e reservas de orçamento (`finance/ledger.py`).
- Escritório 2D/3D, app desktop (Tauri), observabilidade e backups.

**Generalizar:**
- `governance/gates.py` e `golive.py`: os gates fixos de e-commerce viram **gates por plugin**.
- `workforce/routines.py`: o catálogo `KINDS` vira um registro aberto (o núcleo e os plugins registram rotinas).
- `ai/specs.py` (prompts dos agentes): tirar o contexto de dropshipping e montar o prompt a partir de papel
  genérico, diretrizes do dono e contexto da iniciativa.
- `finance/`: orçamento e resultado **por iniciativa** (hoje o orçamento é global).

**Mover para o plugin `commerce` (legado, desligado por padrão):**
- `commerce/`, `providers/` (Mercado Livre, WooCommerce, CJ, fiscal), `robot/` (Dropi), `ai/tools/{ml,dropi,cj,market}.py`,
  `ai/hunt.py`, `ai/ml_market_research.py`, `ai/freight_quotes.py`, `governance/seller.py`, `apps/storefront`.

## 6. Modelo de dados novo

| Tabela | Campos principais |
|---|---|
| `directives` | texto, escopo (global ou iniciativa), ativa, autor, versão |
| `initiatives` | título, hipótese, status (`IDEA`, `RESEARCH`, `CASE_READY`, `APPROVED`, `RUNNING`, `REVIEW`, `PAUSED`, `CLOSED`), plugin, dono |
| `business_cases` | iniciativa, versão, custos[] com fonte, receitas por cenário, horas do dono por semana, riscos[], regras de plataforma[] com URL e cláusula, meta mínima, prazo, regra de parada, hash |
| `initiative_budgets` | iniciativa, teto, gasto, reservas (reusa `BudgetReservation`/ledger) |
| `initiative_reviews` | data, métricas meta × real, recomendação (continuar/ajustar/encerrar), decisão do dono |
| `plugins` | id, versão, ferramentas, rotinas, gates e telas que registra, ativo |

A aprovação de uma iniciativa reusa `ApprovalProposal` com um novo `action_type = initiative.approve`, de modo que
o payload aprovado é exatamente o business case exibido (hash), como já acontece hoje.

## 7. Interface (app desktop e web)

- **Diretrizes:** o dono escreve o que quer ("foquem em X", "não façam Y").
- **Ideias:** lista com nota, fontes e botão "pedir business case".
- **Iniciativas:** quadro por status, com orçamento, meta, prazo e próxima revisão.
- **Aprovações:** a tela atual, que passa a exibir o business case (custos com fonte, regras citadas, regra de parada).
- **Escritório 3D:** mesmas salas, com nomes genéricos; o balão de cada agente mostra a iniciativa em que ele trabalha.

## 8. Fases

| Fase | Entrega | Critério de aceite |
|---|---|---|
| **F0. Fork limpo** | Repositório público sem segredos, CI verde, stack sobe em DRY_RUN | `make test` verde; gitleaks sem achados reais; smoke da stack |
| **F1. Núcleo de iniciativas** | Tabelas da seção 6, API e migrações; diretrizes no prompt dos agentes | Testes de transição de status; aprovação com hash do business case; diretriz respeitada em teste de prompt |
| **F2. Pesquisa e business case** | Tarefas `initiative.research` (Luna, Nexus) e `initiative.case` (Dante, Finn, Clara) | Business case sem custo sem fonte é rejeitado em teste; Clara cita a cláusula de cada plataforma |
| **F3. Execução aprovada** | Atlas quebra a iniciativa em missões; orçamento por iniciativa no ledger | Missão não começa sem aprovação; gasto acima do teto é bloqueado; kill switch para tudo |
| **F4. Plugins** | Registro de plugins; `commerce` (legado) como primeiro; `freelance` (do repositório ai-freelance-agents) e afiliados como próximos | Plugin desligado não expõe ferramentas; testes do commerce continuam verdes com o plugin ligado |
| **F5. Revisão e parada** | Rotina de revisão; recomendação automática; encerramento com relatório | Teste: meta não atingida no prazo gera proposta de encerramento |
| **F6. Interface** | Telas da seção 7 no desktop e no 3D | Build do desktop; screenshots antes/depois; revisão humana |

## 9. Riscos e limites

- **Nada roda sozinho com dinheiro ou com efeito externo:** a aprovação humana continua obrigatória.
- **Regras de plataforma mudam:** a Clara guarda a data de leitura de cada regra, e uma regra lida há mais de 30 dias
  é reconferida antes de uma nova proposta.
- **Custo de IA:** o teto mensal global continua valendo, e cada iniciativa ganha um teto próprio.
- **"Ideia boa" não é "dinheiro":** toda iniciativa precisa de validação barata antes de qualquer construção.

## 10. Decisões do dono

1. Nome definitivo do produto (o nome do repositório é provisório).
2. Teto mensal de IA para pesquisa de ideias.
3. Se o pacote Python `aicommerce` deve ser renomeado na F1 (muita mudança mecânica) ou depois.
4. Quais plugins entram primeiro além do `commerce` legado.
