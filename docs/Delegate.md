# Delegate – Guia para Geração de Código (Compounding Engineering)

Este arquivo define **como o agente de código deve gerar o sistema** a partir dos artefatos de planejamento (PLAN), seguindo a abordagem de **Compounding Engineering**.

O objetivo do DELEGATE é:
- transformar especificações em código funcional,
- **em partes pequenas e iterativas**,
- sempre respeitando os documentos de planejamento (ex.: `/docs`),
- e preparando o terreno para a fase de ASSESS (revisão e melhoria contínua).

Este guia é **genérico** e pode ser aplicado a qualquer tipo de sistema (web, API, painel, etc.), desde que exista documentação mínima:
- Negócio
- Requisitos Funcionais e Não funcionais
- Arquitetura
- Tecnologia

---

## 1. Princípios gerais do Delegate

1. **Sempre ler antes de codar**  
   - Antes de iniciar qualquer implementação, leia os documentos de planejamento (os arquivos em `/docs`), incluindo:
     - modelo de negócio,
     - especificação funcional (RF, RNF),
     - arquitetura,
     - rotas de API,
     - schema de banco,
     - padrões de código.

2. **Nunca implementar tudo de uma vez**  
   - Trabalhe **por etapas pequenas**, cada uma com:
     - objetivo claro,
     - escopo limitado,
     - saída concreta (arquivos criados/alterados).

3. **Documentar suposições**  
   - Se algo não estiver claro na especificação:
     - assuma a opção mais simples,
     - registre a suposição em comentário ou em nota de implementação,
     - mantenha essa suposição visível para revisão na fase de ASSESS.

4. **Integrar com ASSESS após cada etapa**  
   - Ao terminar uma etapa, o agente deve:
     - verificar se o código está alinhado com os documentos de planejamento,
     - ajustar o que for necessário,
     - somente então avançar para a próxima etapa.

---

## 2. Fluxo geral do Delegate

O fluxo de trabalho do agente no DELEGATE deve seguir esta ordem:

1. **Entender o sistema** (leitura dos documentos).  
2. **Propor um plano de implementação em etapas curtas.**  
3. **Implementar a Etapa 1 (por exemplo, setup do backend).**  
4. **Executar um ciclo de ASSESS sobre a Etapa 1 e ajustar.**  
5. **Implementar a Etapa 2 (por exemplo, módulos principais do backend).**  
6. **Executar ASSESS e ajustar.**  
7. **Implementar Etapas seguintes (banco de dados, front-end, integrações), sempre com ASSESS entre elas.**

---

## 3. Etapas do Delegate – visão genérica

As etapas abaixo são sugeridas para qualquer sistema que possua backend, banco de dados e (opcionalmente) frontend.

```text
┌───────────────────────────────────────────────────────────────┐
│          Leitura e consolidação do planejamento               │
└─────────────────────────────┬─────────────────────────────────┘
                              │
                              ▼
┌───────────────────────────────────────────────────────────────┐
│             Setup do Back-end (estrutura base)                │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│           Camadas do Back-end (sem lógica complexa)           │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│            Regras de negócio do Back-end (RF principais)      │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│            Banco de Dados (schema e migrações)                │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│           Integração Back-end + Banco de Dados                │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│      Endurecimento da API (erros, segurança, padrões)         │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│                   Setup do Front-end                          │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│                   Fluxos principais do Front-end              │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌───────────────────────────────────────────────────────────────┐
│                Integração completa e refinamento              │
└───────────────────────────────────────────────────────────────┘                      
```

---

### Etapa 0 – Leitura e consolidação do planejamento

**Objetivo:**  
Compreender o sistema antes de escrever código.

**Tarefas:**
- Ler todos os arquivos de especificação disponíveis (ex.: `/docs`).
- Identificar:
  - objetivos principais do sistema,
  - principais atores,
  - domínios/chunks de funcionalidade (ex.: autenticação, usuários, produtos, pedidos),
  - tecnologias definidas (linguagem, framework, banco de dados, padrões de API).
- Produzir um **resumo curto** da compreensão do sistema:
  - o que o sistema faz,
  - quais módulos principais existirão,
  - quais tecnologias serão utilizadas em cada camada.

---

### Etapa 1 – Setup do Back-end (estrutura base)

**Objetivo:**  
Criar a estrutura mínima do projeto backend, sem implementar regras de negócio.

**Tarefas:**
- Inicializar o projeto na linguagem e framework definidos (ex.: Node.js + Express, Java + Spring, etc.).
- Criar a estrutura de pastas conforme a arquitetura definida (por exemplo):
  - `src/routes` ou equivalente,
  - `src/controllers`,
  - `src/services`,
  - `src/repositories`,
  - `src/middlewares`,
  - `src/utils`,
  - `src/config`.
- Criar arquivos principais de entrada:
  - `app.(js|ts|...)` (configuração do framework),
  - `server.(js|ts|...)` (subir o servidor).
- Configurar:
  - parser de JSON,
  - CORS (se necessário),
  - uma rota simples de healthcheck (ex.: `GET /health`).

**Saída esperada:**
- Backend subindo localmente com uma rota de teste funcional.
- Estrutura de arquivos/pastas coerente com o planejado.

---

### Etapa 2 – Camadas do Back-end (sem lógica complexa)

**Objetivo:**  
Implementar a **arquitetura interna do backend**, ainda com lógica mínima.

**Tarefas:**
- Criar rotas básicas para os domínios principais (ex.: `/auth`, `/users`, `/items`, etc.), mesmo que retornem respostas mockadas inicialmente.
- Implementar controllers correspondentes, que:
  - recebam as requisições,
  - chamem services (ainda simples),
  - retornem JSON padronizado.
- Implementar serviços (services) como funções/objetos prontos para receber lógica de negócio.
- Criar repositórios (repositories) com interfaces ou funções vazias / stubs, que serão ligados ao banco depois.
- Criar middlewares genéricos:
  - log de requisições,
  - tratamento global de erros,
  - estrutura para autenticação/autorização (mesmo que ainda não funcional).

**Saída esperada:**
- Fluxo básico request → route → controller → service → repository já configurado.
- Código preparado para receber regras de negócio reais.

---

### Etapa 3 – Regras de negócio do Back-end (Requisitos Funcionais principais)

**Objetivo:**  
Transformar os RFs em implementação backend.

**Tarefas:**
- Escolher 1 domínio principal por vez (ex.: autenticação, usuários, pedidos).
- Para cada domínio:
  - Ler os RFs e casos de teste de aceitação correspondentes.
  - Implementar lógica de negócio nos services.
  - Ajustar controllers para refletir:
    - validações de entrada,
    - fluxos de sucesso/erro,
    - códigos HTTP adequados.
  - Ajustar/expandir repositories para refletir consultas e operações esperadas.
- Garantir que as respostas sigam o formato JSON e padrões definidos.

**Saída esperada:**
- Rotas chave do backend implementadas conforme a SPEC.
- Fluxos de negócio principais funcionando sem integração real com banco (se o banco ainda não foi configurado) ou já utilizando o banco, se a Etapa 4 já existir.

---

### Etapa 4 – Banco de Dados (schema e migrações)

**Objetivo:**  
Criar a base de dados conforme o planejamento.

**Tarefas:**
- Ler o schema de banco de dados (tabelas, chaves, relacionamentos) definido nos documentos.
- Traduzir o schema para:
  - scripts SQL de criação de tabelas, ou
  - migrações em uma ferramenta (ex.: Prisma, Sequelize, Flyway, etc.).
- Criar:
  - tabelas principais,
  - constraints (PK, FK, UNIQUE, CHECK),
  - índices,
  - campos de auditoria (datas, status), se definidos.
- Configurar script ou comando para aplicar migrações em ambiente local.

**Saída esperada:**
- Banco de dados criado e migrações funcionando.
- Schema coerente com o modelo de domínio do sistema.

---

### Etapa 5 – Integração Back-end + Banco de Dados

**Objetivo:**  
Ligar o backend ao banco, substituindo dados mockados por dados reais.

**Tarefas:**
- Implementar conexão com o banco de dados na camada de configuração (ex.: `config/database`).
- Atualizar repositórios para:
  - usar SQL/ORM reais (SELECT, INSERT, UPDATE, DELETE),
  - seguir o schema criado na Etapa 4.
- Revisar services para garantir que:
  - utilizam corretamente os repositórios,
  - tratam erros de banco (chaves duplicadas, violação de constraint, etc.).

**Saída esperada:**
- Fluxos principais do sistema funcionando com persistência real.
- Nenhuma dependência em dados mockados para casos principais.

---

### Etapa 6 – Endurecimento da API (erros, segurança, padrões)

**Objetivo:**  
Refinar o backend para estar estável, seguro e consistente.

**Tarefas:**
- Implementar:
  - autenticação (ex.: via JWT),
  - autorização por perfil/regra, se especificado.
- Padronizar:
  - formato de erros (códigos, mensagens),
  - respostas de sucesso.
- Configurar middlewares adicionais:
  - proteção contra abuso (rate limiting, se aplicável),
  - headers de segurança (ex.: Helmet),
  - CORS restrito a origens confiáveis.
- Validar se:
  - todos os endpoints seguem o padrão REST,
  - os códigos HTTP usados são adequados,
  - os RNFs (desempenho, segurança, etc.) possuem suporte na implementação.

**Saída esperada:**
- API robusta, com tratamento correto de erros, autenticação/autorização e boas práticas de segurança.

---

### Etapa 7 – Setup do Front-end

**Objetivo:**  
Criar a estrutura base do front-end (quando existir interface).

**Tarefas:**
- Inicializar o projeto na tecnologia definida (ex.: React).
- Configurar:
  - estrutura de pastas (ex.: `components/`, `pages/`, `services/api/`, `styles/`),
  - ferramenta de build/dev (ex.: Vite, CRA, Next, etc.).
- Criar uma página inicial simples (ex.: tela de login ou dashboard em branco).
- Configurar cliente HTTP (ex.: Axios ou Fetch encapsulado em um serviço de API).

**Saída esperada:**
- Front-end rodando localmente,
- Estrutura pronta para consumir a API.

---

### Etapa 8 – Fluxos principais do Front-end

**Objetivo:**  
Implementar interfaces e fluxos principais do usuário final.

**Tarefas:**
- Implementar telas conforme a especificação:
  - tela de login,
  - tela de listagem/cadastro/edição de entidades principais (ex.: usuários, itens),
  - navegação entre telas (roteamento).
- Conectar as telas à API:
  - utilizar o cliente HTTP para chamar endpoints do backend,
  - tratar estados de carregamento, erro e sucesso.
- Garantir:
  - envio do token de autenticação nas rotas protegidas,
  - exibição clara de mensagens de erro e validação.

**Saída esperada:**
- Front-end funcional, capaz de interagir com o backend e executar os fluxos principais definidos nos requisitos.

---

### Etapa 9 – Integração completa e refinamento

**Objetivo:**  
Garantir que todas as partes do sistema funcionem juntas de forma coerente.

**Tarefas:**
- Validar os fluxos ponta-a-ponta:
  - do front → backend → banco → resposta → front.
- Ajustar:
  - mensagens,
  - validações,
  - tratamentos de erro,
  - feedback visual ao usuário.
- Verificar aderência aos casos de teste de aceitação (CTA) definidos.

**Saída esperada:**
- Sistema integrado, pronto para ciclos mais profundos de ASSESS, testes automatizados e melhorias incrementais.

---

## 4. Regras finais para o agente no Delegate

- Ler e respeitar:
   -  `CodifyGeneral` (regras globais)  
    - `08-CodifyProject.md` (regras específicas deste projeto).”
- **Nunca** ignorar ou contradizer o que está nos documentos de planejamento.
- **Sempre** justificar decisões que não estiverem explicitamente cobertas pela especificação.
- **Sempre** trabalhar em etapas pequenas, concluindo e revisando antes de seguir.
- **Sempre** executar um ciclo de ASSESS ao final de cada etapa:
  - comparar o código com:
    - requisitos funcionais e não funcionais,
    - arquitetura proposta,
    - contratos de API,
    - schema de banco.
  - corrigir problemas antes de avançar.

O DELEGATE não é apenas “gerar código”, mas **gerar código alinhado, incremental e preparado para evoluir** dentro do ciclo de Compounding Engineering.
