# Autenticação

## Access Token

Toda chamada autenticada usa um único mecanismo:

- Header HTTP `Authorization: <access_token>`
- O valor é um **UUID**, enviado **sem prefixo** (não use `Bearer <token>`).

```
Authorization: 9b2e4f6a-1c3d-5e7f-8091-a2b3c4d5e6f7
```

## Onde obter o token

O `access_token` é gerado na página de configurações da API no **Távola** (painel administrativo da conta Clicksign). Ele está atrelado a uma conta (`account_id`) e a um usuário (`user_id`) — todos os recursos criados com esse token pertencem a essa conta.

Se sua empresa ainda não tem acesso ao Távola ou precisa provisionar credenciais, contate `professionalservices@clicksign.com`.

## Endpoint sem autenticação

O único endpoint público sem `Authorization` é o **health check**:

```
GET /health
```

Ele consulta a disponibilidade do banco de dados e não expõe dado de conta nenhum — pode ser chamado livremente para monitoramento. Tem rate limit por IP (ver [`06-referencia-de-erros-e-status.md`](06-referencia-de-erros-e-status.md)).

## Ambientes (sandbox / produção)

A especificação da API não fixa uma URL base (`servers`) — o host varia por ambiente:

| Ambiente | `base_url` |
|---|---|
| **Sandbox** (use este para testes/integração) | `https://clickform-sandbox.clicksign.com` |
| Produção | `https://clickform.clicksign.com` |

Use **sandbox** como ambiente padrão de desenvolvimento e teste de integração. Produção deve ser usada apenas quando a integração já estiver validada — as chamadas afetam dados reais de conta.

**Em aberto:** não há, até o momento, um processo documentado de rotação/expiração do `access_token`. Confirmar com o time técnico se necessário.

## Próximo passo

Para conhecer os tipos de campo disponíveis e a lógica condicional de exibição, siga para [`04-tipos-de-campo-e-logica-condicional.md`](04-tipos-de-campo-e-logica-condicional.md).
