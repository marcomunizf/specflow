# 04 — Tech Stack do Projeto

Este documento descreve **as tecnologias, ferramentas e bibliotecas adotadas**
para implementar a arquitetura definida em `03-Architecture.md`.

Ele representa as **decisões técnicas oficiais do projeto** e serve como referência
para desenvolvedores humanos e agentes de IA durante a fase de implementação.

---

## Princípios de Escolha

- Preferência por tecnologias maduras e amplamente utilizadas.
- Clareza e simplicidade antes de complexidade desnecessária.
- Boa integração com automação, testes e agentes de IA.
- Facilidade de manutenção e evolução.

---

## Front-end

- **Framework:** React
- **Linguagem:** JavaScript  
  - TypeScript pode ser adotado se o projeto exigir maior segurança e tipagem.
- **Cliente HTTP:** Axios
- **Formato de dados:** JSON
- **Estilização:** CSS, CSS Modules ou TailwindCSS
- **Gerenciamento de build/dev:** ferramentas modernas de bundling e dev server
- **Ambiente de execução:** navegador (Node.js apenas para build)

---

## Back-end

- **Plataforma:** Node.js
- **Framework HTTP:** Express
- **Linguagem:** JavaScript ou TypeScript
- **Formato de comunicação:** JSON sobre HTTP
- **Autenticação:** JWT
- **Validação de dados:** bibliotecas de validação apropriadas
- **Arquitetura interna:** camadas conforme definido em `03-Architecture.md`

---

## Banco de Dados

- **Banco relacional:** PostgreSQL
- **Acesso ao banco:**
  - SQL direto ou ORM/query builder
- **Migrações:** ferramenta dedicada para versionamento de schema
- **Estratégia de exclusão:** lógica ou física conforme SPEC
- **Padrão de nomes:** snake_case

---

## Infraestrutura

- **Containerização:** Docker
- **Orquestração local:** Docker Compose
- **Isolamento:** backend e banco em containers distintos
- **Configuração:** variáveis de ambiente (.env)


---

## Docker e Containerização

O projeto deve utilizar **Docker** e **Docker Compose** como padrão de containerização para todos os ambientes (desenvolvimento, homologação e produção, quando aplicável).

### Objetivos do uso de Docker

- Padronizar o ambiente de execução (evitar “funciona na minha máquina”).
- Facilitar o setup local para desenvolvedores e agentes de IA.
- Isolar serviços (API, banco de dados, serviços auxiliares).
- Permitir evolução futura para orquestradores (ex.: Kubernetes) sem grandes mudanças.

### Serviços mínimos em containers

No mínimo, o ambiente do projeto deve conter:

- **API**  
  - Imagem baseada em Node.js (versão definida pelo projeto).  
  - Build da aplicação (dev ou prod) conforme ambiente.  
  - Exposição da porta HTTP configurada no backend (ex.: `3000`).  

- **Banco de Dados**  
  - Imagem oficial do PostgreSQL.  
  - Volume para persistência dos dados.  
  - Variáveis de ambiente para usuário, senha, banco padrão.  

Outros serviços (ex.: Redis, filas, workers) podem ser adicionados conforme necessidade do projeto.

### Padrões gerais para Dockerfile da API

- Utilizar imagem oficial do Node.js (LTS) como base.  
- Instalar dependências com `npm ci` ou equivalente.  
- Copiar apenas os arquivos necessários para build/execução.  
- Definir `NODE_ENV` de acordo com o ambiente.  
- Expor a porta da API (ex.: `EXPOSE 3000`).  
- Definir `CMD` para iniciar o servidor (ex.: `npm run start`).

### Padrões gerais para docker-compose

- Definir um serviço `api` para o backend e um serviço `db` para o PostgreSQL.
- Configurar rede interna para comunicação entre containers.
- Mapear portas externas para acesso local (ex.: `3000:3000` para API, `5432:5432` para DB em dev).
- Mapear volume para dados do banco:
  - Exemplo: `pgdata:/var/lib/postgresql/data`.
- Utilizar variáveis de ambiente através de arquivo `.env` (ex.: `DATABASE_URL`, `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`).

### Variáveis de ambiente relacionadas a Docker

As principais variáveis relacionadas a containers devem ser:

- `PORT`



---

## Qualidade e Ferramentas de Suporte

- **Lint:** ESLint (ou equivalente da linguagem)
- **Análise de qualidade:** Sonar (SonarQube ou SonarCloud)
- **Code Review Inteligente:** Agentic Code Review (ASSESS)
- **Logs:** padrão centralizado no backend
- **Tratamento de erros:** middleware global

---

## Testes (quando aplicável)

- **Testes de aceitação:** baseados nos CTA da SPEC
- **Testes automatizados:** unitários e/ou de integração conforme evolução do projeto

---

## O que NÃO é responsabilidade deste arquivo

Este documento **não define**:

- regras de negócio,
- fluxos funcionais,
- responsabilidades de camadas,
- contratos de API em detalhe,
- schema físico do banco.

Esses pontos estão documentados em outros arquivos da pasta `/docs`.

---

## Relação com outros documentos

- `03-Architecture.md`: estrutura e responsabilidades.
- `02-SpecFirst.md`: comportamento esperado do sistema.
- `05-APIRoutes.md`: contratos HTTP.
- `06-DatabaseSchema.md`: estrutura do banco.
- `AssessGuidelines.md`: regras de revisão e qualidade.
