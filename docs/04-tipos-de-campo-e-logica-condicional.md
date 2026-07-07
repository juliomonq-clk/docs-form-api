# Tipos de Campo e Lógica Condicional

## Estrutura comum de um campo

Todo campo em `fields[]` de uma Version tem:

| Propriedade | Descrição |
|---|---|
| `label` | Rótulo exibido ao usuário (obrigatório) |
| `type` | Um dos tipos abaixo (obrigatório) |
| `key` | Identificador estável após persistência; pode ser omitido na criação (a API gera) |
| `required` | Se o preenchimento é obrigatório |
| `order` | Posição do campo dentro da etapa |
| `step` | Índice da etapa (`step`) a que o campo pertence |
| `placeholder` | Texto de exemplo no campo vazio |
| `help_balloon` | Texto de ajuda em balão (não se aplica a `header`/`paragraph`) |
| `conditions[]` | Regras de Skip Logic (ver abaixo) |
| `options` | Estrutura variável conforme o `type` |

## Tipos disponíveis

| Categoria | `type` |
|---|---|
| Texto | `text`, `textarea` |
| Numérico | `number`, `currency` |
| Contato | `email`, `phone_number` |
| Data | `date` |
| Documentos (com máscara) | `cpf`, `cnpj` |
| Endereço | `cep` — autopreenche subcampos via `options.fields[].attribute_type` (`street`, `state`, `city`, `neighborhood`, `number`, `complement`) |
| Upload | `image` (JPG/PNG), `file` (PDF/DOCX/imagens) |
| Seleção | `select`, `radio-group`, `checkbox-group` — opções em `options.values[]` (`label`/`value`); `options.other: true` libera resposta livre |
| Estrutural (não coleta dado) | `header`, `paragraph` |
| Dinâmico | `repeater` — clona um bloco de subcampos |

## Skip Logic (campos condicionais)

A regra de exibição/ocultação é declarada **no campo de origem** (forward-chaining), não no campo alvo. Cada item de `conditions[]` tem:

| Propriedade | Descrição |
|---|---|
| `value` | Valor comparado com o preenchimento do campo de origem |
| `operator` | Ex.: `equal` |
| `action` | `show` ou `hide` |
| `fields[]` | Labels dos campos afetados pela regra |

**Exemplo** — mostrar "Plan details" apenas quando "Plan" for "Full":

```json
{
  "label": "Plan",
  "type": "select",
  "options": { "values": [{ "label": "Full", "value": "FULL" }, { "label": "Basic", "value": "BASIC" }] },
  "conditions": [
    { "operator": "equal", "value": "FULL", "action": "show", "fields": ["Plan details"] }
  ]
}
```

## Repeater (blocos repetíveis)

`type: "repeater"` clona um bloco de subcampos — útil para listas de tamanho variável (ex.: múltiplos dependentes). Configuração em `options`:

- `button_label` — texto do botão de adicionar novo bloco.
- `fields[]` — schema dos subcampos, cada um com `key` estável.

No webhook de resposta, o repeater é entregue como um **array de objetos**, um por bloco preenchido.

## Próximo passo

Para ver um fluxo completo — criar Form, Version e Run, e receber a resposta — siga para [`05-guia-de-integracao.md`](05-guia-de-integracao.md).
