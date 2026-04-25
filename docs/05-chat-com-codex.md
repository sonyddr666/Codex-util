# 05 - Chat com Codex

Este documento explica como estruturar uma conversa com Codex em um app agentic.

## Componentes do chat

```text
Chat UI
  -> composer
  -> anexos
  -> voz/STT
  -> historico
  -> mode router
  -> provider Codex
  -> streaming
  -> tool loop
  -> resposta final
```

## Mensagem simples

```json
{
  "role": "user",
  "content": "Explique o que este projeto faz."
}
```

## Mensagem com contexto

```json
{
  "role": "user",
  "content": "Analise este trecho e explique o bug:\n\n```ts\nfunction sum(a,b){ return a - b }\n```"
}
```

## Instrucoes do sistema

As instrucoes definem o comportamento do agente:

```text
Voce e um agente de desenvolvimento.
Quando precisar de arquivo, use tools.
Nunca invente resultado de tool.
Antes de escrever arquivos, explique o plano.
Acoes destrutivas exigem confirmacao.
```

## Streaming

O provider deve entregar deltas para a UI:

```text
response.started
response.delta
response.completed
response.error
```

A UI deve mostrar:

- texto em tempo real;
- status do run;
- tool calls;
- logs recolhiveis;
- diffs quando houver escrita;
- botao cancelar.

## Chat com tools

Quando o modelo precisar agir, ele pode emitir uma chamada textual:

```json
{
  "tool": "read_file",
  "args": {
    "path": "package.json"
  }
}
```

O runtime deve:

1. detectar JSON;
2. validar ferramenta;
3. validar argumentos;
4. checar permissao;
5. executar;
6. salvar evento;
7. devolver resultado ao modelo.

## Resultado de tool

O resultado deve ser devolvido como contexto claro:

```json
{
  "role": "tool",
  "tool": "read_file",
  "result": {
    "path": "package.json",
    "content": "{ ... }"
  }
}
```

Se o backend usado nao suportar `role: tool`, o app pode reinjetar como mensagem textual padronizada:

```text
[TOOL RESULT: read_file]
path: package.json
content:
...
[/TOOL RESULT]
```

## Historico

O historico pode ser gerenciado de tres formas:

1. reenviar mensagens importantes;
2. usar resumo de conversa;
3. usar `previous_response_id` quando disponivel.

## Resumo de contexto

Para conversas longas:

```text
Context Summary:
- Usuario esta criando um app agentic.
- Projeto usa Codex HTTP e Codex CLI/MCP.
- Workspace tem files, skills e uploads.
- Ultima decisao: usar dois modos.
```

## Erros comuns

- mandar historico inteiro sempre;
- deixar modelo inventar tools;
- nao validar JSON;
- executar comando sem approval;
- misturar STT dentro do provider;
- deixar arquivo grande estourar contexto;
- nao salvar eventos de tool.

## Melhor pratica

O chat deve ser uma casca bonita em cima de um runtime auditavel.

A resposta visivel deve ser limpa, mas o app precisa guardar o rastro completo do que aconteceu.
