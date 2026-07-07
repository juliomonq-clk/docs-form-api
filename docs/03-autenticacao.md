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

**Em aberto:** a especificação da API não fixa hoje uma URL base (`servers`) documentada publicamente. Antes de publicar este repositório para uma empresa integradora, confirme com o time técnico do ClickForm:

- O hostname de sandbox (ambiente de testes).
- O hostname de produção.
- Se existe algum processo de rotação/expiração do `access_token`.

Enquanto isso não for confirmado, trate `base_url` como uma variável a preencher manualmente nas [coleções de teste](../collections/README.md).

## Próximo passo

Para conhecer os tipos de campo disponíveis e a lógica condicional de exibição, siga para [`04-tipos-de-campo-e-logica-condicional.md`](04-tipos-de-campo-e-logica-condicional.md).
