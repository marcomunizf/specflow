# Arquitetura do Sistema
Este documento descreve a arquitetura técnica do projeto, detalhando como cada parte do sistema é organizada, quais tecnologias são utilizadas em cada camada e como os componentes se comunicam entre si.

A arquitetura define a estrutura fundamental do software, servindo como:
- guia para implementação do front-end, back-end e banco de dados,
- referência para agentes de IA,
- base para desenvolvimento consistente e padronizado,
- contrato de comunicação entre camadas,
- documento de alinhamento entre especificação (SPEC), API Routes e Database Schema.

Este arquivo NÃO descreve regras de negócio, mas sim **como** o sistema será construído tecnicamente para atender a essas regras.

Ele contém:

1. **Diagrama da Arquitetura Geral**  
   Representação visual das camadas (Front-end, Back-end e Database) e como elas interagem usando HTTP, JSON e SQL.

2. **Definições Tecnológicas por Camada**  
   - Front-end (React, JS/TS, Axios/Fetch, CSS)  
   - Back-end (Node.js, Express, camadas internas, middlewares, JSON API)  
   - Database (PostgreSQL, schema, tabelas, constraints, migrações)

3. **Padrões de Implementação**  
   Convenções de código, organização de diretórios, formato de mensagens, padrões REST e requisitos técnicos transversais.

Este arquivo deve ser consultado sempre que:
- for criada uma nova feature,  
- houver dúvidas sobre como estruturar código,  
- forem introduzidas tecnologias no projeto,  
- os agentes de IA precisarem entender como gerar ou alterar código,  
- houver necessidade de alinhar front, back e banco em uma única visão.

A arquitetura aqui descrita deve ser seguida em TODO o ciclo de desenvolvimento.

---
## Diagrama da Arquitetura Geral
```text
┌───────────────────────────────────────────────────────────────┐
│                          FRONT-END                            │
│  ▸ React (SPA)                                                │
│  ▸ Linguagem: JavaScript  (ou TypeScript, se adotado)         │
│  ▸ HTTP client: Axios (ou Fetch API)                          │
│  ▸ Formato de dados: JSON                                     │
│  ▸ Estilos: CSS / CSS Modules / Tailwind                      │
└───────────────▲───────────────────────────────────────────────┘
                │
                │  Requisições HTTP (JSON)                      
                │  Ex.: GET /api/items, POST /api/auth/login    
                ▼
┌───────────────────────────────────────────────────────────────┐
│                           BACK-END                            │
│                    Node.js + Express                          │
│                                                               │
│  Linguagem: JavaScript (ou TypeScript, se adotado)            │
│                                                               │
│  Camadas internas:                                            │
│  ▸ /src/middlewares                                           │                       
│  ▸ /src/routes                                                │
│  ▸ /src/controllers                                           │
│  ▸ /src/services                                              │
│  ▸ /src/repositories                                          │
│  ▸ /src/config                                                │
└─────────────────┬─────────────────────────────────────────────┘
                  │
                  │  Consultas SQL / ORM                        
                  │  Ex.: SELECT, INSERT, UPDATE, DELETE        
                  ▼
┌───────────────────────────────────────────────────────────────┐
│                           DATABASE                            │
│                         PostgreSQL                            │
└───────────────────────────────────────────────────────────────┘
```

---

## Front-end

- **Framework principal:**  
  - Usar **React** para construção da interface (componentes reutilizáveis, SPA). 

- **Linguagem:**
  - Adotar **JavaScript** como padrão.
  - Avaliar o Business Plan, e as SPEC do projeto para definir se compensa adotar **TypeScript** (JavaScript tipado) para maior segurança, legibilidade e melhor suporte a agentes de IA e refatorações futuras.
  - Se **TypeScript** for mais indicado, utilizar **TypeScript**


- **Comunicação com o Back-end:**  
  - Fazer requisições HTTP utilizando **Axios** (sobre a Fetch API), pela facilidade de:
    - tratar erros,
    - trabalhar com interceptors (ex.: anexar token JWT automaticamente),
    - já retornar JSON por padrão.
  - Todas as requisições devem consumir a API REST exposta em `/api/...`, retornando e enviando **JSON**


- **Formato de dados:**  
  - Enviar e receber dados exclusivamente em **JSON**, seguindo os contratos definidos nas rotas da API
  - Se o projeto utilizar apenas **JavaScript**, avaliar uso de **JSDoc** para documentar tipos críticos (ex.: payloads de API)
  - Se TypeScript for adotado, definir **types/interfaces TypeScript** para:
    - requests
    - responses


- **Autenticação no front:**  
  - Armazenar o token JWT retornado pelo endpoint de login (em `localStorage` ou `sessionStorage`, ou em memória dependendo da estratégia de segurança).
  - Avaliar, em versões futuras, uso de **cookies HTTP-only** para maior segurança do token, dependendo do nível de risco e requisitos de segurança do projeto.
  - Em cada chamada autenticada, enviar o header:
    - `Authorization: Bearer <token>`.


- **Estilização (UI):**
  - Usar **CSS** (pode ser CSS Modules ou uma lib utilitária como TailwindCSS) para controlar layout, cores e responsividade.


- **Organização do código front-end:**
  - Estruturar pastas por domínio ou por tipo (ex.: `components/`, `pages/`, `services/api/`, `hooks/`, `styles/`), sempre alinhado aos domínios definidos na SPEC


- **Build e Dev Server:**
  - Usar **Node.js** apenas como ambiente de desenvolvimento (Vite/CRA/Next, bundler, TypeScript compiler), sem rodar Node no navegador.

---

## Back-end

- **Linguagem:**
  - Usar **JavaScript** ou **TypeScript** no backend conforme definido no front-end
  - Caso **TypeScript** seja adotado, seguir convenções de:
    - `ts-node` ou build com `tsc`
    - tipagem em services/repositories
    - configuração de `tsconfig.json`


- **Plataforma e framework:**
  - Utilizar **Node.js** com **Express** como base do servidor HTTP.


- **Arquitetura interna (camadas):**   
  - Estruturar o código conforme:
    - `/src/routes` – definição das rotas HTTP.
    - `/src/controllers` – recebem `req/res`, chamam services, retornam JSON.
    - `/src/services` – regras de negócio, orquestram repositórios.
    - `/src/repositories` – acesso ao banco (PostgreSQL).
    - `/src/middlewares` – autenticação, autorização, logs, validação, tratamento de erros.
    - `/src/utils` – helpers, formatação, geração de token, etc.
    - `/src/config` – configurações (banco, env, logs).
    - Arquivos raiz `app.(js|ts)` (configura o Express) e `server.(js|ts)` (sobe o servidor).


- **Avaliar uso de:**
  - **Rate limiting** (ex.: `express-rate-limit`) para proteger contra abuso.
  - **Helmet** para aplicar headers de segurança padrão em todas as respostas HTTP.


- **Padrão de API / JSON:**  
  - Todas as rotas seguem padrão **REST** com base URL `/api`.
  - Todas as respostas e erros seguem o formato **JSON** padronizado, por exemplo:
    ```json
    {
      "error": "RESOURCE_NOT_FOUND",
      "message": "Recurso não encontrado."
    }
    ```
  - Usar `express.json()` para parse de corpo JSON.


- **Domínios principais de rotas (MVP):**   
  - `/api/auth` – autenticação (login → retorna JWT).
  - `/api/users` – CRUD de usuários (acesso restrito a ADMIN).
  - `/api/items` – CRUD da entidade principal (acesso a usuários autenticados).


- **Middlewares (Express):**  
  - **Autenticação:** validar token JWT e anexar `req.user`.
  - **Autorização:** checar perfil/permissões (ex.: ADMIN).
  - **Tratamento global de erros:** capturar erros e converter em respostas JSON conforme tabela de status codes.
  - **Logs:** logar requisições, respostas e erros.
  - **CORS:** configurar origens autorizadas.


- **Conexão com o banco de dados:**   
  - Usar driver `pg` ou um ORM/query builder (Prisma, Sequelize, TypeORM) apontando para o PostgreSQL.
  - Centralizar a conexão em `/config/database.ts` com um `Pool` reutilizável.


- **Alinhamento com SPEC e API Routes:**
  - Serviços e repositórios implementam exatamente os **Requisitos Funcionais** descritos na SPEC (auth, CRUD users, CRUD items).
  - Rotas e payloads seguem o contrato de routes.


- **Infra (execução):**
  - Rodar o backend via **Docker** (serviço da API + serviço do PostgreSQL usando `docker-compose`).   

---

## Database

- **Tecnologia:**
  - Utilizar **PostgreSQL** como banco de dados relacional padrão do projeto.


- **Schema e convenções:** 
  - Definir um schema principal (ex.: `app` ou `public`).
  - Utilizar padrão de nomes `snake_case` para tabelas e colunas.
  - Manter tabelas principais alinhadas ao **modelo de domínio** da SPEC (ex.: `usuarios`, `items`, etc). 


- **Estrutura das tabelas (exemplos):**
  - Tabela `usuarios` com campos:
    - `id` (PK, `SERIAL` ou `UUID`),
    - `nome`, `usuario`, `senha_hash`, `perfil`, `status`,
    - `created_at`, `updated_at`.
  - Tabela `items` com campos:
    - `id` (PK),
    - `nome`, `descricao`, `status`,
    - `created_at`, `updated_at`.


- **Padrão de timestamps:**
  - `created_at` com `DEFAULT NOW()`.
  - `updated_at` atualizado via:
    - trigger no banco, ou
    - lógica na aplicação (services/repositories).


- **Chaves e relacionamentos:**
  - Definir **Primary Keys (PK)** em todas as tabelas.
  - Definir **Foreign Keys (FK)** quando houver relacionamento entre entidades (ex.: se futuramente `items` pertencerem a um usuário, `id_usuario` referenciando `usuarios(id)`).
  - Usar **constraints** para garantir integridade (ex.: `CHECK` para status válidos, `UNIQUE` para `usuario`).


- **Índices e performance:**
  - Criar índices para campos com busca frequente (ex.: `usuario`, `status`).
  - Considerar views (`VIEW`) para consultas agregadas ou relatórios.


- **Estratégia de exclusão:**
  - Definir se as deleções serão:
    - **lógicas** (ex.: `status = 'inativo'`), ou
    - **físicas** (`DELETE`).
  - Garantir que a SPEC e a API deixem isso explícito para evitar comportamento ambíguo.


- **Migrações:**
  - Usar ferramenta de migração (ex.: Flyway, Prisma migrations, Sequelize migrations).  
  - Nomear arquivos seguindo convenção (ex.: `V001__create_usuarios.sql`, `V002__create_items.sql`). 


- **Alinhamento com o restante da arquitetura:**
  - Todo o schema deve refletir as entidades e regras descritas na SPEC e na Arquitetura (modelo de domínio → tabelas físicas).   

---


