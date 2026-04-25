# 08 - Skills

Skills sao workflows reutilizaveis que ensinam o agente a resolver classes de tarefas.

Uma skill nao e uma tool. Uma tool faz uma acao especifica. Uma skill combina contexto, regras, ferramentas e passos.

## Exemplo mental

```text
Tool: read_file
  -> le um arquivo

Skill: revisar-repo
  -> lista arquivos
  -> le configs
  -> entende stack
  -> analisa arquitetura
  -> gera relatorio
```

## Formato Markdown

Skills em Markdown sao boas para workflows de prompt:

```md
---
name: revisar-repo
description: Analisa arquitetura, riscos e melhorias de um projeto.
version: 1.0
tools:
  - list_files
  - read_file
  - search_text
  - web_search
---

# Quando usar

Use quando o usuario pedir analise de projeto, arquitetura, melhorias ou roadmap.

# Processo

1. Listar arquivos principais.
2. Ler README e configs.
3. Identificar stack.
4. Mapear camadas.
5. Apontar riscos.
6. Sugerir melhorias.
7. Gerar resposta estruturada.

# Regras

- Nao inventar arquivos.
- Citar arquivos lidos.
- Pedir approval antes de escrever.
```

## Formato JSON/code

Skills executaveis podem ter schema e codigo:

```json
{
  "id": "formatar_texto",
  "name": "formatar_texto",
  "description": "Formata texto em markdown limpo",
  "parameters": {
    "type": "object",
    "properties": {
      "input": { "type": "string" }
    },
    "required": ["input"]
  },
  "code": "return { text: args.input.trim() };"
}
```

## Escopos de skill

```text
global skill
  -> disponivel para todos

user skill
  -> criada por usuario

project skill
  -> vive dentro do projeto/workspace

thread skill
  -> temporaria para uma conversa
```

Prioridade sugerida:

```text
thread > project > user > global
```

## Skill registry

```ts
type Skill = {
  id: string;
  name: string;
  description: string;
  scope: "global" | "user" | "project" | "thread";
  format: "markdown" | "json" | "code";
  tools: string[];
  enabled: boolean;
};
```

## Loader

O loader deve:

- ler skills do disco/banco;
- validar frontmatter;
- verificar se tools existem;
- recusar skill com tool inexistente;
- mostrar erros na UI;
- permitir ativar/desativar;
- versionar alteracoes.

## UI de skills

```text
Skills

[+] Nova skill

revisar-repo
Tipo: markdown
Escopo: projeto
Tools: list_files, read_file, search_text
Status: ativa
Acoes: editar, duplicar, desativar, remover
```

## Skill creator

O app pode ter uma skill de criar skills.

Ela deve:

- perguntar finalidade;
- listar tools disponiveis;
- gerar frontmatter valido;
- evitar tools inventadas;
- incluir fallback;
- criar exemplos;
- validar antes de salvar.

## Boas praticas

- skills devem ser pequenas e especificas;
- nao colocar segredos na skill;
- nao permitir execucao perigosa sem approval;
- documentar quando usar e quando nao usar;
- manter exemplos;
- testar skill com casos reais;
- versionar mudancas.

## Regra de ouro

Skill boa nao promete magia. Skill boa descreve um processo que o harness consegue executar.
