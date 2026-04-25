# 02 - Codex HTTP

Codex HTTP e o modo normal de uso: uma camada de chat/streaming que recebe texto, contexto, instrucoes e devolve resposta.

Ele e ideal para:

- conversa;
- pesquisa;
- docs;
- explicacoes;
- skills simples;
- leitura leve de arquivos;
- geracao de markdown;
- HTML simples;
- chamadas HTTP simples;
- fluxo com voz.

## Ideia basica

```text
Usuario
  -> UI
  -> Input Composer
  -> Codex HTTP Provider
  -> streaming de texto
  -> resposta
```

## Payload conceitual

```json
{
  "model": "gpt-5.x-codex",
  "instructions": "Voce e um agente de desenvolvimento...",
  "input": [
    {
      "role": "user",
      "content": "Explique este projeto"
    }
  ],
  "stream": true,
  "store": true
}
```

## Contexto nativo

Alguns fluxos observados usam `store:true` e `previous_response_id`.

```json
{
  "model": "gpt-5.x-codex",
  "instructions": "Continue a conversa anterior",
  "input": [
    {
      "role": "user",
      "content": "Agora gere um plano"
    }
  ],
  "previous_response_id": "resp_...",
  "store": true,
  "stream": true
}
```

Esse padrao evita reenviar todo o historico em toda chamada. O servidor do provedor consegue encadear o contexto pela resposta anterior.

## Fallback stateless

Como endpoints e contas podem variar, o app deve suportar fallback:

```text
Tenta store:true + previous_response_id
  -> se falhar com 400/403/422
  -> limpa response_id
  -> tenta store:false
```

## Tools no modo HTTP

No modo HTTP, normalmente o app nao deve assumir que ha tool calling nativo completo. A abordagem segura e usar protocolo textual controlado pelo harness.

Exemplo:

```json
{
  "tool": "read_file",
  "args": {
    "path": "README.md"
  }
}
```

Fluxo:

```text
Modelo escreve JSON
  -> runtime parseia
  -> valida schema
  -> executa tool local
  -> injeta resultado no proximo turno
```

## Vantagens

- simples de hospedar;
- bom para chat;
- bom para voz;
- bom para web tools;
- bom para docs e arquivos pequenos;
- funciona sem depender de CLI local;
- pode usar auth pool e proxy proprio.

## Limitacoes

- tool calling precisa ser emulado;
- sandbox nao vem pronto;
- approvals precisam ser do app;
- output de comandos precisa ser do app;
- eventos ricos de agente precisam ser normalizados localmente;
- nao e o mesmo que rodar Codex CLI.

## Recomendacao

Use Codex HTTP como modo padrao. Ele deve ser rapido, leve e seguro.

Use Codex CLI/MCP apenas quando a tarefa envolver repos, varios arquivos, testes, shell, debug ou refactor profundo.
