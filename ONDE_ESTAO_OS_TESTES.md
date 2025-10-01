# Localização dos Testes no Repositório activist

Este documento identifica onde estão localizados todos os testes neste repositório.

## 📋 Sumário

- [Testes do Backend](#testes-do-backend)
- [Testes do Frontend](#testes-do-frontend)
- [Documentação de Testes](#documentação-de-testes)

---

## Testes do Backend

Os testes do backend estão localizados no diretório `backend/` e utilizam **pytest** como framework de testes.

### Estrutura Principal

```
backend/
├── authentication/tests/          # Testes de autenticação
│   ├── flag/                      # Testes de flag de autenticação
│   └── session/                   # Testes de sessão
├── communities/
│   ├── groups/tests/              # Testes de grupos
│   └── organizations/tests/       # Testes de organizações
│       ├── event/                 # Testes de eventos de organizações
│       ├── faq/                   # Testes de FAQ de organizações
│       ├── flag/                  # Testes de flag de organizações
│       ├── image/                 # Testes de imagens de organizações
│       ├── member/                # Testes de membros de organizações
│       ├── resource/              # Testes de recursos de organizações
│       ├── social_link/           # Testes de links sociais
│       ├── text/                  # Testes de texto de organizações
│       └── topic/                 # Testes de tópicos de organizações
├── content/tests/                 # Testes de conteúdo
│   ├── discussion/                # Testes de discussões
│   ├── faq/                       # Testes de FAQ
│   ├── image/                     # Testes de imagens
│   ├── location/                  # Testes de localização
│   ├── resource/                  # Testes de recursos
│   ├── social_link/               # Testes de links sociais
│   ├── tag/                       # Testes de tags
│   ├── task/                      # Testes de tarefas
│   └── topic/                     # Testes de tópicos
├── core/tests/                    # Testes core (Swagger, etc.)
├── events/tests/                  # Testes de eventos
│   ├── calender/                  # Testes de calendário
│   ├── faq/                       # Testes de FAQ de eventos
│   ├── flag/                      # Testes de flag de eventos
│   ├── resource/                  # Testes de recursos de eventos
│   ├── social_link/               # Testes de links sociais de eventos
│   └── text/                      # Testes de texto de eventos
├── tests/                         # Testes gerais do backend
└── utils/tests/                   # Testes de utilitários
```

### Como Executar os Testes do Backend

```bash
# Formatar, lint e verificação de tipos:
ruff format ./backend
ruff check ./backend
mypy ./backend --config-file ./backend/pyproject.toml

# Iniciar o container Docker:
docker compose --env-file .env.dev up backend --build -d

# Entrar no container backend:
docker exec -it django_backend sh

# Executar todos os testes:
pytest

# Executar um teste específico:
pytest path/to/test_file.py::test_function

# Executar com relatório de cobertura:
pytest --cov --cov-report=term-missing --cov-config=pyproject.toml -vv
```

---

## Testes do Frontend

Os testes do frontend estão localizados no diretório `frontend/` e utilizam **Vitest** (testes de componente/unidade) e **Playwright** (testes E2E).

### Estrutura Principal

```
frontend/
├── test/                          # Testes de componentes e unidade (Vitest)
│   ├── components/
│   │   ├── dropdown/              # Testes de componentes dropdown
│   │   ├── filter/                # Testes de componentes filter
│   │   ├── form/                  # Testes de componentes form
│   │   └── image/                 # Testes de componentes image
│   ├── composables/               # Testes de composables
│   └── pages/
│       └── auth/                  # Testes de páginas de autenticação
├── test-e2e/                      # Testes End-to-End (Playwright)
│   ├── accessibility/             # Testes de acessibilidade
│   ├── actions/                   # Ações reutilizáveis para testes E2E
│   ├── component-objects/         # Objetos de componentes para E2E
│   ├── page-objects/              # Objetos de página para E2E
│   └── specs/
│       ├── all/                   # Testes que rodam em desktop e mobile
│       ├── desktop/               # Testes apenas para desktop
│       └── mobile/                # Testes apenas para mobile
├── test-utils/                    # Utilitários para testes
└── tests/                         # Testes adicionais
    └── specs/
```

### Tipos de Testes no Frontend

#### 1. Testes de Componentes e Unidade
- **Localização**: `frontend/test/`
- **Framework**: Vitest + Vue Testing Library
- **Descrição**: Testes unitários de componentes Vue, composables e funções

#### 2. Testes End-to-End (E2E)
- **Localização**: `frontend/test-e2e/`
- **Framework**: Playwright
- **Descrição**: Testes de integração completa da aplicação (frontend + backend + banco de dados)

### Como Executar os Testes do Frontend

#### Testes de Componentes/Unidade (Vitest)
```bash
cd frontend
npm run test         # Executar testes
npm run test:ui      # Interface visual dos testes
```

#### Testes E2E (Playwright)
```bash
cd frontend
npm run test:e2e     # Executar testes E2E
```

---

## Documentação de Testes

O repositório possui documentação detalhada sobre como escrever e executar testes:

### Documentos Principais

1. **BACKEND_TESTING.md**
   - Padrões para testes do backend
   - Convenções de nomenclatura
   - Estrutura de diretórios de testes

2. **FRONTEND_TESTING.md**
   - Guia de testes E2E
   - Guia de testes de componentes e unidade
   - Melhores práticas para testes frontend

3. **CONTRIBUTING.md** (Seção de Testes)
   - Como executar testes do backend
   - Como executar testes do frontend
   - Como executar testes E2E localmente e remotamente

---

## 📊 Resumo Estatístico

### Backend
- **Framework**: pytest
- **Diretórios de testes**: 8 diretórios principais
- **Subdiretórios de testes**: ~40+ subdiretórios
- **Arquivos de teste**: 119 arquivos (test_*.py)
- **Tipos de testes**: Testes de API, testes de modelos, testes de serializers

### Frontend
- **Frameworks**: Vitest (componentes/unidade) + Playwright (E2E)
- **Diretórios de testes**: 4 diretórios principais
- **Arquivos de teste**: 26 arquivos (*.test.ts, *.spec.ts)
- **Tipos de testes**: 
  - Testes de componentes
  - Testes de composables
  - Testes de páginas
  - Testes E2E (desktop, mobile, todos)
  - Testes de acessibilidade

---

## 🔍 Comandos Úteis para Encontrar Testes

```bash
# Encontrar todos os diretórios de testes:
find . -type d -name "test*"

# Encontrar todos os arquivos de teste Python:
find . -name "test_*.py"

# Encontrar todos os arquivos de teste TypeScript/JavaScript:
find . -name "*.test.ts" -o -name "*.test.js" -o -name "*.spec.ts"

# Contar quantidade de arquivos de teste no backend:
find backend -name "test_*.py" | wc -l

# Contar quantidade de arquivos de teste no frontend:
find frontend -name "*.test.ts" -o -name "*.spec.ts" | wc -l
```

---

## 📚 Referências

- [CONTRIBUTING.md](./CONTRIBUTING.md#testing-) - Guia de contribuição com instruções de testes
- [BACKEND_TESTING.md](./BACKEND_TESTING.md) - Documentação completa de testes backend
- [FRONTEND_TESTING.md](./FRONTEND_TESTING.md) - Documentação completa de testes frontend
