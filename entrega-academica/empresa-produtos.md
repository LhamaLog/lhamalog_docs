# Empresa e Produtos E04.F1

Investigação em 04/11/2026. Trabalho local, sem commit, push ou PR.

## Fontes e decisões

A especificação de implementação fica em [spec Core](../../lhamalog-core/specs/001-empresa-produtos/spec.md). O frontend referencia o mesmo contrato, sem duplicar requisitos.

- `LhamaLog casos de uso.xlsx`: USID_021 (dados empresariais), USID_022 (contexto empresarial), USID_023 (contexto do produto), USID_024 (cadastro do produto), USID_029.1 e USID_030.1 (membros e acesso).
- `Modelo de Classes LhamaLog.xlsx`: Empresa, Contexto Empresa, Produto, ContextoProduto, Endereco e ConfiguracaoProduto.
- ADR_022: validação JWT Clerk, assinatura, validade, origem autorizada e sessão pendente.
- ADR_029 e ADR_116: MongoDB como banco Core.
- ADR_122: Tenant vinculado a clerkOrgId; Produto vinculado a tenantId. Substitui o modelo antigo da ADR_033 de tenant por usuário/produto.
- `Figma/figma.svg`: referência visual final, conforme a proveniência em `entrega-academica/figma/README.md`. Componentes existentes no frontend já reproduzem os resumos.
- `LhamaLog_backlog_pendente.docx` de 14/10/2026 não foi localizado. E04.F1 e sua prioridade vêm da transcrição fornecida pelo usuário, não de leitura desse arquivo.

## Esclarecimento do usuário em 04/11/2026

“Pode ser por usuario e gestor mesmo, ele vai pode cadastrar quantos produtos e empresa quiser, o nosso custo e pagamento vai ser por log lido, no caso milhão de log.”

Esta decisão substitui o limite de produtos por plano de USID_024. A feature não implementa faturamento: registra a unidade comercial indicada e não impõe cotas de empresas/produtos. A criação de empresas reutiliza Organizations da Clerk. A instância Clerk precisa permitir criação ilimitada de organizações; isso não é garantido apenas pelo frontend.

O usuário confirmou campos opcionais de contexto do modelo, manutenção do acesso por produto e liberação automática para o criador. A autorização para cadastrar não concede acesso aos demais produtos.

## Divergências encontradas

| Tema | Evidência | Tratamento |
| --- | --- | --- |
| Atores | USID_021–023 dizem Usuário; USID_024 diz gestor com liberação; casos novos usam DONO/ADMIN/MEMBRO | Usuário esclareceu cadastro por usuário/gestor; acesso por produto mantido, com vínculo automático ao criador |
| Contexto geral | USID_022 cita ramo e stack; modelo cita setor, missão, público, diferenciais e idioma; SVG mostra ambientes, janelas e sistemas | Usuário confirmou campos opcionais do modelo |
| Produtos | Modelo cita categoria, versão e status; SVG acrescenta ambiente, área, tags e responsável | Não transformar campos ilustrativos em regras obrigatórias |
| Empresa | Caso cita contato; modelo não o inclui; SVG contém inscrição estadual, logo e informações adicionais | Manter rastreabilidade por campo; não implementar upload e preferências por inferência |
| Pré-condição | USID_021/022 exigem sistema monitorado; cadastro de empresa precede produto no fluxo Clerk | Evitar tornar impossível o primeiro cadastro; esclarecer no contrato final |
| Tenant | ADR_033 antiga conflita com ADR_122 posterior | Usar organização ativa como fronteira de empresa |
| Erros | Hook web existente encerra sessão em todo 403; USID_030.1 exige preservar sessão em recusa de permissão | Tratar 403 de negócio localmente sem alterar login/logout |

## Estado inicial e branches

| Repositório | Base verificada | Branch de trabalho |
| --- | --- | --- |
| lhamalog-web | origin/LLT-162-login-e-logout-na-web-com-clerk, f0c9325 | dev/empresa-produtos |
| lhamalog-core | origin/Development, 41bb92d, mesmo commit de origin/main | dev/empresa-produtos |
| lhamalog_docs | upstream/main, 01140e7, única branch de integração remota disponível | dev/empresa-produtos |

O remote pessoal origin da documentação foi preservado. Foi acrescentado upstream para a organização LhamaLog; ambos apontavam para 01140e7 após fetch. Os três checkouts estavam limpos antes do trabalho. Core e Web foram clonados porque não existiam em C:/GitHub. As instruções desta tarefa autorizam Git local e execução completa, prevalecendo sobre as restrições anteriores do frontend e sobre o nome de branch feature/LLT do Core.

Core só possuía bootstrap, configurações e testes básicos, sem entidades/endpoints de Empresa/Produto ou JWT Clerk. Web possuía login/logout Clerk e composições demonstrativas, sem navegação funcional Empresa/Produtos nem persistência.

## Spec Kit e validação inicial

Core: `.cursor/skills/speckit-*`; Web: `.agents/skills/speckit-*` com preset `lhamalog-local`. Ambos têm scripts PowerShell e templates em `.specify`. Documentação não tem instalação própria do Spec Kit. Constituição Core 2.0.0 efetiva; constituição Web ainda é template, sem princípios preenchidos.

Foram usados create-new-feature, check-prerequisites, setup-plan e setup-tasks existentes, sem reinstalar Spec Kit. O resolvedor Web exigiu PyYAML ausente: dependência instalada em ambiente isolado de trabalho, sem alterar configurações do projeto. O identificador 001-empresa-produtos do Spec Kit não muda a branch Git dev/empresa-produtos.

Build inicial Web aprovado com npm ci e npm run build no Node 24.16.0; projeto declara Node 26.12.1, portanto não equivale à validação na versão prescrita. Maven no Java 8 falhou antes dos testes. Build Core com Java 25 portátil e testes omitidos passou. Docker inicialmente parado. Relatório final de testes será registrado no quickstart da feature.
