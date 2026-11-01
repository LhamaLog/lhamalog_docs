# Processo real de desenvolvimento

Estado observado em 01/11/2026. [Fontes locais versionadas na origem](fontes-repositorios/README.md). Este relato não é uma ADR da equipe e não cria política de desenvolvimento.

## Evidência observada

| Evidência | O que sustenta |
|---|---|
| Branches `feature/LLT-181-otlp-receiver`, `LLT-132-Consumo-da-fila`, `task/LLT-236-estrutura-de-pastas` | Trabalho por tarefa; convenções diferentes entre módulos |
| Tickets LLT em branches/commits | Vínculo declarado com Jira; o quadro Jira não foi auditado |
| Collector PR 2: 10 commits, 86 arquivos, aberta | Revisão e integração pendentes; comentário automatizado não equivale a aprovação humana |
| Finder PR 1: 2 commits, 40 arquivos, aberta | Conflitos explicitados pelo GitHub; plano de teste da PR sem marcação de conclusão |
| Web PR 1: 23 commits, 99 arquivos, aberta | Componentes reunidos em branch de tarefa; principal ainda sem aplicação |
| Checks 0 nas três PRs consultadas | Nenhum check associado visível nessa consulta |
| Nenhum workflow de GitHub Actions nos recortes baixados | Não foi comprovado pipeline automatizado de CI/CD |
| Docker, Compose, wrappers e scripts | Padronização de execução local, não deploy comprovado |

## Processo declarado no Collector

A [constituição 2.0.0 da PR 2](fontes-repositorios/collector-pr2/.specify/memory/constitution.md.txt), alterada em 31/10/2026, estabelece SpecKit na sequência specify → plan → tasks → implement; revisão e domínio do código pelo desenvolvedor; build e testes locais antes do merge; convenção de branches/commits ligada ao Jira; segredos fora do versionamento. O checklist de merge funciona como critério de conclusão nesse módulo, ainda na branch da PR.

A mesma constituição substitui a regra anterior de percentual de autoria humana por revisão e responsabilidade do desenvolvedor. Isso é uma política declarada no documento, não prova independente de revisão humana efetivamente realizada. Não extrapolar para toda a organização.

## SDD e testes

Collector e Finder possuem especificações e roteiros em `specs/`; são evidências concretas de uso de SDD nessas features. O frontend contém infraestrutura SpecKit, mas sua constituição é um modelo não preenchido. A presença desse modelo não prova governança definida.

O Collector tem testes e seu autor relata resultado positivo no quickstart/PR. O Finder possui testes de domínio, aplicação e adaptador, mas o quickstart informa que a validação com Redis real depende do contrato de nomes. O frontend não declara script de testes. O DOCX de testes de Ocorrências é um plano, sem relatório de execução localizado.

## Lacuna documental para a entrega

Falta uma ADR consolidada e aprovada pela equipe justificando o SDLC comum: fluxo de tarefas, estratégia de branches/revisão, uso ou ausência de CI e critérios de conclusão. A constituição do Collector e as práticas observadas são insumos, não substituição automática dessa decisão. Não foi criada uma ADR fictícia para preencher essa lacuna.
