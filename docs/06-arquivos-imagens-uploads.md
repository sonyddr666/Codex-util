# 06 - Arquivos, imagens e uploads

Um app com Codex precisa tratar anexos como parte do contexto, mas sem jogar tudo cru no modelo.

## Tipos de entrada

O usuario pode enviar:

- texto;
- Markdown;
- JSON;
- CSV;
- HTML;
- CSS;
- JavaScript/TypeScript;
- imagens;
- screenshots;
- PDFs;
- ZIPs;
- logs;
- arquivos de projeto;
- audios para STT.

## Principio

```text
Upload nao e contexto automaticamente.
Upload vira recurso no workspace.
O agente pede o que precisa ler.
```

## Fluxo de upload

```text
Usuario envia arquivo
  -> backend valida tipo/tamanho
  -> salva em workspace/uploads
  -> cria metadata
  -> mostra na UI
  -> modelo recebe resumo do anexo
  -> modelo pode pedir leitura via tool
```

## Estrutura sugerida

```text
workspace/
  uploads/
    2026-04/
      image-001.png
      spec.pdf
      logs.txt
  metadata/
    uploads.json
```

## Metadata

```ts
type UploadMetadata = {
  id: string;
  originalName: string;
  path: string;
  mimeType: string;
  sizeBytes: number;
  createdAt: string;
  kind: "text" | "image" | "pdf" | "archive" | "audio" | "binary";
  extractedTextPath?: string;
  thumbnailPath?: string;
};
```

## Arquivos de texto

Para texto, o app pode:

- detectar encoding;
- limitar tamanho;
- dividir em chunks;
- indexar para busca;
- gerar resumo;
- permitir leitura parcial.

Tool sugerida:

```json
{
  "tool": "read_file",
  "args": {
    "path": "workspace/uploads/spec.md",
    "startLine": 1,
    "endLine": 120
  }
}
```

## PDFs

PDFs podem ter texto embutido ou paginas escaneadas.

Pipeline:

```text
PDF
  -> extrair texto se existir
  -> gerar thumbnails das paginas
  -> OCR apenas se necessario
  -> salvar extraido em workspace/cache
```

Nao use OCR como primeira opcao quando texto extraido ja existe.

## Imagens

Imagens podem ser usadas para:

- screenshots de erro;
- UI/design;
- diagramas;
- fotos de quadro;
- comprovantes visuais;
- bugs visuais.

Fluxo:

```text
Imagem enviada
  -> salva no workspace
  -> gera thumbnail
  -> cria metadata
  -> se modelo suportar imagem, envia como input visual
  -> se nao, usa OCR/descricao opcional
```

## Envio ao modelo

Se o modelo aceitar input visual:

```json
{
  "role": "user",
  "content": [
    { "type": "input_text", "text": "Analise esta tela" },
    { "type": "input_image", "image_url": "file-or-signed-url" }
  ]
}
```

Se nao aceitar:

```text
[IMAGE ATTACHMENT]
name: screenshot.png
metadata: 1920x1080, png
caption/OCR: ...
[/IMAGE ATTACHMENT]
```

## ZIP e pastas

ZIPs devem ser tratados com cuidado:

- limitar tamanho descompactado;
- bloquear path traversal;
- recusar arquivos perigosos;
- gerar arvore de arquivos;
- permitir leitura seletiva.

## Segurança de upload

Regras importantes:

- validar tamanho;
- validar MIME e extensao;
- checar magic bytes quando possivel;
- bloquear paths suspeitos;
- nao executar upload automaticamente;
- isolar por usuario/projeto;
- limpar arquivos temporarios;
- nunca expor upload fora do workspace sem token.

## UX recomendada

A UI deve mostrar:

```text
Arquivo: spec.pdf
Tipo: PDF
Tamanho: 1.2 MB
Status: texto extraido
Acoes: abrir, resumir, usar como contexto, remover
```

## Regra de ouro

O upload entra como recurso. O contexto entra sob demanda.

Nao transforme o prompt em mala de viagem de familia brasileira.
