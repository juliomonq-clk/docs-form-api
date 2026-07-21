# Conceitos e Modelo de Dados

## Form

Um **Form** é a "casca": metadados base e o webhook padrão de destino.

| Campo | Descrição |
|---|---|
| `key` | Identificador único (UUID) do formulário |
| `name` | Nome do formulário |
| `callback_url` | URL de webhook disparada quando um `Run` deste formulário é concluído |
| `account_id` / `user_id` | Conta e usuário proprietários |
| `created_at` / `updated_at` | Timestamps |

Um Form pode ter várias `Versions` ao longo do tempo, mas só a mais recente ativa (`is_active: true`) deve ser usada para novas execuções.

## Version

Uma **Version** é o schema do formulário: os campos, o texto da interface e a configuração de etapas.

**[CORREÇÃO 21/07/2026] Não é imutável.** `PUT /api/v1/forms/{form_key}/versions/{version_key}` substitui `fields`/`settings`/`is_active` **na mesma `version_key`** (ver exemplo `fullReplace` no OpenAPI) — não cria uma versão nova. Criar uma nova Version (`POST`) é uma operação separada e continua disponível, mas editar a existente in-place via `PUT` também é.

| Campo | Descrição |
|---|---|
| `key` / `version` | Identificador único (UUID) da versão |
| `is_active` | Se esta é a versão vigente do formulário |
| `fields[]` | Lista de `FormField` (ver [`04-tipos-de-campo-e-logica-condicional.md`](04-tipos-de-campo-e-logica-condicional.md)) |
| `settings` | Textos de UI (`title`, `subtitle`, mensagens de conclusão) e rótulos de `steps` |
| `form_url` | URL pública de preenchimento desta versão |
| `created_at` / `updated_at` | Timestamps |

Criar uma nova Version (`POST`) não altera versões anteriores. **Em aberto, não confirmado:** o efeito de um `PUT` na mesma `version_key` sobre um Run `pending` que já referencia esse schema (ou uma tela de Link Reutilizável já carregada) — se o webview rebusca o schema antes do envio, e o que ocorre se a resposta referenciar um campo removido pelo `PUT` ou deixar de preencher um campo que passou a `required` depois que a tela já estava aberta. Até essa confirmação, evite editar `fields`/`settings` de uma Version com Runs `pending` conhecidos — prefira criar uma nova Version via `POST`.

## Run

Um **Run** é uma execução: uma instância de preenchimento único, com link próprio e dados de pré-preenchimento.

| Campo | Descrição |
|---|---|
| `key` | Identificador único (UUID) da execução |
| `status` | `pending` \| `done` \| `access_verification_required` |
| `context` | Mapa de dados pré-injetados (Two-Way API / Smart Fill) |
| `responses` | Respostas coletadas do usuário final (populado ao concluir) |
| `form_url` | URL única de preenchimento deste Run |
| `expires_at` | Expiração opcional do link |
| `callback_url` | Webhook exclusivo deste Run (disparado junto com o do Form, quando ambos configurados) |
| `access_verification` | Gate opcional de validação antes de liberar o preenchimento |
| `created_at` / `updated_at` | Timestamps |

### Regra de imutabilidade

Um Run com `status: done` **não aceita mais `PUT` nem `DELETE`** — a API retorna `409 Conflict`. Trate um Run concluído como registro final, não como rascunho editável.

### Estados de um Run

```
pending ──(usuário conclui o preenchimento)──► done

pending ──(access_verification adicionado via PUT)──► access_verification_required
access_verification_required ──(valor validado)──► pending ──► done
```

- **`pending`** — aguardando preenchimento pelo usuário final.
- **`access_verification_required`** — existe um gate de acesso configurado (ver abaixo) ainda não validado.
- **`done`** — preenchimento concluído; imutável a partir daqui.

### Two-Way API (Smart Fill)

Campos podem ser pré-injetados no `context` do Run com `read_only: true` (o usuário só confirma, não edita) ou `read_only: false` (vem pré-preenchido, mas editável). Ao final, `responses` traz o que foi efetivamente confirmado/preenchido — elimina a necessidade de a empresa integradora redigitar dados que já tinha.

### Access Verification (gate de acesso ao Run)

Objeto opcional em `access_verification`, com `label` (mensagem exibida ao usuário), `expected_value` (valor a validar, ex.: um CPF) e `mask_percentage` (percentual mascarado na tela, default 90). Enquanto o valor não é validado pelo usuário, o `status` do Run fica `access_verification_required` — útil para proteger links compartilhados por canais menos controlados (ex.: encaminhamento no WhatsApp).

## Como as três entidades se relacionam

```
Form
 └── Version (editável in-place via PUT na mesma version_key; pode haver várias ao longo do tempo)
       └── Run #1 (pending → done)
       └── Run #2 (access_verification_required → pending → done)
       └── Run #N
```

Um mesmo Form/Version pode originar múltiplos Runs independentes — um por preenchimento.

## Próximo passo

Para autenticar suas chamadas contra a API, siga para [`03-autenticacao.md`](03-autenticacao.md).
