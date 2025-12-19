# AGENT_WORKFLOW

## PROPÓSITO
Definir o fluxo operacional que o agente DEVE seguir para planejar, executar, avaliar e codificar um projeto de software de forma iterativa.

---

## REGRAS GLOBAIS

- O agente DEVE seguir rigorosamente a ordem das etapas definidas neste documento.
- O agente NÃO DEVE pular nenhuma etapa.
- O agente DEVE iterar pelas etapas de execução até que todas estejam concluídas.
- O agente DEVE sempre respeitar os arquivos em `/guidelines/` como fonte máxima de autoridade.
- O agente DEVE tratar todos os arquivos `.md` como fontes autoritativas.

---
## DEFINIÇÕES DE DIRETÓRIOS (OBRIGATÓRIO)

- `/docs/plan/`:
  - CONTÉM TEMPLATES (modelo fixo).
  - É READ-ONLY para conteúdo do projeto.

- `/docs/planproject/`:
  - CONTÉM O PLANO DO PROJETO ATUAL (instância preenchida ou a ser preenchida).
  - É o local onde o agente DEVE escrever os arquivos de planejamento do projeto .
  - Todos os arquivos gerados aqui DEVEM seguir a estrutura e a ordem do `/plan/`.

---

## ETAPA 0 — READ_CONTEXT

### ENTRADA
- Raiz do repositório

### AÇÕES
- Ler TODOS os arquivos listados abaixo:
  - `/guidelines/**`
  - `/plan/**`
  - `/planproject/**`

### SAÍDA
- Representação interna contendo:
  - Regras do projeto
  - Planejamento existente
  - Restrições de execução

---

## ETAPA 1 — CHECK_PROJECT_DESCRIPTION

### AÇÃO
- Verificar se existe um **arquivo de descrição do projeto** com nome `description` dentro de `/planproject/**`.

### DECISÃO
- SE a descrição do projeto EXISTE → ir para `ETAPA 2A — PLAN_GENERATION`
- SE a descrição do projeto NÃO EXISTE → ir para `ETAPA 2B — PLAN_READING`

---

## ETAPA 2A — PLANPROJECT_GENERATION

### CONDIÇÃO
- Executar SOMENTE se existir descrição do projeto. Arquivo `description` dentro de `/planproject/**`

### AÇÕES
- Ler os arquivos `/plan/` (templates).
- Ler o arquivo de descrição do projeto.
- Para cada template em `/plan/`, gerar o arquivo equivalente dentro de `/planproject/`.
  - Mesmo nome
  - Mesma ordem e seções
  - preencher usando apenas o arquivo de description, usando regras em `/guidelines/`
- Os arquivos gerados DEVEM:
  - Ser arquivos em formato **.md**
  - Seguir os templates definidos em `/plan/`
  - Utilizar o mesmo nome dos arquivos definidos em `/plan/`
  - Seguir todas as regras de `/guidelines/`
  - Ser internamente consistentes

### SAÍDA
- Diretório `/planproject/` totalmente preenchido com o plano do projeto atual.

---

## ETAPA 2B — PLANPROJECT_READING

### CONDIÇÃO
- Executar SOMENTE se NÃO existir descrição do projeto em `/planproject/`.

### DEFINIÇÕES IMPORTANTES
- Os templates em `/docs/plan/` PODEM ser:
  - CORE (obrigatórios para qualquer projeto)
  - OPTIONAL (dependem do escopo do projeto)
    
CORE
- 01-BusinessModel.md
- 02-00-SpecFirst.md
- 02-01-SpecGeral.md
- 02-02-AtoresGlossario.md
- 03-Architecture.md
- 04-TechStack.md

OPTIONAL
- 02-03-RF.md
- 02-04-RNF.md
- 02-05-testesaceitacao.md
- 05-APIRoutes.md
- 06-DatabaseSchema.md
- 07-BackendStructure.md
- 08-CodifyProject.md

### AÇÕES
- Ler TODOS os arquivos existentes em `/docs/planproject/`.
- Ler TODOS os templates em `/docs/plan/` e identificar:
  - Quais são CORE
  - Quais são OPTIONAL

### DECISÃO (DETERMINÍSTICA)

- Para templates marcados como CORE:
  - SE o arquivo NÃO existir em `/docs/planproject/`:
    - O agente DEVE criar um arquivo contendo:
      - Preenchimento de acordo com description.md e o template em `/docs/plan/`."

- Para templates marcados como OPTIONAL:
  - O agente NÃO DEVE criar arquivos em `/docs/planproject/` sem confirmação explícita no `description.md`.

- O agente NÃO DEVE:
  - Preencher conteúdo de projeto
  - Inferir necessidades técnicas
  - Criar arquivos OPTIONAL por suposição

### SAÍDA
- Compreensão interna completa do planejamento existente

---

## ETAPA 3 — DELEGATE

### ENTRADA
- Todos os arquivos da pasta `/planproject/` são usados como fonte do projeto.
- `/plan/` é referencia estrututal (template), não é conteúdo do projeto.

### AÇÕES
- Criar um plano estruturado de execução.
- Definir explicitamente:
  - Etapas de execução (Etapa X, X+1, ...)
  - Ordem de implementação
  - Tecnologias por camada
  - Dependências entre etapas

### SAÍDA
- Roadmap interno de execução
- Definição clara da `ETAPA X`

---

## ETAPA 4 — EXECUTE_STAGE_X

### ENTRADA
- Definição da Etapa X vinda do `DELEGATE`

### AÇÕES
- Implementar TODOS os itens definidos para a Etapa X.
- Seguir rigorosamente:
  - `/guidelines/`
  - `/plan/`
  - `/planproject/`
  - `/delegate/`
  - Arquitetura definida

### SAÍDA
- Alterações de código correspondentes EXCLUSIVAMENTE à Etapa X

---

## ETAPA 5 — ASSESS_STAGE_X

### ENTRADA
- Código produzido na Etapa X

### AÇÕES
- Aplicar TODAS as camadas de avaliação definidas em `/guidelines/assessguidelines`.
- Avaliar obrigatoriamente:
  - Qualidade do código
  - Riscos de segurança
  - Consistência arquitetural
  - Estilo e manutenibilidade

### DECISÃO
- SE a avaliação FALHAR → retornar para `ETAPA 4 — EXECUTE_STAGE_X`
- SE a avaliação PASSAR → seguir para `ETAPA 6 — CODIFY`

---

## ETAPA 6 — CODIFY

### ENTRADA
- Código aprovado da Etapa X

### AÇÕES
- Verificar:
  - Quais arquivos foram criados
  - Quais arquivos foram modificados
- Garantir que as alterações:
  - São coerentes com o código existente
  - Não quebram etapas futuras planejadas
- Verificar:
  - Necessidade de modificar os arquivos de codify.
  - Modificar o arquivo `/guidelines/codifygeneral` se a alteração for válida para qualquer tipo de projeto.
  - Modificar o arquivo `/planproject/08-codifyproject` se a alteração for válida apenas para o projeto que está sendo executado.

### SAÍDA
- Código consolidado e pronto para a próxima iteração

---

## ETAPA 7 — LOOP_CONTROL

### AÇÃO
- Verificar se ainda existem etapas pendentes.

### DECISÃO
- SE existirem etapas pendentes → incrementar X e retornar para `ETAPA 4 — EXECUTE_STAGE_X`
- SE NÃO existirem mais etapas → FINALIZAR

---

## RESUMO DO CICLO (AUTORITATIVO)

```text
READ_CONTEXT
  → CHECK_PROJECT_DESCRIPTION
    → PLAN_GENERATION | PLAN_READING
      → DELEGATE
        → EXECUTE_STAGE_X
          → ASSESS_STAGE_X
            → CODIFY
              → LOOP_CONTROL → EXECUTE_STAGE_X+1
