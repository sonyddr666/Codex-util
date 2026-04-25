# 01 - Visao geral do Codex-util

Este documento apresenta a visao geral de um app agentic baseado em Codex.

A ideia central e separar claramente tres coisas:

1. **Modelo**: o Codex gera texto, raciocina e sugere acoes.
2. **Harness**: o app decide contexto, tools, permissoes, workspace, streaming e execucao.
3. **Ambiente**: arquivos, terminal, web, GitHub, skills, voz, uploads e historico.

Sem harness, o modelo e um chat. Com harness, ele vira um operador.

## Arquitetura macro

```text
Usuario
  |
  v
UI Chat / Voz / Upload
  |
  v
Input Composer
  |
  v
Mode Router
  |------------------------|
  v                        v
Codex HTTP              Codex CLI/MCP
modo normal             modo profundo
  |                        |
  v                        v
Tool Router / Event Normalizer
  |
  v
Workspace / Web / GitHub / Skills / Shell
  |
  v
Resposta / Arquivo / Diff / Log / PR / Issue
```

## Modo normal

Use o modo normal para tarefas cotidianas:

- conversa;
- explicacoes;
- documentacao;
- pesquisa web;
- HTTP requests simples;
- skills de texto;
- criacao de HTML simples;
- leitura e escrita leve de arquivos;
- STT/TTS;
- analise de anexos pequenos;
- geracao de markdown, README, prompts e planos.

Neste modo, Codex HTTP e suficiente. O app manda texto e contexto, recebe streaming e, quando necessario, interpreta tool calls feitas por JSON-over-text.

## Modo profundo

Use o modo profundo com Codex CLI/MCP para tarefas de engenharia reais:

- analisar repos inteiros;
- revisar arquitetura de codigo;
- refatorar varios arquivos;
- rodar testes;
- depurar build;
- acompanhar output de terminal;
- trabalhar com sandbox e approvals;
- operar como agente de coding.

Este modo deve ser ativado manualmente ou por um roteador de intencao quando a tarefa realmente exigir.

## O papel do workspace

O workspace e o chao do agente.

```text
workspace/
  uploads/
  projects/
  skills/
  outputs/
  cache/
```

O modelo nao acessa arquivos diretamente. Ele pede. O harness valida. A tool executa. O resultado volta ao modelo.

## O papel das tools

Tools sao capacidades concretas do app:

- `read_file`;
- `write_file`;
- `list_files`;
- `search_text`;
- `apply_patch`;
- `run_shell`;
- `web_search`;
- `http_request`;
- `github_create_issue`;
- `github_create_pr`.

O modelo nao deve inventar ferramenta. O harness deve registrar, validar e auditar tudo.

## O papel das skills

Skills sao workflows reutilizaveis. Elas dizem ao agente quando e como usar ferramentas para resolver um tipo especifico de tarefa.

Exemplo:

```text
Skill: revisar-repo
- listar arquivos
- ler configs
- identificar stack
- mapear arquitetura
- sugerir melhorias
- gerar relatorio
```

## O papel da voz

STT e TTS nao devem morar dentro do provider Codex.

```text
Audio do usuario
  -> STT
  -> texto
  -> composer
  -> mode router
  -> Codex
```

Resposta do Codex:

```text
Texto final
  -> limpeza de markdown/codigo
  -> TTS
  -> audio
```

## Regra de ouro

Codex HTTP e o modo cotidiano. Codex CLI/MCP e o modo de intervencao profunda.

Nao chame o modo profundo para trocar lampada. Chame quando o monorepo comecar a rosnar.
