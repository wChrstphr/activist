# Métricas de Testes do Projeto activist.org

Este documento descreve todas as métricas de testes utilizadas no projeto activist.org, incluindo cobertura de código, tipos de testes e ferramentas de qualidade.

<a id="contents"></a>

## **Índice**

- [Visão Geral](#visão-geral-)
- [Backend - Métricas de Testes](#backend---métricas-de-testes-)
- [Frontend - Métricas de Testes](#frontend---métricas-de-testes-)
- [Testes de Segurança](#testes-de-segurança-)
- [Integração Contínua (CI)](#integração-contínua-ci-)
- [Como Visualizar as Métricas](#como-visualizar-as-métricas-)

---

<a id="visão-geral-"></a>

## Visão Geral [`⇧`](#contents)

O projeto activist.org utiliza várias métricas e ferramentas para garantir a qualidade do código:

### Métricas Principais

1. **Cobertura de Código (Code Coverage)**: Medida da porcentagem do código testado
2. **Testes End-to-End (E2E)**: Validação de fluxos completos de usuário
3. **Testes de Acessibilidade**: Verificação de padrões WCAG
4. **Testes de Segurança**: Análise de vulnerabilidades (OWASP ZAP)
5. **Análise Estática de Código**: Linting e verificação de tipos
6. **Testes de Integração**: Validação de APIs e componentes

---

<a id="backend---métricas-de-testes-"></a>

## Backend - Métricas de Testes [`⇧`](#contents)

### Cobertura de Código

**Meta de Cobertura**: **85% mínimo**

A cobertura de código do backend é medida usando **pytest-cov** e é verificada automaticamente em cada Pull Request.

#### Configuração de Cobertura

Localização: `backend/pyproject.toml`

```toml
[tool.coverage.run]
omit = [
    "*/migrations/*",      # Migrações do Django
    "*/tests/*",           # Arquivos de teste
    "asgi.py",            # Arquivos de configuração
    "wsgi.py",
    "manage.py",
    "*/__init__.py",
    "*/factories.py",     # Factories de teste
    "*/utils/type_checker/*",
]
```

#### Comando para Executar com Cobertura

```bash
# No container Docker do backend:
pytest --cov --cov-report=term-missing --cov-config=pyproject.toml -vv

# Com requisito de cobertura mínima (usado no CI):
pytest --cov=. --cov-report=term-missing --cov-fail-under=85 --cov-config=./pyproject.toml -vv
```

#### Relatórios de Cobertura

- **Formato**: Terminal com linhas faltantes (`term-missing`)
- **Falha se**: Cobertura < 85%
- **Arquivos excluídos**: Migrações, testes, configurações, factories

### Framework de Testes

- **Framework**: pytest
- **Plugins**: pytest-django, pytest-cov, factory-boy
- **Tipos de testes**:
  - Testes de API (CRUD operations)
  - Testes de modelos
  - Testes de serializers
  - Testes de autenticação
  - Testes de permissões

### Análise Estática

#### 1. Ruff (Linting e Formatação)

```bash
# Formatação:
ruff format ./backend

# Linting:
ruff check ./backend
```

#### 2. MyPy (Verificação de Tipos)

```bash
mypy ./backend --config-file ./backend/pyproject.toml
```

**Configuração**: Modo strict ativado, com plugins Django e DRF

### Estatísticas do Backend

- **Arquivos de teste**: ~119 arquivos
- **Diretórios de testes**: 8 diretórios principais, ~40+ subdiretórios
- **Estrutura**: Testes organizados por módulo e funcionalidade

---

<a id="frontend---métricas-de-testes-"></a>

## Frontend - Métricas de Testes [`⇧`](#contents)

### Tipos de Testes

O frontend utiliza dois frameworks de teste complementares:

#### 1. Vitest (Testes de Componentes e Unidade)

**Localização**: `frontend/test/`

**Características**:
- Testes rápidos de componentes Vue
- Testes de composables
- Testes de utilitários
- Integração com Nuxt Test Utils

**Comando**:
```bash
cd frontend
yarn test          # Executar testes
yarn test:ui       # Interface visual
```

**Configuração**: `frontend/vitest.config.mts`
- Environment: nuxt
- Globals: habilitado
- Setup: `./test/setup.ts`

#### 2. Playwright (Testes End-to-End)

**Localização**: `frontend/test-e2e/`

**Características**:
- Testes em múltiplos navegadores (Chromium, WebKit, Firefox)
- Testes em dispositivos móveis e tablets
- Testes de acessibilidade integrados
- Screenshots e traces automáticos em falhas

**Configuração**: `frontend/playwright.config.ts`

**Ambientes de Teste**:
- Desktop (Chromium, WebKit)
- Tablets (iPad Landscape, iPad Portrait)
- Mobile (Chrome, Safari)

**Comandos**:
```bash
cd frontend
yarn test:local    # Testes E2E locais (http://localhost:3000)
yarn test:prod     # Testes E2E produção (https://activist.org)
```

**Métricas E2E**:
- **Retries**: 4 tentativas no CI, 0 localmente
- **Workers**: 1 no CI (sequencial), paralelo localmente
- **Timeout de navegação**: 10 segundos
- **Trace**: Ativado na primeira falha
- **Screenshots**: Apenas em falhas, página completa

### Testes de Acessibilidade

**Ferramenta**: axe-core integrado com Playwright

**Características**:
- Geração automática de relatórios HTML
- Detecção de violações WCAG
- Relatórios organizados por página, navegador e dispositivo

**Reporter Customizado**: `frontend/test-e2e/accessibility/axe-reporter.ts`

**Relatórios salvos em**: `frontend/test-results/`

### Análise Estática do Frontend

#### 1. Prettier (Formatação)

```bash
yarn prettier . --check
```

#### 2. ESLint (Linting)

```bash
yarn lint
```

#### 3. TypeScript (Verificação de Tipos)

```bash
yarn typecheck
```

### Estatísticas do Frontend

- **Arquivos de teste**: ~26 arquivos
- **Frameworks**: Vitest + Playwright
- **Estrutura**:
  - `test/`: Componentes e unidade
  - `test-e2e/`: End-to-End
  - `test-utils/`: Utilitários compartilhados

---

<a id="testes-de-segurança-"></a>

## Testes de Segurança [`⇧`](#contents)

### OWASP ZAP (Zed Attack Proxy)

**Frequência**: Semanal (segundas-feiras às 2 AM UTC)

**Características**:
- **Attack Strength**: HIGH
- **Alert Threshold**: MEDIUM
- **Spider**: Traditional + AJAX spider
- **Tipos de scan**:
  - Passive scanning (análise de respostas)
  - Active scanning (testes de vulnerabilidades)

**Configuração**: `.zap/zap.yaml`

**Relatórios**:
- HTML report
- JSON report
- Upload automático como artifact no GitHub Actions

**Workflow**: `.github/workflows/owasp_zap_full_scan.yaml`

**Vulnerabilidades Testadas**:
- SQL Injection
- Cross-Site Scripting (XSS)
- CSRF
- Security Headers
- TLS/SSL Configuration
- E muito mais...

**Exclusões de Falsos Positivos**:
```yaml
alertFilters:
  - ruleId: 10016     # cross-domain Misconfiguration
    newRisk: "False Positive"
  - ruleId: 10020     # x-frame-options header
    newRisk: "False Positive"
```

---

<a id="integração-contínua-ci-"></a>

## Integração Contínua (CI) [`⇧`](#contents)

### Workflows de CI

O projeto possui vários workflows que executam testes automaticamente:

#### 1. Backend CI (`pr_ci_backend.yaml`)

**Quando executa**:
- Pull Requests para `main`
- Push para `main`
- Mudanças em `backend/**`

**Passos**:
1. ✅ Ruff Format (formatação)
2. ✅ Ruff Check (linting)
3. ✅ MyPy (verificação de tipos)
4. ✅ Django Migrations (verificação de migrações)
5. ✅ Pytest (testes com cobertura ≥85%)

**Serviços**:
- PostgreSQL 15

#### 2. Frontend CI (`pr_ci_frontend.yaml`)

**Quando executa**:
- Pull Requests para `main`
- Push para `main`
- Mudanças em `frontend/**`

**Passos**:
1. ✅ Prettier (formatação)
2. ✅ TypeCheck (verificação de tipos)
3. ✅ ESLint (linting)
4. ✅ Vitest (testes de componentes)

#### 3. E2E CI (`pr_ci_playwright_e2e.yaml`)

**Quando executa**:
- Manual (workflow_dispatch)

**Passos**:
1. Setup Docker (backend + database)
2. Build do frontend
3. Start do servidor Node
4. Instalação do Playwright
5. Execução dos testes E2E
6. Upload do relatório (retention: 30 dias)

**Características**:
- Wait-on com timeout e retries
- Verificação de disponibilidade de portas
- Logs detalhados de containers

#### 4. Outros Workflows

- **License Header Check**: Verifica headers de licença
- **i18n Check**: Verifica traduções
- **TypeScript Backend Check**: Verifica tipos no backend
- **OWASP ZAP Scan**: Testes de segurança semanais

### Métricas de CI

**Status dos Testes**: Visível através do badge no README
```markdown
[![testing](https://img.shields.io/badge/Testing-F0F0EB.svg?logo=github&logoColor=000000)](https://github.com/activist-org/activist/actions)
```

---

<a id="como-visualizar-as-métricas-"></a>

## Como Visualizar as Métricas [`⇧`](#contents)

### 1. Cobertura de Código Backend

```bash
# Local
docker exec -it django_backend sh
pytest --cov --cov-report=html --cov-config=pyproject.toml
# Abrir: backend/htmlcov/index.html
```

### 2. Relatórios de Testes E2E

```bash
# Após executar testes E2E
cd frontend
yarn playwright show-report
```

### 3. Relatórios de Acessibilidade

- **Local**: `frontend/test-results/`
- **Formato**: HTML com detalhes das violações
- **Organização**: Por página, navegador e dispositivo

### 4. Relatórios de Segurança (ZAP)

- **Local**: `zap-reports/`
- **Formato**: HTML e JSON
- **GitHub**: Artifacts nas Actions semanais

### 5. GitHub Actions

Acesse: https://github.com/activist-org/activist/actions

**Visualização**:
- Status dos workflows
- Logs detalhados de cada step
- Artifacts (relatórios Playwright, ZAP)
- Histórico de execuções

---

## 📊 Resumo das Métricas

### Backend
- ✅ **Cobertura**: ≥85% (obrigatório no CI)
- ✅ **Framework**: pytest
- ✅ **Linting**: Ruff
- ✅ **Types**: MyPy (strict mode)
- ✅ **Arquivos de teste**: ~119

### Frontend
- ✅ **Testes de Componentes**: Vitest
- ✅ **Testes E2E**: Playwright
- ✅ **Navegadores**: Chromium, WebKit, Firefox
- ✅ **Dispositivos**: Desktop, Tablet, Mobile
- ✅ **Acessibilidade**: axe-core
- ✅ **Linting**: ESLint
- ✅ **Types**: TypeScript
- ✅ **Formatação**: Prettier
- ✅ **Arquivos de teste**: ~26

### Segurança
- ✅ **OWASP ZAP**: Scans semanais
- ✅ **Attack Strength**: HIGH
- ✅ **Alert Threshold**: MEDIUM

### CI/CD
- ✅ **Workflows**: 8 workflows automatizados
- ✅ **Verificações**: Formato, Lint, Tipos, Testes, Segurança
- ✅ **Artifacts**: Relatórios Playwright e ZAP

---

## 📚 Documentação Relacionada

- [CONTRIBUTING.md](./CONTRIBUTING.md#testing-) - Guia de testes
- [BACKEND_TESTING.md](./BACKEND_TESTING.md) - Padrões de testes backend
- [FRONTEND_TESTING.md](./FRONTEND_TESTING.md) - Padrões de testes frontend
- [ONDE_ESTAO_OS_TESTES.md](./ONDE_ESTAO_OS_TESTES.md) - Localização dos testes
- [.zap/README.md](./.zap/README.md) - Configuração OWASP ZAP

---

## 🎯 Conclusão

O projeto activist.org utiliza um conjunto abrangente de métricas de teste que garantem:

1. **Qualidade de Código**: Cobertura mínima de 85% no backend
2. **Funcionalidade**: Testes E2E em múltiplos ambientes
3. **Acessibilidade**: Verificação automática de padrões WCAG
4. **Segurança**: Scans semanais de vulnerabilidades
5. **Manutenibilidade**: Análise estática rigorosa (linting, tipos)

Todas essas métricas são verificadas automaticamente através de GitHub Actions, garantindo que o código mantém alta qualidade em todos os Pull Requests.
