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
Garantir que o código segue padrões básicos de estilo, sintaxe, organização e boas práticas simples, de acordo com o linter configurado para a linguagem e stack do projeto.  
O lint é a primeira etapa da revisão e deve eliminar erros óbvios antes de análises mais profundas (Sonar e Agentic Code).

---

### Exemplos de Ferramentas
- **JavaScript / TypeScript:** ESLint  
- **Python:** Flake8, Ruff  
- **Java:** Checkstyle  
- **Outras linguagens:** o linter padrão recomendado da comunidade

---

### Como executar (genérico)
1. **Quando o ambiente permitir execução real de comandos (ex.: VS Code com terminal, CI/CD):**
   - Rodar o comando oficial do projeto, por exemplo:
     - `npm run lint`
     - `yarn lint`
     - `eslint src --ext .js,.ts`
     - ou equivalente na tecnologia usada.
   - Ler e interpretar o relatório do linter (errors e warnings).

2. **Quando o agente não puder rodar comandos (ex.: execução puramente textual):**
   - Realizar uma **análise estática simulada**, verificando manualmente:
     - variáveis não utilizadas,
     - imports não utilizados,
     - funções nunca chamadas,
     - problemas de sintaxe evidentes,
     - código morto,
     - má formatação que prejudique leitura,
     - inconsistência básica de padrões definidos no projeto.
   - O agente deve aplicar o conhecimento da linguagem e das regras típicas do linter para simular o resultado da ferramenta.

---

### Como interpretar o resultado
- **Erros (errors):**
  - Sempre devem ser corrigidos.
  - Erros de lint geralmente impedem build/execução ou representam má prática crítica.

- **Avisos (warnings):**
  - Devem ser avaliados.
  - Warnings importantes (complexidade excessiva, código morto, variáveis não usadas, padrões inconsistentes) devem ser corrigidos.
  - Caso algum warning não seja corrigido por decisão técnica, deve ser **justificado claramente** (comentário no código, documentação interna ou anotação no backlog).

---

### Critério de saída da etapa Lint
Para considerar que a etapa de lint está aprovada:

1. **Nenhum erro de lint deve permanecer.**
2. **Warnings críticos devem ser corrigidos ou justificados** formalmente.
3. O código deve estar:
   - legível,
   - coerente com o estilo adotado pelo projeto,
   - livre de problemas simples detectáveis por análise estática.
4. Apenas após atender esses critérios o ASSESS pode avançar para a **Camada 2 – Sonar**.

---

### Observação importante sobre o papel do agente
O agente deve:
- executar lint **real** quando o ambiente permitir,
- simular lint quando o ambiente não permitir,
- listar todos os problemas encontrados,
- corrigir o código antes de seguir para a próxima camada de análise.

O propósito desta etapa é assegurar que a base do código está limpa e organizada antes de passar para validações mais profundas de qualidade, segurança e requisitos.


---

## 3. Camada 2 – Sonar (Quality Gate)

### Objetivo
Avaliar a **qualidade estrutural e a segurança do código** por meio de análise estática avançada.  
A camada Sonar identifica problemas que vão além do lint, como:

- bugs potenciais,
- vulnerabilidades de segurança,
- code smells (más práticas estruturais),
- duplicação de código,
- complexidade excessiva,
- cobertura insuficiente de testes,
- violações de boas práticas arquiteturais.

Esta camada atua como um **quality gate**, impedindo que código de baixa qualidade avance no ciclo de Compounding Engineering.

---

### Ferramentas recomendadas
- **SonarQube** (local)
- **SonarCloud** (SaaS)
- Alternativas equivalentes podem ser usadas, desde que façam análise estática em profundidade.

---

### Como executar (dois cenários)

#### **1. Quando o ambiente permite execução real**
Em ambientes como CI/CD, VS Code com plugin Sonar, Docker ou pipelines:

1. Executar a análise:
   - via script (ex.: `sonar-scanner`),
   - via plugin integrado ao build,
   - ou usando pipeline configurado (GitHub Actions, GitLab CI, Azure DevOps, etc.).

2. Aguardar o relatório completo contendo:
   - bugs,
   - vulnerabilities,
   - security hotspots,
   - code smells,
   - duplicated lines,
   - coverage,
   - maintainability rating,
   - reliability rating,
   - security rating.

3. Avaliar o status do **Quality Gate**:
   - **Aprovado:** avançar para próxima etapa.
   - **Reprovado:** analisar cada erro crítico e corrigir antes de continuar.

---

#### **2. Quando o agente não pode executar Sonar de verdade**
O agente deve realizar uma **simulação inteligente** baseada em heurísticas de análise estática e boas práticas.  
Essa simulação deve verificar:

1. **Possíveis bugs**
   - retorno de funções ignorado,
   - lógica condicional suspeita,
   - variáveis não inicializadas,
   - mutação inesperada de objetos.

2. **Riscos de segurança**
   - dados sensíveis expostos,
   - falta de validação em entradas críticas,
   - falta de try/catch em operações sensíveis,
   - tratamento inadequado de senhas/chaves.

3. **Code smells**
   - funções longas demais,
   - múltiplas responsabilidades em um controller/service,
   - uso excessivo de parâmetros,
   - nomes confusos ou inconsistentes,
   - duplicação visível de código.

4. **Complexidade**
   - blocos com muitos `if/else`,
   - loops aninhados desnecessários,
   - falta de abstração.

5. **Problemas arquiteturais**
   - regra de negócio em controllers,
   - SQL em controllers ou services (violando arquitetura),
   - acoplamento excessivo.

6. **Conformidade com SPEC**
   - Sonar simulado deve verificar se:
     - os endpoints têm comportamento previsível,
     - erros retornam JSON consistente,
     - ausência de validações pode gerar falhas.

O agente deve documentar claramente que a análise é simulada quando não houver execução real.

---

### Como interpretar os resultados

#### **Problemas Críticos (Blocking)**
Devem ser corrigidos imediatamente:
- bugs de runtime,
- vulnerabilidades,
- violações graves de arquitetura,
- duplicação significativa em módulos principais,
- funções com complexidade excessiva,
- violações de RNF de segurança, desempenho ou escalabilidade.

#### **Problemas Importantes**
Podem ser corrigidos nesta etapa ou documentados para o próximo ciclo:
- code smells moderados,
- estruturas repetitivas,
- inconsistências menores.

#### **Problemas Cosméticos**
Podem ser deixados para refinamento posterior:
- pequenas questões de estilo (desde que não interfiram no lint),
- pequenas otimizações.

---

### Critério de saída da etapa Sonar

Para a camada Sonar estar aprovada, é necessário:

1. **Nenhum bug crítico ou vulnerabilidade pendente.**
2. **Aderência aos princípios arquiteturais e ao modelo de camadas.**
3. **Ausência de duplicação relevante ou justificativa para mantê-la.**
4. **Complexidade das funções em níveis aceitáveis.**
5. **Ausência de violações graves de RF/RNF relacionadas ao comportamento do código.**
6. **Quality Gate aprovado**, quando Sonar real é possível.
7. Quando a análise for simulada, o agente deve explicar:
   - quais heurísticas aplicou,
   - por que considera o código aprovado,
   - quais fragilidades foram encontradas e corrigidas.

---

### Papel do agente nesta etapa
O agente deve:

- Executar Sonar real **se o ambiente permitir**.
- Se não permitir, aplicar Sonar **simulado e inteligente**.
- Apontar todos os problemas detectados.
- Relacionar problemas com:
  - arquitetura,
  - RF,
  - RNF,
  - padrões de API,
  - schema do banco.
- Corrigir o código sempre que necessário.
- Somente avançar para a **Camada 3 – Agentic Code Review** quando esta etapa estiver concluída.

A etapa Sonar garante que o código não apenas funciona, mas é **seguro, limpo, compreensível e sustentável**, preparando terreno para a análise final de alinhamento com a SPEC e arquitetura.


---

## 4. Camada 3 – Agentic Code Review (Revisão Inteligente e Contextual)

### Objetivo
Realizar a análise mais profunda e inteligente de todo o código implementado, verificando:

- **Coerência com a SPEC (02-Spec-First)**
- **Aderência total aos Requisitos Funcionais (RF)**
- **Conformidade com os Requisitos Não Funcionais (RNF)**
- **Compatibilidade com a Arquitetura Definida (03-Architecture)**
- **Respeito ao Domain Model e API Routes (05-APIRoutes)**
- **Integração correta com o Banco de Dados (06-DatabaseSchema)**
- **Qualidade técnica e arquitetural da solução**
- **Legibilidade, clareza e extensibilidade**

Esta camada funciona como a **revisão humana ideal**, mas feita por um agente inteligente, capaz de cruzar todas as informações do projeto.

---

### O que esta camada deve analisar

#### 1. Conformidade com a SPEC
- Verificar se todo comportamento implementado corresponde aos fluxos definidos.  
- Conferir se não existe código que contradiz a SPEC ou funcionalidades invisíveis ao escopo definido.  
- Garantir que não há requisitos ignorados ou mal implementados.

#### 2. Aderência aos Requisitos Funcionais (RF)
- Cada RF deve ser mapeado para:
  - rotas,
  - casos de uso,
  - controllers,
  - services,
  - respostas da API.
- Verificar:
  - entradas esperadas,
  - saídas esperadas,
  - fluxos alternativos,
  - regras de validação,
  - tratamento de erros.

#### 3. Aderência aos Requisitos Não Funcionais (RNF)
Analisar:
- padrões de segurança,
- desempenho mínimo,
- organização e clareza do código,
- práticas de escalabilidade,
- consistência de logs e monitoramento,
- padronização das respostas da API,
- robustez frente a entradas inesperadas.

#### 4. Conformidade com a Arquitetura (Camadas)
O agente deve validar:
- controllers não podem conter regra de negócio,
- services devem orquestrar repositórios e validar regras,
- repositories devem conter apenas acesso a banco,
- middlewares devem atuar antes dos controllers,
- utils não podem conter lógica crítica,
- camadas não podem vazar responsabilidades.

Qualquer violação deve ser sinalizada como **arquiteturalmente incorreta**.

#### 5. Validação das API Routes
Conferir:
- paths corretos,
- verbos HTTP corretos,
- payloads idênticos aos definidos na SPEC,
- status codes coerentes,
- formatação JSON padronizada,
- mensagens de erro consistentes,
- coerência com autenticação/autorização.

#### 6. Validação do Banco de Dados
Checar:
- correspondência entre models ↔ tabelas,
- tipos corretos,
- constraints respeitadas,
- relacionamento entre tabelas,
- queries seguras (SQL injection),
- uso adequado de transações quando necessário.

#### 7. Qualidade técnica geral
Detectar:
- duplicação de lógica,
- funções muito grandes,
- falta de abstrações importantes,
- nomes inadequados,
- comentários desnecessários,
- dependências mal organizadas,
- código morto ou não utilizado.

#### 8. Extensibilidade e manutenção
Avaliar:
- facilidade de adicionar novos módulos,
- clareza dos padrões,
- separação de responsabilidades,
- modularização adequada,
- aderência aos princípios SOLID (quando aplicável).

---

### Como executar a revisão (metodologia)

#### O agente deve:

1. **Carregar todos os arquivos do projeto**:
   - /src
   - /docs (BusinessModel, SpecFirst, Architecture, APIRoutes, DatabaseSchema, BackendStructure)
   - migrations, seeders, configs

2. **Cruzar as informações** entre:
   - especificações,
   - API,
   - banco,
   - rotas,
   - controllers,
   - serviços,
   - repositórios.

3. **Detectar divergências**:
   - código que não implementa a SPEC,
   - código que adiciona funcionalidades não previstas,
   - código que viola regras de RF ou RNF,
   - endpoints diferentes do definido,
   - falhas na arquitetura.

4. **Listar erros, pontos fracos e melhorias**, categorizando:
   - críticos,
   - importantes,
   - opcionais.

5. **Corrigir o código**, quando possível:
   - refatorar,
   - melhorar arquitetura,
   - alinhar retornos JSON,
   - ajustar queries,
   - reorganizar camadas,
   - corrigir violações de padrão.

6. **Gerar um relatório final** contendo:
   - problemas encontrados,
   - justificativa técnica,
   - correções realizadas,
   - sugestões de melhorias de médio e longo prazo.

---

### Critério de saída da etapa Agentic Code Review

A etapa só é considerada aprovada quando:

1. **Todo o código está 100% alinhado à SPEC.**  
2. **Todos os RF e RNF estão implementados corretamente.**  
3. **A arquitetura está sendo respeitada sem violações relevantes.**  
4. **As API Routes estão 100% coerentes com o documento 05-APIRoutes.**  
5. **O codebase está limpo, claro e extensível.**  
6. **Não há riscos de segurança óbvios nem inconsistências de banco.**  
7. **O agente não identifica gaps entre documentação e implementação.**  
8. O agente declara formalmente que:
   - O código é **correto**,  
   - A arquitetura está **coerente**,  
   - O sistema está pronto para o próximo ciclo do Compounding Engineering.

---

### Papel do agente nesta etapa
O agente atua como um **arquiteto + tech lead**, fazendo uma revisão profunda:

- cruza documentação com código,
- identifica incoerências,
- propõe e executa correções,
- assegura aderência ao design,
- protege a qualidade da base do código,
- previne débitos técnicos futuros.

A Camada 3 é o coração do ASSESS e garante que o sistema evolua com qualidade incremental real.


