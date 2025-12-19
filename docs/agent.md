# AGENT_WORKFLOW

## PROPÓSITO
Definir o fluxo operacional que o agente DEVE seguir para planejar, executar, avaliar e codificar um projeto de software de forma iterativa, evitando alucinação, artefatos desnecessários e decisões implícitas.

---

## REGRAS GLOBAIS

- O agente DEVE seguir rigorosamente a ordem das etapas definidas neste documento.
- O agente NÃO DEVE pular nenhuma etapa.
- O agente DEVE iterar pelas etapas de execução até que todas estejam concluídas.
- O agente DEVE sempre respeitar os arquivos em `/docs/guidelines/` como fonte máxima de autoridade.
- O agente DEVE tratar todos os arquivos `.md` como fontes autoritativas.
- O agente NÃO DEVE inferir requisitos, arquitetura ou decisões técnicas sem base explícita em arquivos do projeto.

---

## DEFINIÇÕES DE DIRETÓRIOS (OBRIGATÓRIO)

- `/docs/plan/`
  - CONTÉM TEMPLATES (modelo fixo).
  - Representa a **estrutura possível** de planejamento.
  - É READ-ONLY para conteúdo de projeto.
  - O agente NÃO DEVE escrever arquivos de projeto neste diretório.

- `/docs/planproject/`
  - CONTÉM O PLANO DO PROJETO ATUAL (instância concreta).
  - É o local onde o agente DEVE escrever arquivos de planejamento do projeto.
  - Os arquivos aqui DEVEM manter:
    - o mesmo nome
    - a mesma estrutura
    - a mesma ordem dos templates correspondentes em `/docs/plan/`.

- `/docs/guidelines/`
  - CONTÉM regras globais, regras de avaliação (ASSESS) e regras de codificação (CODIFY).
  - Tem prioridade máxima sobre qualquer outra instrução.

---

## CLASSIFICAÇÃO DOS TEMPLATES

Os templates em `/docs/plan/` são classificados como:

### CORE (sempre aplicáveis)
- 01-BusinessModel.md  
- 02-00-SpecFirst.md  
- 02-01-SpecGeral.md  
- 02-02-AtoresGlossario.md  
- 03-Architecture.md  
- 04-TechStack.md  

### OPTIONAL (dependem do escopo do projeto)
- 02-03-RF.md  
- 02-04-RNF.md  
- 02-05-testesaceitacao.md  
- 05-APIRoutes.md  
- 06-DatabaseSchema.md  
- 07-BackendStructure.md  
- 08-CodifyProject.md  

Templates OPTIONAL SÓ podem ser criados quando explicitamente ativados no `description.md`.

---

## ETAPA 0 — READ_CONTEXT

### ENTRADA
- Raiz do repositório

### AÇÕES
- Ler TODOS os arquivos listados abaixo:
  - `/docs/guidelines/**`
  - `/docs/plan/**`
  - `/docs/planproject/**`

### SAÍDA
- Representação interna contendo:
  - Regras globais
  - Templates disponíveis
  - Planejamento existente do projeto
  - Restrições de execução

---

## ETAPA 1 — CHECK_PROJECT_DESCRIPTION

### AÇÃO
- Verificar se existe um arquivo de descrição do projeto com nome EXATO `description.md` dentro de `/docs/planproject/`.

### DECISÃO
- SE `description.md` EXISTE → ir para `ETAPA 2A — PLANPROJECT_GENERATION`
- SE `description.md` NÃO EXISTE → ir para `ETAPA 2B — PLANPROJECT_READING`

---

## ETAPA 2A — PLANPROJECT_GENERATION

### CONDIÇÃO
- Executar SOMENTE se existir `description.md` em `/docs/planproject/`.

### OBJETIVO
Instanciar o planejamento completo do projeto atual usando os templates definidos em `/docs/plan/`.

### AÇÕES
- Ler TODOS os templates em `/docs/plan/`.
- Ler `/docs/planproject/description.md`.
- Para CADA template:
  - Verificar se é CORE ou OPTIONAL.
  - Gerar o arquivo correspondente em `/docs/planproject/` SOMENTE se:
    - for CORE, ou
    - for OPTIONAL e estiver explicitamente ativado no `description.md`.
- Os arquivos gerados DEVEM:
  - Estar em formato `.md`
  - Manter o mesmo nome do template
  - Manter a mesma estrutura e seções
  - Ser preenchidos APENAS com base em:
    - `description.md`
    - regras em `/docs/guidelines/`
- O agente NÃO DEVE escrever nada em `/docs/plan/`.

### SAÍDA
- Diretório `/docs/planproject/` preenchido apenas com os arquivos necessários ao projeto atual.

---

## ETAPA 2B — PLANPROJECT_READING

### CONDIÇÃO
- Executar SOMENTE se NÃO existir `description.md` em `/docs/planproject/`.

### OBJETIVO
Evitar alucinação e evitar criação de artefatos sem definição explícita de escopo.

### AÇÕES
- Ler TODOS os arquivos existentes em `/docs/planproject/`.
- Ler os templates em `/docs/plan/` APENAS para identificar:
  - Quais templates são CORE
  - Quais templates são OPTIONAL

### DECISÃO (DETERMINÍSTICA)

- Para templates CORE:
  - SE o arquivo NÃO existir em `/docs/planproject/`:
    - O agente PODE criar um arquivo PLACEHOLDER contendo SOMENTE:
      - Título do arquivo
      - Estrutura básica (seções) do template
      - Aviso explícito no topo:
        "PLACEHOLDER — preenchimento depende de description.md"

- Para templates OPTIONAL:
  - O agente NÃO DEVE criar arquivos em `/docs/planproject/` sem ativação explícita no `description.md`.

- O agente NÃO DEVE:
  - Preencher conteúdo semântico do projeto
  - Inferir requisitos ou decisões técnicas
  - Criar arquivos OPTIONAL por suposição

### SAÍDA
- Compreensão interna do planejamento existente
- Lista interna de:
  - Templates CORE ausentes
  - Templates OPTIONAL ignorados por falta de contexto

---

## ETAPA 3 — DELEGATE

### ENTRADA
- `/docs/planproject/**` é a ÚNICA fonte do projeto atual.
- `/docs/plan/**` é referência estrutural, não conteúdo do projeto.

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
  - `/docs/guidelines/`
  - `/docs/planproject/`
  - Arquitetura definida no projeto
- O agente NÃO DEVE usar `/docs/plan/` como fonte de requisitos.

### SAÍDA
- Alterações de código correspondentes EXCLUSIVAMENTE à Etapa X

---

## ETAPA 5 — ASSESS_STAGE_X

### ENTRADA
- Código produzido na Etapa X

### AÇÕES
- Aplicar TODAS as camadas de avaliação definidas em:
  - `/docs/guidelines/assessguidelines.md`
  - e demais arquivos relevantes em `/docs/guidelines/`

### DECISÃO
- SE a avaliação FALHAR → retornar para `ETAPA 4 — EXECUTE_STAGE_X`
- SE a avaliação PASSAR → seguir para `ETAPA 6 — CODIFY`

---

## ETAPA 6 — CODIFY

### ENTRADA
- Código aprovado da Etapa X

### AÇÕES
- Verificar:
  - Arquivos criados
  - Arquivos modificados
- Garantir que as alterações:
  - São coerentes com o código existente
  - Não quebram etapas futuras planejadas

### REGRAS DE ATUALIZAÇÃO DE CODIFY
- Modificar `/docs/guidelines/codifygeneral.md` SOMENTE se a regra for válida para qualquer tipo de projeto.
- Modificar `/docs/planproject/08-CodifyProject.md` SOMENTE se a regra for específica do projeto atual.

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
    → PLANPROJECT_GENERATION | PLANPROJECT_READING
      → DELEGATE
        → EXECUTE_STAGE_X
          → ASSESS_STAGE_X
            → CODIFY
              → LOOP_CONTROL → EXECUTE_STAGE_X+1
