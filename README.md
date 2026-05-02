# Leo Vidros — Sistema de Gestão Empresarial

Sistema desenvolvido de **agosto de 2025 a julho de 2026** por estudantes e profissionais de TI, com o objetivo de digitalizar e automatizar os processos de uma empresa do ramo de vidraçaria com CNPJ ativo há mais de 5 anos.

O projeto nasceu de uma necessidade real: transformar uma operação consolidada no mercado — que ainda dependia de processos manuais e planilhas — em uma empresa com gestão digitalizada, rastreável e eficiente.

---

## O que o sistema resolve

- Cadastro e acompanhamento de clientes, funcionários e fornecedores
- Controle de estoque com histórico de movimentações
- Pedidos de produtos e serviços com fluxo completo de status
- Agendamento de serviços vinculado a funcionários e etapas
- Geração automática de orçamentos em PDF via fila de mensagens
- Importação de dados em massa via planilhas Excel
- Dashboard com KPIs e gráficos de desempenho
- Localização geográfica de clientes no mapa
- Infraestrutura em nuvem provisionada com Terraform na AWS

---

## Repositórios

### [`leo-vidros-app`](./leo-vidros-app)
**Frontend — React + Vite**

Interface web da aplicação. Consome a API principal e os microserviços via Axios, com gerenciamento de cache por TanStack Query, formulários validados com React Hook Form + Zod, e estilização com Tailwind CSS e Material UI.

Principais módulos: dashboard, clientes, pedidos (produto e serviço), agendamentos, estoque, orçamentos, funcionários, geolocalização e autenticação JWT.

---

### [`leo-vidros-api`](./leo-vidros-api)
**Backend principal — Java 21 + Spring Boot 3.5.4**

API REST que centraliza as regras de negócio da aplicação. Autenticação stateless com JWT, ORM via Spring Data JPA com banco MySQL, comunicação assíncrona com RabbitMQ e armazenamento de arquivos no AWS S3.

Padrões utilizados: Strategy (pedidos e agendamentos), Doc/Impl para controllers com documentação Swagger, MapStruct para mapeamento de DTOs e Flyway para versionamento do schema.

Módulos: auth, cliente, funcionario, pedido, agendamento, estoque, orçamento, solicitação, dashboard e usuário.

---

### [`leo-vidros-microservice-orcamento`](./leo-vidros-microservice-orcamento)
**Microserviço de geração de PDFs — Spring Boot 4.0.2**

Responsável exclusivamente por gerar os PDFs de orçamento. Consome mensagens da fila RabbitMQ publicadas pela API principal, gera o documento com OpenPDF e publica o resultado de volta na fila de resposta, que notifica o frontend via SSE (Server-Sent Events).

---

### [`leo-vidros-microservice-etl`](./leo-vidros-microservice-etl)
**Microserviço de importação Excel — Spring Boot 3.5.4**

Processa planilhas `.xlsx` enviadas pelo usuário (Apache POI), extrai os dados e os envia para a API principal para criação ou atualização de produtos e estoque em lote, registrando as movimentações no histórico.

---

### [`leo-vidros-database`](./leo-vidros-database)
**Schema e scripts de banco de dados — MySQL 8.0**

Contém o schema SQL completo com todas as tabelas e relacionamentos, scripts de seed para dados iniciais, e um script Python automatizado para backup do banco diretamente no AWS S3 com agendamento via cron.

---

### [`leo-vidros-infra`](./leo-vidros-infra)
**Infraestrutura como código — Terraform + AWS**

Provisionamento completo da infraestrutura na AWS utilizando Terraform com separação por módulos reutilizáveis e ambientes (`dev` / `prod`). Inclui VPC, subnets, Internet Gateway, NAT Gateway, instâncias EC2, grupos de segurança, NACLs e buckets S3 para backups de banco de dados e PDFs.

---

## Arquitetura geral

```
┌─────────────────────────────────────────────────┐
│                  leo-vidros-app                 │
│              React + Vite (porta 5173)          │
└────────────────────┬────────────────────────────┘
                     │ HTTP / SSE
          ┌──────────┴──────────┐
          │                     │
┌─────────▼────────┐  ┌─────────▼──────────┐
│  leo-vidros-api  │  │  microservice-etl  │
│  Spring Boot     │  │  Spring Boot       │
│  (porta 8080)    │  │  (porta 8081)      │
└─────────┬────────┘  └────────────────────┘
          │ RabbitMQ
┌─────────▼───────────────┐
│ microservice-orcamento  │
│ Spring Boot (porta 8082)│
└─────────────────────────┘

Infraestrutura: MySQL 8.0 · RabbitMQ 3.13 · AWS S3 · EC2 (Terraform)
```

---

## Como rodar o projeto

Consulte o `README.md` de cada repositório para instruções detalhadas. Para subir o ambiente completo localmente com Docker:

```bash
# Clone todos os repositórios na mesma pasta
# Copie o .env.example para .env e preencha as variáveis

docker-compose up -d
```

Serviços disponíveis após o boot:

| Serviço | URL |
|---|---|
| Frontend | http://localhost:5173 |
| API Principal | http://localhost:8080/api |
| Swagger API | http://localhost:8080/api/swagger-ui.html |
| Microserviço ETL | http://localhost:8081/api |
| Microserviço Orçamento | http://localhost:8082/api |
| RabbitMQ Management | http://localhost:15672 |

---

## Stack resumida

| Camada | Tecnologias |
|---|---|
| Frontend | React 19, Vite 7, TanStack Query, Zod, Tailwind CSS, MUI |
| Backend | Java 21, Spring Boot 3/4, Spring Security, JPA, JWT |
| Mensageria | RabbitMQ 3.13 |
| Banco de dados | MySQL 8.0 |
| Armazenamento | AWS S3 |
| Infra | Terraform, AWS EC2, VPC |
| PDF | OpenPDF |
| Excel | Apache POI |

---

**Status:** Em desenvolvimento  
**Período:** Agosto 2025 – Julho 2026
