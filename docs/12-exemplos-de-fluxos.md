# 12 - Exemplos de fluxos

Este documento mostra exemplos praticos de uso de um app baseado em Codex.

## 1. Conversa simples

Usuario:

```text
Explique a diferenca entre Codex HTTP e Codex CLI.
```

Fluxo:

```text
UI
  -> Codex HTTP
  -> resposta em streaming
```

Nao precisa tool, workspace ou CLI.

## 2. Pesquisa web simples

Usuario:

```text
Pesquise bibliotecas atuais para editor Markdown em React.
```

Fluxo:

```text
Codex HTTP
  -> tool web_search
  -> resultados
  -> resposta resumida
```

## 3. Chamada HTTP simples

Usuario:

```text
Faz um GET nesse endpoint e resume o JSON.
```

Tool:

```json
{
  "tool": "http_request",
  "args": {
    "method": "GET",
    "url": "https://api.example.com/status"
  }
}
```

GET pode ser baixo risco. POST/DELETE devem pedir approval.

## 4. Criar HTML simples

Usuario:

```text
Cria um HTML simples de dashboard com cards.
```

Fluxo:

```text
Codex HTTP
  -> gerar HTML
  -> opcional write_file index.html
  -> preview
```

Nao precisa Codex CLI. Chamar CLI aqui seria trator em ciclovia.

## 5. Enviar imagem de erro

Usuario envia screenshot.

Fluxo:

```text
Upload imagem
  -> metadata
  -> preview
  -> input visual ou OCR/descricao
  -> Codex HTTP analisa
  -> resposta
```

Se o modelo aceitar imagem, envie como input visual. Se nao, gere descricao/OCR.

## 6. Enviar PDF

Usuario envia `spec.pdf`.

Fluxo:

```text
Upload PDF
  -> extrair texto
  -> criar cache
  -> resumir secoes
  -> modelo pede paginas/trechos
```

Nao jogar PDF inteiro no prompt sem necessidade.

## 7. Usar skill

Usuario:

```text
Use a skill revisar-repo neste projeto.
```

Fluxo:

```text
Skill registry encontra revisar-repo
  -> injeta processo da skill
  -> list_files
  -> read_file README/configs
  -> search_text
  -> resposta estruturada
```

## 8. Navegar workspace

Usuario:

```text
Entenda esse projeto e me diga a arquitetura.
```

Fluxo:

```text
list_files
read_file README.md
read_file package.json
list_files src
search_text "createServer"
read_file arquivos centrais
resposta
```

## 9. Corrigir bug pequeno sem CLI

Usuario cola erro e trecho de codigo.

```text
Esse trecho esta retornando NaN. Corrige.
```

Fluxo:

```text
Codex HTTP
  -> raciocina sobre trecho colado
  -> sugere patch
```

Sem workspace, sem shell, sem CLI.

## 10. Corrigir bug real no repo

Usuario:

```text
Roda os testes, acha o erro e corrige.
```

Fluxo:

```text
Mode Router escolhe Codex CLI/MCP
  -> workspace cwd
  -> rodar testes com approval
  -> ler erro
  -> editar arquivos
  -> rodar testes novamente
  -> mostrar diff
```

## 11. Refactor amplo

Usuario:

```text
Refatora o sistema de tools para separar web, fs e shell.
```

Fluxo:

```text
Codex CLI/MCP
  -> analisar estrutura
  -> plano
  -> approval
  -> patches multi-arquivo
  -> testes
  -> diff final
```

## 12. Criar issue no GitHub

Usuario:

```text
Cria uma issue com esse plano de melhorias.
```

Fluxo:

```text
Codex HTTP
  -> gerar body
  -> github_create_issue com approval
```

## 13. Criar PR

Usuario:

```text
Implementa e abre PR.
```

Fluxo:

```text
Codex CLI/MCP ou GitHub tools
  -> branch
  -> patches
  -> testes
  -> commit
  -> create_pull_request
```

Exigir approval antes de publicar.

## 14. Voz

Usuario segura botao e fala:

```text
analisa o repo e me diz onde fica o runtime
```

Fluxo:

```text
STT
  -> texto
  -> Mode Router
  -> se so analise leve: Codex HTTP
  -> se precisa repo profundo: CLI/MCP
```

## Regra de exemplos

Sempre escolha o menor modo capaz de resolver a tarefa.

```text
Texto resolve? Use HTTP.
Tool leve resolve? Use HTTP + tool.
Precisa repo/shell/testes? Use CLI/MCP.
```
