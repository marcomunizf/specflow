# Project Guidelines

## Descrição do Guia

Este documento reúne **regras específicas do projeto atual**.
Ele registra decisões técnicas, arquiteturais e de domínio que **valem apenas para
este sistema** e não devem ser automaticamente reutilizadas em outros projetos.

O objetivo deste guia é:
- evitar ambiguidades durante o desenvolvimento,
- alinhar todos os envolvidos (humanos e agentes de IA),
- garantir consistência entre código, SPEC, arquitetura e decisões de negócio.

---

## Quando criar uma nova regra neste guideline

Uma nova regra deve ser adicionada a este documento quando:

- A regra depende diretamente do **domínio de negócio** do projeto.
- A decisão foi tomada durante o desenvolvimento ou ASSESS e precisa ser preservada.
- Existe mais de uma forma possível de implementar algo, e o projeto escolheu uma delas.
- A ausência da regra pode gerar comportamentos inconsistentes.
- A regra **não faz sentido fora deste projeto específico**.

Se a regra for aplicável a qualquer sistema, ela **não deve** entrar aqui,
devendo ser registrada em `general-guidelines.txt`.

---

## Estrutura de uma regra específica do projeto

Cada regra deste guideline deve conter os seguintes itens:

1. **Identificador da Regra**  
   Código único da regra (ex.: P-01, P-02).

2. **Nome da Regra**  
   Descrição curta e objetiva da decisão tomada.

3. **Contexto**  
   Explicação do problema ou necessidade específica do projeto.

4. **Regra**  
   Definição clara do comportamento obrigatório do sistema.

5. **Onde se aplica**  
   Camadas ou partes do sistema afetadas (ex.: backend, frontend, database, API).

6. **Impacto técnico**  
   Consequências da regra na implementação e manutenção do sistema.

7. **Relação com RF/RNF**  
   Referência aos requisitos funcionais ou não funcionais relacionados.

---

## Exemplo de Regra do Projeto

### Regra P-01 — Exclusão lógica de entidades

**Contexto**  
O projeto exige preservação de histórico e rastreabilidade de dados.

**Regra**  
Entidades não devem ser removidas fisicamente do banco de dados.
A exclusão deve ser realizada por meio de um campo de status
(ex.: `status = 'inativo'`).

**Onde se aplica**
- Database
- Repositories
- Services
- API

**Impacto técnico**
- Consultas devem filtrar registros ativos por padrão.
- Repositórios devem considerar status nas operações.
- Endpoints de exclusão devem apenas alterar o status.

**Relação com RF**
RF-XX — Desativar entidade

---

## Uso deste documento

- Este guideline é **exclusivo do projeto**.
- Deve ser lido antes de iniciar qualquer etapa de desenvolvimento.
- Deve ser atualizado sempre que uma nova decisão específica for tomada.
- Regras obsoletas devem ser revisadas ou removidas ao longo do tempo.
