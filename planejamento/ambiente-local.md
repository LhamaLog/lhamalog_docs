# Ambiente local de Empresa e Produtos

Mantenha `lhamalog-core` e `lhamalog-web` lado a lado, por exemplo em `C:\GitHub`. Docker Desktop deve estar executando containers Linux. Não é necessário instalar Java, Maven ou Node no host para esta execução.

## 1. Configurar uma vez

Na pasta Core, copie `infra/local/.env.example` para `infra/local/.env` **somente se ainda não existir**. Preencha as senhas e o usuário do OpenObserve. Não versione esse arquivo. Não há senha padrão.

Preencha `MONGODB_URI_DOCKER` com o mesmo usuário, senha e banco do Mongo configurado, usando o host `mongo`, por exemplo `mongodb://USUARIO:SENHA@mongo:27017/lhamalog_core?authSource=lhamalog_core`. Caracteres especiais de usuário/senha precisam de percent-encoding. Use o usuário da aplicação, não root. Alterar a senha no arquivo não modifica automaticamente usuários de um banco já inicializado.

Para login e telas autenticadas, configure também `VITE_CLERK_PUBLISHABLE_KEY`, `CLERK_ISSUER` e `CLERK_JWKS_URI` da mesma instância. Nunca coloque secret key no frontend. Nesta etapa o usuário optou por deixar Clerk sem chave: os serviços sobem, mas a interface autenticada não funciona e a API protegida recusa requisições. Isso não é aceite funcional das telas.

## 2. Iniciar

No PowerShell, dentro de `C:\GitHub\lhamalog-core`:

```powershell
docker compose --env-file infra/local/.env -f infra/local/compose.app.yaml up -d --build
```

O comando compila e inicia Core e Web, reutilizando MongoDB, Redis, OpenObserve e os volumes do Compose anterior. Na primeira vez baixa imagens e dependências. Não execute o Compose separado do Web ao mesmo tempo, pois usa a mesma porta.

- Frontend: http://localhost:5173 (depende da configuração Clerk para funcionar).
- Saúde do Core: http://localhost:8080/actuator/health.
- API: http://localhost:8080/api/company e /api/products, exigindo sessão válida.

O navegador acessa `localhost:8080`; o backend acessa `mongo:27017` e `redis:6379` pela rede Docker. Exportação OTLP fica desligada nesta execução de Empresa/Produtos.

## 3. Conferir e parar

```powershell
docker compose --env-file infra/local/.env -f infra/local/compose.app.yaml ps
docker compose --env-file infra/local/.env -f infra/local/compose.app.yaml logs --tail 80 core frontend
docker compose --env-file infra/local/.env -f infra/local/compose.app.yaml stop
```

`stop` preserva os dados. Para iniciar novamente, use o comando do passo 2. Não use `down -v` para reiniciar: ele apaga os volumes.

Após mudar código, repita `up -d --build`. Para testar regras de Empresa e Produtos automaticamente, consulte os quickstarts em `specs/001-empresa-produtos/` de Core e Web. Compilar a imagem não executa testes; o aceite completo está separado no plano P06.

## Atualização: Clerk local configurada

Em 04/11/2026, o usuário autorizou criar LhamaLog Local e configurar a instância de desenvolvimento no .env ignorado. Tela de login real conferida no localhost:5173. As observações anteriores sobre ausência de chave são históricas; login e operações autenticadas ainda precisam de aceite. Consulte o progresso em lhamalog_docs/planejamento/progresso-empresa-produtos.md.
