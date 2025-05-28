# Oxid Gateway

**Um API Gateway moderno e Cloud Native**

---

**Nome do Estudante:** Gabriel Eduardo da Silva Rosa  
**Curso:** Engenharia de Software

---

## Introdução

O crescimento de sistemas distribuídos e integrações complexas entre APIs aumentou a demanda por gateways eficientes, extensíveis e seguros. Soluções existentes apresentam limitações em performance, extensibilidade ou complexidade de configuração.

**Oxid Gateway** é uma proposta de solução moderna, utilizando tecnologias atuais e open source para preencher lacunas não atendidas por soluções do mercado. O projeto se alinha aos princípios da engenharia de software, como modularidade, escalabilidade e manutenção.

Focando em pontos que não parecem ter sido atacados por outras soluções como: Debug a nível de Proxy, tradução a nível de Proxy, capacidade de lidar com número de usuários na casa dos milhões e fazer gestão de permissões utilizando o padrão OPA.

---

## Objetivos

**Principal:**  
Desenvolver um API Gateway eficiente, extensível e de código aberto, com recursos como cache, controle granular de permissões e ferramentas de observabilidade.

**Secundários:**  
Promover uma arquitetura Cloud Native, utilizar tecnologias emergentes como Pingora, e oferecer uma experiência de desenvolvedor completa com frontend e documentação.

---

## Descrição do Projeto

Desenvolvimento de um sistema API Gateway com backend em Go (Fuego), frontend em Next.js (shad cn), proxy em Rust (Pingora) e suporte a plugins de cache.

---

## Problemas a Resolver

- Ausência de gateways modernos com arquitetura cloud native.
- Falta de flexibilidade/extensibilidade em soluções existentes.
- Necessidade de uma interface intuitiva para gestão e debug de APIs.
- Baixa performance de algumas soluções atuais sob alta carga (trilhões).
- Falta de governança nas soluções, mais especificamente *self management*.
- Falta de um API Gateway que centralize a tradução.
- Padronizar o processo de permissões com OPA.

---

## Limitações

- O projeto não implementa funcionalidades não explicitamente citadas e detalhadas nos requisitos técnicos definidos.
- Não serão abordadas integrações específicas com sistemas legados fora do escopo HTTP/Rest.

---

## Requisitos Funcionais (RF)

- **REQ01**: O sistema deve possuir um serviço de proxy reverso escrito em Rust utilizando o framework Pingora.
- **REQ02**: O backend deve ser desenvolvido em Golang e responsável pela gestão do proxy.
- **REQ03**: O frontend deve ser construído em Next.js, permitindo gerenciamento via interface gráfica.
- **REQ04**: A gestão de rotas deve utilizar uma estrutura de dados baseada em Radix Tree.
- **REQ05**: O sistema deve expor uma API gRPC para controle de rotas e plugins.
- **REQ06**: O sistema deve expor métricas sobre o consumo das rotas.
- **REQ07**: Deve existir um endpoint de debug das rotas configuradas.
- **REQ08**: As respostas das rotas devem ser armazenadas em cache.
- **REQ09**: O sistema deve realizar *health checks* nos servidores upstream.
- **REQ10**: A persistência de dados deve ser realizada em PostgreSQL utilizando SQLC.
- **REQ11**: O sistema deve implementar autenticação e autorização baseadas em RBAC e IBAC.
- **REQ12**: A documentação da API deve estar disponível em formato OpenAPI.
- **REQ13**: A interface deve possuir recursos para debug de rotas.
- **REQ14**: O sistema deve suportar um plugin de autenticação baseado em chave (key-auth).
- **REQ15**: O sistema deve suportar um plugin de ACL.
- **REQ16**: O sistema deve suportar um plugin para tradução de propriedades JSON.
- **REQ17**: O sistema deve oferecer funcionalidades de CRUD para rotas, upstreams, targets e serviços.
- **REQ18**: O sistema deve suportar um plugin de bilhetagem baseado em hits das rotas.
- **REQ19**: O Redis deve ser utilizado como mecanismo de cache.
- **REQ20**: A arquitetura do sistema deve seguir princípios cloud native.
- **REQ21**: O sistema não deve conter funcionalidades além das descritas neste documento.

---

## Requisitos Não-Funcionais (RNF)

- **REQ22**: O sistema deve apresentar alto desempenho e baixa latência em sua operação.
- **REQ23**: O sistema deve permitir escalabilidade horizontal.
- **REQ24**: O sistema deve seguir o princípio de segurança por padrão, com suporte a TLS e autenticação.
- **REQ25**: O sistema deve ser observável, incluindo logs, métricas e *tracing* distribuído.
- **REQ26**: O sistema deve ser modular e extensível por meio de plugins.
- **REQ27**: A interface gráfica deve ser amigável e responsiva.
- **REQ28**: O sistema deve ser compatível com ambientes Kubernetes e Docker.

---

## Arquitetura da Aplicação

---

## Stack Tecnológica

- Proxy em Rust (Pingora)
- Backend em Go com SQLC e PostgreSQL
- Frontend Next.js com shad/cn
- Redis para cache e rate limiting
- PostgreSQL como banco de dados relacional
- Docker e Kubernetes
- Grafana/Prometheus para métricas
- gRPC e OpenAPI para comunicação
- Git e GitHub para versionamento
- CI/CD com GitHub Actions
- OPA como motor de política
- Plugins como estratégia de extensão

---

## Considerações de Segurança

- Uso de TLS por padrão nas conexões
- Autenticação por chave (Key-Auth)
- Autorização RBAC/IBAC
- Validação de políticas via OPA
- Monitoramento e alertas para falhas
- Proteção contra *rate limit abuse* com Redis
