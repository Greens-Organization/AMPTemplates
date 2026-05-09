# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## O que é este repositório

Coleção comunitária de templates do Generic Module para o AMP (Application Management Panel) da CubeCoders. Cada template define como o AMP instala, configura e executa um servidor de jogo ou aplicação.

**IMPORTANTE:** Configurações geradas por IA não são aceitas neste repositório.

## Estrutura dos Templates

Todos os arquivos ficam no diretório raiz (sem subdiretórios). Cada aplicação usa o mesmo prefixo em minúsculas sem espaços:

| Arquivo | Obrigatório | Descrição |
|---|---|---|
| `{app}.kvp` | Sim | Configuração raiz (metadados + app) |
| `{app}config.json` | Sim | Manifesto de settings (UI do AMP) |
| `{app}metaconfig.json` | Não | Mapeamento de arquivos de config |
| `{app}ports.json` | Não | Definições de portas (referenciado via `@IncludeJson`) |
| `{app}updates.json` | Não | Fontes de atualização (referenciado via `@IncludeJson`) |

Exemplo: `valheim.kvp`, `valheimconfig.json`, `valheimmetaconfig.json`

## Estrutura do arquivo .kvp

Formato `Chave=Valor`, organizado em seções por prefixo:

- **`Meta.*`** — metadados (DisplayName, OS, Arch, Author, MinAMPVersion, ConfigManifest, etc.)
- **`App.*`** — configuração da aplicação (ExecutableWin, ExecutableLinux, CommandLineArgs, Ports, UpdateSources, etc.)
- **`Console.*`** — regex para parsing do console (AppReadyRegex, UserJoinRegex, UserLeaveRegex, etc.)
- **`Limits.*`** — configurações de sleep/doze

Campos JSON inline (Ports, UpdateSources) são extraídos para arquivos separados e referenciados com `@IncludeJson[arquivo.json]`.

## Estrutura do arquivo config.json

Array de objetos, cada um representando um setting na UI do AMP:

```json
{
  "DisplayName": "Nome visível",
  "Category": "Categoria:icone_material",
  "Subcategory": "Sub:icone:ordem",
  "Description": "Descrição para o usuário",
  "Keywords": "palavras,chave,para,busca",
  "FieldName": "NomeDoCampo",
  "InputType": "text|number|checkbox|select|...",
  "ParamFieldName": "CampoNoArquivoDeConfig",
  "DefaultValue": "valor_padrão",
  "EnumValues": {}
}
```

## Utilitário

`utilities/MassEdit.ps1` — PowerShell script que processa arquivos `.kvp` em lote: extrai `App.Ports` e `App.UpdateSources` inline para arquivos `*ports.json` / `*updates.json` separados e adiciona `Meta.ConfigVersion` se ausente.

## Restrições para contribuição

- Aplicação não pode exigir login/autenticação (exceto SteamCMD)
- Deve ter versão Linux ou suporte via Proton + SteamCMD
- Settings customizáveis devem usar Settings Manifest (config.json)
- Deve expor um console que o AMP consiga capturar
- Somente executáveis — não invocar scripts shell/batch
- Templates gerados pelo configurador online não são aceitos
- **Configurações geradas por IA não são aceitas**

## Testes

Não há testes locais. Após o PR ser submetido, uma ferramenta automatizada testa: load, update, start, ready state, stop — em Windows e Linux.
