# LhamaLog Web

Frontend do LhamaLog usando TypeScript, React e Tailwind CSS. A base foi iniciada na [LLT-236](https://logviewer2026.atlassian.net/browse/LLT-236); o desenvolvimento atual inclui componentes reutilizáveis e a composição visual de Produtos, relacionados ao planejamento da LLT-237. O Vite inicia o servidor de desenvolvimento, processa os estilos e gera o build.

## Estado atual

A aplicação abre uma página de demonstração chamada **Componentes LhamaLog**, com exemplos dos componentes e dados ilustrativos. Estão implementados:

- Campos, botões, checkbox, badges para status e tags, avatar, busca, cartões, paginação e barra de progresso.
- Navbar com recolhimento no desktop e menu em telas pequenas, Header e Footer compostos pelo AppLayout.
- Cartões e lista de produtos, painéis de resumo, ambientes e logs, indicadores e composição responsiva de Produtos.
- Cartões e lista de ocorrências com severidade, tags, data e projeto; busca local, paginação, métricas, intensidade, ranking e composição responsiva de Ocorrências.
- Cabeçalho de Empresa, resumo cadastral com logo e verificação, conclusão do perfil e cartões de dados, contexto de atuação, endereço e preferências, com dados fictícios e ações de edição demonstrativas. O mapa do endereço é ilustrativo, sem geolocalização; as preferências não aplicam configurações ao sistema.
- Componentes compartilhados de métricas, ranking, minigráficos e barras de distribuição.
- Bloco de governança de Empresa com ações demonstrativas de exportação de PDF e sincronização, sem executar essas operações.
- Composição responsiva de Empresa com navegação por links internos para os cartões e rodapé.
- Estados de carregamento, lista vazia, busca sem resultados e falha com ação de tentar novamente.

A seleção de itens na Navbar apenas troca o destaque; ainda não há navegação entre páginas. As buscas de Produtos recebem texto, mas não filtram os produtos. Na composição de Ocorrências, a busca filtra os exemplos locais por título, descrição, projeto e tags. Filtros, cadastro, detalhes, seletores de produto/período e notificações mostram avisos de demonstração. A paginação troca os itens locais exibidos. A recuperação de falha usa um temporizador local, sem requisição ao backend.

As composições visuais de Ocorrências e Empresa estão implementadas com dados ilustrativos. O gráfico de intensidade usa amostras fictícias; seus intervalos reais e as fórmulas das métricas ainda dependem de definição. Em Empresa, a navegação interna mantém os cartões visíveis e leva ao bloco selecionado, com foco e destaque do destino. Autenticação, integração com Clerk, acesso ao backend, formulários e regras de negócio permanecem pendentes de suas respectivas etapas e definições. A implementação visual não representa o aceite das tarefas pela equipe.

O detalhamento das entregas e das definições pendentes está em [docs/planejamento-componentes.md](docs/planejamento-componentes.md). As referências visuais em SVG ficam em [docs/referencias](docs/referencias).

## Pré-requisitos

- Node.js 26.12.1, com npm, para execução e build locais.
- Docker Desktop com contêineres Linux, para execução pelo Docker.
- GNU Make (opcional), para utilizar os atalhos do Makefile. Os comandos diretos continuam disponíveis sem ele.

## Executar localmente

Instale o [Node.js](https://nodejs.org/en/download), versão 26.12.1. O npm acompanha a instalação. A versão de referência está em `.node-version` e também está declarada em `package.json`.

Na pasta do projeto:

```powershell
npm ci
npm run dev
```

Abra [http://localhost:5173](http://localhost:5173). A página deve mostrar **Componentes LhamaLog**, com Navbar, Header e exemplos de interface. Para parar o servidor, use `Ctrl+C`. A porta é fixa: se estiver ocupada, o servidor informa o erro em vez de escolher outra porta.

`npm run build` verifica os tipos e gera os arquivos em `dist`. `npm run preview` permite conferir esse build localmente.

## Ambiente local (infra/local)

A configuração Docker do frontend fica em `infra/local/`, seguindo a organização do coletor:

```text
infra/local/
  Dockerfile
  compose.yaml
```

Execute os comandos na raiz do repositório. Com GNU Make instalado, use `make up` (ou `make infra-up`), `make infra-ps`, `make infra-logs` e `make infra-down`; os equivalentes diretos estão abaixo e na tabela de comandos.

Instale e inicie o [Docker Desktop](https://docs.docker.com/desktop/setup/install/windows-install/), usando contêineres Linux. No Windows, siga os requisitos de WSL 2 da instalação. Confirme com `docker version`: as informações de Client e Server devem aparecer.

Para esta opção, não é necessário instalar Node.js ou executar `npm ci` na máquina: a imagem fornece Node.js 26.12.1 e instala as dependências dentro do contêiner.

Com o Docker Desktop iniciado, basta executar `make up`. Esse comando constrói a imagem, executando `npm ci` no Dockerfile, e inicia o frontend em segundo plano. As dependências vêm do `package-lock.json`; a etapa de instalação pode reutilizar o cache quando os arquivos de dependências não mudaram. Não é necessário executar `npm install` manualmente.

A versão exata do Node fica em `build.args.NODE_VERSION` no `infra/local/compose.yaml`, sem usar `latest`. O Dockerfile recebe esse argumento para selecionar a imagem oficial `node:<versão>-bookworm-slim`, sem definir uma versão padrão. O npm é o incluído nessa imagem. Ao atualizar o Node, mantenha `.node-version` e a declaração `engines.node` de `package.json` e `package-lock.json` alinhados com o YAML.

Na pasta do projeto, construa a imagem e inicie o ambiente em segundo plano:

```powershell
docker compose -f infra/local/compose.yaml up -d --build
```

Para acompanhar os logs:

```powershell
docker compose -f infra/local/compose.yaml logs -f
```

Abra [http://localhost:5173](http://localhost:5173). A página deve mostrar a mesma demonstração **Componentes LhamaLog** da execução local. A porta 5173 da máquina aponta para a porta 5173 do contêiner. Execute localmente, pelo Docker ou pelo preview, um de cada vez, pois todos usam a mesma porta.

Use `Ctrl+C` para sair da visualização dos logs; o ambiente continua funcionando. Para parar e remover os contêineres e a rede deste projeto:

```powershell
docker compose -f infra/local/compose.yaml down
```

A imagem permanece disponível depois de encerrar o ambiente. Como o código é copiado para a imagem, depois de alterar arquivos execute novamente `docker compose -f infra/local/compose.yaml up -d --build`; o Compose reconstrói a imagem e atualiza o contêiner quando necessário. A primeira construção precisa de acesso à internet para baixar a imagem e as dependências.

Esta configuração executa o servidor de desenvolvimento do Vite. Não é uma configuração de publicação em produção.

- `infra/local/Dockerfile`: prepara o ambiente Node.js, instala as dependências como usuário `node` e define a inicialização.
- `.dockerignore` (na raiz): evita copiar Git, dependências locais, build, configurações privadas e logs para a imagem. Permanece na raiz porque ela é o contexto do build.
- `infra/local/compose.yaml`: define o serviço frontend, o build e o mapeamento da porta 5173, evitando repetir essas opções no terminal.

## Instalando o make

Os atalhos `make` abaixo precisam do GNU Make instalado na máquina. Ele é opcional e não é instalado pelo Docker deste projeto. Se `make --version` já funcionar, não é necessário instalar novamente.

Windows (PowerShell):

```powershell
winget install ezwinports.make
# Alternativa, se já utiliza Chocolatey: choco install make
```

Depois feche e abra o terminal e confira com `make --version`.

Linux:

```bash
# Debian/Ubuntu, inclusive no WSL
sudo apt update && sudo apt install -y make
# Fedora
sudo dnf install -y make
# Arch
sudo pacman -S make
make --version
```

Execute somente o comando correspondente à sua distribuição.

macOS: o Make pode ser instalado com as ferramentas de linha de comando da Apple:

```bash
xcode-select --install
make --version
```

Sem Make no Windows, use os equivalentes diretos, na pasta do projeto:

```powershell
docker compose -f infra/local/compose.yaml up -d --build
docker compose -f infra/local/compose.yaml logs -f
docker compose -f infra/local/compose.yaml down
npm run build
```

Esses comandos são alternativas independentes: execute a ação desejada, não o bloco inteiro em sequência.

## Comandos

| Ação | Sem make | Makefile |
| --- | --- | --- |
| Rodar localmente | `npm run dev` | `make run` |
| Gerar o build local | `npm run build` | `make build` |
| Subir o frontend pelo Docker | `docker compose -f infra/local/compose.yaml up -d --build` | `make up` ou `make infra-up` |
| Parar o ambiente Docker | `docker compose -f infra/local/compose.yaml down` | `make infra-down` |
| Status dos contêineres | `docker compose -f infra/local/compose.yaml ps` | `make infra-ps` |
| Acompanhar os logs | `docker compose -f infra/local/compose.yaml logs -f` | `make infra-logs` |
| Validar o Compose | `docker compose -f infra/local/compose.yaml config -q` | `make infra-config` |

`make run` e `make build` executam fora do Docker: requerem Node.js e a instalação local das dependências com `npm ci`. Os comandos `infra-*` operam o ambiente Docker; `infra-config` apenas valida a configuração e não inicia contêineres. O Makefile escolhe `npm.cmd` no Windows e `npm` no Linux/macOS automaticamente.

Ao interromper `make infra-logs` com `Ctrl+C`, o Make pode exibir `Error 130`, indicando a interrupção da visualização. O frontend continua rodando até executar `make infra-down`.

## Entrada da aplicação

- `index.html`: contém o elemento onde o React é montado e carrega `src/main.tsx`.
- `src/main.tsx`: inicia o React e renderiza `App`.
- `src/styles.css`: importa o Tailwind; é carregado pelo `main.tsx`.
- `src/app/App.tsx`: compõe a página de demonstração, os dados ilustrativos e os estados locais dos exemplos.
- `src/app/layouts/AppLayout.tsx`: reúne Navbar, Header, área de conteúdo e Footer; controla o recolhimento e a abertura do menu móvel.
- `package.json` e `package-lock.json`: dependências, comandos e versões de instalação.
- `tsconfig.json` e `vite.config.ts`: configuração do TypeScript e do Vite.

O Tailwind está integrado ao Vite pelo plugin `@tailwindcss/vite` e é importado em `src/styles.css`. Os componentes usam classes utilitárias para aparência, estados e responsividade. No Docker, as dependências são instaladas pelo mesmo `npm ci`; depois de mudanças nas dependências, reconstrua a imagem antes de iniciar o contêiner.

## Estrutura atual

```text
docs/
  planejamento-componentes.md
  referencias/
infra/
  local/
    Dockerfile
    compose.yaml
src/
  main.tsx
  styles.css
  app/
    App.tsx
    layouts/
      AppLayout.tsx
      Navbar.tsx
      Header.tsx
      HeaderControls.tsx
      Footer.tsx
  features/
    products/
      components/
    occurrences/
      components/
    company/
      components/
  shared/
    assets/
    ui/
```

## Responsabilidades

- `app/layouts`: estrutura visual compartilhada entre páginas.
- `app/layouts/HeaderControls.tsx`: controles específicos do Header, reunindo estado do sistema, período, notificações e identificação do usuário.
- `features`: funcionalidades organizadas por domínio; componentes, hooks, serviços e tipos específicos ficam junto da funcionalidade que os utiliza.
- `shared/ui`: componentes visuais reutilizáveis entre funcionalidades.
- `shared/assets`: recursos visuais compartilhados, como o logo.
- `docs/referencias`: referências visuais usadas para implementar as interfaces em React.

`app/providers`, `app/routes`, `shared/hooks`, `shared/services` e `shared/types` são diretórios previstos para quando houver código que precise deles. Podem existir vazios localmente, mas não fazem parte da estrutura implementada.

## Organização

A organização é por funcionalidade, separando apresentação, lógica e acesso a dados conforme o código for desenvolvido. `app` compõe as funcionalidades; `shared` não depende de `app` ou de funcionalidades específicas. Uma funcionalidade não importa arquivos de `app`.

As subdivisões de cada funcionalidade serão criadas quando necessárias. Não há exigência de classes ou de um ViewModel para cada componente.

Os componentes usam exportações nomeadas e importações relativas, sem extensão `.tsx`. Exemplo:

```tsx
import { Button } from '../shared/ui/Button'
```

`shared/ui` recebe dados e eventos; regras de negócio e acesso a dados pertencem às funcionalidades. Os dados ilustrativos e as interações locais atuais ficam em `App.tsx`.

## Conferência manual

Depois de iniciar a aplicação:

1. Confira os exemplos de campos, botões e indicadores, incluindo foco pelo teclado e controles desabilitados.
2. No desktop, use o botão de três linhas para recolher e expandir a Navbar. O logo leva ao início da página.
3. Em uma janela menor que 768 px, use **Abrir menu**. Selecionar um item fecha o menu; Escape também fecha quando o foco está dentro dele.
4. Nas composições de Produtos e Ocorrências, troque as páginas e redimensione a janela para conferir o empilhamento dos painéis. Em Ocorrências, busque por `deploy` e depois por um termo inexistente para conferir a busca local e o estado sem resultados.
5. Em **Estados compartilhados**, confira carregamento, lista vazia, busca sem resultados e falha. **Tentar novamente** exibe dados ilustrativos após 1,2 segundo.
6. Execute `npm run build` para verificar os tipos e gerar o build.
7. Em **Composição de Empresa**, use os links internos por clique ou teclado. Confira o bloco de destino, o destaque ao voltar pelo navegador e o empilhamento dos cartões no celular. As seis ações dos cartões e da governança mostram apenas avisos.

O build não substitui a conferência visual e de comportamento. Não há comando de testes automatizados configurado em `package.json` neste momento.

