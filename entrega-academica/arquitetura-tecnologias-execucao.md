# Arquitetura, tecnologias e execução

Consulta de 01/11/2026. A análise combina código, manifestos, configurações, documentação e estado das PRs. Não foram instaladas dependências nem executados builds, testes ou serviços. [Evidências locais e commits exatos](fontes-repositorios/README.md).

## Implementação observada

| Repositório | Branch principal | Desenvolvimento ainda não integrado |
|---|---|---|
| [Collector](https://github.com/LhamaLog/lhamalog-collector) | `main`, `f5f50b691ac0`: inicialização Spring, configuração e testes de base | [PR 2](https://github.com/LhamaLog/lhamalog-collector/pull/2), `a9b0a6efbcea`: receptores OTLP HTTP/gRPC para logs e traces, validação Bearer em MongoDB, encaminhamento para OpenObserve |
| [Finder](https://github.com/LhamaLog/lhamalog-finder) | `master`, `1767acabc8da`: configuração, HTTP health check e estrutura de pacotes | [PR 1](https://github.com/LhamaLog/lhamalog-finder/pull/1), `117f9ddf9822`: consumo Redis Streams, reserva/conclusão e deduplicação de códigos; conflitos na PR |
| [Web](https://github.com/LhamaLog/lhamalog-web) | `main`: somente README | [PR 1](https://github.com/LhamaLog/lhamalog-web/pull/1), `519909ad401d`: layout e componentes de ocorrências, produtos e empresa com dados locais |
| [Core](https://github.com/LhamaLog/lhamalog-core) | `main`: somente README no estado acessível | Nenhuma branch adicional ou PR localizada |
| [.github](https://github.com/LhamaLog/.github) | Perfil público e descrição arquitetural | A descrição expressa intenção, não comprova serviços funcionando |

Os cinco repositórios acima eram os acessíveis à conta autenticada. Varredura, Ocorrência e Notificação aparecem na documentação, mas não foram encontrados como repositórios separados nessa listagem. Isso não prova inexistência em outros locais.

## Fluxos e limites

**Collector na PR 2:** produtor → OTLP HTTP/gRPC → token Bearer validado por hash SHA-256 em MongoDB → identificação de produto/tenant → OpenObserve, com destinos de logs e traces. O código possui serviços de aplicação, regras de domínio, adaptadores HTTP/gRPC, autenticação e interfaces de destino. Há cache de validação de token. O sucesso depende do aceite pelo destino. O Redis permanece na infraestrutura local, mas não é destino da ingestão dessa feature. Métricas e gestão de tokens pela aplicação estão fora do ensaio descrito.

**Finder na PR 1:** Redis Streams → adaptador de fila → serviço de consumo → controle de reserva e código concluído. O serviço conclui o passo de fila e disponibiliza códigos; não faz a análise por LLM prevista nas ADRs. O ponto de entrada desta branch é diferente da `master`: o health server da principal não é iniciado pelo `main.go` da PR. Não apresentar as duas branches como uma versão combinada.

**Web na PR 1:** React com estado local e componentes compartilhados. A busca/paginação de ocorrências e interações visuais são demonstráveis no código; diversas ações usam alertas e dados simulados. Não há integração comprovada com APIs, autenticação Clerk ou dados persistidos. A renderização de produtos, configurações, sugestões ou botão de exportação não comprova a funcionalidade de negócio correspondente.

**Arquitetura planejada:** serviços separados, isolamento por tenant/produto, processamento assíncrono, varredura, análise e sugestões por IA, governados por ADRs. O Finder organiza portas e adaptadores; o Collector separa domínio, aplicação e entradas/saídas. Isso não autoriza afirmar que todos os módulos do desenho estejam implementados.

## Tecnologias declaradas nos arquivos

| Componente | Versões e ferramentas encontradas |
|---|---|
| Collector | Java 25, Spring Boot 4.1.1, Maven Wrapper; MongoDB, Redis, OpenTelemetry, OTLP/protobuf/gRPC |
| Infra Collector principal | MongoDB 8.0.34, Redis 8.2.10, Elasticsearch 9.4.5, OpenTelemetry Collector 0.163.1 |
| Infra Collector PR 2 | MongoDB 8.0.34, Redis 8.2.10, OpenObserve 1.1.2, OpenTelemetry Collector 0.163.1 |
| Finder principal | Go 1.27.2, biblioteca padrão, Docker/Compose |
| Finder PR 1 | Diretiva Go 1.27 e go-redis/v9 9.23.0; o quickstart menciona imagem Go 1.27.0 |
| Web PR 1 | Node 26.12.1, React/ReactDOM 19.3.1, TypeScript 7.0.2, Vite 8.3.4, Tailwind CSS 4.3.3 |

**Divergência de armazenamento:** a principal do Collector e a ADR 116 ainda usam Elasticsearch; a PR 2 troca o destino por OpenObserve e cita a ADR 168. Essa ADR não está entre os documentos recebidos. O relatório registra os dois estados, sem resolver a decisão em nome da equipe.

## Execução documentada

Executar somente na cópia de código correspondente à branch/commit indicado, não nesta pasta de documentos. Os comandos abaixo foram conferidos contra os arquivos de origem, mas não executados nesta revisão.

### Collector, principal ou PR 2

Requer JDK 25, Docker e Maven Wrapper. Na PR 2, preparar `infra/local/.env` com `OPENOBSERVE_USERNAME` e `OPENOBSERVE_PASSWORD` conforme o quickstart; não versionar credenciais.

```powershell
docker compose -f infra/local/compose.yaml up -d
.\mvnw.cmd spring-boot:run "-Dspring-boot.run.profiles=local"
.\mvnw.cmd clean verify
```

A principal fornece a base Spring/Actuator, não o receptor da PR. Na PR 2, HTTP OTLP local é `http://localhost:4328/v1/logs` e `/v1/traces`; gRPC local usa 4327. As portas 4317/4318 do Compose são do coletor de observabilidade. O ensaio de ingestão exige preparar produto e token de teste em MongoDB conforme o [quickstart exportado](fontes-repositorios/collector-pr2/specs/001-otlp-receiver/quickstart.md.txt). A documentação da PR declara execução anterior dos testes pelo autor; esta revisão não reproduziu esse resultado.

### Finder principal

Requer a versão Go declarada no `go.mod`.

```powershell
go run ./cmd/finder
go test ./...
```

O health server usa a porta 8085 por padrão, configurável por `FINDER_HTTP_ADDR`. Essa base não consome a fila. O alvo de testes do Makefile também usa detector de concorrência (`go test -race ./...`).

### Finder PR 1

Requer Redis acessível e nomes reais definidos pela equipe para `FINDER_REDIS_STREAM`, `FINDER_REDIS_GROUP` e `FINDER_REDIS_CODIGOS`. `FINDER_REDIS_ADDR` usa `localhost:6379` por padrão. Não foram inventados nomes para permitir uma execução aparente. Após configuração, o ponto de entrada é `go run ./cmd/finder`; a suíte existe no código. O [quickstart exportado](fontes-repositorios/finder-pr1/specs/001-consumir-fila-redis/quickstart.md.txt) ainda declara bloqueio do ensaio com Redis real. A PR também tem conflitos em `Makefile`, `cmd/finder/main.go` e `go.mod`.

### Web PR 1

Requer Node 26.12.1 declarado pelo projeto.

```powershell
npm ci
npm run dev
npm run build
```

Desenvolvimento em `http://localhost:5173`. Alternativa local documentada: `docker compose -f infra/local/compose.yaml up -d --build`. O build verifica TypeScript e gera o bundle Vite. Não há script `test` no `package.json`; não foi considerada existente uma suíte de frontend. O Compose é de desenvolvimento, não evidência de implantação de produção.

## Escopo planejado que não deve aparecer como concluído

Autenticação e gestão via Core, integração ponta a ponta, LLM, recorrência vetorial, notificações e varredura não foram comprovadas como fluxo integrado nos recortes consultados. A ADR 166 adia PDF/Excel, varredura avançada e link externo de LLM; a ADR 025 mantém mobile em standby. Não transformar esses adiamentos em promessas de entrega implementada.
