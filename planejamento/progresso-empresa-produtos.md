# Progresso de Empresa e Produtos — 04/11/2026

## Ponto de retomada

Core, Web e Docs estão na branch `dev/empresa-produtos`. P00–P04 têm implementação e verificações locais; P05 tem inicialização integrada validada. Próxima etapa: P06, aceite autenticado de Empresa e Produtos e conclusão da suíte completa do Core.

## Entregue

- Core: consulta/salvamento de Empresa; cadastro, lista, consulta e edição de contexto de Produto; MongoDB; validação JWT Clerk; isolamento por empresa e acesso por produto; acesso automático ao criador; controle de concorrência e erros.
- Web: páginas Empresa e Produtos consumindo a API, seleção de organização, formulários, busca/paginação e estados de carregamento/erro/sucesso. Login/logout existentes preservados.
- Docker: `infra/local/compose.app.yaml` no Core reúne Spring, Web, MongoDB, Redis e OpenObserve, reutilizando volumes. Java e Node não precisam rodar manualmente no host.
- Documentação: plano P00–P06, guia local, fontes/decisões e artefatos Spec Kit nos dois repositórios de código.
- Clerk: aplicação de desenvolvimento **LhamaLog Local** criada com autorização do usuário, organizações habilitadas, e-mail e Google. Chave publicável, issuer e JWKS configurados somente no `.env` local ignorado do Core. Nenhuma secret key necessária nesta implementação.

## Evidências e limites

- Verificação anterior dos testes novos do Core: 13 aprovados, incluindo 4 com MongoDB real em banco isolado.
- Web: 5 testes aprovados, também executados no container; build aprovado anteriormente.
- Interface com identidade de teste isolada: salvar/recarregar empresa e produto, contexto, troca de empresa, prevenção de envio duplicado e recuperação de erro. Isso não substitui aceite com Clerk real.
- Docker: imagens construídas, Core saudável (`UP`), API sem token recusada (`401`), Web HTTP `200`; stop/up executados sem remover volumes.
- Com a configuração real da Clerk, a tela de login local foi aberta e conferida. Ainda não foram verificados cadastro/login real de usuário, criação de organização e operações autenticadas de Empresa/Produtos nessa nova instância.
- Suíte completa do Core: tentativa com Docker interrompida durante download de `grafana/otel-lgtm:0.37.0`; permanece pendente. Ao repetir, configurar `TEST_MONGODB_URI` para um banco isolado para não omitir os testes novos de persistência.

## Regras confirmadas

Usuários/gestores podem cadastrar empresas e produtos sem cota do Core. Contextos são opcionais. Preservar acesso por produto e isolamento entre empresas; liberar automaticamente o produto ao criador. Cobrança por milhão de logs lidos fica fora desta entrega. Limites externos de organizações da Clerk não são limites implementados pelo Core.

## Como continuar

1. Abrir Docker Desktop e, na raiz do Core, executar `docker compose --env-file infra/local/.env -f infra/local/compose.app.yaml up -d --build`.
2. Abrir http://localhost:5173 e cadastrar/entrar com um usuário na nova instância Clerk.
3. Criar/selecionar empresa, salvar dados, cadastrar produto e editar contexto. Recarregar e conferir persistência.
4. Verificar troca de empresa e acesso de outro membro; reiniciar serviços e conferir cadastros persistidos.
5. Concluir os testes pendentes e atualizar os quickstarts e checklists com resultados reais.

## Arquivos de referência

- [Plano por repositório](empresa-produtos.md)
- [Execução local](ambiente-local.md)
- Core: `specs/001-empresa-produtos/spec.md`, `contracts/api.md`, `tasks.md`, `quickstart.md`.
- Web: `specs/001-empresa-produtos/tasks.md` e `quickstart.md`.

## Versionamento

Usuário autorizou commits locais nos três repositórios. Push e publicação não foram solicitados. Não incluir `.env`, credenciais, dados de banco, imagens compiladas ou arquivos temporários. A mudança local de `Figma/figma.svg` para `Figma/figma.txt`, encontrada ao preparar os commits, fica fora desta entrega por não ter sido feita durante a implementação.
