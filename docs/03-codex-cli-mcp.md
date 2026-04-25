# 03 - Codex CLI / MCP

Codex CLI/MCP e o modo profundo de uso. Ele deve ser usado quando o usuario realmente precisa que o agente opere um projeto de codigo.

A ideia nao e substituir o modo normal. A ideia e ter um modo especializado para tarefas com mais risco e mais contexto.

## Quando usar

Use Codex CLI/MCP para:

- analisar codebase inteira;
- refatorar varios arquivos;
- rodar testes;
- corrigir build;
- acompanhar output de comando;
- aplicar patch complexo;
- revisar arquitetura de codigo;
- criar feature integrada ao projeto;
- operar com sandbox;
- exigir approval antes de acoes perigosas.

## Quando nao usar

Nao use CLI para:

- conversa geral;
- resumo;
- pesquisa web simples;
- HTTP request simples;
- HTML isolado pequeno;
- docs;
- prompt engineering;
- transformacao de texto;
- explicacao de codigo colado no chat.

Usar CLI para tudo e como chamar guincho para mover uma cadeira.

## Arquitetura conceitual

```text
Usuario pede tarefa de codigo profunda
  -> Mode Router decide CLI
  -> CodexCliMcpProvider
  -> spawn codex mcp-server
  -> JSON-RPC
  -> eventos do agente
  -> Event Normalizer
  -> UI / logs / diffs / resposta
```

## Eventos desejados

Um provider CLI/MCP deve converter eventos internos para eventos padronizados:

```ts
type AgentProviderEvent =
  | { type: "text_delta"; text: string }
  | { type: "reasoning_delta"; text: string }
  | { type: "tool_start"; name: string; args?: unknown }
  | { type: "tool_output"; name: string; output: string }
  | { type: "command_output_delta"; stream: "stdout" | "stderr"; text: string }
  | { type: "task_complete" }
  | { type: "error"; error: string };
```

## Capabilities

O app deve declarar capacidades por provider:

```ts
const codexHttpCapabilities = {
  textStreaming: true,
  nativeToolEvents: false,
  reasoningDeltas: false,
  commandOutputDeltas: false,
  sandbox: false,
  approvals: false,
  previousResponseId: true
};

const codexCliCapabilities = {
  textStreaming: true,
  nativeToolEvents: true,
  reasoningDeltas: true,
  commandOutputDeltas: true,
  sandbox: true,
  approvals: true,
  previousResponseId: false
};
```

## UX sugerida

```text
Modo atual: Codex CLI
Motivo: tarefa envolve varios arquivos, testes e shell.
Acoes permitidas: ler arquivos, aplicar patch, rodar comandos aprovados.
Acoes que exigem confirmacao: delete, install, git push, alteracao de env.
```

## Fallback

Se a CLI nao estiver disponivel:

```text
1. avisar que o modo profundo nao esta disponivel;
2. oferecer continuar no modo HTTP;
3. limitar a tarefa a analise/plano, sem execucao profunda;
4. permitir configurar caminho da CLI.
```

## Regra de produto

Codex CLI/MCP deve ser um modo escalado, nao o padrao.

O usuario deve sentir que entrou em uma oficina de codigo real: com logs, approvals, diffs, status e possibilidade de cancelar.
