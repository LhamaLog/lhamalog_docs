# Conferência com a tarefa

Fontes: [issue 3](https://github.com/endersonmenezes/talks/issues/3) e [avaliação de artefatos](https://github.com/endersonmenezes/talks/blob/main/courses/escola-de-ti/evaluation/evaluation_01_artifacts.md). A tabela resume os itens aplicáveis mencionados pelo professor e o que foi encontrado; não atribui nota nem presume que um arquivo tenha sido aprovado pela equipe.

| Item | Evidência local | Situação |
|---|---|---|
| Especificação de casos de uso | [XLSX](../LhamaLog%20casos%20de%20uso.xlsx), 98 abas | Presente; versões e modelos coexistem |
| Diagrama de casos de uso | Não localizado como diagrama consolidado | Falta exportação/localização |
| DER | ADR 029 cita o artefato; não localizado como diagrama consolidado | Falta exportação/localização |
| Diagrama de classes | [XLSX](../Modelo%20de%20Classes%20LhamaLog.xlsx), 93 abas e 76 imagens | Modelo presente; falta visão consolidada e relações completas |
| Diagrama de atividades | Fotos de quadro e textos não identificam versão final consolidada | Falta exportação/localização |
| Protótipo de alta fidelidade | [SVG final e identificação visual](figma/README.md) | Presente e validado |
| ADRs consolidadas | [Índice de 56 documentos](adrs.md) | Índice concluído; referências e evolução OpenObserve precisam de consolidação pela equipe |
| Backend e frontend | [Estados das branches e PRs](arquitetura-tecnologias-execucao.md) | Código parcial disponível; não equivale a sistema integrado |
| DevOps, quando aplicável | Docker/Compose nos repositórios | Execução local definida; CI/CD não localizada |
| Processo/SDLC justificado em ADR | [Processo observado](processo-desenvolvimento.md) | Há prática e constituição do Collector; falta ADR consolidada da equipe |
| SDD, opcional | Specs no Collector e Finder | Evidência em branches; não presumir adoção uniforme |

O plano de testes de Ocorrências, o RIPD e a análise de monetização estão preservados e relacionados no [inventário](inventario.md). São evidências complementares. Plano de teste não foi classificado como relatório de teste executado.

A tarefa exige artefatos versionados; esta preparação deixa os arquivos prontos para revisão, sem executar o commit proibido pelo solicitante. Para a avaliação, deve-se declarar o estado parcial dos componentes e apontar os commits das branches consultadas. Não se deve apresentar PR aberta como mudança integrada.
