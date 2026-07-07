# Referência de Erros e Status

## Formato de erro

Respostas de erro seguem o mesmo formato simples:

```json
{ "error": "descrição do problema" }
```

## Códigos HTTP

| Código | Significado | Quando ocorre |
|---|---|---|
| `200` | OK | Leitura ou atualização bem-sucedida |
| `201` | Criado | Form, Version ou Run criados com sucesso |
| `204` | Sem conteúdo | Remoção bem-sucedida (Form, Version ou Run) |
| `400` | Requisição inválida | Corpo malformado ou campo obrigatório ausente (ex.: `name is required`) |
| `401` | Não autenticado | `Authorization` ausente ou inválido |
| `404` | Não encontrado | `form_key`, `version_key` ou `run_key` inexistente |
| `409` | Conflito | `PUT`/`DELETE` em um Run com `status: done` |
| `429` | Limite excedido | Apenas em `/health` — rate limit por IP |
| `503` | Indisponível | Apenas em `/health` — falha de conectividade com o banco |

## Status de um Run

| Valor | Significado |
|---|---|
| `pending` | Aguardando preenchimento pelo usuário final |
| `access_verification_required` | Existe um gate de acesso (`access_verification`) ainda não validado |
| `done` | Preenchimento concluído — Run imutável a partir daqui |

Detalhe do ciclo de vida em [`02-conceitos-e-modelo-de-dados.md`](02-conceitos-e-modelo-de-dados.md#estados-de-um-run).

## Status do Health Check

`GET /health` não requer autenticação e retorna:

| `status` | Código HTTP | Significado |
|---|---|---|
| `ok` | `200` | Conectividade com o banco estabelecida |
| `too_many_requests` | `429` | Rate limit por IP excedido — respeite o header `Retry-After` (segundos) |
| `unavailable` | `503` | Falha ao conectar com o banco |

## Próximo passo

Para perguntas comuns de quem está integrando pela primeira vez, siga para [`07-perguntas-frequentes.md`](07-perguntas-frequentes.md).
