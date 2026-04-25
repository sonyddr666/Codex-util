# 13 - Roadmap agentic

Este roadmap organiza a evolucao de um app Codex-util/Agentic.

## Fase 1 - Base de chat

Objetivo: conversar com Codex de forma estavel.

Entregas:

- UI de chat;
- streaming;
- historico;
- composer;
- mensagens markdown;
- cancelamento;
- logs basicos;
- provider Codex HTTP;
- configuracao de modelo;
- tratamento de erro.

## Fase 2 - Workspace e arquivos

Objetivo: permitir que o agente leia e escreva arquivos com controle.

Entregas:

- workspace root;
- upload de arquivos;
- list_files;
- read_file;
- write_file;
- search_text;
- apply_patch;
- path guard;
- diff viewer;
- snapshots;
- rollback simples.

## Fase 3 - Tools locais

Objetivo: transformar o chat em harness operacional.

Entregas:

- tool registry;
- schema validation;
- permission layer;
- approval gate;
- run logs;
- http_request;
- web_search;
- crawl_page;
- rss_read;
- run_shell seguro.

## Fase 4 - Skills

Objetivo: criar workflows reutilizaveis.

Entregas:

- skills Markdown;
- skills JSON/code;
- skill registry;
- skill loader;
- skill validator;
- skill UI;
- skill creator;
- escopos global/user/project/thread.

## Fase 5 - Dois modos Codex

Objetivo: separar uso cotidiano e uso profundo.

Entregas:

- Mode Router;
- Codex HTTP mode;
- Codex CLI/MCP mode;
- capability badges;
- fallback sem CLI;
- event normalizer;
- reasoning/command deltas quando disponivel.

## Fase 6 - Auth e contas

Objetivo: gerenciar credenciais com seguranca.

Entregas:

- app user auth;
- Codex account manager;
- API key provider;
- ChatGPT session provider experimental;
- auth pool;
- rotacao por rate limit;
- status por conta;
- redacao de segredos.

## Fase 7 - Voz

Objetivo: permitir entrada e saida por voz sem contaminar o provider.

Entregas:

- push-to-talk;
- STT oficial;
- STT experimental via backend interno;
- browser/system fallback;
- live mode;
- VAD;
- TTS;
- limpeza de markdown para fala;
- fila/cancelamento de audio.

## Fase 8 - GitHub

Objetivo: conectar o agente ao ciclo de desenvolvimento.

Entregas:

- github_search_code;
- github_fetch_file;
- github_create_issue;
- github_create_branch;
- github_commit_file;
- github_create_pull_request;
- github_comment_pr;
- github_review_pr;
- approval para escrita;
- logs de auditoria.

## Fase 9 - Jobs persistentes

Objetivo: tarefas continuarem mesmo se a aba cair.

Entregas:

- runs persistentes;
- SSE reanexavel;
- eventos com sequence number;
- cancel/retry;
- status por run;
- painel de jobs;
- worker interno.

## Fase 10 - Produto unico

Objetivo: juntar tudo em uma experiencia coerente.

Entregas:

- sidebar de projetos;
- chat central;
- painel de arquivos;
- painel de skills;
- painel de runs;
- painel de approvals;
- painel de contas;
- modo normal e modo profundo;
- documentacao integrada;
- templates de projeto.

## Principio de priorizacao

1. Primeiro estabilidade.
2. Depois seguranca.
3. Depois ferramentas.
4. Depois profundidade.
5. Depois brilho visual.

Nao adianta ter UI linda se o agente apaga arquivo como quem derruba pao na chapa.

## Produto final

Um unico app capaz de:

- conversar;
- ouvir;
- falar;
- pesquisar;
- ler arquivos;
- editar arquivos;
- usar skills;
- operar workspace;
- rodar comandos com approval;
- abrir issues/PRs;
- usar Codex HTTP no cotidiano;
- usar Codex CLI/MCP em tarefas profundas.
