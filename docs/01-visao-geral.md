# Visão Geral

## O que é o ClickForm

O ClickForm é o motor de coleta de dados da Clicksign. Diferente de um formulário tradicional preso à geração de um documento, ele opera de forma **desacoplada**: cria links de preenchimento versionados, injeta dados prévios para reduzir fricção, valida e estrutura as respostas em JSON, e entrega tudo via webhook para o sistema que a empresa integradora já usa (ERP, CRM, ou o próprio ClickFlow).

## Problema que resolve

Formulários de coleta de dados tradicionais tendem a ter três limitações:

- **Vínculo obrigatório com geração de documento** — a coleta só existe para alimentar um PDF/contrato, o que trava o dado dentro do documento final e impede reaproveitá-lo em outros sistemas.
- **Sem pré-preenchimento** — o usuário final redigita dados que a empresa já tem no CRM/ERP, gerando fricção e erro de digitação.
- **Quebra de contexto** — links que abrem em navegador externo, fora do canal onde a conversa já estava acontecendo (ex.: WhatsApp), aumentam abandono.

O ClickForm resolve isso com uma arquitetura API-first, pré-preenchimento via **Two-Way API** e entrega nativa dentro do WhatsApp (webview).

## Modelos de uso oferecidos

| Modelo | Quando usar |
|---|---|
| **ClickForm isolado** | A empresa só precisa coletar/sanitizar dados para o próprio ERP/CRM, sem gerar documento nem assinatura. |
| **ClickFlow (jornada completa)** | O ClickForm é a etapa de coleta de uma esteira que segue para autenticação e assinatura eletrônica. |
| **Integração manual (apartada)** | A empresa usa o ClickForm para coletar e, por conta própria, aciona outras APIs da Clicksign (ex.: Modelos/Assinatura) nas etapas seguintes. |

Este repositório documenta a API pública do ClickForm, comum aos três modelos. Se sua integração é (ou vai evoluir para) uma jornada completa orquestrada pelo ClickFlow, veja também a documentação irmã: [`docs-flow-api`](https://github.com/juliomonq-clk/docs-flow-api).

## Diferenciais

- **Two-Way API (Smart Fill)** — o `context` de um Run aceita dados pré-injetados (com `read_only` opcional) e devolve, na conclusão, as `responses` preenchidas pelo usuário — elimina redigitação de dados que a empresa já possui.
- **Skip Logic** — regras condicionais declaradas no próprio campo de origem (`conditions`), que mostram/ocultam outros campos com base na resposta dada.
- **Repeater** — permite blocos de subcampos repetíveis (ex.: múltiplos dependentes), retornados como array no webhook.
- **WhatsApp First** — o link de preenchimento (`form_url`) pode ser aberto em webview nativa dentro do WhatsApp, sem tirar o usuário do app.
- **White Label** — customização visual do formulário e das mensagens de conclusão.

## Próximo passo

Para entender o vocabulário exato (Form, Version, Run) antes de olhar o contrato de API, siga para [`02-conceitos-e-modelo-de-dados.md`](02-conceitos-e-modelo-de-dados.md).
