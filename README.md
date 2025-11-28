# Capa

- **Título do Projeto**: Oxid Gateway - Sistema de Debug em Tempo Real para API Gateway
- **Nome do Estudante**: Gabriel Eduardo da Silva Rosa
- **Curso**: Engenharia de Software

---

# Resumo

Este documento apresenta a especificação técnica do Oxid Gateway, um API Gateway desenvolvido com foco em debug em tempo real de requisições HTTP. O projeto visa solucionar problemas de configuração e troubleshooting encontrados em ambientes de produção, permitindo a ativação de breakpoints de debug para inspeção detalhada de requisições. A solução utiliza o framework Pingora (Cloudflare) para o proxy em Rust, backend em Go para gerenciamento, e frontend em Next.js para interface administrativa.

---

## 1. Introdução

### Contexto

API Gateways são componentes críticos em arquiteturas modernas de software. Em ambientes corporativos como a Weg, a implantação de gateways open-source (ex.: Kong Gateway) frequentemente resulta em desafios operacionais relacionados à configuração e troubleshooting. A ausência de ferramentas eficazes para debug em produção dificulta a identificação precisa de falhas, especialmente quando o problema está relacionado a transformações de dados ou regras de roteamento.

![Debug image](./static/debug.jpg)

### Justificativa

O projeto é relevante para o campo da engenharia de software por:

1. **Inovação Tecnológica**: Explora o framework Pingora da Cloudflare, tecnologia emergente com capacidade de processar trilhões de requisições diárias, mas ainda subexplorada no ecossistema open-source.

2. **Resolução de Problema Real**: Atende necessidade identificada na Weg, onde a falta de capacidade de debug em gateways resulta em aumento de tempo de resolução de incidentes.

3. **Observabilidade Avançada**: Propõe abordagem inovadora para debug em produção através de breakpoints ativáveis dinamicamente, permitindo inspeção detalhada de requisições sem necessidade de redeployment.

### Objetivos

**Objetivo Principal**: Desenvolver um API Gateway com capacidade de debug em tempo real de requisições HTTP/HTTPS, permitindo inspeção detalhada através de breakpoints ativáveis dinamicamente.

**Objetivos Secundários**:
- Implementar sistema de roteamento eficiente utilizando estrutura de dados Radix Tree
- Criar interface administrativa para configuração de rotas e breakpoints
- Estabelecer arquitetura de microserviços com comunicação gRPC
- Demonstrar viabilidade técnica do framework Pingora para aplicações de API Gateway

---

## 2. Descrição do Projeto

### Linha de Projeto
**Web Apps**

### Tema do Projeto

O Oxid Gateway é um API Gateway com funcionalidades de debug em tempo real. O sistema permite que desenvolvedores configurem breakpoints em rotas específicas e inspecionem detalhadamente os parâmetros, headers e body de cada requisição que passa pelo gateway, sem necessidade de parar ou reiniciar o serviço.

### Propósito e Uso Prático

**Problema Resolvido**: Quando ocorrem falhas relacionadas a configurações de gateway em produção (roteamento incorreto, transformações de headers), as equipes enfrentam dificuldades para identificar a causa raiz. Logs tradicionais oferecem visibilidade limitada, e reproduzir o problema em desenvolvimento nem sempre é viável.

**Uso Prático**:
1. **Troubleshooting em Produção**: Operadores podem ativar breakpoints em rotas específicas para capturar requisições problemáticas
2. **Validação de Configurações**: Desenvolvedores podem testar novas configurações verificando em tempo real como as requisições são processadas
3. **Identificação de Erros**: Visualização clara de onde ocorrem falhas na cadeia de processamento

### Público-Alvo

- **DevOps Engineers**: Responsáveis pela operação e manutenção do gateway
- **Desenvolvedores Backend**: Que precisam validar integrações e configurações de API
- **SREs (Site Reliability Engineers)**: Focados em troubleshooting de incidentes

### Problemas a Resolver

1. **Falta de Visibilidade**: Impossibilidade de inspecionar detalhadamente requisições em tempo real
2. **Dificuldade de Debug**: Ausência de ferramentas para identificar onde ocorrem falhas no gateway
3. **Tempo de Resolução de Incidentes**: Alto tempo gasto para diagnosticar problemas de configuração
4. **Validação de Configurações**: Necessidade de deploy e rollback para testar configurações

### Diferenciação/Ineditismo

**Diferenciais em relação a soluções existentes** (Kong Gateway, Nginx, Traefik):

1. **Debug em Tempo Real**: Capacidade única de ativar breakpoints dinâmicos sem reiniciar o serviço
2. **Performance Superior**: Utilização do Pingora (Rust), oferecendo performance superior a soluções em Lua (Kong) ou outras linguagens
3. **Visualização Interativa**: Console web com visualização em tempo real do fluxo de requisições através de WebSockets
4. **Roteamento Otimizado**: Implementação customizada de Radix Tree para roteamento de alta performance
5. **Arquitetura Híbrida**: Combinação de Rust (data plane) e Go (control plane) para balancear performance e produtividade

### Limitações

O projeto **não** abrangerá:

1. **Managed Service**: Não será oferecido como serviço gerenciado em cloud (será self-hosted)
2. **Plugin Marketplace**: Não haverá sistema extensível de plugins de terceiros
3. **Suporte a Múltiplos Protocolos**: Foco em HTTP/HTTPS; protocolos como gRPC passthrough, WebSocket proxy e TCP/UDP não serão suportados
4. **Service Mesh Features**: Não incluirá funcionalidades completas de service mesh
5. **Interface YAML**: Configuração será apenas via API/UI, não via arquivos YAML

### Normas e Legislações Aplicáveis

1. **LGPD (Lei Geral de Proteção de Dados Pessoais - Lei nº 13.709/2018)**
   - Aplicável pois o sistema captura temporariamente dados de requisições que podem conter informações pessoais
   - Será observada através de: retenção limitada de dados de debug, consentimento explícito para captura

2. **OWASP API Security Top 10**
   - Aplicável por ser um API Gateway que lida com roteamento
   - Será observada através de: validação de input, proteção básica contra injection attacks

3. **RFC 7231 (HTTP/1.1 Semantics and Content)**
   - Aplicável por implementar proxy HTTP
   - Será observada através de: conformidade com métodos HTTP, códigos de status, headers padrão

### Métricas de Sucesso

1. **Performance**:
   - Latência P99 < 10ms para requisições simples
   - Throughput mínimo de 100.000 requisições/segundo em servidor de 4 cores

2. **Funcionalidade**:
   - Captura completa de dados de requisição em breakpoints (headers, body, metadata)
   - Visualização em tempo real com latência < 500ms da requisição ao dashboard

3. **Confiabilidade**:
   - Zero perda de requisições devido a falhas de debug
   - Cobertura de testes: 75% backend, 25% frontend

---

## 3. Especificação Técnica

### 3.1. Requisitos de Software

#### Requisitos Funcionais (RF)

**RF01 - Gerenciamento de Rotas**
- O sistema deve permitir criar, editar, listar e deletar rotas de proxy
- Cada rota deve conter: path pattern, método HTTP, upstream target

**RF02 - Configuração de Breakpoints**
- O sistema deve permitir ativar/desativar breakpoints em rotas específicas
- Breakpoints devem capturar: headers, body, query parameters

**RF03 - Captura de Requisições**
- O sistema deve capturar e armazenar temporariamente requisições que atingem breakpoints
- Deve preservar: timestamp, headers completos, body, metadata de processamento

**RF04 - Visualização em Tempo Real**
- O sistema deve transmitir via WebSocket requisições capturadas para o console administrativo
- A interface deve exibir: dados da requisição, tempo de processamento

**RF05 - Roteamento Dinâmico**
- O sistema deve realizar roteamento de requisições sem necessidade de reinício
- Deve suportar: path matching com wildcards

**RF06 - Gestão de Configurações**
- O sistema deve permitir exportar/importar configurações

#### Requisitos Não-Funcionais (RNF)

**RNF01 - Escalabilidade**
- Suporte a pelo menos 1.000 rotas configuradas
- Capacidade de escalar horizontalmente (stateless proxy instances)

**RNF02 - Usabilidade**
- Interface responsiva compatível com desktop (1920x1080+)
- Tempo de aprendizado < 30 minutos para usuários técnicos

**RNF03 - Manutenibilidade**
- Código modular com separação clara de responsabilidades
- Cobertura de testes: 75% backend, 25% frontend

**RNF04 - Portabilidade**
- Suporte a deployment em: Kubernetes, Docker, bare metal
- Compatibilidade com Linux (kernel 5.4+), arquiteturas x86_64 e ARM64

#### Representação dos Requisitos

![Caso de uso](./static/use_case.png)

*Principais casos de uso:*
- Gerenciar Rotas (CRUD)
- Configurar Breakpoints
- Visualizar Requisições em Tempo Real
- Exportar/Importar Configurações

---

### 3.2. Considerações de Design

#### Visão Inicial da Arquitetura

O sistema é composto por três componentes principais:

1. **Proxy Engine (Rust + Pingora)**: Data plane responsável por processar requisições HTTP e realizar roteamento via Radix Tree

2. **Control Plane (Go)**: Gerencia configurações (rotas, breakpoints), expõe API REST/gRPC, persiste dados em PostgreSQL

3. **Admin Console (Next.js)**: Interface web para configuração e visualização em tempo real via WebSocket

**Comunicação entre componentes**:
- Control Plane ↔ Proxy: gRPC para sincronização de configurações
- Admin Console ↔ Control Plane: REST API + WebSocket
- Proxy ↔ Upstreams: HTTP/HTTPS

#### Padrões de Arquitetura

1. **Microserviços**: Separação de responsabilidades em serviços independentes
2. **API Gateway Pattern**: O próprio produto implementa este padrão
3. **Repository Pattern**: Abstração de acesso a dados no backend Go

#### Modelos C4

![C4 - 1](./static/c4_1.png)

*Elementos principais:*
- Usuário Administrador → Admin Console
- Cliente HTTP → Proxy Engine
- Proxy Engine → Upstream Services
- Admin Console → Control Plane
- Control Plane → PostgreSQL

![C4 - 2](./static/c4_2.png)

*Containers principais:*
- Admin Console (Next.js SPA)
- Control Plane API (Go Service)
- Proxy Engine (Rust Service + Pingora)
- PostgreSQL Database

![C4 - 2](./static/c4_3.png)

*Componentes principais:*
- HTTP Server (Pingora)
- Radix Tree Router
- Debug Capture Module
- gRPC Client (config sync)

#### Mockups das Telas Principais

**[TODO: Inserir Mockup - Tela de Listagem de Rotas]**

*Elementos principais:*
- Tabela com: Path, Upstream
- Botão "Nova Rota"
- Filtros: Status, Método

**[TODO: Inserir Mockup - Tela de Configuração de Rota]**

*Elementos principais:*
- Formulário: Path Pattern, Upstream
- Seção de breakpoints (ativar/desativar)
- Botões: Salvar, Testar, Cancelar

**[TODO: Inserir Mockup - Console de Debug em Tempo Real]**

*Elementos principais:*
- Detalhes: Headers, Body, Metadata

#### Decisões e Alternativas Consideradas

**Decisão 1: Uso do Pingora (Rust) para Proxy Engine**

*Alternativas consideradas*: Envoy Proxy, Nginx, Traefik, HAProxy

*Justificativa da escolha*:
- Performance superior comprovada pela Cloudflare
- Segurança de memória garantida por Rust
- Inovação: Framework relativamente novo e subexplorado
- Programabilidade: Estrutura modular permite customização

*Trade-offs*:
- Curva de aprendizado mais íngreme
- Ecossistema menor comparado a Nginx/Envoy

**Decisão 2: Uso de Radix Tree para Roteamento**

*Alternativas consideradas*: Hash Table, Regex matching, Trie Tree padrão

*Justificativa da escolha*:
- Performance O(k) onde k = comprimento do path
- Suporte nativo a wildcards e parâmetros dinâmicos
- Uso eficiente de memória

**Decisão 3: gRPC para Comunicação Control Plane ↔ Proxy**

*Alternativas consideradas*: REST API, Message Broker, Banco compartilhado

*Justificativa da escolha*:
- Performance: Protocol Buffers são mais eficientes que JSON
- Type-safety: Schema bem definido
- Streaming bidirecional para eventos em tempo real

**Decisão 4: WebSocket para Real-Time Debug**

*Alternativas consideradas*: Server-Sent Events, Long Polling

*Justificativa da escolha*:
- Comunicação bidirecional
- Baixa latência com conexão persistente
- Amplo suporte em browsers modernos

#### Critérios de Escalabilidade e Resiliência

**Escalabilidade**:

1. **Vertical**:
   - Async I/O: Pingora usa Tokio (runtime assíncrono Rust)
   - Connection pooling: Reutilização de conexões com upstreams

**Resiliência**:

1. **Tolerância a Falhas**:
   - Graceful shutdown: Drain de conexões antes de parar
   - Hot reload: Atualização de configurações sem downtime

2. **Isolamento**:
   - Resource limits via cgroups (Docker/Kubernetes)
   - Timeouts configuráveis para evitar travamentos

---

### 3.3. Stack Tecnológica

#### Linguagens de Programação

**Rust (Proxy Engine)**
- Versão: 1.75+
- Justificativa: Performance próxima a C/C++, segurança de memória, zero-cost abstractions

**Go (Control Plane)**
- Versão: 1.22+
- Justificativa: Produtividade para CRUD APIs, excelente tooling, concorrência simplificada

**TypeScript (Frontend)**
- Versão: 5.3+
- Justificativa: Type safety, melhor IDE support, compatibilidade com React/Next.js

#### Frameworks e Bibliotecas

**Backend (Rust)**
- **Pingora** (0.3+): Framework da Cloudflare para proxies HTTP
- **Tokio** (1.35+): Runtime assíncrono
- **Tonic** (0.11+): Framework gRPC
- **Serde** (1.0+): Serialização/deserialização

**Backend (Go)**
- **Gin** (1.9+): Web framework
- **GORM** (1.25+): ORM
- **go-grpc** (1.60+): Framework gRPC

**Frontend (Next.js)**
- **Next.js** (14+): Framework React
- **Tailwind CSS** (3.4+): Framework CSS
- **Shadcn/ui**: Biblioteca de componentes

**Infraestrutura**
- **PostgreSQL** (16+): Banco de dados relacional

#### Ferramentas de Desenvolvimento e Gestão

**Desenvolvimento**
- **NeoVim** + **rust-analyzer**: IDE para Rust
- **NeoVim** / **gopls**: IDE para Go

**Testes**
- **cargo test**: Framework de testes Rust
- **go test** + **testify**: Framework de testes Go
- **Jest** + **React Testing Library**: Testes JavaScript/TypeScript

**Qualidade de Código**
- **clippy**: Linter Rust
- **golangci-lint**: Linter Go
- **ESLint** + **Prettier**: Linter JavaScript/TypeScript

**Containerização**
- **Docker** (24+): Containerização
- **Docker Compose**: Orquestração local

#### Licenciamento

| Software/Biblioteca | Licença | Compatibilidade Comercial |
|---------------------|---------|---------------------------|
| Pingora | Apache 2.0 | ✅ Sim |
| Tokio | MIT | ✅ Sim |
| Go | BSD 3-Clause | ✅ Sim |
| Gin | MIT | ✅ Sim |
| Next.js | MIT | ✅ Sim |
| PostgreSQL | PostgreSQL License | ✅ Sim |
| Prometheus | Apache 2.0 | ✅ Sim |
| Tailwind CSS | MIT | ✅ Sim |

---

### 3.4. Considerações de Segurança

#### Riscos Identificados

**R01 - Injeção de Código**
- **Descrição**: Entrada maliciosa em campos de configuração
- **Impacto**: Alto
- **Probabilidade**: Média

**R02 - Vazamento de Dados Sensíveis em Logs**
- **Descrição**: Tokens ou dados pessoais capturados em breakpoints podem vazar
- **Impacto**: Alto
- **Probabilidade**: Alta

#### Medidas de Mitigação

**M01 - Proteção contra Injeção**
- ORM com prepared statements (GORM)
- Validação e sanitização de inputs

**M02 - Proteção de Dados Sensíveis**
- Redação automática de headers sensíveis (Authorization, Cookie) em logs
- TTL: dados de debug expiram após 24h automaticamente

**M03 - Proteção de Comunicação**
- TLS obrigatório em todas as conexões

#### Normas e Boas Práticas Seguidas

**OWASP API Security Top 10**
- Validação de input em todas as entradas

**LGPD**
- Retenção limitada (24h)
- Minimização de dados capturados

**RFC 7231 (HTTP/1.1)**
- Conformidade com métodos HTTP padrão
- Uso correto de códigos de status
- Headers padrão preservados

---

### 3.5. Conformidade e Normas Aplicáveis

#### LGPD – Lei Geral de Proteção de Dados

**Aplicabilidade**: O sistema captura temporariamente requisições HTTP que podem conter dados pessoais.

**Medidas de Conformidade**:

1. **Minimização de Dados**: Capturar apenas quando breakpoint ativo
2. **Consentimento**: Checkbox ao ativar breakpoint
3. **Retenção Limitada**: TTL de 24 horas para dados de debug

### Definição de Marcos

| Marco | Data | Critério de Aceitação |
|-------|------|----------------------|
| M1 - Arquitetura Definida | Semana 3 | Diagramas C4, schemas de banco |
| M2 - Proxy Funcional | Semana 6 | Roteamento básico, 100 req/s |
| M3 - Control Plane Integrado | Semana 9 | CRUD de rotas, sincronização gRPC |
| M4 - MVP Completo | Semana 12 | UI funcional, deploy staging |
| M5 - Debug System Live | Semana 21 | Breakpoints funcionais, WebSocket |
| M6 - Production Ready | Semana 27 | Deploy produção, documentação |
| M7 - Demo Day | Semana 30 | Apresentação, pôster, vídeo |

---

## 5. Links e Recursos

### Repositórios

**Proxy Engine (Rust)**
- Repositório: [Link do GitHub](https://github.com/oxid-gateway/reverse-proxy)
- Branch principal: `main`

**Control Plane (Go)**
- Repositório: [Link do GitHub](https://github.com/oxid-gateway/admin-api/tree/main)
- Branch principal: `main`

**Admin Console (Next.js)**
- Repositório: [Link do GitHub](https://github.com/oxid-gateway/console-ui/tree/main)
- Branch principal: `main`

### Acesso

- URL: [URL de acesso]
- Vídeo de demonstração: [Vídeo no youtube]

### Banner

![Banner](./static/banner.png)

---

## 6. Guia de Execução dos Componentes

### Pré-requisitos

- **Docker** e **Docker Compose** instalados
- **Rust** 1.75+ (para desenvolvimento do proxy)
- **Go** 1.22+ (para desenvolvimento do backend)
- **Node.js** 18+ (para desenvolvimento do frontend)
- **Git** para clonar repositórios

### 1. Proxy Engine (Rust + Pingora)

#### Setup Inicial

```bash
# Clonar repositório
git clone https://github.com/oxid-gateway/reverse-proxy.git
cd reverse-proxy

# Instalar dependências Rust (se não tiver)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Build do projeto
cargo build --release
```

#### Executar

```bash
# Modo produção
cargo run --release
```

#### Testes

```bash
# Rodar todos os testes
cargo test

# Testes com cobertura
cargo tarpaulin --out Html

# Lint
cargo clippy -- -D warnings
```

### 2. Control Plane (Go)

#### Setup Inicial

```bash
# Clonar repositório
git clone https://github.com/oxid-gateway/admin-api.git
cd admin-api

# Instalar dependências
go mod download
```

#### Configuração

Criar arquivo `.env` na raiz:
```env
DATABASE_URL="postgresql://admin:admin@localhost:5432/oxidgateway"
```

#### Setup do Banco de Dados

```bash
# Subir PostgreSQL via Docker
docker-compose up
```

#### Executar

```bash
# Modo desenvolvimento
go run .
```

#### Testes

```bash
# Rodar todos os testes
go test ./...

# Testes com cobertura
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out

# Lint
golangci-lint run
```

### 3. Admin Console (Next.js)

#### Setup Inicial

```bash
# Clonar repositório
git clone https://github.com/oxid-gateway/console-ui.git
cd oxid-gateway-console

# Instalar dependências
npm install
```

#### Executar

```bash
# Modo desenvolvimento
npm run dev
```

#### Testes

```bash
# Rodar testes
npm run test

# Testes com cobertura
npm run test:coverage

# Lint
npm run lint
```

## 4. Referências

### Frameworks e Bibliotecas

1. **Cloudflare Pingora**. Disponível em: https://github.com/cloudflare/pingora

2. **Tokio - Asynchronous Rust Runtime**. Disponível em: https://tokio.rs

3. **Gin Web Framework**. Disponível em: https://gin-gonic.com

4. **Next.js Documentation**. Disponível em: https://nextjs.org/docs

5. **PostgreSQL Official Documentation**. Disponível em: https://www.postgresql.org/docs

### Padrões e Arquitetura

6. **C4 Model for Visualising Software Architecture**. BROWN, Simon. Disponível em: https://c4model.com

7. **gRPC Official Documentation**. Disponível em: https://grpc.io/docs

### Segurança

8. **OWASP API Security Top 10 (2023)**. Disponível em: https://owasp.org/API-Security/

9. **RFC 7231 - HTTP/1.1 Semantics**. IETF. Disponível em: https://datatracker.ietf.org/doc/html/rfc7231

### Legislação

10. **BRASIL. Lei nº 13.709/2018**. Lei Geral de Proteção de Dados Pessoais (LGPD). Disponível em: https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm

### Estruturas de Dados

11. **Radix Tree Implementation**. MORRISON, Donald R. "PATRICIA—Practical Algorithm To Retrieve Information Coded in Alphanumeric". Journal of the ACM, v. 15, n. 4, 1968.

---

## 8. Apêndices

### Apêndice A - Glossário Técnico

**API Gateway**: Servidor que atua como ponto de entrada único para um conjunto de microserviços, oferecendo roteamento e outras funcionalidades.

**Breakpoint**: Ponto de captura configurável que registra detalhes de uma requisição HTTP para debug.

**Data Plane**: Componente que processa requisições (proxy Rust).

**Control Plane**: Componente que gerencia configurações (API Go).

**gRPC**: Framework RPC que utiliza Protocol Buffers para serialização.

**Pingora**: Framework Rust da Cloudflare para proxies HTTP.

**Radix Tree**: Estrutura de dados tipo árvore otimizada para busca de strings.

**Upstream**: Serviço de backend para o qual o gateway encaminha requisições.

**WebSocket**: Protocolo de comunicação full-duplex para streaming em tempo real.
