# ClickForm API — Documentação de Integração

O **ClickForm** é o motor de coleta de dados da Clicksign: cria e executa formulários versionados, consumidos via API, com pré-preenchimento de dados, validação estruturada e entrega automatizada das respostas por webhook. É desacoplado da geração de documentos — pode ser usado isoladamente ou como módulo de entrada do ClickFlow.

Este repositório é o ponto de entrada para qualquer empresa que precise **entender como o ClickForm funciona e como plugar sua operação nele**: conceitos, contrato de API e coleções prontas para importar no Postman/Insomnia/Bruno.

> Este repositório documenta exclusivamente o **ClickForm** (coleta de dados). Para a orquestração completa de jornadas (**ClickFlow**, do qual o Form pode ser um módulo), consulte a documentação irmã em [`docs-flow-api`](https://github.com/juliomonq-clk/docs-flow-api).

---

## Por onde começar

| Se você quer... | Vá para |
|---|---|
| Entender o que o ClickForm é e resolve | [`docs/01-visao-geral.md`](docs/01-visao-geral.md) |
| Conhecer o modelo de dados (Form, Version, Run) | [`docs/02-conceitos-e-modelo-de-dados.md`](docs/02-conceitos-e-modelo-de-dados.md) |
| Autenticar suas chamadas | [`docs/03-autenticacao.md`](docs/03-autenticacao.md) |
| Conhecer os tipos de campo e a lógica condicional (Skip Logic) | [`docs/04-tipos-de-campo-e-logica-condicional.md`](docs/04-tipos-de-campo-e-logica-condicional.md) |
| Seguir um passo a passo de ponta a ponta | [`docs/05-guia-de-integracao.md`](docs/05-guia-de-integracao.md) |
| Interpretar erros e estados de execução | [`docs/06-referencia-de-erros-e-status.md`](docs/06-referencia-de-erros-e-status.md) |
| Ver perguntas comuns | [`docs/07-perguntas-frequentes.md`](docs/07-perguntas-frequentes.md) |
| Testar a API sem escrever código | [`collections/`](collections/README.md) (OpenAPI + Postman + Insomnia + Bruno) |

---

## Modelo em 30 segundos

O domínio do ClickForm tem três entidades, separando configuração de execução:

```
Form (casca: nome + callback_url)
  └── Version (schema imutável: campos + configurações visuais)
        └── Run (execução: dados pré-preenchidos + respostas coletadas)
```

- **Form** — metadados base e o webhook padrão de destino.
- **Version** — o schema versionado e imutável (campos, tipos, Skip Logic).
- **Run** — uma instância de preenchimento único, com link próprio (`form_url`) e dados de pré-preenchimento (`context`).

Detalhe completo em [`docs/02-conceitos-e-modelo-de-dados.md`](docs/02-conceitos-e-modelo-de-dados.md).

## Modelos de uso oferecidos

O ClickForm pode ser contratado/integrado de três formas:

1. **ClickForm isolado** — coleta e sanitiza dados para o ERP/CRM da empresa contratante, sem geração de documento nem assinatura.
2. **ClickFlow (jornada completa)** — o ClickForm é a porta de entrada de uma esteira que segue para autenticação e assinatura eletrônica.
3. **Integração manual (apartada)** — a empresa usa o ClickForm para coletar e opera por conta própria as etapas seguintes (ex.: chamando a API de Modelos/Assinatura da Clicksign separadamente).

Este repositório cobre a API pública do ClickForm, válida para os três modelos.

## Como hoje se configura um formulário

Na versão atual, a criação e a configuração de um formulário são feitas **via API/JSON** (não há builder visual). O fluxo típico para uma empresa nova é:

1. Contato comercial/técnico com a Clicksign para provisionar o `access_token` (gerado na página de configurações da API no Távola).
2. Definição do schema do formulário (campos, validações, Skip Logic) em JSON — hoje normalmente com apoio do time de Professional Services da Clicksign.
3. Integração da empresa contratante para criar `Runs` (pré-preenchimento) e consumir os webhooks de resposta.

Dúvidas de acesso/credenciais: `professionalservices@clicksign.com`.

## Status deste documento

Este repositório documenta apenas o que já está confirmado no contrato de API (spec OpenAPI em [`collections/openapi`](collections/openapi/clickform-api.openapi.yaml)) ou validado com o time técnico do ClickForm. Um ponto ainda em aberto:

- **Verificação de assinatura do webhook** — não há, até o momento, um mecanismo documentado (ex.: HMAC) para a empresa integradora validar a autenticidade do callback recebido. Vale confirmar com o time técnico se existe e não está documentado, ou se é uma lacuna real.

Hostnames de sandbox e produção estão confirmados — ver [`docs/03-autenticacao.md`](docs/03-autenticacao.md). **Use sandbox como padrão para testes e integração.**
