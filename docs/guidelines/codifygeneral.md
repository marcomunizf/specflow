# CODIFY - General Guidelines

## Descrição do Guia

Este documento reúne **regras gerais e reutilizáveis** aplicáveis a qualquer tipo de projeto
de software, independentemente de domínio, linguagem ou tecnologia.

O objetivo deste guia é capturar **aprendizados recorrentes**, erros comuns e boas práticas
identificadas ao longo de ciclos de desenvolvimento, revisão de código (lint, sonar,
agentic review) e evolução arquitetural.

Essas regras servem como base para orientar desenvolvedores humanos e agentes de IA,
reduzindo retrabalho, ambiguidade e reincidência de problemas já conhecidos.

---

## Quando criar uma nova regra neste guideline

Uma nova regra deve ser adicionada a este documento quando:

- Um mesmo erro ou problema aparece repetidamente em projetos diferentes.
- Uma decisão arquitetural ou prática mostrou-se eficaz e deve ser reaplicada.
- Uma falha detectada no ASSESS (lint, sonar ou agentic code) pode ser evitada no futuro.
- Existe risco de ambiguidade ou interpretação errada sem uma regra explícita.
- A regra **não depende de domínio de negócio**, sendo válida para qualquer sistema.

Se a regra for específica de um único projeto ou domínio, ela **não deve** entrar aqui,
devendo ser registrada no guideline específico do projeto.

---

## Estrutura de uma regra geral

Cada regra deste guideline deve conter os seguintes itens:

1. **Identificador da Regra**  
   Código único da regra (ex.: G-01, G-02).

2. **Nome da Regra**  
   Descrição curta e objetiva do princípio tratado.

3. **Contexto**  
   Situação ou problema recorrente que motivou a criação da regra.

4. **Regra**  
   Definição clara do que deve ou não deve ser feito.

5. **Justificativa**  
   Explicação do motivo da regra existir e qual problema ela evita.

6. **Exemplos (opcional)**  
   Exemplos de uso correto e incorreto, quando ajudarem no entendimento.

7. **Impacto**  
   Benefícios trazidos pela aplicação da regra (qualidade, segurança, manutenção, etc.).

---

## Exemplo de Regra Geral

### Regra G-01 — Controllers não devem conter regras de negócio

**Contexto**  
Em vários projetos, regras de negócio foram implementadas diretamente em controllers,
gerando código difícil de testar, manter e reutilizar.

**Regra**  
Controllers devem apenas:
- receber requisições,
- validar dados básicos,
- delegar a execução para services,
- retornar respostas.

Qualquer regra de negócio deve residir exclusivamente na camada de services.

**Justificativa**  
Centralizar regras de negócio evita duplicação de lógica, reduz acoplamento e melhora
testabilidade.

**Impacto**  
- Código mais limpo e organizado  
- Facilidade de testes unitários  
- Evolução arquitetural mais segura  

---

## Uso deste documento

- Este guideline é **global** e reutilizável.
- Deve ser lido antes de iniciar qualquer novo projeto.
- Deve ser atualizado continuamente conforme novos aprendizados surgirem.
