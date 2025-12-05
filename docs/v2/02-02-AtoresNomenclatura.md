# 02.02 — Atores e Glossário

---
## 👥 Atores do Sistema
- Separar todos os atores do sistema com roles

### 1. Administrador
- Permissões
  - Acesso total ao sistema.
  - Pode criar, editar e excluir usuários.
  - Pode gerenciar configurações internas.
  - Pode visualizar, editar e remover todos os itens.

### 2. Usuário Operacional
- Permissões
  - Pode autenticar.
  - Pode visualizar itens.
  - Pode criar ou editar itens conforme permissões específicas.
  - Acesso limitado às informações do sistema (Definir as informações que tem acesso ou que não tem acesso)

### 3. Serviço Externo (atual ou futuro)
- Consumirá endpoints específicos.
- Terá permissões restritas e controladas via token de integração.

---

## 📘 Glossário
- Definir nessa etapa toda a nomenclatura do sistema


**Autenticação:**  
Processo de validar usuário via credenciais.

**Autorização:**  
Determinar o que o usuário pode ou não pode acessar.

**Item:**  
Entidade principal manipulada pelo usuário. Pode representar qualquer registro operacional do domínio.

**Token JWT:**  
Token de autenticação que identifica e valida o usuário logado.

**Entidade:**  
Qualquer recurso persistido no banco de dados.

**Registro:**  
Linha individual de uma entidade/tabela.

**Endpoint:**  
Rota da API utilizada para comunicação entre frontend e backend.
