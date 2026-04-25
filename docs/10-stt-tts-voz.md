# 10 - STT, TTS e voz

Voz deve ser uma camada separada do provider Codex.

O fluxo certo e:

```text
Audio do usuario
  -> STT
  -> texto
  -> composer
  -> mode router
  -> Codex HTTP ou Codex CLI/MCP
```

E na resposta:

```text
Texto do agente
  -> limpar markdown/codigo
  -> TTS
  -> audio
```

## STT nao e Codex

Codex nao precisa receber audio para funcionar. O app transcreve o audio antes e envia texto.

## Providers de STT

### 1. OpenAI Audio API

Caminho recomendado para produto estavel.

```text
Browser grava audio
  -> POST /api/stt/transcribe
  -> /v1/audio/transcriptions
  -> texto
```

Modelos possiveis:

- gpt-4o-transcribe;
- gpt-4o-mini-transcribe;
- modelos futuros de transcricao.

### 2. Backend interno de transcricao

Alguns clientes usam ou parecem usar endpoints internos como:

```text
/backend-api/transcribe
```

Esse modo deve ser tratado como experimental.

Riscos:

- pode mudar;
- pode exigir auth especifica;
- pode bater em Cloudflare;
- pode falhar fora do cliente oficial;
- nao e contrato publico.

### 3. Browser/System dictation

Fallback:

- Web Speech API;
- dictado do sistema operacional;
- Win+H no Windows;
- recursos nativos do navegador.

## Push-to-talk

UX simples:

```text
Segura botao/atalho
  -> grava
Solta
  -> transcreve
  -> coloca texto no composer
```

Atalhos sugeridos:

```text
Space segurado: gravar
Esc: cancelar
Ctrl+Enter: enviar
Shift+Space: modo continuo
```

## Live mode

Modo mais avancado:

```text
Microfone ligado
  -> VAD detecta fala/silencio
  -> chunks sao enviados
  -> transcricao aparece em tempo real
  -> usuario confirma ou auto-envia
```

## Realtime transcription

Para UX moderna:

```text
WebSocket/WebRTC
  -> audio PCM
  -> transcription session
  -> deltas de texto
  -> completed event
```

## Prompt de transcricao

Transcricao tecnica melhora com prompt:

```text
Transcreva em portugues brasileiro. Preserve termos tecnicos em ingles: Codex, MCP, API, repo, branch, pull request, Next.js, TypeScript, backend, endpoint, token, auth.
```

## Correcao pos-STT

Depois de transcrever:

```text
1. corrigir pontuacao;
2. preservar termos tecnicos;
3. nao inventar conteudo;
4. manter intencao do usuario;
5. mostrar preview antes de enviar.
```

## Preview antes de enviar

```text
Voce disse:
"analisa o repo e cria uma issue com as melhorias"

[Enviar] [Editar] [Gravar de novo]
```

## TTS

TTS deve falar a resposta final, nao logs brutos.

Antes de falar:

- remover blocos de codigo longos;
- simplificar markdown;
- trocar listas gigantes por resumo;
- nao ler JSON de tool;
- permitir pausa/cancelamento;
- respeitar idioma.

## TTS com fila

```text
Resposta em chunks
  -> limpar chunk
  -> enfileirar audio
  -> tocar na ordem
  -> permitir cancelar
```

## Integração com dois modos

STT acontece antes do Mode Router.

Exemplo 1:

```text
Usuario fala: cria um HTML simples
  -> STT
  -> texto
  -> Router escolhe Codex HTTP
```

Exemplo 2:

```text
Usuario fala: abre o projeto, roda testes e corrige o build
  -> STT
  -> texto
  -> Router escolhe Codex CLI/MCP
```

## Regra de ouro

Voz e boca/ouvido do app. Codex continua sendo cerebro textual.
