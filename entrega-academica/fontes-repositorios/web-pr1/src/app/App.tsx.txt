import { useEffect, useState } from 'react'
import type { ComponentProps } from 'react'
import { Input } from '../shared/ui/Input'
import { PasswordInput } from '../shared/ui/PasswordInput'
import { Checkbox } from '../shared/ui/Checkbox'
import { Button } from '../shared/ui/Button'
import { IconButton } from '../shared/ui/IconButton'
import { Badge } from '../shared/ui/Badge'
import { Avatar } from '../shared/ui/Avatar'
import { SearchInput } from '../shared/ui/SearchInput'
import { Card } from '../shared/ui/Card'
import { PageHeader } from '../shared/ui/PageHeader'
import { FilterButton } from '../shared/ui/FilterButton'
import { Pagination } from '../shared/ui/Pagination'
import { ProgressBar } from '../shared/ui/ProgressBar'
import { Breadcrumbs } from '../shared/ui/Breadcrumbs'
import { SystemStatus, PeriodSelector, NotificationButton, UserIdentity } from './layouts/HeaderControls'
import { AppLayout } from './layouts/AppLayout'
import { ProductsHeader } from '../features/products/components/ProductsHeader'
import { ProductCard } from '../features/products/components/ProductCard'
import { ProductList } from '../features/products/components/ProductList'
import type { ProductListItem } from '../features/products/components/ProductList'
import { MetricCard } from '../shared/ui/MetricCard'
import { ProductsSummary } from '../features/products/components/ProductsSummary'
import { DistributionBars } from '../shared/ui/DistributionBars'
import { ProductsByEnvironment } from '../features/products/components/ProductsByEnvironment'
import { LogsByProduct } from '../features/products/components/LogsByProduct'
import { ProductsView } from '../features/products/components/ProductsView'
import { Sparkline } from '../shared/ui/Sparkline'
import { LoadingState } from '../shared/ui/LoadingState'
import { EmptyState } from '../shared/ui/EmptyState'
import { ErrorState } from '../shared/ui/ErrorState'
import { OccurrencesHeader } from '../features/occurrences/components/OccurrencesHeader'
import { SeverityIndicator } from '../features/occurrences/components/SeverityIndicator'
import { OccurrencesView } from '../features/occurrences/components/OccurrencesView'
import type { OccurrenceItem } from '../features/occurrences/components/OccurrenceCard'
import { CompanySummary } from '../features/company/components/CompanySummary'
import { CompanyDataCard, CompanyContextCard, CompanyPreferencesCard } from '../features/company/components/CompanyInfoCards'
import { CompanyAddressCard } from '../features/company/components/CompanyAddressCard'
import { CompanyGovernance } from '../features/company/components/CompanyGovernance'
import { CompanyView } from '../features/company/components/CompanyView'
import { Footer } from './layouts/Footer'
import companySymbol from '../shared/assets/lhamalog-symbol.svg'

const demoListIcon = (
  <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="1.5" strokeLinejoin="round" aria-hidden="true">
    <path d="m12 3 9 5v8l-9 5-9-5V8ZM3 8l9 5 9-5M12 13v8" />
  </svg>
)

const demoProducts: ProductListItem[] = [
  {
    id: 'demo-platform',
    name: 'Plataforma Web',
    description: 'Portal do cliente e área logada',
    icon: demoListIcon,
    status: 'connected',
    categories: ['Aplicação web'],
    area: 'Comercial',
    lastLog: { label: 'há 2 min (exemplo)', date: '22 de out. de 2026' },
    environment: 'production',
  },
  {
    id: 'demo-payments',
    name: 'API de Pagamentos',
    description: 'Processamento de cobranças e estornos',
    icon: demoListIcon,
    iconColor: 'green',
    status: 'connected',
    categories: ['API / Serviço'],
    area: 'Financeiro',
    lastLog: { label: 'há 5 min (exemplo)', date: '22 de out. de 2026' },
    environment: 'production',
  },
  {
    id: 'demo-mobile',
    name: 'App Mobile',
    description: 'Aplicativo Android e iOS',
    icon: demoListIcon,
    iconColor: 'purple',
    status: 'waiting',
    categories: ['Aplicativo'],
    area: 'Comercial',
    lastLog: null,
    environment: 'staging',
  },
  {
    id: 'demo-warehouse',
    name: 'Data Warehouse',
    description: 'Carga e consolidação de dados',
    icon: demoListIcon,
    status: 'none',
    categories: ['Job / Batch'],
    area: 'Dados',
    lastLog: null,
    environment: 'development',
  },
  {
    id: 'demo-backoffice',
    name: 'Backoffice Financeiro',
    description: 'Conciliação e relatórios internos',
    icon: demoListIcon,
    status: 'error',
    categories: ['Aplicação web'],
    area: 'Financeiro',
    lastLog: { label: 'há 3 dias (exemplo)', date: '19 de out. de 2026' },
    environment: 'production',
  },
]

const demoLogRanking = [
  { id: 'demo-platform', name: 'Plataforma Web', logs: 9800, formattedValue: '9,8 mil' },
  { id: 'demo-payments', name: 'API de Pagamentos', logs: 12400, formattedValue: '12,4 mil' },
  { id: 'demo-mobile', name: 'App Mobile', logs: 5100, formattedValue: '5,1 mil' },
  { id: 'demo-backoffice', name: 'Backoffice Financeiro', logs: 3200, formattedValue: '3,2 mil' },
  { id: 'demo-warehouse', name: 'Data Warehouse', logs: 0, formattedValue: '0' },
]

const occurrenceExamples: (Omit<OccurrenceItem, 'id' | 'dateTime' | 'dateLabel' | 'timeLabel'> & { time: string })[] = [
  {
    severity: 'error', title: 'Falha na autenticação de token', time: '14:12',
    description: 'Token inválido para o usuário 8342. Verificar configuração do provider.',
    tags: ['api-gateway', 'autenticação'], project: 'Todos os projetos',
  },
  {
    severity: 'warning', title: 'Latência acima do esperado', time: '13:45',
    description: 'Tempo de resposta médio acima de 2 segundos nos últimos 5 minutos.',
    tags: ['payment-service', 'performance'], project: 'Aplicação Web',
  },
  {
    severity: 'info', title: 'Deploy concluído com sucesso', time: '12:30',
    description: 'Versão v1.4.3 implantada em produção.',
    tags: ['auth-service', 'deploy'], project: 'Backoffice',
  },
  {
    severity: 'error', title: 'Erro ao conectar com o banco', time: '11:18',
    description: 'Connection timeout após 30 segundos. Verificar rede.',
    tags: ['user-service', 'banco de dados'], project: 'Aplicação Web',
  },
  {
    severity: 'warning', title: 'Fila com alto volume de mensagens', time: '10:02',
    description: 'Mais de 10.000 mensagens na fila de processamento.',
    tags: ['queue-worker', 'mensageria'], project: 'Processamento',
  },
  {
    severity: 'info', title: 'Novo usuário registrado', time: '09:25',
    description: 'Usuário de demonstração cadastrado com sucesso.',
    tags: ['frontend', 'cadastro'], project: 'Aplicação Web',
  },
  {
    severity: 'warning', title: 'Uso elevado de memória', time: '09:10',
    description: 'Consumo de memória acima do limite de exemplo.',
    tags: ['notification', 'memória'], project: 'Notificações',
  },
  {
    severity: 'info', title: 'Sincronização finalizada', time: '08:40',
    description: 'Dados de demonstração sincronizados.',
    tags: ['storage', 'sincronização'], project: 'Processamento',
  },
]

const demoOccurrences: OccurrenceItem[] = occurrenceExamples.map(({ time, ...example }, index) => ({
  ...example,
  id: `demo-occurrence-${index + 1}`,
  dateTime: `2026-10-23T${time}:00-03:00`,
  dateLabel: '23 de out. de 2026 (exemplo)',
  timeLabel: `Dia de exemplo, ${time}`,
}))

const demoOccurrencePanels: ComponentProps<typeof OccurrencesView>['panels'] = {
  periodLabel: 'Últimos 7 dias (exemplo)',
  metrics: {
    errors: { value: 48, variation: '↑ 12%', trend: [12, 18, 15, 26, 21, 35, 48] },
    warnings: { value: 73, variation: '↑ 5%', trend: [42, 38, 53, 48, 64, 56, 73] },
    information: { value: 284, variation: '↑ 8%', trend: [120, 150, 146, 195, 220, 250, 284] },
    resolved: { value: 312, variation: '↑ 22%', trend: [150, 180, 168, 240, 215, 270, 312] },
  },
  activityColumns: Array.from({ length: 14 }, (_, index) => `Amostra ${index + 1}`),
  activityCaption: 'Amostras fictícias por dia. Os intervalos reais ainda serão definidos.',
  activity: [
    { label: 'Domingo', values: [0, 1, 0, 3, 0, 0, 2, 0, 1, 2, 0, 2, 0, 0] },
    { label: 'Segunda-feira', values: [1, 3, 3, 1, 2, 1, 4, 3, 0, 1, 2, 1, 2, 0] },
    { label: 'Terça-feira', values: [2, 0, 4, 1, 2, 3, 1, 4, 1, 2, 4, 1, 2, 2] },
    { label: 'Quarta-feira', values: [2, 1, 4, 1, 0, 2, 3, 4, 3, 2, 3, 3, 2, 4] },
    { label: 'Quinta-feira', values: [2, 1, 2, 0, 3, 4, 3, 2, 3, 2, 1, 2, 4, 3] },
    { label: 'Sexta-feira', values: [1, 2, 0, 3, 3, 0, 1, 4, 2, 1, 2, 3, 3, 1] },
    { label: 'Sábado', values: [0, 1, 0, 3, 3, 0, 0, 3, 3, 1, 3, 2, 3, 1] },
  ],
  services: [
    { id: 'api', label: 'api-gateway', value: 128, color: 'red' },
    { id: 'payments', label: 'payment-service', value: 93, color: 'amber' },
    { id: 'auth', label: 'auth-service', value: 67 },
    { id: 'notification', label: 'notification', value: 52, color: 'lightBlue' },
    { id: 'storage', label: 'storage', value: 38, color: 'lightBlue' },
  ],
  resolutionTime: { value: 28, unit: 'min', variation: '↑ 32% vs. período anterior (exemplo)' },
  resolutionRate: { value: 86, unit: '%', variation: '↑ 12% vs. período anterior (exemplo)' },
}

export function App() {
  const [demoPage, setDemoPage] = useState(1)
  const [demoProgress, setDemoProgress] = useState(85)
  const [demoNavigation, setDemoNavigation] = useState('occurrences')
  const [demoProductSearch, setDemoProductSearch] = useState('')
  const [demoProductPage, setDemoProductPage] = useState(1)
  const [demoViewPage, setDemoViewPage] = useState(1)
  const [demoViewSearch, setDemoViewSearch] = useState('')
  const [demoOccurrenceSearch, setDemoOccurrenceSearch] = useState('')
  const [occurrenceSearch, setOccurrenceSearch] = useState('')
  const [occurrencePage, setOccurrencePage] = useState(1)
  const occurrencePageSize = 6
  const occurrenceQuery = occurrenceSearch.trim().toLocaleLowerCase('pt-BR')
  const filteredOccurrences = demoOccurrences.filter((item) =>
    [item.title, item.description, item.project, ...item.tags].join(' ').toLocaleLowerCase('pt-BR').includes(occurrenceQuery),
  )
  const [demoDataState, setDemoDataState] = useState<'loading' | 'empty' | 'no-results' | 'error' | 'success'>('error')
  useEffect(() => {
    if (demoDataState !== 'loading') return
    const timeout = window.setTimeout(() => setDemoDataState('success'), 1200)
    return () => window.clearTimeout(timeout)
  }, [demoDataState])
  const productsPerPage = 2
  const firstProductIndex = (demoProductPage - 1) * productsPerPage
  const visibleProducts = demoProducts.slice(firstProductIndex, firstProductIndex + productsPerPage)

  return (
    <AppLayout
      navbar={{
        activeItem: demoNavigation,
        onItemSelect: setDemoNavigation,
        productName: 'API Gateway',
        onProductSelect: () => alert('Demonstração do seletor. A lista de produtos ainda será definida.'),
      }}
      header={{
        breadcrumbs: {
          homeHref: '#components-top',
          items: [{ label: 'Demonstração' }, { label: 'Componentes' }],
        },
        systemStatus: { label: 'Sistema operacional (exemplo)' },
        period: {
          label: 'Últimas 24 horas',
          onClick: () => alert('Demonstração: as opções de período ainda serão definidas.'),
        },
        notifications: {
          count: 1,
          onClick: () => alert('Demonstração: o conteúdo das notificações ainda será definido.'),
        },
        user: { name: 'João Silva', role: 'Administrador' },
      }}
      footer={{ links: [{ label: 'Voltar ao início', href: '#components-top' }] }}
    >
      <h1 id="components-top" className="text-2xl font-bold text-slate-900">
        Componentes LhamaLog
      </h1>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Componentes básicos
        </h2>

        <div className="flex max-w-96 flex-col gap-6">
          <Input
            id="email"
            label="E-mail"
            type="email"
            placeholder="seu@exemplo.com"
            autoComplete="email"
          />

          <PasswordInput
            id="password"
            label="Senha"
            placeholder="Sua senha"
            autoComplete="current-password"
          />

          <Checkbox
            id="remember"
            label="Lembrar de mim"
          />

          <Button onClick={() => alert('Entrar clicado!')}>
            Entrar
          </Button>
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Variantes
        </h2>

        <div className="flex flex-wrap gap-3">
          <Button
            fullWidth={false}
            onClick={() => alert('Primário clicado!')}
          >
            Primário
          </Button>

          <Button
            variant="outline"
            fullWidth={false}
            onClick={() => alert('Contorno clicado!')}
          >
            Contorno
          </Button>

          <Button
            variant="soft"
            fullWidth={false}
            onClick={() => alert('Suave clicado!')}
          >
            Suave
          </Button>
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Tamanhos
        </h2>

        <div className="flex flex-wrap items-center gap-3">
          <Button size="small" fullWidth={false}>
            Pequeno
          </Button>

          <Button size="normal" fullWidth={false}>
            Normal
          </Button>
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Largura total
        </h2>

        <Button>Ocupa toda a largura disponível</Button>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Desabilitados
        </h2>

        <div className="flex flex-wrap gap-3">
          <Button
            disabled
            fullWidth={false}
            onClick={() => alert('Este alerta não deve aparecer')}
          >
            Primário
          </Button>

          <Button
            variant="outline"
            disabled
            fullWidth={false}
          >
            Contorno
          </Button>

          <Button
            variant="soft"
            disabled
            fullWidth={false}
          >
            Suave
          </Button>
        </div>
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Botões com ícone
        </h2>

        <div className="flex flex-wrap gap-3">
          <IconButton
            aria-label="Adicionar produto"
            onClick={() => alert('Adicionar produto')}
          >
            <svg
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              strokeWidth="2"
              strokeLinecap="round"
            >
              <path d="M12 5v14M5 12h14" />
            </svg>
          </IconButton>

          <IconButton
            variant="outline"
            aria-label="Próxima página"
            onClick={() => alert('Próxima página')}
          >
            <svg
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              strokeWidth="2"
              strokeLinecap="round"
              strokeLinejoin="round"
            >
              <path d="m9 5 7 7-7 7" />
            </svg>
          </IconButton>

          <IconButton
            variant="soft"
            aria-label="Adicionar produto indisponível"
            disabled
            onClick={() => alert('Este alerta não deve aparecer')}
          >
            <svg
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              strokeWidth="2"
              strokeLinecap="round"
            >
              <path d="M12 5v14M5 12h14" />
            </svg>
          </IconButton>
        </div>
      </section>


      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Indicadores de status
        </h2>

        <div className="flex flex-wrap items-center gap-3">
          <Badge variant="success" dot>
            Conectado
          </Badge>

          <Badge variant="warning" dot>
            Aguardando primeiro log
          </Badge>

          <Badge variant="danger" dot>
            Falha na conexão
          </Badge>

          <Badge variant="neutral" dot>
            Sem integração
          </Badge>

          <Badge variant="info" dot>
            Empresa verificada
          </Badge>
        </div>

        <h3 className="font-medium">
          Sem bolinha
        </h3>

        <div className="flex flex-wrap items-center gap-3">
          <Badge>Aplicação web</Badge>

          <Badge variant="neutral">
            Dados
          </Badge>
        </div>
      </section>


      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Tags de categorias e etiquetas
        </h2>

        <p className="text-sm text-slate-600">
          Exemplos de etiquetas reutilizando o Badge.
        </p>

        <div className="flex flex-wrap items-center gap-3">
          <Badge>Aplicação web</Badge>
          <Badge>API / Serviço</Badge>
          <Badge>Microsserviço</Badge>
          <Badge variant="neutral" dot>Dados</Badge>
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Avatar com iniciais
        </h2>

        <div className="flex flex-wrap items-center gap-6">
          <div className="flex items-center gap-2">
            <Avatar name="João Silva" />
            <span className="text-sm text-slate-700">João Silva</span>
          </div>

          <div className="flex items-center gap-2">
            <Avatar name="Ana" />
            <span className="text-sm text-slate-700">Ana</span>
          </div>

          <div className="flex items-center gap-2">
            <Avatar name="" />
            <span className="text-sm text-slate-700">Sem nome</span>
          </div>
        </div>
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Input com ícone inicial
        </h2>

        <div className="max-w-96">
          <Input
            id="email-with-icon"
            label="E-mail com ícone"
            type="email"
            placeholder="seu@exemplo.com"
            autoComplete="email"
            startIcon={
              <svg
                width="16"
                height="16"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                strokeWidth="2"
                strokeLinecap="round"
                strokeLinejoin="round"
              >
                <rect x="3" y="5" width="18" height="14" rx="2" />
                <path d="m3 7 9 6 9-6" />
              </svg>
            }
          />
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Busca compacta
        </h2>

        <p className="text-sm text-slate-600">
          Demonstração visual dos campos, sem filtrar produtos nesta etapa.
        </p>

        <SearchInput
          id="product-search"
          label="Buscar produtos"
          placeholder="Buscar produtos..."
        />

        <SearchInput
          id="disabled-search"
          label="Busca indisponível"
          placeholder="Busca indisponível"
          disabled
        />

        <SearchInput
          id="search-with-error"
          label="Busca com erro de exemplo"
          placeholder="Buscar produtos..."
          error="Mensagem de erro apenas para demonstração."
        />
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">
          Card para painéis
        </h2>

        <div className="grid gap-4 rounded-xl bg-slate-50 p-4 sm:grid-cols-2">
          <Card>
            <h3 className="font-semibold text-[#083B7A]">
              Resumo dos produtos
            </h3>
            <p className="mt-2 text-sm text-slate-600">
              Conteúdo ilustrativo do painel, sem dados reais.
            </p>
            <div className="mt-4">
              <Badge variant="success" dot>Conectado</Badge>
            </div>
          </Card>

          <Card>
            <h3 className="font-semibold text-[#083B7A]">
              Produtos por ambiente
            </h3>
            <p className="mt-2 text-sm text-slate-600">
              O mesmo Card pode receber conteúdos diferentes.
            </p>
          </Card>
        </div>
      </section>

      <section className="space-y-6">
        <PageHeader
          title="Produtos — exemplo de PageHeader"
          headingLevel={2}
          description="Acompanhe os produtos cadastrados e o status da integração."
          actions={
            <Button
              size="small"
              fullWidth={false}
              onClick={() => alert('Demonstração: cadastrar produto')}
            >
              <svg
                width="16"
                height="16"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                strokeWidth="2"
                strokeLinecap="round"
                aria-hidden="true"
              >
                <path d="M12 5v14M5 12h14" />
              </svg>
              Cadastrar produto
            </Button>
          }
        />

        <PageHeader
          title="Cabeçalho somente com título"
          headingLevel={2}
        />
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Botão de filtros</h2>
        <p className="text-sm text-slate-600">
          Demonstração do botão; o painel de filtros será definido depois.
        </p>
        <div className="flex flex-wrap items-center gap-6 pt-2">
          <FilterButton onClick={() => alert('Demonstração: filtros sem seleção')} />
          <FilterButton count={3} onClick={() => alert('Demonstração: três filtros ativos')} />
          <FilterButton count={2} disabled />
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Paginação</h2>
        <p className="text-sm text-slate-600">
          Dados ilustrativos: 24 produtos, seis por página.
        </p>
        <Pagination
          page={demoPage}
          pageSize={6}
          totalItems={24}
          itemLabel="produtos"
          onPageChange={setDemoPage}
        />
        <h3 className="font-medium">Sem resultados</h3>
        <Pagination page={1} pageSize={6} totalItems={0} onPageChange={() => {}} />
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Barra de progresso</h2>
        <Card className="max-w-md">
          <ProgressBar value={demoProgress} label="Conclusão do perfil" />
          <label htmlFor="demo-progress" className="mt-4 block text-sm text-slate-600">
            Ajustar percentual de demonstração
          </label>
          <input
            id="demo-progress"
            type="range"
            min={0}
            max={100}
            value={demoProgress}
            onChange={(event) => setDemoProgress(Number(event.target.value))}
            className="mt-2 w-full accent-[#0C70F2]"
          />
        </Card>
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Caminho da página</h2>
        <p className="text-sm text-slate-600">
          Exemplo: o ícone de início e o link Componentes levam ao topo desta demonstração.
        </p>
        <Breadcrumbs
          homeHref="#components-top"
          items={[
            { label: 'Componentes', href: '#components-top' },
            { label: 'Navegação' },
            { label: 'Breadcrumbs' },
          ]}
        />
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Estado do sistema</h2>
        <p className="text-sm text-slate-600">
          Estados ilustrativos, sem consultar o backend.
        </p>
        <div className="flex flex-wrap items-center gap-6">
          <SystemStatus label="Sistema operacional" />
          <SystemStatus label="Exemplo de aviso" tone="warning" />
          <SystemStatus label="Exemplo de indisponibilidade" tone="danger" />
          <SystemStatus label="Estado desconhecido" tone="neutral" />
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Seletor de período</h2>
        <p className="text-sm text-slate-600">
          Apresentação do seletor; as opções e o funcionamento serão definidos depois.
        </p>
        <div className="flex flex-wrap items-center gap-4">
          <PeriodSelector
            label="Últimas 24 horas"
            onClick={() => alert('Demonstração do seletor. As opções de período ainda serão definidas.')}
          />
          <PeriodSelector label="Últimas 24 horas" disabled />
        </div>
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Notificações</h2>
        <p className="text-sm text-slate-600">
          Exemplos sem notificações, com uma, com mais de 99 e desabilitado.
          O conteúdo das notificações ainda será definido.
        </p>
        <div className="flex flex-wrap items-center gap-6">
          <NotificationButton onClick={() => alert('Demonstração: nenhuma notificação não lida.')} />
          <NotificationButton count={1} onClick={() => alert('Demonstração: uma notificação não lida.')} />
          <NotificationButton count={120} onClick={() => alert('Demonstração: 120 notificações não lidas.')} />
          <NotificationButton count={2} disabled />
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Identificação do usuário</h2>
        <p className="text-sm text-slate-600">
          Dados ilustrativos, sem autenticação ou menu de usuário.
        </p>
        <div className="flex flex-wrap items-center gap-6">
          <UserIdentity name="João Silva" role="Administrador" />
          <UserIdentity name="Ana" role="Desenvolvedora" />
        </div>
      </section>
      <section className="space-y-4">
        <p className="text-sm text-slate-600">
          Demonstração dos componentes de Produtos, com dados ilustrativos.
          A busca apenas recebe texto; filtros e cadastro ainda não têm fluxos definidos.
        </p>
        <ProductsHeader
          headingLevel={2}
          search={demoProductSearch}
          onSearchChange={setDemoProductSearch}
          filterCount={3}
          onFilterClick={() => alert('Demonstração: o conteúdo dos filtros ainda será definido.')}
          onCreateClick={() => alert('Demonstração: o formulário de cadastro ainda será definido.')}
        />
        <ProductCard
          name="Plataforma Web"
          description="Portal do cliente e área logada"
          status="connected"
          categories={['Aplicação web']}
          area="Comercial"
          lastLog={{ label: 'há 2 min (exemplo)', date: '22 de out. de 2026' }}
          environment="production"
          icon={
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="1.5" aria-hidden="true">
              <circle cx="12" cy="12" r="9" />
              <ellipse cx="12" cy="12" rx="4" ry="9" />
              <path d="M3 12h18" />
            </svg>
          }
        />
        <ProductCard
          name="App Mobile"
          description="Aplicativo Android e iOS"
          status="waiting"
          categories={['Aplicativo']}
          area="Comercial"
          lastLog={null}
          environment="staging"
          iconColor="purple"
          icon={
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="1.5" aria-hidden="true">
              <rect x="6" y="2" width="12" height="20" rx="1" />
              <path d="M10 19h4" />
            </svg>
          }
        />
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Lista de produtos com paginação</h2>
        <p className="text-sm text-slate-600">
          Cinco produtos ilustrativos, dois por página. A navegação troca os cartões exibidos,
          sem consultar o backend ou aplicar a busca dos exemplos anteriores.
        </p>
        <ProductList products={visibleProducts} />
        <Pagination
          page={demoProductPage}
          pageSize={productsPerPage}
          totalItems={demoProducts.length}
          itemLabel="produtos"
          onPageChange={setDemoProductPage}
        />
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Cartão de métrica</h2>
        <p className="text-sm text-slate-600">
          Valores ilustrativos para demonstrar título, unidade, ícone e variação opcional.
        </p>
        <div className="grid gap-4 sm:grid-cols-2">
          <MetricCard
            title="Exemplo com unidade e variação"
            value={85}
            unit="%"
            icon={demoListIcon}
            variation="↑ 8% em relação ao período anterior (exemplo)"
            tone="green"
          />
          <MetricCard title="Exemplo sem ícone ou variação" value={24} />
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Painel de resumo dos produtos</h2>
        <p className="text-sm text-slate-600">
          Dados ilustrativos independentes da lista anterior, sem cálculo de métricas ou consulta ao backend.
          Os minigráficos mostram séries ilustrativas recebidas pelo componente.
        </p>
        <div className="max-w-md">
          <ProductsSummary
            periodLabel="Últimos 7 dias"
            total={{ value: 24, variation: '↑ 4%', trend: [16, 17, 17, 19, 20, 22, 24] }}
            connected={{ value: 17, variation: '↑ 12%', trend: [10, 12, 11, 14, 13, 15, 17] }}
            waiting={{ value: 4, variation: '↓ 1', trend: [8, 7, 7, 6, 5, 5, 4] }}
            withoutIntegration={{ value: 3, variation: 'Sem variação' }}
          />
        </div>
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Produtos por ambiente</h2>
        <p className="text-sm text-slate-600">
          Dados ilustrativos: 14 em produção, seis em homologação e quatro em desenvolvimento.
          As barras comparam cada valor com o maior do painel.
        </p>
        <div className="max-w-md">
          <ProductsByEnvironment production={14} staging={6} development={4} />
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Barras de distribuição com valores zero</h2>
        <p className="text-sm text-slate-600">
          Exemplo do componente reutilizável: as trilhas ficam sem preenchimento e os valores continuam visíveis.
        </p>
        <Card className="max-w-md">
          <DistributionBars
            items={[
              { label: 'Produção', value: 0, color: 'blue' },
              { label: 'Homologação', value: 0, color: 'amber' },
              { label: 'Desenvolvimento', value: 0, color: 'navy' },
            ]}
          />
        </Card>
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Ranking de logs por produto</h2>
        <p className="text-sm text-slate-600">
          Dados ilustrativos ordenados do maior volume para o menor. O produto com zero mantém sua posição e o valor visível.
        </p>
        <div className="max-w-md">
          <LogsByProduct products={demoLogRanking} periodLabel="Últimos 7 dias" />
        </div>
      </section>

      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Minigráficos de tendência</h2>
        <p className="text-sm text-slate-600">
          Séries ilustrativas: variável, constante e com um único ponto. Uma série vazia não desenha gráfico.
        </p>
        <div className="flex flex-wrap gap-6 text-[#0C70F2]">
          <Sparkline values={[2, 4, 3, 7, 8, 6, 10]} label="Exemplo variável: 2, 4, 3, 7, 8, 6, 10" />
          <Sparkline values={[5, 5, 5]} label="Exemplo constante: 5, 5, 5" />
          <Sparkline values={[5]} label="Exemplo com um ponto: 5" />
          <Sparkline values={[]} label="Exemplo vazio" />
        </div>
      </section>

      <section id="products-view-demo" className="space-y-4">
        <h2 className="text-lg font-semibold">Composição de Produtos</h2>
        <p className="text-sm text-slate-600">
          Demonstração com cinco produtos e três por página. A busca apenas recebe texto;
          filtros e cadastro mostram avisos. Todos os indicadores e históricos são ilustrativos.
        </p>
        <ProductsView
          header={{
            headingLevel: 2,
            search: demoViewSearch,
            onSearchChange: setDemoViewSearch,
            filterCount: 0,
            onFilterClick: () => alert('Demonstração: o conteúdo dos filtros ainda será definido.'),
            onCreateClick: () => alert('Demonstração: o formulário de cadastro ainda será definido.'),
          }}
          list={{ products: demoProducts.slice((demoViewPage - 1) * 3, demoViewPage * 3) }}
          pagination={{
            page: demoViewPage,
            pageSize: 3,
            totalItems: demoProducts.length,
            itemLabel: 'produtos',
            onPageChange: setDemoViewPage,
          }}
          summary={{
            periodLabel: 'Últimos 7 dias',
            total: { value: 5, variation: '↑ 1', trend: [2, 3, 3, 4, 4, 5, 5] },
            connected: { value: 2, trend: [1, 1, 2, 2, 2, 2, 2] },
            waiting: { value: 1, trend: [3, 2, 2, 2, 1, 1, 1] },
            withoutIntegration: { value: 1, variation: 'Sem variação' },
          }}
          environments={{ production: 3, staging: 1, development: 1 }}
          logs={{ products: demoLogRanking, periodLabel: 'Últimos 7 dias' }}
          indicators={{
            activeIntegrations: { value: 40, unit: '%', variation: '↑ 8% vs. semana anterior (exemplo)' },
            logsToday: { value: '8,2', unit: 'mil', variation: '↑ 14% vs. ontem (exemplo)' },
          }}
        />
      </section>
      <section id="shared-states-demo" className="space-y-4">
        <h2 className="text-lg font-semibold">Estados compartilhados</h2>
        <p className="text-sm text-slate-600">
          Demonstração local, sem requisição ao backend. Escolha um estado;
          carregar ou tentar novamente mostra os produtos após 1,2 segundo.
        </p>
        <div className="flex flex-wrap gap-2">
          <Button size="small" fullWidth={false} variant="outline" onClick={() => setDemoDataState('loading')}>Ver carregamento</Button>
          <Button size="small" fullWidth={false} variant="outline" onClick={() => setDemoDataState('empty')}>Ver lista vazia</Button>
          <Button size="small" fullWidth={false} variant="outline" onClick={() => setDemoDataState('no-results')}>Ver busca sem resultados</Button>
          <Button size="small" fullWidth={false} variant="outline" onClick={() => setDemoDataState('error')}>Ver falha</Button>
        </div>
        <div role="region" aria-label="Resultado da demonstração de estados" aria-busy={demoDataState === 'loading'}>
          {demoDataState === 'loading' && <LoadingState label="Carregando produtos de demonstração..." rows={2} />}
          {demoDataState === 'empty' && (
            <EmptyState
              title="Nenhum produto cadastrado"
              description="Os produtos cadastrados aparecerão nesta área."
            />
          )}
          {demoDataState === 'no-results' && (
            <EmptyState
              title="Nenhum resultado encontrado"
              description="Tente outro termo de busca ou revise os filtros."
              action={<Button size="small" fullWidth={false} onClick={() => setDemoDataState('success')}>Mostrar dados de demonstração</Button>}
            />
          )}
          {demoDataState === 'error' && (
            <ErrorState
              title="Não foi possível carregar os produtos"
              description="Falha de exemplo. Tente novamente para demonstrar a recuperação."
              onRetry={() => setDemoDataState('loading')}
            />
          )}
          {demoDataState === 'success' && (
            <div className="space-y-3">
              <p role="status" className="text-sm text-green-700">Produtos de demonstração carregados.</p>
              <ProductList products={demoProducts.slice(0, 2)} />
            </div>
          )}
        </div>
      </section>
      <section className="space-y-4">
        <h2 className="text-lg font-semibold">Campo com orientação e erro</h2>
        <p className="text-sm text-slate-600">
          Exemplo de uma orientação e uma mensagem de erro associadas ao mesmo campo.
          O erro é ilustrativo; este exemplo não realiza validação.
        </p>
        <div className="max-w-96 space-y-2">
          <p id="review-email-help" className="text-sm text-slate-600">
            Utilize seu e-mail profissional.
          </p>
          <Input
            id="review-email"
            label="E-mail (exemplo)"
            className="tracking-wide"
            type="email"
            placeholder="seu@empresa.com"
            autoComplete="email"
            aria-describedby="review-email-help"
            error="Informe um endereço de e-mail válido (exemplo)."
          />
        </div>
      </section>
      <section id="occurrences-header-demo" className="space-y-4">
        <p className="text-sm text-slate-600">
          Demonstração do cabeçalho de Ocorrências. A busca recebe texto, sem consultar dados;
          o botão de filtros mostra apenas um aviso.
        </p>
        <OccurrencesHeader
          headingLevel={2}
          search={demoOccurrenceSearch}
          onSearchChange={setDemoOccurrenceSearch}
          filterCount={2}
          onFilterClick={() => alert('Demonstração: as opções dos filtros de ocorrências ainda serão definidas.')}
        />
        <h3 className="font-semibold text-slate-900">Indicadores de severidade</h3>
        <div className="flex flex-wrap items-center gap-3">
          <SeverityIndicator severity="error" />
          <SeverityIndicator severity="warning" />
          <SeverityIndicator severity="info" />
        </div>
      </section>
      <section id="occurrences-view-demo" className="space-y-4">
        <h2 className="text-lg font-semibold">Composição de Ocorrências</h2>
        <p className="text-sm text-slate-600">
          Demonstração com busca local e oito ocorrências fictícias, seis por página.
          Métricas e históricos são exemplos independentes da lista; filtros e detalhes mostram avisos.
        </p>
        <OccurrencesView
          header={{
            headingLevel: 2,
            search: occurrenceSearch,
            onSearchChange: (value) => { setOccurrenceSearch(value); setOccurrencePage(1) },
            filterCount: 0,
            onFilterClick: () => alert('Demonstração: as opções dos filtros ainda serão definidas.'),
          }}
          list={{
            occurrences: filteredOccurrences.slice((occurrencePage - 1) * occurrencePageSize, occurrencePage * occurrencePageSize),
            onOpen: () => alert('Demonstração: a tela de detalhes da ocorrência ainda será definida.'),
          }}
          pagination={{
            page: occurrencePage,
            pageSize: occurrencePageSize,
            totalItems: filteredOccurrences.length,
            itemLabel: 'ocorrências',
            onPageChange: setOccurrencePage,
          }}
          panels={demoOccurrencePanels}
        />
      </section>
      <section id="company-summary-demo" className="space-y-4">
        <p className="text-sm text-slate-600">
          Demonstração inicial de Empresa com dados fictícios. A verificação e a conclusão
          do perfil são valores recebidos pelo componente, sem validação ou cálculo cadastral.
        </p>
        <PageHeader title="Configurações da Empresa" headingLevel={2} />
        <CompanySummary
          name="LhamaLog Tecnologia LTDA"
          logoSrc={companySymbol}
          verified
          context="Matriz Operacional"
          cnpj="12.345.678/0001-90"
          stateRegistration="123.456.789.012"
          completion={85}
          completionDescription="Conclua os dados pendentes para chegar a 100%."
        />
      </section>
      <section id="company-info-demo" className="space-y-4">
        <h2 className="text-lg font-semibold">Cartões de Empresa</h2>
        <p className="text-sm text-slate-600">
          Resumos com dados fictícios e status ilustrativos. Os botões apenas demonstram as ações;
          os formulários ainda serão definidos.
        </p>
        <div className="grid gap-4 lg:grid-cols-2">
          <CompanyDataCard
            legalName="LhamaLog Tecnologia LTDA"
            tradeName="LhamaLog"
            cnpj="12.345.678/0001-90"
            identityLabel="Logo atualizada"
            statusLabel="Completo"
            onManage={() => alert('Demonstração: o formulário dos dados da empresa ainda será definido.')}
          />
          <CompanyContextCard
            segment="Tecnologia / SaaS"
            environments={['Produção', 'Homologação', 'Desenvolvimento']}
            criticalWindows="Horário comercial e deploys"
            monitoredSystems="5 nós conectados"
            statusLabel="Ativo"
            onEdit={() => alert('Demonstração: o formulário de contexto de atuação ainda será definido.')}
          />
        </div>
      </section>
      <section id="company-address-demo" className="space-y-4">
        <h2 className="text-lg font-semibold">Endereço da Empresa</h2>
        <p className="text-sm text-slate-600">
          Endereço fictício com mapa ilustrativo, sem geolocalização. O botão mostra um aviso;
          o formulário de atualização ainda será definido.
        </p>
        <div className="max-w-3xl">
          <CompanyAddressCard
            street="Avenida Brasil, 1234 – Sala 502"
            district="Centro"
            postalCode="87000-000"
            city="Maringá"
            state="PR"
            referencePoint="Em frente à praça central, Edifício Business Tower"
            statusLabel="Confirmado"
            onUpdate={() => alert('Demonstração: o formulário de atualização do endereço ainda será definido.')}
          />
        </div>
      </section>
      <section id="company-preferences-demo" className="space-y-4">
        <h2 className="text-lg font-semibold">Preferências da Empresa</h2>
        <p className="text-sm text-slate-600">
          Resumo com valores ilustrativos. Não aplica configurações de IA, notificações ou segurança;
          o botão demonstra a ação de ajuste, sem formulário.
        </p>
        <div className="max-w-3xl">
          <CompanyPreferencesCard
            currency="Real (BRL)"
            timeZone="(UTC-03:00) Brasília"
            language="Português (Brasil)"
            dataRange="Últimas 24 horas"
            aiAnalysis="Detalhamento alto • Criticidade"
            notifications="Críticas imediatas • Falhas recorrentes"
            security="2FA para gestores • Dados mascarados"
            statusLabel="Otimizado"
            onAdjust={() => alert('Demonstração: o formulário de preferências da empresa ainda será definido.')}
          />
        </div>
      </section>
      <section id="company-governance-demo" className="space-y-4">
        <h2 className="text-lg font-semibold">Governança da Empresa</h2>
        <p className="text-sm text-slate-600">
          Demonstração visual das ações de governança. Os botões mostram avisos;
          a exportação de PDF e a sincronização ainda não estão implementadas.
        </p>
        <CompanyGovernance
          description="Centralize a documentação e a sincronização das informações da empresa."
          onExport={() => alert('Demonstração: a exportação do dossiê em PDF ainda será definida.')}
          onSync={() => alert('Demonstração: a sincronização das áreas da empresa ainda será definida.')}
        />
      </section>
      <section id="company-view-demo" className="space-y-4">
        <h2 className="text-lg font-semibold">Composição de Empresa</h2>
        <p className="text-sm text-slate-600">
          Tela com dados fictícios. Os links internos levam aos blocos abaixo;
          editar, exportar e sincronizar continuam como ações demonstrativas.
        </p>
        <CompanyView
          id="company-overview"
          headingLevel={2}
          summary={{
            name: 'LhamaLog Tecnologia LTDA', logoSrc: companySymbol, verified: true,
            cnpj: '12.345.678/0001-90', stateRegistration: '123.456.789.012',
            context: 'Matriz Operacional', completion: 85,
            completionDescription: 'Conclua os dados pendentes para chegar a 100%.',
          }}
          data={{
            legalName: 'LhamaLog Tecnologia LTDA', tradeName: 'LhamaLog',
            cnpj: '12.345.678/0001-90', identityLabel: 'Logo atualizada', statusLabel: 'Completo',
            onManage: () => alert('Demonstração: o formulário dos dados da empresa ainda será definido.'),
          }}
          context={{
            segment: 'Tecnologia / SaaS', environments: ['Produção', 'Homologação', 'Desenvolvimento'],
            criticalWindows: 'Horário comercial e deploys', monitoredSystems: '5 nós conectados', statusLabel: 'Ativo',
            onEdit: () => alert('Demonstração: o formulário de contexto de atuação ainda será definido.'),
          }}
          address={{
            street: 'Avenida Brasil, 1234 – Sala 502', district: 'Centro', postalCode: '87000-000',
            city: 'Maringá', state: 'PR', referencePoint: 'Em frente à praça central, Edifício Business Tower', statusLabel: 'Confirmado',
            onUpdate: () => alert('Demonstração: o formulário de atualização do endereço ainda será definido.'),
          }}
          preferences={{
            currency: 'Real (BRL)', timeZone: '(UTC-03:00) Brasília', language: 'Português (Brasil)',
            dataRange: 'Últimas 24 horas', aiAnalysis: 'Detalhamento alto • Criticidade',
            notifications: 'Críticas imediatas • Falhas recorrentes', security: '2FA para gestores • Dados mascarados', statusLabel: 'Otimizado',
            onAdjust: () => alert('Demonstração: o formulário de preferências da empresa ainda será definido.'),
          }}
          governance={{
            description: 'Centralize a documentação e a sincronização das informações da empresa.',
            onExport: () => alert('Demonstração: a exportação do dossiê em PDF ainda será definida.'),
            onSync: () => alert('Demonstração: a sincronização das áreas da empresa ainda será definida.'),
          }}
          footer={<Footer links={[{ label: 'Voltar ao início', href: '#components-top' }]} />}
        />
      </section>
    </AppLayout>
  )
}
