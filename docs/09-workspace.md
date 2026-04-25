# 09 - Workspace

Workspace e o espaco operacional do agente.

O modelo nao deve navegar no sistema inteiro. Ele deve operar dentro de um workspace controlado, com permissoes, logs e limites.

## Estrutura sugerida

```text
workspace/
  projects/
    meu-projeto/
      files...
  uploads/
    2026-04/
      screenshot.png
      spec.pdf
  skills/
    revisar-repo.md
  outputs/
    relatorio.md
  cache/
    pdf-text/
    thumbnails/
  metadata/
    workspace.json
```

## Principio

```text
Modelo pede recurso
  -> harness valida path
  -> tool le/escreve
  -> resultado volta
```

O modelo nao deve receber acesso irrestrito ao filesystem.

## Path guard

Toda operacao de arquivo deve garantir:

- path fica dentro do workspace;
- nao aceita `../` para escapar;
- nao segue symlink perigoso;
- bloqueia arquivos sensiveis;
- respeita permissoes do usuario/projeto.

## Navegacao pelo modelo

Fluxo para entender um projeto:

```text
1. list_files raiz
2. ler README
3. ler package.json/pyproject/go.mod/etc
4. listar src/app/lib
5. buscar entrypoints
6. ler arquivos centrais
7. resumir arquitetura
```

O agente deve explorar por etapas. Nao despejar o repo inteiro no contexto.

## Tools de workspace

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

## Indice de workspace

Para projetos grandes, criar indice:

```ts
type WorkspaceIndex = {
  files: Array<{
    path: string;
    sizeBytes: number;
    language?: string;
    lastModified?: string;
    summary?: string;
  }>;
};
```

## Leitura parcial

Nunca leia arquivo gigante inteiro sem necessidade.

```json
{
  "tool": "read_file",
  "args": {
    "path": "src/runtime.ts",
    "startLine": 120,
    "endLine": 240
  }
}
```

## Escrita segura

Antes de escrever:

1. gerar plano;
2. mostrar diff;
3. pedir approval se necessario;
4. salvar snapshot;
5. aplicar mudanca;
6. registrar evento;
7. permitir rollback.

## Diffs

Toda escrita deveria produzir diff:

```text
--- before
+++ after
@@
- const x = 1
+ const x = 2
```

## Multiusuario

Em app multiusuario:

```text
.data/
  users/
    user-id/
      workspaces/
        project-id/
      uploads/
      skills/
```

O usuario A nunca deve acessar workspace do usuario B.

## Workspace e Codex CLI

No modo CLI/MCP, o `cwd` deve apontar para o workspace/projeto autorizado.

```text
codex mcp-server
  cwd = workspace/projects/meu-projeto
```

## Regra de ouro

Workspace e a coleira anti-apocalipse do agente.

Ele da poder sem deixar o modelo sair andando pela casa mexendo no quadro de energia.
