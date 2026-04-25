# 04 - Auth e contas

Este documento explica formas de autenticacao para um app baseado em Codex.

## Tipos de autenticacao

Existem dois mundos principais:

1. **API key oficial**: usa APIs publicas, mais estavel e previsivel.
2. **Sessao ChatGPT/Codex**: usa credenciais de login do ChatGPT/Codex, geralmente para endpoints internos ou fluxos de cliente.

## API key

A API key e o caminho mais estavel para produto.

Uso comum:

```text
OPENAI_API_KEY=...
```

Vantagens:

- contrato publico;
- docs oficiais;
- menos risco de Cloudflare/challenge;
- bom para STT oficial;
- bom para deploy servidor.

Limites:

- pode nao acessar recursos especificos do app Codex;
- billing separado;
- pode nao ter o mesmo fluxo de contexto interno usado por clientes oficiais.

## Sessao ChatGPT/Codex

A sessao ChatGPT/Codex pode ser usada por clientes como Codex CLI ou apps que chamam endpoints do dominio `chatgpt.com`.

Exemplos conceituais:

```text
/backend-api/codex/responses
/backend-api/transcribe
```

Vantagens:

- aproxima o app dos fluxos usados pelo cliente Codex;
- pode reaproveitar auth da conta;
- pode permitir contexto nativo via response id;
- pode ser util para experimentos.

Riscos:

- endpoints internos nao sao contrato publico;
- podem mudar sem aviso;
- podem exigir headers especificos;
- podem cair em Cloudflare/challenge;
- podem falhar em ambientes diferentes;
- devem ser tratados como experimentais.

## Auth Pool

Para apps locais ou experimentais, pode existir um pool de contas:

```text
AccountPool
  - conta A
  - conta B
  - conta C
```

O provider seleciona a melhor conta disponivel e troca em caso de:

- rate limit;
- quota;
- token expirado;
- erro temporario;
- bloqueio de endpoint.

## Modelo de dados sugerido

```ts
type CodexAccount = {
  id: string;
  label: string;
  type: "api_key" | "chatgpt_session";
  status: "active" | "rate_limited" | "expired" | "disabled";
  lastUsedAt?: string;
  lastError?: string;
};
```

## Separar usuario do app e conta Codex

Em um produto multiusuario, nao misture:

```text
Usuario do app
  -> login local
  -> permissoes
  -> workspace
  -> preferencias

Conta Codex/OpenAI
  -> credencial para chamar modelo/transcricao
  -> rate limits
  -> billing
```

Modelo:

```text
users
user_codex_accounts
projects
threads
runs
```

## Armazenamento seguro

Recomendacoes:

- criptografar tokens em repouso;
- nunca salvar credenciais em logs;
- redigir headers sensiveis;
- bloquear export acidental;
- permitir remover conta;
- mostrar status da conta sem exibir segredo;
- limitar acesso por usuario.

## UI sugerida

```text
Contas Codex

[+] Adicionar conta

Conta principal
Status: ativa
Tipo: ChatGPT session
Ultimo uso: agora
Acoes: testar, desativar, remover

API key backup
Status: ativa
Tipo: API key
Acoes: testar, desativar, remover
```

## Regra de seguranca

Nunca trate endpoint interno como garantia. Sempre tenha fallback oficial ou modo degradado.
