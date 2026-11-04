# AI Company Agents

Criei este projeto porque queria descobrir até onde um time de agentes de IA consegue tocar um negócio sem sair do controle do dono.

São 13 agentes que pesquisam, calculam, propõem e preparam o trabalho. Eles não têm a palavra final: tudo que gasta dinheiro ou tem efeito fora do sistema vira uma proposta, e eu aprovo ou recuso.

> **Estado atual:** o projeto roda em simulação. O código ainda é o do AI Commerce, minha primeira versão focada em comércio eletrônico. Os testes usam um provedor de IA simulado, e nenhum agente foi aprovado para produção.

## De onde veio

Comecei com o AI Commerce, um aplicativo em que os agentes operam uma loja online. Usei o Claude Code como ferramenta para construir tudo.

O aprendizado mais importante não foi técnico. Passei um mês construindo antes de provar que existia uma venda lucrativa. Por isso, a próxima fase muda o foco: toda ideia nasce com hipótese, custo com fonte, prazo e regra de parada. O plano está em [`docs/plano/PLANO_EMPRESA_DE_AGENTES.md`](docs/plano/PLANO_EMPRESA_DE_AGENTES.md).

## Como funciona

1. O dono define uma missão.
2. O Atlas, agente de direção, quebra a missão em tarefas.
3. Cada agente trabalha com ferramentas e permissões próprias.
4. Validadores conferem a saída do agente.
5. Ações externas viram propostas com evidências.
6. O dono aprova com segundo fator de autenticação.
7. Tudo fica registrado em uma auditoria encadeada.

## O que eu quis resolver

**Gasto de IA.** Antes de chamar o modelo, o sistema reserva o pior caso. Se a chamada falhar depois da resposta, o custo real ainda é lançado.

**Números inventados.** Encontrei agentes citando fatos reais com números que não existiam neles. Agora os validadores conferem cada número contra os fatos citados.

**Queda no meio do trabalho.** Tarefas têm pontos de retomada. Testei quedas reais em 8 pontos do fluxo.

**Parar tudo.** Existe um botão para bloquear novas despesas.

## Testes

Na versão entregue em 25 de outubro de 2026:

| Suíte | Resultado |
| --- | --- |
| API, com PostgreSQL real | 428 passaram |
| Interface | 70 passaram |
| Navegador | 32 passaram |

A avaliação offline de 26 de outubro recusou os 21 casos de ataque. Essa avaliação usa provedor simulado, então não mede a qualidade com um modelo real.

## O que ainda não está bom

- A qualidade com modelo real não foi avaliada.
- A busca por paráfrase teve recall@5 de 0,417, abaixo do mínimo de 0,75 que defini. Recall@5 é a fração de perguntas em que o trecho certo aparece entre os 5 primeiros resultados.
- O aplicativo não tem assinatura de código, então macOS e Windows mostram avisos na instalação.

## Tecnologias

Python, FastAPI, PostgreSQL com pgvector, Redis, Tauri, Next.js, React Three Fiber, Docker.

O orquestrador de missões é próprio.

## Como rodar

Você precisa de Docker, uv e Node 24.

```bash
make up    # sobe tudo em modo simulado em http://localhost:8080
make test  # roda os testes da API e da interface
```

Credenciais reais nunca entram no repositório. Elas ficam em `infra/env/*.env`, que o Git ignora.

## Documentação

- [`docs/plano/`](docs/plano/): para onde o projeto vai
- [`docs/adr/`](docs/adr/): decisões de arquitetura
- [`docs/legado/`](docs/legado/): documentação original do AI Commerce
