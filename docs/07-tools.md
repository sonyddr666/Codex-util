# 07 - Tools

Tools sao a forma do agente agir fora do texto.

O modelo nao deve ter permissao direta sobre o sistema. Ele deve pedir uma tool. O harness valida, executa e devolve o resultado.

## Fluxo

```text
Modelo decide que precisa agir
  -> emite chamada de tool
  -> runtime parseia
  -> schema valida
  -> permission layer autoriza
  -> tool executa
  -> resultado e salvo
  -> resultado volta ao modelo
```

## Tool call textual

Quando nao ha tool calling nativo, use JSON-over-text:

```json
{
  "tool": "read_file",
  "args": {
    "path": "README.md"
  }
}
```

Tambem e possivel suportar blocos de acao para compatibilidade:

```text
::fs-read::README.md::
```

A recomendacao e usar JSON como protocolo principal e action blocks como modo legado/compatibilidade.

## Tool registry

```ts
type ToolDefinition = {
  name: string;
  description: string;
  schema: unknown;
  risk: "low" | "medium" | "high";
  requiresApproval: boolean;
  execute(args: unknown, ctx: ToolContext): Promise<ToolResult>;
};
```

## Tools essenciais

### Filesystem

```text
list_files
read_file
write_file
write_files
append_file
rename_file
delete_file
search_text
apply_patch
glob
```

### Shell

```text
run_shell
```

Deve ter:

- timeout;
- limite de output;
- allowlist de comandos;
- cwd restrito;
- env filtrado;
- approval para comandos perigosos.

### Web

```text
web_search
fetch_url
crawl_page
scrape_html
rss_read
```

### HTTP

```text
http_request
http_get
http_post
http_put
http_delete
```

HTTP com metodo destrutivo deve exigir approval.

### GitHub

```text
github_search_code
github_fetch_file
github_create_issue
github_create_branch
github_commit_file
github_create_pull_request
github_comment_pr
```

### Historico

```text
search_history
load_thread
list_threads
summarize_thread
```

## Resultado de tool

```ts
type ToolResult = {
  ok: boolean;
  data?: unknown;
  error?: string;
  display?: string;
  artifacts?: string[];
};
```

## Risco por tool

| Tool | Risco | Approval |
|---|---:|---:|
| read_file | baixo | nao |
| list_files | baixo | nao |
| search_text | baixo | nao |
| write_file | medio | sim, dependendo do arquivo |
| apply_patch | medio | sim |
| delete_file | alto | sempre |
| run_shell | alto | sempre ou policy |
| http_get | baixo | nao |
| http_post | medio | sim |
| http_delete | alto | sempre |
| github_create_pr | medio | sim |
| github_merge_pr | alto | sempre |

## Validacao

Nunca execute tool sem validar:

- nome existe;
- args batem com schema;
- path esta dentro do workspace;
- usuario tem permissao;
- risco exige approval;
- output nao excede limite;
- segredo nao aparece em logs.

## Approval gate

Exemplo de prompt de approval:

```text
O agente quer executar:
npm test

Motivo: verificar se a alteracao passou nos testes.

[Permitir uma vez] [Permitir neste projeto] [Negar]
```

## Logs

Todo tool call deve gerar evento:

```json
{
  "type": "tool_call",
  "tool": "run_shell",
  "argsPreview": "npm test",
  "status": "completed",
  "durationMs": 1234,
  "approvedBy": "user"
}
```

## Regra de ouro

O modelo pede. O harness decide. A tool executa. O log prova.
