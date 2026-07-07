# ClickForm API — Coleções

Gerado a partir da spec OpenAPI do ClickForm ([`openapi/clickform-api.openapi.yaml`](openapi/clickform-api.openapi.yaml)) — fonte de verdade dos endpoints, schemas e exemplos.

16 endpoints em 4 grupos: Forms, Versions, Runs, Health.

## Estrutura

```
collections/
├── openapi/    clickform-api.openapi.yaml   (OpenAPI 3.0.3)
├── postman/    coleção + ambientes Sandbox/Produção
├── insomnia/   export v4 (workspace + ambientes + pastas)
└── bruno/      coleção em pastas .bru (abrir a pasta direto no Bruno)
```

## Autenticação

Todas as chamadas autenticadas usam:
- Header `Authorization: <access_token>` — **sem** prefixo `Bearer`.

O `/health` é o único endpoint sem autenticação.

Gere o `access_token` na página de configurações da API no Távola. Detalhe completo em [`../docs/03-autenticacao.md`](../docs/03-autenticacao.md).

## Variáveis de ambiente

Todas as coleções usam as mesmas variáveis:

| Variável | Descrição | Default |
|---|---|---|
| `base_url` | Host da API do ClickForm | Sandbox: `https://clickform-sandbox.clicksign.com` |
| `access_token` | Seu Access Token | (vazio, preencha) |
| `form_key`, `version_key`, `run_key` | IDs usados nos paths | (vazio, preencha conforme for testando o fluxo) |

⚠️ **Use o ambiente Sandbox como padrão** para testes e integração. Produção (`https://clickform.clicksign.com`) afeta dados reais de conta — use apenas com integração já validada.

## Como importar

### Postman
1. Import → arraste `postman/ClickForm-API.postman_collection.json` + os 2 arquivos de ambiente.
2. Selecione o ambiente "ClickForm - Sandbox" no canto superior direito.
3. Preencha `base_url` e `access_token`.

### Insomnia
1. Application → Preferences → Import Data → From File → selecione `insomnia/ClickForm-API.insomnia.json`.
2. Isso cria o workspace, o "Base environment" e os sub-ambientes Sandbox/Produção. Edite o ambiente escolhido para preencher `base_url` e `access_token`.

### Bruno
1. Open Collection → selecione a pasta `bruno/` inteira (ela já tem o `bruno.json` na raiz).
2. Escolha o ambiente Sandbox ou Produção no seletor de ambiente do Bruno.
3. Preencha `base_url` e `access_token` no ambiente.

## Observações sobre o spec

- Os corpos de exemplo de cada request vêm diretamente dos `examples` nomeados da spec OpenAPI (ex.: pré-preenchimento por `run_key`, campo com `read_only`, verificação de acesso) — não foram inventados para as coleções.
- A criação de `Version` (`POST /api/v1/forms/{form_key}/versions`) tem um exemplo por tipo de campo (texto, CPF, CEP, repeater, Skip Logic etc.) na própria spec — vale abrir `openapi/clickform-api.openapi.yaml` para ver todos antes de montar seu próprio schema.
- A spec não declara um bloco `servers` — por isso o `base_url` de cada ambiente vem preenchido diretamente nas coleções (Postman/Insomnia/Bruno), e não a partir do próprio OpenAPI.
