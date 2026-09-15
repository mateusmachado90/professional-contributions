# MCP Elipse Server - v0.12

(pending translation...)

Prototipo de MCP Server em Python usando transporte HTTP para consultar uma aplicacao Elipse Power aberta no Studio.

## Escopo

- MCP Server Python via HTTP.
- Tools MCP atuais: `get_property(path, property)` e `export_to_xml(...)`.
- O `runner.vbs` agora entende os comandos `GETPROPERTY` e `EXPORTTOXML`.
- Executa o Studio sob demanda com `E3Studio.exe /runscript runner.vbs`.
- O `runner.vbs` le requisicoes em `C:\MCP\requests` e posta respostas em `http://127.0.0.1:8765/internal/result`.
- O Python remove o arquivo de requisicao que criou.
- Timeout padrao: 30 segundos.

## Configuracoes importantes do runner

No topo de `src/mcp_server/runner.vbs` ficam os pontos que podem precisar de ajuste conforme a aplicacao:

```vbscript
Const DEFAULT_SCREEN_PATH = "TelaInicial"
Const EXPORTTOXML_COMPONENT_TYPE = "psl_exportimporttools.ExportToXML"
Const EXPORTTOXML_COMPONENT_PREFIX = "MCP_ExportToXML_"
```

`DEFAULT_SCREEN_PATH` deve apontar para uma tela existente, pois o componente `ExportToXML` precisa ser instanciado em uma tela via `AddObject()`.

Se o identificador aceito pelo `AddObject()` for diferente na aplicacao, ajuste `EXPORTTOXML_COMPONENT_TYPE`.

## Comando GETPROPERTY

Exemplo de requisicao:

```json
{
  "requestId": "req001",
  "command": "GETPROPERTY",
  "path": "Dados.TagInterno1",
  "property": "Value"
}
```

## Comando EXPORTTOXML

Exemplo minimo:

```json
{
  "requestId": "req002",
  "command": "EXPORTTOXML",
  "path": "Subestacao2.Disjuntor3"
}
```

Campos aceitos:

- `path` ou `rootReference`: objeto raiz usado em `RootReference`.
- `screenPath`: tela onde o componente sera instanciado. Se omitido, usa `DEFAULT_SCREEN_PATH`.
- `componentType`: tipo passado ao `AddObject()`. Se omitido, usa `EXPORTTOXML_COMPONENT_TYPE`.
- `componentName`: nome da instancia temporaria. Se omitido, o runner gera um nome a partir do `requestId`.
- `searchFilter`: valor para `SearchFilter`.
- `searchWhitelist`: lista de TypeNames permitidos, separados por `;`. Use normalmente o sufixo
  `!` em cada TypeName para incluir somente a classe informada. Omita `!` apenas quando quiser
  incluir também seus filhos ou tipos descendentes; em caso de dúvida, use `!`.
- `customProperties`: valor para `CustomProperties`.
- `listFiles`: valor para `ListFiles`.
- `msxmlVersion`: valor para `MSXMLVersion`.
- `replaceSpecialCharacters`, `logTimestampOnRoot`, `includeLinkData`, `wrapLinksIntoCDATA`, `includeUserFieldsData`, `includePropertyTypes`, `showProgressWindow`, `enableLog`, `indentOutput`, `useAccurateTypeNames`: booleanos opcionais.

Fluxo do `EXPORTTOXML`:

1. O runner valida `path`/`rootReference` com `Application.GetObject`. Se o objeto nao existir,
   retorna `success=false` sem instanciar o `ExportToXML`.
2. O runner localiza a tela configurada.
3. Instancia o componente com `screen.AddObject(componentType, False, componentName)`.
4. Configura `RootReference`, `OutputToValue=True` e demais propriedades informadas.
5. Executa `expxml.RunEvent "CustomConfig"` no proprio componente.
6. Le `expxml.Value` e devolve o XML no campo `value` da resposta JSON.
7. Tenta remover o componente temporario; se falhar, o Studio ainda fecha descartando alteracoes pendentes.


### export_to_xml

Exporta a arvore de objetos usando o componente `ExportToXML` instanciado pelo runner.

Parametros principais:

```json
{
  "path": "Subestacao1",
  "screen_path": "TelaInicial",
  "search_filter": "",
  "search_whitelist": "",
  "custom_properties": "PathName;Name;ID;Terminal1;Terminal2",
  "show_progress_window": false,
  "indent_output": true,
  "use_accurate_type_names": true
}
```

A resposta segue o mesmo envelope do runner, com o XML no campo `value` quando `success=true`.


## Quick Start

- Abrir um PowerShell no diretório raiz do projeto e executar os seguintes comandos:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install .
python -m mcp_server.mcp_server
```

## Observacoes

- O dominio Elipse deve estar aberto/rodando previamente.
- `OpenDomain` e `CloseDomain` ainda nao estao implementados no runner.
- O `ConfigStepsSequencer` nao e usado nesta versao.
- O runner chama `Application.Exit` ao final para liberar a instancia/licenca do Studio.
- O runner salva copia de debug em `C:\MCP\responses\<requestId>.json`.
- Para `EXPORTTOXML`, tambem configura `ExportToFile` para `C:\MCP\responses\<requestId>.xml`.
