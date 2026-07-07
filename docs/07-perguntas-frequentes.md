# Perguntas Frequentes

**O ClickForm gera documento ou coleta assinatura?**
Não. O ClickForm é desacoplado da geração de documentos e da assinatura eletrônica — ele só coleta, valida e entrega dados estruturados via webhook. Documento e assinatura são responsabilidade de outras APIs da Clicksign (ou do sistema da própria empresa integradora), acionadas separadamente. Ver [modelos de uso](01-visao-geral.md#modelos-de-uso-oferecidos).

**Preciso usar o ClickFlow para usar o ClickForm?**
Não. O ClickForm pode ser contratado e integrado de forma isolada, sem nunca entrar em uma esteira do ClickFlow.

**Existe um builder visual para montar o formulário?**
Não, hoje não. A configuração é feita via JSON/API. Normalmente o time de Professional Services da Clicksign apoia o desenho inicial do schema.

**Qual a diferença entre o link da Version e o link do Run?**
A Version tem um `form_url` genérico (schema publicado), mas cada **Run** tem seu próprio `form_url` único, com os dados de `context` já pré-injetados para aquele preenchimento específico. Na prática, você cria um Run por pessoa/preenchimento.

**Como funciona o pré-preenchimento (Smart Fill)?**
Você envia os dados que já tem no `context` do Run, marcando cada um como `read_only: true` (usuário só confirma) ou `false` (vem preenchido, mas editável). Ver [Two-Way API](02-conceitos-e-modelo-de-dados.md#two-way-api-smart-fill).

**Que tipos de anexo o formulário aceita?**
`file` aceita PDF, DOCX e imagens; `image` aceita apenas PNG/JPG. Ver [tipos de campo](04-tipos-de-campo-e-logica-condicional.md).

**Por quais canais o formulário é entregue?**
Hoje, primariamente por link web, com suporte a webview nativa dentro do WhatsApp (WhatsApp First) — o usuário preenche sem sair do app.

**O que acontece se eu tentar editar um Run já concluído?**
A API retorna `409 Conflict`. Um Run com `status: done` é imutável — ver [regra de imutabilidade](02-conceitos-e-modelo-de-dados.md#regra-de-imutabilidade).

**Como valido que o webhook recebido realmente veio do ClickForm?**
Em aberto — não há, até o momento, um mecanismo de verificação de assinatura documentado. Confirme com o time técnico do ClickForm antes de depender disso em produção.

**Onde consigo suporte?**
Dúvidas de acesso/credenciais: `professionalservices@clicksign.com`. Para o contrato completo de endpoints, consulte a [spec OpenAPI](../collections/openapi/clickform-api.openapi.yaml) e as [coleções prontas para importar](../collections/README.md).
