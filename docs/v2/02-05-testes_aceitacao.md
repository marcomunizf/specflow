# 02-06 — Casos de Teste de Aceitação (CTA)

Os Casos de Teste de Aceitação (CTA) descrevem como cada Requisito Funcional deve ser validado na prática.
Eles representam cenários reais de uso, contendo entradas, condições, passos e resultados esperados.
O objetivo dos CTA é garantir que:

- cada funcionalidade implementada atende exatamente ao que foi especificado no RF,
- o comportamento do sistema está correto,
- a experiência do usuário final corresponde ao esperado,
- e a entrega pode ser considerada “aceita”.

Os CTAs servem como ponte entre RF + regras de negócio → implementação → validação final.

---
## Estrutura dos Casos de Teste de Aceitação (CTA)

Cada caso de teste deve conter:
- Objetivo – O que o teste pretende validar.
- Pré-condições – O que deve existir ou estar configurado antes do teste iniciar.
- Entrada / Ação do Usuário – O que o usuário faz ou o que o sistema recebe como entrada.
- Passos do Teste – A sequência de ações executadas para validar o caso.
- Resultado Esperado – Resposta que o sistema deve apresentar para que o teste seja considerado aprovado.

Os CTAs são sempre baseados nos Requisitos Funcionais e devem cobrir os cenários principais, alternativos e de erro.

---
## Exemplos de Casos de Teste de Aceitação

CTA-01 — Login com credenciais válidas

Objetivo:
- Validar que um usuário consegue realizar login fornecendo credenciais corretas.

Pré-condições:
- Usuário cadastrado no sistema.
- Usuário ativo.

Entrada / Ação:
- Enviar POST /api/auth/login com usuario e senha válidos.

Passos:
- Usuário preenche login e senha.
- Envia o formulário.
- O backend valida credenciais.
- O sistema gera um token JWT.

Resultado Esperado:
- Retorno HTTP 200.
- Corpo da resposta contém:
  ```json
  { "token": "<jwt>" }
  ```
- Usuário é autenticado e pode acessar rotas protegidas.

  ---
  CTA-02 — Falha no login com senha incorreta

Objetivo:
- Confirmar que o sistema rejeita credenciais inválidas.

Pré-condições:
- Usuário cadastrado.
- Senha fornecida é incorreta.

Entrada / Ação:
- Enviar POST /api/auth/login com senha inválida.

Passos:
- Usuário tenta autenticar.
- Backend identifica a senha incorreta.
- Sistema bloqueia o acesso.

Resultado Esperado:
- HTTP 401.
- Mensagem:
  ```http
  "Usuário ou senha inválidos"
  ```
