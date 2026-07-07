# Guia de Integração

Passo a passo de ponta a ponta: criar um formulário, publicar uma versão, abrir uma execução pré-preenchida e receber a resposta via webhook.

> Todas as chamadas abaixo levam o header `Authorization: <access_token>` (ver [`03-autenticacao.md`](03-autenticacao.md)). Omitido nos exemplos por brevidade.

## 1. Criar o Form

```
POST /api/v1/forms
```

```json
{
  "name": "Cadastro de cliente",
  "callback_url": "https://partner.example.com/hooks/clickform"
}
```

Resposta (`201`):

```json
{
  "key": "9b2e4f6a-1c3d-5e7f-8091-a2b3c4d5e6f7",
  "name": "Cadastro de cliente",
  "callback_url": "https://partner.example.com/hooks/clickform",
  "account_id": "acc_01HQXYZ",
  "user_id": "usr_01HQXYZ",
  "created_at": "2026-05-06T12:00:00Z",
  "updated_at": "2026-05-06T12:00:00Z"
}
```

Guarde o `key` retornado — é o `form_key` usado nos próximos passos.

## 2. Criar uma Version (schema do formulário)

```
POST /api/v1/forms/{form_key}/versions
```

```json
{
  "settings": {
    "title": "Atualização de contato",
    "subtitle": "Por favor, confirme os dados de contato.",
    "finish_title": "Atualização de dados concluída!",
    "finish_subtitle": "Em breve nossa equipe entrará em contato."
  },
  "fields": [
    { "label": "Nome completo", "type": "text", "placeholder": "Maria da Silva", "required": true, "order": 1 },
    { "label": "Whatsapp", "type": "phone_number", "placeholder": "(11) 99999-9999", "required": true, "order": 2 },
    { "label": "Email", "type": "email", "placeholder": "nome@empresa.com", "required": true, "order": 3 }
  ]
}
```

Resposta (`201`) traz o schema persistido, com `key` estável por campo e o `form_url` (link genérico da versão). Guarde o `key`/`version` retornado — é o `version_key` do próximo passo.

Mais exemplos de campo (CPF, CEP, repeater, Skip Logic etc.) em [`04-tipos-de-campo-e-logica-condicional.md`](04-tipos-de-campo-e-logica-condicional.md) e na [spec OpenAPI](../collections/openapi/clickform-api.openapi.yaml).

## 3. Criar um Run (execução pré-preenchida)

```
POST /api/v1/versions/{version_key}/runs
```

```json
{
  "callback_url": "https://erp.cliente.com/callback",
  "context": {
    "nome-completo-59661f5fe933": { "value": "John Doe", "read_only": false },
    "whatsapp-f9c2322fcc74": { "value": "48999999999", "read_only": false },
    "email-6679641e7540": { "value": "john.doe@developer.com", "read_only": true }
  },
  "notification": { "whatsapp": "(11) 9 1234-5678" }
}
```

As chaves de `context` são os `key` dos campos da Version (retornados no passo 2). Campos com `read_only: true` são exibidos ao usuário apenas para confirmação, não edição — ver [Two-Way API](02-conceitos-e-modelo-de-dados.md#two-way-api-smart-fill).

Resposta (`201`):

```json
{
  "key": "cc65ba6a-3ed5-44fb-bd28-e996610d69cd",
  "status": "pending",
  "form_url": "https://app.example.com/cc65ba6a-3ed5-44fb-bd28-e996610d69cd",
  "callback_url": "https://erp.cliente.com/callback",
  "context": { "...": "..." },
  "responses": {},
  "created_at": "2026-05-06T12:00:00Z",
  "updated_at": "2026-05-06T12:00:00Z"
}
```

`form_url` é o link único de preenchimento deste Run — envie-o ao usuário final (ex.: via WhatsApp).

## 4. Usuário preenche o formulário

O usuário acessa `form_url`, confirma os dados pré-preenchidos e completa os campos restantes. Ao concluir, o `status` do Run muda para `done` — a partir daí o Run é imutável (ver [regra de imutabilidade](02-conceitos-e-modelo-de-dados.md#regra-de-imutabilidade)).

## 5. Receber o callback (webhook)

Ao concluir, o ClickForm dispara um `POST` para a(s) `callback_url` configurada(s) (a do Run e/ou a do Form):

```json
{
  "event": "form_run.completed",
  "data": {
    "run_key": "cc65ba6a-3ed5-44fb-bd28-e996610d69cd",
    "status": "done",
    "context": {
      "nome-completo-59661f5fe933": { "value": "John Doe", "read_only": false }
    },
    "responses": {
      "email-6679641e7540": "john.doe@developer.com",
      "dependentes": [{ "nome": "Item do Repeater" }]
    },
    "created_at": "2026-05-06T12:00:00Z",
    "completed_at": "2026-05-06T12:04:11Z"
  }
}
```

**Em aberto:** não há, até o momento, um mecanismo documentado de verificação de assinatura do webhook (ex.: HMAC em header). Se sua integração exige validar a autenticidade do callback, confirme com o time técnico do ClickForm antes de ir para produção.

## 6. (Opcional) Consultar o Run depois

```
GET /api/v1/versions/{version_key}/runs/{run_key}
```

Útil como reconciliação caso o webhook falhe ou não tenha sido recebido.

## Próximo passo

Para a lista completa de códigos de erro e estados possíveis, siga para [`06-referencia-de-erros-e-status.md`](06-referencia-de-erros-e-status.md).
