# LhamaLog — entrega acadêmica

Organização dos artefatos em 01/11/2026. Os originais foram preservados. Esta preparação **não realizou commit, push ou publicação**. O SVG informado pelo usuário é a referência final de design; telas e documentos não comprovam, por si, funcionalidades implementadas.

## Índice

| Conteúdo | Arquivo local |
|---|---|
| Inventário completo e originais | [Inventário](entrega-academica/inventario.md) |
| Conferência da tarefa | [Matriz de entrega](entrega-academica/comparacao-tarefa.md) |
| Casos de uso | [Planilha original](LhamaLog%20casos%20de%20uso.xlsx) |
| Modelo de classes | [Planilha original](Modelo%20de%20Classes%20LhamaLog.xlsx) |
| Índice das abas | [Planilhas](entrega-academica/planilhas.md) |
| Decisões arquiteturais | [Índice das 56 ADRs](entrega-academica/adrs.md) |
| Protótipo final exportado | [SVG completo](Figma/figma.svg) · [Telas e proveniência](entrega-academica/figma/README.md) |
| Arquitetura, tecnologias e execução | [Relatório técnico](entrega-academica/arquitetura-tecnologias-execucao.md) |
| Processo real de desenvolvimento | [Processo observado](entrega-academica/processo-desenvolvimento.md) |
| Evidências dos repositórios | [Fontes locais e commits consultados](entrega-academica/fontes-repositorios/README.md) |
| Integridade e abertura dos arquivos | [Validação](entrega-academica/validacao.md) |
| Bloco para envio na issue | [YAML da entrega](submissao.yaml) |

Os diretórios `Design/`, `Documentações/` e `Figma/`, as planilhas e o ZIP foram mantidos nos caminhos recebidos. O conteúdo organizado está em `entrega-academica/`. O ZIP de aproximadamente 1,41 GB é uma cópia de origem, com um vídeo adicional; não é necessário duplicá-lo na entrega dos documentos já extraídos.

## Situação do software

O Collector tem base Spring Boot na `main` e ingestão OTLP implementada na PR 2 ainda aberta. O Finder tem base e health check na `master` e consumo Redis na PR 1 aberta, com conflitos. O frontend está na PR 1 aberta e usa dados locais; sua `main` tem somente README. O Core acessível tem somente README. Não há evidência de aplicação completa integrada ou publicada.

## Fontes da entrega

- [Tarefa do professor — issue 3](https://github.com/endersonmenezes/talks/issues/3).
- [Orientações da Escola de TI](https://github.com/endersonmenezes/talks/tree/main/courses/escola-de-ti).
- [Organização LhamaLog](https://github.com/LhamaLog).
- [Figma original](https://www.figma.com/design/e88SaMez3hQZ0nmmC3uEtm/LhamaLog?node-id=765-1426).

A tarefa informa o último commit válido até **02/11/2026 às 09:00**; o fuso não está explicitado no texto consultado. A submissão deve ser feita pelo representante, com equipe, turma e repositórios dos artefatos. Os arquivos locais complementam os links; a etapa de versionamento e envio continua sob responsabilidade da equipe.

## Desenvolvimento de Empresa e Produtos

[Plano P00–P06 e andamento](planejamento/empresa-produtos.md) · [Ambiente local integrado](planejamento/ambiente-local.md). O estado acima descreve a entrega acadêmica anterior; o plano registra a implementação local atual, ainda sem publicação.

[Progresso atual e ponto de retomada](planejamento/progresso-empresa-produtos.md)
