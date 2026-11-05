# Plano de Empresa e Produtos por repositório

Este plano organiza o trabalho como se começássemos do zero, em entregas pequenas e testáveis. Não é necessário apagar ou refazer o código existente: em cada etapa, conferir o que já está implementado e completar o que falta. As caixas registram a implementação e as verificações locais. O aceite com Clerk real permanece separado em P06.

O fluxo é: tela → requisição HTTP → Core → MongoDB → resposta para a tela. O frontend cuida da interação; o backend valida, autoriza e persiste. O frontend não acessa o MongoDB diretamente.

## Regras já decididas

- Empresa corresponde a uma organização da Clerk. Criar a organização e salvar os dados cadastrais no Core são operações distintas.
- Um usuário pode participar de várias empresas; trabalha com uma empresa ativa por vez.
- Empresas e produtos não têm limite de quantidade imposto pelo Core. O limite externo de criação de organizações deve ser configurado na Clerk.
- A cobrança por milhão de logs lidos fica fora desta entrega.
- Cada produto pertence a uma empresa. O Core determina essa empresa pela sessão validada, não por um identificador livre enviado pelo formulário.
- Administradores/donos com papel devidamente mapeado acessam todos os produtos da empresa. Os demais membros acessam apenas os produtos liberados.
- Quem cadastra um produto recebe acesso automaticamente.
- Contextos são opcionais: avisar quando incompletos e permitir salvar.
- Login/logout existentes devem ser preservados.
- Exclusão de empresas/produtos e gestão de permissões de outros membros não estão autorizadas neste escopo.

## Ordem das entregas

| Parte | Repositório | Entrega | Depende de |
| --- | --- | --- | --- |
| P00 | lhamalog_docs | Requisitos, campos e contrato combinado | — |
| P01 | lhamalog-core | API e persistência de Empresa | P00 |
| P02 | lhamalog-web | Tela de Empresa consumindo a API | P01 |
| P03 | lhamalog-core | API de Produtos vinculados à Empresa | P01 |
| P04 | lhamalog-web | Telas de Produtos consumindo a API | P02 e P03 |
| P05 | Core e Web, documentado em Docs | Inicialização integrada com Docker Compose | Configurações dos dois projetos |
| P06 | Todos | Teste completo e documentação final | P02, P04 e P05 |

P05 pode começar após P00 para facilitar os testes das demais partes. A ordem sugerida para desenvolver a funcionalidade é concluir Empresa de ponta a ponta antes de começar Produtos.

## P00 — Definir o contrato

**Repositório:** `C:/GitHub/lhamalog_docs`.

**Objetivo:** frontend e backend trabalharem com os mesmos campos e regras.

- [x] Conferir casos de uso, modelo de classes, ADRs e protótipo.
- [x] Registrar as decisões do usuário e as divergências com documentos antigos.
- [x] Definir campos, tipos, obrigatoriedade, permissões e respostas de erro.
- [x] Referenciar a especificação e o contrato únicos do Core; não copiar versões divergentes para cada repositório.
- [x] Preparar spec, plano, checklist e tarefas conforme o Spec Kit instalado em Core e Web.

**Campos previstos:**

- Empresa: razão social, nome fantasia, documento, data de inscrição, porte e endereço.
- Contexto da empresa: setor, missão, público-alvo, diferenciais e idioma.
- Produto: nome, descrição, categoria e versão.
- Contexto do produto: problema resolvido, público-alvo, diferenciais, palavras-chave e restrições.

Nome do produto é obrigatório. Os contextos são opcionais. Validações adicionais devem vir das fontes ou de uma decisão explícita, não apenas do desenho da tela.

**Pronto quando:** é possível descrever o que a tela envia, o que a API devolve e quem pode executar cada operação, sem dúvidas de negócio bloqueantes.

## P01 — Empresa no Core

**Repositório:** `C:/GitHub/lhamalog-core`.

**Objetivo:** consultar e salvar a empresa ativa com persistência e isolamento.

### Trabalho

- [x] Validar a sessão Clerk e identificar a organização ativa.
- [x] Criar os modelos e documentos de Empresa, contexto e endereço.
- [x] Criar DTOs de entrada e saída. O cliente não pode alterar identificadores de empresa.
- [x] Implementar o serviço com validações e controle de atualização concorrente.
- [x] Implementar persistência no MongoDB.
- [x] Criar os endpoints e documentar exemplos de requisição/resposta.
- [x] Testar autenticação, gravação, consulta, validação e isolamento entre empresas.

### Contrato usado nesta implementação

| Método e rota | Função |
| --- | --- |
| `GET /api/company` | Consultar dados da empresa ativa |
| `PUT /api/company` | Salvar pela primeira vez ou atualizar os dados, contexto e endereço |

O `PUT` recebe `data`, `context`, `address` e `version`. A empresa vem da sessão. Sem dados cadastrados, o `GET` retorna uma estrutura vazia para preenchimento e não grava no banco. Uma versão desatualizada deve gerar conflito, não sobrescrever silenciosamente.

**Atenção ao termo CRUD:** não precisamos criar automaticamente POST, GET, PUT e DELETE para tudo. Aqui, a organização é criada pela Clerk, e o Core salva seus dados complementares. A exclusão não está definida. Se futuramente a criação da própria organização passar pelo Core, isso deve ser uma tarefa específica.

### Como testar antes de ter a tela

Usar um cliente HTTP, como Postman, com um token válido de teste:

1. Consultar a empresa ativa.
2. Salvar os dados e consultar novamente.
3. Conferir que os dados foram gravados no MongoDB.
4. Repetir a consulta com uma sessão de outra empresa e verificar que os dados não aparecem.
5. Testar sem autenticação e com dados inválidos.

**Pronto quando:** a API funciona sem depender do frontend, e os testes de persistência e isolamento passam.

## P02 — Empresa no Web

**Repositório:** `C:/GitHub/lhamalog-web`.

**Objetivo:** permitir que o usuário mantenha a empresa pela interface.

### Trabalho

- [x] Reutilizar login/logout e criação/seleção de organização da Clerk.
- [x] Criar ou completar a rota `/empresa` e sua navegação.
- [x] Criar o serviço HTTP que envia o token para o Core.
- [x] Consumir `GET /api/company` para preencher a tela.
- [x] Montar o formulário com componentes e estilos existentes.
- [x] Consumir `PUT /api/company` ao salvar.
- [x] Exibir carregamento, campos vazios, validação, falha e sucesso.
- [x] Impedir envio duplicado e preservar os valores quando o salvamento falhar.
- [x] Ao trocar de empresa, limpar o formulário anterior e descartar respostas antigas.
- [x] Conferir teclado, celular, build e testes relevantes.

### Como testar

1. Entrar e selecionar uma empresa.
2. Preencher dados e contexto, salvar e recarregar a página.
3. Conferir que os valores continuam preenchidos.
4. Trocar de empresa e verificar o isolamento.
5. Simular falha de conexão e conferir a mensagem e a possibilidade de tentar novamente.

**Pronto quando:** o caminho tela → Core → MongoDB → tela funciona. Dados simulados e localStorage não substituem a persistência no backend.

## P03 — Produtos no Core

**Repositório:** `C:/GitHub/lhamalog-core`.

**Objetivo:** cadastrar e consultar produtos da empresa, mantendo seus contextos e permissões.

### Trabalho

- [x] Criar o modelo Produto com identificador e vínculo à empresa.
- [x] Criar contexto do produto e vínculo de acesso dos membros.
- [x] Criar DTOs e validações; nome do produto não pode ser vazio.
- [x] Derivar a empresa da sessão validada no servidor.
- [x] Implementar cadastro sem cota de quantidade.
- [x] Liberar acesso ao criador automaticamente.
- [x] Filtrar consultas por empresa e permissão de acesso ao produto.
- [x] Implementar atualização de contexto com controle de concorrência.
- [x] Documentar o contrato e testar persistência, permissões e acesso cruzado.

### Endpoints

| Método e rota | Função |
| --- | --- |
| `POST /api/products` | Cadastrar produto na empresa ativa |
| `GET /api/products` | Listar produtos acessíveis da empresa ativa |
| `GET /api/products/{id}` | Consultar um produto acessível |
| `PUT /api/products/{id}/context` | Atualizar seu contexto |

Editar o cadastro geral do produto, excluí-lo, ativá-lo ou suspendê-lo não entra automaticamente por chamarmos a tarefa de CRUD. Essas operações precisam de requisitos próprios.

### Como testar

1. Cadastrar produto como membro da empresa A.
2. Conferir que o criador consegue consultar e editar o contexto.
3. Consultar como outro membro sem vínculo: o produto não deve aparecer.
4. Consultar como administrador da mesma empresa: deve ter acesso.
5. Tentar consultar/alterar o ID usando uma sessão da empresa B: deve ser recusado.
6. Salvar contexto e consultar novamente para confirmar persistência.

**Pronto quando:** produto e empresa permanecem associados corretamente, e conhecer um ID não permite ultrapassar as regras de acesso.

## P04 — Produtos no Web

**Repositório:** `C:/GitHub/lhamalog-web`.

**Objetivo:** listar, cadastrar e manter contexto de produtos pela interface.

### Trabalho

- [x] Criar ou completar a rota `/produtos` e o acesso pela navegação.
- [x] Consumir a lista de produtos acessíveis da empresa ativa.
- [x] Implementar estado vazio, busca e paginação.
- [x] Criar formulário de cadastro usando os campos definidos no contrato.
- [x] Enviar o cadastro para `POST /api/products`.
- [x] Permitir consultar e editar contexto com o endpoint correspondente.
- [x] Atualizar a lista após salvar e exibir confirmação somente depois do sucesso da API.
- [x] Tratar erros e bloquear envios duplicados.
- [x] Limpar dados e formulários quando a empresa ativa mudar.
- [x] Não exibir métricas ou status de integração simulados como se fossem reais.

### Como testar

1. Selecionar empresa e abrir Produtos.
2. Cadastrar um produto e verificar sua presença na lista.
3. Recarregar, buscar pelo nome e editar o contexto.
4. Trocar de empresa e conferir que a lista muda.
5. Testar acesso com outro membro sem vínculo.

**Pronto quando:** cadastro e contexto persistem após recarga, e a tela reflete as permissões aplicadas pelo Core.

## P05 — Subir o ambiente com um comando

**Repositórios:** Core e Web. **Instruções compartilhadas:** Docs.

**Objetivo:** depois da configuração inicial, iniciar o ambiente completo com Docker Compose.

### Divisão

- [x] Core: preparar imagem Docker do Spring, configuração por ambiente e verificação de prontidão.
- [x] Web: conferir/adaptar o Dockerfile existente para iniciar o frontend e receber a configuração da API e da Clerk.
- [x] Integração: escolher e documentar um único Compose que reúna frontend, Core, MongoDB e serviços auxiliares necessários, sem duplicar os mesmos serviços.
- [x] Configurar rede, portas, volumes, dependências e espera pela prontidão do banco.
- [x] Disponibilizar `.env.example` sem segredos e instruções de primeiro uso.
- [x] Validar o comando único de iniciar, os logs e o comando de parar sem apagar dados.

**Endereços precisam ser distintos:** dentro do Docker, o Core acessa o banco pelo nome do serviço, como `mongo:27017`; no navegador, a API precisa ser acessível pela máquina, como `http://localhost:8080`, ou por um proxy configurado. O navegador não resolve o nome interno `core` do Docker.

**Estado atual:** `infra/local/compose.app.yaml` inclui a infraestrutura existente e acrescenta Core e Web. Consulte [execução local](ambiente-local.md). Sem configuração Clerk, os serviços podem subir, mas login e telas autenticadas não funcionam.

**Pronto quando:** seguindo o guia a partir de um ambiente limpo, configurar o `.env` uma vez, executar o Compose e abrir a aplicação é suficiente. A configuração real da Clerk continua necessária para os fluxos autenticados.

## P06 — Aceite e documentação

**Repositórios:** Core, Web e Docs.

- [ ] Executar os testes do Core, incluindo persistência e autorização.
- [ ] Executar testes e build do Web.
- [ ] Testar login/logout e criação/seleção de empresa na instância real da Clerk.
- [ ] Testar Empresa e Produtos pela interface, incluindo recarga, falhas e troca de empresa.
- [ ] Conferir que os dados permanecem após reiniciar os serviços sem apagar volumes.
- [ ] Registrar comandos, resultados e limitações nos artefatos do Spec Kit.
- [ ] Marcar como concluído somente o que foi implementado e verificado.
- [ ] Manter alterações locais, sem commit, push ou PR, enquanto essa for a orientação.

## Como transformar cada parte em uma tarefa

Cada tarefa deve trazer: objetivo, repositório, fontes, escopo, dependências, arquivos/módulos afetados e critérios de aceite.

Exemplo de solicitação para P01:

> No lhamalog-core, implemente a consulta e o salvamento dos dados da empresa ativa conforme a especificação. Reutilize a autenticação, derive a empresa da sessão, persista no MongoDB e documente GET/PUT /api/company. Teste validação, persistência e isolamento. Trabalhe somente no backend; não implemente exclusão. Confira o código existente antes de criar novos arquivos.

Exemplo de solicitação para P02:

> No lhamalog-web, implemente a tela Empresa consumindo o contrato GET/PUT /api/company já validado no Core. Preserve Clerk, reutilize componentes e trate carregamento, vazio, erro, envio e sucesso. Teste salvamento e recarga com backend real. Não altere o contrato da API silenciosamente; se encontrar incompatibilidade, registre o ajuste necessário.

## Aproveitamento do trabalho existente

P01–P04 já possuem implementação local e testes descritos no guia entregue. O próximo trabalho pode ser revisar o aceite dessas partes, concluir P05 e executar as verificações reais pendentes de P06. Este plano não autoriza apagar repositórios ou descartar alterações para “começar do zero”.

Artefatos atuais:

- `C:/GitHub/lhamalog-core/specs/001-empresa-produtos/`: especificação, plano, tarefas, contrato e testes documentados.
- `C:/GitHub/lhamalog-web/specs/001-empresa-produtos/`: plano e tarefas do frontend com referências ao contrato Core.
- `C:/GitHub/lhamalog_docs/entrega-academica/empresa-produtos.md`: fontes e decisões.

## Acompanhamento em 04/11/2026

P00–P04 implementados e verificados localmente conforme os quickstarts. P05 implementado: imagens construídas, serviços iniciados, saúde do Core e HTTP do Web conferidos. Login e aceite funcional continuam pendentes. P06 pendente de aceite com Clerk real, cuja chave foi dispensada pelo usuário nesta etapa. As tarefas detalhadas permanecem nos arquivos specs/001-empresa-produtos/tasks.md de Core e Web.



## Atualização: Clerk local configurada

Em 04/11/2026, o usuário autorizou criar LhamaLog Local e configurar a instância de desenvolvimento no .env ignorado. Tela de login real conferida no localhost:5173. As observações anteriores sobre ausência de chave são históricas; login e operações autenticadas ainda precisam de aceite. Consulte o progresso em lhamalog_docs/planejamento/progresso-empresa-produtos.md.
