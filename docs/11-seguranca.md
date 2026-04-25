# 11 - Seguranca

Um app agentic precisa de seguranca desde o primeiro dia.

Se o agente consegue ler arquivos, escrever, rodar shell, chamar HTTP e mexer no GitHub, ele precisa de coleira, trilho e auditoria. Nao e falta de confianca no modelo. E engenharia basica para nao transformar um chat em um aspirador de `.env`.

## Camadas de seguranca

```text
Usuario
  -> Auth do app
  -> Projeto/workspace autorizado
  -> Permission Layer
  -> Approval Gate
  -> Tool Registry
  -> Sandbox/Executor
  -> Audit Log
```

## Permission Layer

Antes de qualquer tool:

```ts
type PermissionDecision =
  | { allow: true }
  | { allow: false; reason: string }
  | { allow: "ask"; reason: string };
```

Perguntas que a camada deve responder:

- pode ler este arquivo?
- pode escrever neste path?
- pode deletar?
- pode rodar este comando?
- pode acessar rede?
- pode chamar HTTP POST?
- pode usar esta conta Codex?
- pode criar PR?
- pode expor este arquivo ao modelo?

## Approval Gate

Acoes perigosas devem exigir confirmacao:

```text
- delete_file
- rename_file
- write_file em arquivos sensiveis
- apply_patch amplo
- run_shell
- http_post/http_put/http_delete
- instalar dependencias
- git push
- criar branch/PR
- alterar secrets
```

Exemplo:

```text
O agente quer executar:
npm install axios

Motivo informado:
Adicionar cliente HTTP para a integracao.

Risco: altera package-lock e dependencias.

[Permitir uma vez] [Permitir neste projeto] [Negar]
```

## Path Guard

Toda tool de arquivo deve normalizar path:

```text
input path
  -> normalize
  -> resolve contra workspace root
  -> checar se continua dentro do root
  -> bloquear symlink perigoso
```

Bloquear:

```text
../
~/.ssh
.env
.env.*
secrets.json
node_modules se desnecessario
.git/config
arquivos fora do workspace
```

## Shell seguro

`run_shell` e uma das ferramentas mais perigosas.

Regras:

- timeout;
- max output;
- cwd restrito;
- env allowlist;
- comandos bloqueados;
- approval obrigatorio;
- shell=false por padrao quando possivel;
- logs com redacao de segredos;
- opcao de container/sandbox.

Policy sugerida:

```ts
type ExecPolicy = {
  allowedBins: string[];
  allowShell: boolean;
  timeoutMs: number;
  maxOutputBytes: number;
  envAllowlist: string[];
  network: "deny" | "allow" | "ask";
};
```

## Rede e HTTP

HTTP GET pode ser permitido com menos atrito. Metodos mutantes precisam de approval.

```text
GET     -> baixo risco
POST    -> medio/alto
PUT     -> medio/alto
PATCH   -> medio/alto
DELETE  -> alto
```

O app tambem deve bloquear:

- localhost sensivel sem permissao;
- metadata endpoints de cloud;
- IPs privados quando nao for esperado;
- exfiltracao de arquivos;
- headers com tokens em logs.

## Upload seguro

Regras:

- limite de tamanho;
- validacao de MIME;
- checagem de extensao;
- magic bytes quando possivel;
- antivirus opcional;
- impedir path traversal em ZIP;
- nao executar arquivo enviado;
- isolar por usuario/projeto.

## Segredos

Nunca mandar segredos ao modelo sem consentimento explicito.

Redigir:

```text
OPENAI_API_KEY
GITHUB_TOKEN
AUTH_SECRET
cookies
bearer tokens
session ids
refresh tokens
```

## Logs e auditoria

Salvar eventos:

```json
{
  "type": "tool_call",
  "runId": "run_123",
  "tool": "run_shell",
  "argsPreview": "npm test",
  "risk": "high",
  "approval": "approved_once",
  "status": "completed",
  "durationMs": 1870
}
```

O log deve permitir responder:

- quem pediu?
- qual modelo decidiu?
- qual tool foi chamada?
- quais arquivos foram lidos?
- quais arquivos foram alterados?
- qual comando rodou?
- qual approval foi dado?
- qual foi o resultado?

## Multiusuario

Cada usuario deve ter:

- workspace separado;
- skills separadas ou escopadas;
- contas Codex separadas;
- historico separado;
- permissoes separadas.

## Regra de ouro

Quanto mais poder o agente tem, mais visivel e auditavel ele precisa ser.

Agente bom nao e agente solto. E agente com ferramenta, permissao e recibo.
