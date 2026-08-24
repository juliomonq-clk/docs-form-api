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
| Numérico | `number`, `number_range` (ver abaixo), `currency` |
| Contato | `email`, `phone_number` |
| Data | `date` |
| Documentos (com máscara) | `cpf`, `cnpj` |
| Endereço | `cep` — autopreenche subcampos via `options.fields[].attribute_type` (`street`, `state`, `city`, `neighborhood`, `number`, `complement`) |
| Upload | `image` (JPG/PNG), `file` (PDF/DOCX/imagens) |
| Seleção | `select`, `radio-group`, `checkbox-group` — opções em `options.values[]` (`label`/`value`); `options.other: true` libera resposta livre |
| Estrutural (não coleta dado) | `header`, `paragraph` |
| Dinâmico | `repeater` — clona um bloco de subcampos |

## Intervalo numérico (`number_range`)

`type: "number_range"` é um campo numérico com limites e precisão declarados na própria Version — use quando a resposta só é válida dentro de uma faixa (ex.: uma taxa entre 1,2% e 4,5%, com duas casas decimais). Diferente de `number`, que aceita qualquer numeral, e de `currency`, que carrega formatação monetária.

Configuração em `options`:

| Propriedade | Descrição |
|---|---|
| `min_value` | Valor mínimo aceito (obrigatório) |
| `max_value` | Valor máximo aceito (opcional — sem ele, a faixa é meio-aberta, ex.: "mínimo 18, sem teto") |
| `decimal_places` | Casas decimais permitidas (opcional, `0` a `6`, default `0`) |
| `min_error_message` | Mensagem exibida quando o valor informado é menor que `min_value` |
| `max_error_message` | Mensagem exibida quando o valor informado é maior que `max_value` |

As duas mensagens aceitam os placeholders `{min_value}`, `{max_value}` e `{decimal_places}`, substituídos na exibição pelos valores configurados no campo.

```json
{
  "label": "Taxa de administração",
  "type": "number_range",
  "placeholder": "Taxa de administração",
  "required": false,
  "order": 1,
  "step": 0,
  "options": {
    "min_value": 1.2,
    "max_value": 4.5,
    "decimal_places": 2,
    "min_error_message": "Informe um valor a partir de {min_value} com até {decimal_places} casas decimais.",
    "max_error_message": "Informe um valor até {max_value} com até {decimal_places} casas decimais."
  }
}
```

No webhook de resposta, o valor é entregue como **número canônico** (ponto como separador decimal), independentemente do separador usado no preenchimento.

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
