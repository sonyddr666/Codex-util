# Codex-util

Documentacao pratica e arquitetural para construir apps com Codex, usando um harness proprio ao redor do modelo: chat, arquivos, imagens, tools, skills, workspace, voz, autenticacao, Codex HTTP, Codex CLI/MCP, seguranca e exemplos.

Este repositorio nao e uma implementacao final. Ele e uma base de estudo e especificacao para orientar projetos como `agentic-chat-codex`, `chat-md`, `fluxo-codex`, `skills-chat` e outros experimentos de agentes.

## Ideia central

Um modelo puro conversa. Um sistema agentic opera.

```text
Modelo Codex
+ interface de chat
+ workspace
+ upload de arquivos
+ imagens
+ tools
+ skills
+ voz
+ auth
+ sandbox
+ logs
+ approvals
= app agentic de verdade
```

A divisao mais importante deste repo e entender que Codex pode ser usado de duas formas principais:

1. **Codex HTTP / backend de respostas**: bom para conversa, pesquisa, voz, docs, skills simples, arquivos pequenos e operacoes leves.
2. **Codex CLI / MCP**: bom para tarefas profundas de codigo, revisao de repositorios, refactor, testes, shell, sandbox e approvals.

## Estrutura

```text
docs/
  01-visao-geral.md
  02-codex-http.md
  03-codex-cli-mcp.md
  04-auth-contas.md
  05-chat-com-codex.md
  06-arquivos-imagens-uploads.md
  07-tools.md
  08-skills.md
  09-workspace.md
  10-stt-tts-voz.md
  11-seguranca.md
  12-exemplos-de-fluxos.md
  13-roadmap-agentic.md
  14-referencias.md
```

## Finalidade

Este repo serve para documentar:

- como conversar com Codex;
- como montar payloads de chat;
- como lidar com streaming;
- como enviar arquivos, imagens e outros anexos;
- como criar tools locais;
- como criar skills reutilizaveis;
- como navegar um workspace;
- como separar modo normal e modo profundo de codigo;
- como obter e usar autenticacao;
- como pensar em STT/TTS;
- como proteger execucao local;
- como transformar tudo isso em um unico projeto agentic.

## Principio de arquitetura

```text
Voz / Upload / Chat
  -> Input Composer
  -> Mode Router
     -> Codex HTTP para uso normal
     -> Codex CLI/MCP para coding profundo
  -> Tool Router
  -> Workspace / Web / GitHub / Shell / Skills
  -> Event Log / UI / Resposta
```

## Aviso importante

Alguns fluxos citam endpoints internos ou comportamentos observados em clientes como Codex CLI e ChatGPT. Endpoints internos nao devem ser tratados como contrato publico. Para produto estavel, prefira APIs publicas e coloque recursos internos como experimentais, com fallback claro.
