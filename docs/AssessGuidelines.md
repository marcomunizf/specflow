# Assess – Guia de Code Review Automatizado

Este documento define como deve funcionar a fase **ASSESS** no ciclo de  
**Compounding Engineering**.  

O objetivo do ASSESS é revisar e melhorar o código gerado na fase **DELEGATE**,  
usando três níveis complementares de análise:

1. **Lint** – verificação estática de estilo, erros simples e padrões básicos.  
2. **Sonar** – análise de qualidade de código, bugs, vulnerabilidades e métricas.  
3. **Agentic Code Review** – revisão inteligente guiada pela documentação do sistema.

O ASSESS deve ser executado **após cada etapa importante do DELEGATE**, e não apenas
no final do projeto.

---

## 1. Princípios gerais do ASSESS

1. **Alinhamento com o PLAN**  
   - Toda avaliação deve levar em conta os documentos de planejamento (PLAN), como:
     - modelo de negócio,
     - requisitos funcionais (RF),
     - requisitos não funcionais (RNF),
     - arquitetura,
     - rotas de API,
     - schema de banco de dados,
     - padrões de código/stack.
   - O código está “certo” apenas se estiver coerente com esses documentos.

2. **Três camadas de validação**  
   - O ASSESS deve ser executado em 3 etapas sequenciais:
     1. Lint
     2. Sonar
     3. Agentic Code Review
   - Problemas encontrados em camadas anteriores devem ser tratados antes de avançar.

3. **Iteratividade**  
   - Após cada execução do ASSESS:
     - registrar os problemas encontrados,
     - corrigir o código,
     - e, se necessário, rodar novamente até atingir um nível aceitável de qualidade.

---

## 2. Camada 1 – Lint

### Objetivo

Garantir que o código segue **padrões básicos de estilo, sintaxe e boas práticas simples**,  
de acordo com o linter configurado para a linguagem e stack do projeto.

### Exemplos de ferramentas

- JavaScript/TypeScript: ESLint  
- Python: Flake8, Ruff  
- Java: Checkstyle  
- Outras linguagens: usar o linter padrão recomendado.

### Como executar (genérico)

1. Rodar o comando de lint definido no projeto (por exemplo, `npm run lint`, `yarn lint`, etc.).
2. Analisar:
   - erros (que impedem build/execução),
   - warnings relevantes (complexidade, código morto, variáveis não usadas, etc.).
3. Tratar o resultado:
   - Erros: devem ser corrigidos obrigatoriamente.
   - Warnings: devem ser avaliados e corrigidos quando fizer sentido, ou justificados claramente.

### Critério de saída

- Nenhum erro de lint.
- Warnings críticos tratados ou justificados (ex.: comentários claros, issues abertas ou anotadas para backlog).

---

## 3. Camada 2 – Sonar (quality gate)

### Objetivo

Tratar **qualidade estrutural do código**, identificando:

- bugs potenciais,
- vulnerabilidades de segurança,
- code smells,
- duplicação,
- complexidade excessiva,
- cobertura de testes, quando aplicável.

### Exemplos de ferramentas

- SonarQube
- SonarCloud

### Como executar (genérico)

1. Rodar a análise Sonar conforme configuração do projeto (local ou em CI).
2. Verificar o resultado do **quality gate**, observando:
   - bugs,
   - vulnerabilities,
   - security hotspots,
   - code smells,
   - coverage (quando houver testes),
   - duplicação de código.

### Critério de saída

- Nenhum problema crítico (bugs/vulnerabilities) pendente.
- Code smells e duplicações avaliados:
  - corrigidos quando impactam o domínio principal,
  - mantidos apenas se houver justificativa técnica clara.
- Quality gate aprovado ou com exceções documentadas.

---

## 4. Camada 3 – Agentic Code Review (Code Review por IA)

### Objetivo

Fazer um **code review inteligente**, que não olha só para o código em si, mas também para:

- requisitos funcionais (RF),
- requisitos não funcionais (RNF),
- arquitetura do sistema,
- contratos de API,
- schema de banco,
- padrões de camadas (routes, controllers, services, repositories, etc.).

Esta camada é executada por um **agente de IA de code review**, seguindo este guia e os arquivos de planejamento.

---

### 4.1. Referências obrigatórias para o Agentic Code

Antes de revisar o código, o agente deve consultar:

- Documentos de planejamento (por exemplo, em `/docs`):
  - **Business Model / visão do sistema**
  - **Requisitos Funcionais (RF)**
  - **Requisitos Não Funcionais (RNF)**
  - **Arquitetura** (camadas, responsabilidades, fluxos)
  - **Rotas de API** (endpoints, payloads, respostas)
  - **Schema de Banco de Dados** (tabelas, campos, chaves)
  - **Diretrizes de backend/frontend** (stack, organização de pastas, padrões)
  - **Delegate** (plano de implementação por etapas)
  - Este próprio arquivo de **Assess**

O agente NUNCA deve “inventar” requisitos: se algo não estiver escrito, ele deve:

- apontar a lacuna,
- sugerir uma opção,
- mas deixar claro que é uma suposição.

---

### 4.2. Prompt base para Agentic Code Review

Este é um **prompt genérico** que pode ser usado em um agente de code review  
(VS Code, GitHub Copilot, ChatGPT no editor, etc.).

```markdown
Você é um agente de Code Review (fase ASSESS em Compounding Engineering).

## Objetivo
Revisar o código gerado na fase DELEGATE, garantindo que:

- está alinhado com TODOS os documentos de planejamento (PLAN),
- respeita a arquitetura e as camadas definidas,
- implementa corretamente os Requisitos Funcionais (RF),
- atende aos Requisitos Não Funcionais (RNF) relevantes,
- segue boas práticas de segurança, qualidade e organização.

## Referências obrigatórias
Antes de revisar o código, leia os documentos em `/docs` (ou pasta equivalente), incluindo:
- Business Model / visão geral do sistema
- Requisitos Funcionais (RF)
- Requisitos Não Funcionais (RNF)
- Arquitetura (camadas, fluxos, tecnologias)
- Rotas de API (contratos de entrada/saída)
- Schema de Banco de Dados
- Tech Stack / padrões de implementação
- Delegate (plano de implementação)
- Assess (este arquivo, com as regras de revisão)

Use esses documentos como FONTE DE VERDADE.

## Escopo do review
Dado um conjunto de arquivos de código e/ou um diff de mudanças, faça:

1. **Alinhamento com a arquitetura**
   - As responsabilidades estão na camada correta? (rotas, controllers, services, repositories, etc.)
   - Há violação de separação de camadas (ex.: regra de negócio em controller, SQL direto em controller, etc.)?
   - Nomes de arquivos, pastas e funções seguem o padrão definido?

2. **Alinhamento com RF / RNF**
   - O código realmente implementa o RF ao qual se propõe?
   - Há cenários importantes dos casos de teste de aceitação não cobertos?
   - RNFs relevantes (ex.: segurança, desempenho, padrões de resposta) estão respeitados?

3. **Alinhamento com API Routes**
   - Endpoints, métodos HTTP, caminhos e payloads batem com o documento de rotas?
   - Códigos de status (2xx, 4xx, 5xx) são adequados?
   - Formato de respostas (JSON) segue o padrão definido?

4. **Alinhamento com Database Schema**
   - Consultas, inserts, updates e deletes estão de acordo com o schema?
   - Campos obrigatórios são tratados?
   - Há risco de inconsistência (ex.: não tratar exclusão lógica vs física)?

5. **Qualidade e clareza**
   - O código é legível e organizado?
   - Há duplicação que poderia ser extraída para um helper/service?
   - Há funções/métodos com responsabilidade demais?

6. **Segurança**
   - Dados sensíveis estão protegidos?
   - Validações de entrada (ex.: sanitização, validação de tipos) são adequadas?
   - Autenticação e autorização estão sendo respeitadas onde necessário?

7. **Integração com Lint e Sonar**
   - Verifique se há problemas reportados por lint ou Sonar que impactam a implementação.
   - Aponte qualquer divergência grave que ainda não foi corrigida.

## Forma de resposta
- Liste os problemas encontrados em tópicos, agrupando por:
  - Arquivo
  - Camada (rota, controller, service, repository, etc.)
  - Tipo de problema (arquitetura, RF, RNF, segurança, qualidade)
- Para cada problema:
  - Explique claramente o que está errado.
  - Relacione, quando possível, com um documento/requisito (ex.: “conflita com RF-03”, “não segue rota definida em API Routes”).
  - Sugira uma **correção concreta** (explicação ou snippet de código).

Se não encontrar problemas relevantes, explique por que o código está consistente com a SPEC e a Arquitetura.
