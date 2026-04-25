# 14 - Referencias

Esta pagina organiza referencias e temas de pesquisa para evoluir o Codex-util.

## Repos do ecossistema pessoal

- https://github.com/sonyddr666/agentic-chat-codex
- https://github.com/sonyddr666/chat-md
- https://github.com/sonyddr666/fluxo-codex
- https://github.com/sonyddr666/skills-chat
- https://github.com/sonyddr666/code-chat-skills
- https://github.com/sonyddr666/Codex-util

## Temas principais

### Codex HTTP

Pesquisar:

```text
backend-api/codex/responses
previous_response_id
store:true
store:false
Codex backend responses
```

### Codex CLI/MCP

Pesquisar:

```text
codex mcp-server
codex/event
agent_reasoning_delta
exec_command_output_delta
opencode codex provider
```

### STT e transcricao

Pesquisar:

```text
backend-api/transcribe
voice_transcription
realtime_conversation
gpt-4o-transcribe
OpenAI audio transcriptions
Realtime transcription
```

### Tools

Pesquisar:

```text
JSON-over-text tool calling
action blocks
MCP tools/call
agent tool loop
function calling harness
```

### Skills

Pesquisar:

```text
ChatGPT Skills
Claude Skills
MCP skill workflow
local skills markdown
agent workflow prompts
```

### Workspace

Pesquisar:

```text
agent workspace sandbox
path guard
workspace indexing
codebase agent architecture
```

### Segurança

Pesquisar:

```text
agent sandbox approval gate
AI tool permission model
LLM agent audit logs
path traversal prevention
shell command allowlist
```

## Fontes publicas uteis

- GitHub issues e discussions.
- Reddit: r/codex, r/OpenAI, r/ChatGPTCoding, r/LocalLLaMA.
- Hacker News.
- Lobsters.
- TabNews.
- Dev.to.
- Zenn.
- Qiita.
- OpenAI docs.
- OpenAI Help Center.
- Docs de MCP.
- Repos de providers para opencode, OpenClaw, Codex-Pool, Continue, Aider e similares.

## Strings de busca importantes

```text
"backend-api/transcribe"
"/backend-api/transcribe"
"voice_transcription = true"
"realtime_conversation = true"
"backend-api/codex/responses"
"previous_response_id"
"store": true
"codex mcp-server"
"tools/call" "codex"
"agent_reasoning_delta"
"exec_command_output_delta"
"gpt-4o-transcribe"
```

## Como classificar evidencia

### Forte

- codigo fonte;
- logs com endpoint;
- issue reproduzivel;
- docs oficiais;
- request/response observado pelo proprio usuario.

### Media

- artigo tecnico com leitura de codigo;
- comentario de maintainer;
- reproducao parcial;
- varios relatos independentes.

### Fraca

- especulacao;
- print sem contexto;
- comentario sem log;
- thread antiga;
- comportamento inferido sem evidencia.

## Regra de documentacao

Sempre separar:

```text
Confirmado
Provavel
Experimental
Desconhecido
```

Isso evita transformar arquitetura em lenda urbana com markdown bonito.
