# 05 - API Routes — Guia Completo de Contrato da API

## 📌 O que este arquivo deve conter
Este documento descreve **todas as rotas da API (endpoints)** e funciona como um **contrato formal** entre:
- Frontend  
- Backend  
- Serviços internos  
- Serviços externos  
- Agentes de IA

Deve ser claro, completo e sem ambiguidade.  
Ele representa **o padrão oficial de comunicação** do projeto.

---

# 🔍 Pontos de atenção
- Cada rota deve ser completamente documentada.  
- Os exemplos devem ser reais e claros.  
- Todas as regras de autenticação e autorização precisam estar explícitas.  
- Toda rota aqui deve existir na SPEC.  
- Cada rota deve indicar seus **parâmetros**, **request body**, **respostas** e **erros esperados**.

---

# 1. Convenções Gerais

## 1.1 Base URL
A base de todas as rotas deve ser única e padronizada.

Exemplo:
```
Base URL: /api
```

## 1.2 Formato das Respostas
Todas as respostas da API seguem o formato **JSON**.

Exemplo de erro padrão:
```json
{
  "error": "RESOURCE_NOT_FOUND",
  "message": "Recurso não encontrado."
}
```

## 1.3 Autenticação
O formato padrão de autenticação é:

```
Authorization: Bearer <token>
```

## 1.4 Padrões de Erro
Utilizar códigos HTTP consistentes:

- **400** – Dados inválidos  
- **401** – Token ausente ou inválido  
- **403** – Usuário sem permissão  
- **404** – Recurso não encontrado  
- **409** – Conflito (registro duplicado / estado inválido)  
- **422** – Regras de negócio inválidas  
- **500** – Erro interno inesperado  

---

# 2. Organização das Rotas por Domínio
Cada grupo de rotas deve ser organizado por domínio funcional do sistema.  
Os exemplos abaixo devem ser usados como modelo geral.

Domínios comuns:
- Autenticação  
- Usuários  
- Configurações  
- Entidades principais do sistema  
- Relatórios  
- Integrações externas  

Cada domínio deve conter:
- Descrição de propósito  
- Lista de endpoints  
- Regras de permissão  
- Exemplos completos  

---

# 3. Domínio: Autenticação

## 3.1 POST `/api/auth/login`

**Descrição:**  
Autentica um usuário e retorna um token válido.

**Autenticação:**  
- Rota aberta (não exige token)

**Request Body (exemplo):**
```json
{
  "usuario": "usuario.teste",
  "senha": "SenhaSegura123"
}
```

**Response 200 (exemplo):**
```json
{
  "token": "<jwt_token_aqui>",
  "usuario": {
    "id": 1,
    "nome": "Usuário Teste",
    "usuario": "usuario.teste",
    "permissoes": ["BASICO"]
  }
}
```

**Erros possíveis:**
- `400` – corpo inválido  
- `401` – credenciais incorretas  

---

# 4. Domínio: Usuários

## 4.1 GET `/api/users`

**Descrição:**  
Lista usuários cadastrados.

**Autenticação:**  
- Exige token  
- Pode exigir permissão administrativa  

**Query Params (opcionais):**
```
status=ativo|inativo
page=1
pageSize=20
```

**Response 200 (exemplo):**
```json
[
  {
    "id": 1,
    "nome": "Administrador",
    "usuario": "admin",
    "status": "ativo"
  },
  {
    "id": 2,
    "nome": "Usuário Operacional",
    "usuario": "operador",
    "status": "ativo"
  }
]
```

---

## 4.2 POST `/api/users`

**Descrição:**  
Cria um novo usuário no sistema.

**Autenticação:**  
- Exige token válido  
- Permissão: Administrador  

**Body (exemplo):**
```json
{
  "nome": "Novo Usuário",
  "usuario": "novo.usuario",
  "senha": "SenhaForte123",
  "perfil": "OPERACIONAL"
}
```

**Response 201 (exemplo):**
```json
{
  "id": 3,
  "nome": "Novo Usuário",
  "usuario": "novo.usuario",
  "perfil": "OPERACIONAL",
  "status": "ativo"
}
```

**Erros possíveis:**
- `400` – campos obrigatórios ausentes  
- `409` – usuário já existente  

---

## 4.3 GET `/api/users/:id`

**Descrição:**  
Retorna detalhes de um usuário.

**Response 200 (exemplo):**
```json
{
  "id": 2,
  "nome": "Usuário Operacional",
  "usuario": "operador",
  "perfil": "BASICO",
  "status": "ativo"
}
```

**Erros possíveis:**  
- `404` – usuário não encontrado  

---

## 4.4 PUT `/api/users/:id`

**Descrição:**  
Atualiza completamente um usuário.

**Body (exemplo):**
```json
{
  "nome": "Usuário Atualizado",
  "perfil": "ADMIN",
  "status": "ativo"
}
```

**Response 200:**
```json
{
  "id": 2,
  "nome": "Usuário Atualizado",
  "perfil": "ADMIN",
  "status": "ativo"
}
```

---

## 4.5 PATCH `/api/users/:id/password`

**Descrição:**  
Atualiza apenas a senha do usuário.

**Body (exemplo):**
```json
{
  "senhaAtual": "SenhaAntiga",
  "novaSenha": "SenhaNova123"
}
```

**Response 204:** sem corpo.

---

# 5. Domínio Genérico: Entidades Principais

> Esta seção serve como **exemplo para qualquer entidade do sistema**  
> (projetos, tarefas, itens, registros, processos etc)

---

## 5.1 GET `/api/items`

**Descrição:**  
Lista itens do domínio principal.

**Response 200 (exemplo):**
```json
[
  {
    "id": 1,
    "nome": "Item A",
    "descricao": "Descrição do item A",
    "status": "ativo"
  }
]
```

---

## 5.2 GET `/api/items/:id`

**Response 200 (exemplo):**
```json
{
  "id": 1,
  "nome": "Item A",
  "descricao": "Descrição do item A",
  "status": "ativo"
}
```

---

## 5.3 POST `/api/items`

**Body (exemplo):**
```json
{
  "nome": "Novo Item",
  "descricao": "Um item criado para teste",
  "status": "ativo"
}
```

**Response 201:**
```json
{
  "id": 10,
  "nome": "Novo Item",
  "descricao": "Um item criado para teste",
  "status": "ativo"
}
```

---

## 5.4 PUT `/api/items/:id`

**Body (exemplo):**
```json
{
  "nome": "Item Atualizado",
  "descricao": "Valor atualizado",
  "status": "ativo"
}
```

**Response 200 (exemplo):**
```json
{
  "id": 1,
  "nome": "Item Atualizado",
  "descricao": "Valor atualizado",
  "status": "ativo"
}
```

---

## 5.5 PATCH `/api/items/:id`

**Body (exemplo):**
```json
{
  "status": "inativo"
}
```

**Response 200:**
```json
{
  "id": 1,
  "nome": "Item A",
  "status": "inativo"
}
```

---

## 5.6 DELETE `/api/items/:id`

**Descrição:**  
Remove um item (lógico ou físico conforme regra da SPEC).

**Response 204:** sem conteúdo.

---

# 6. Regras de Autorização

Exemplo de tabela:

| Rota                  | Público | Autenticado | Admin |
|-----------------------|:-------:|:-----------:|:-----:|
| POST /auth/login      |   ✅    |      -      |   -   |
| GET /users            |   -     |      -      |  ✅   |
| GET /items            |   -     |      ✅     |  ✅   |
| POST /items           |   -     |      ✅     |  ✅   |

---

# 7. Status Codes Específicos do Domínio

- **422** – regras de negócio específicas violadas  
- **409** – duplicidade  
- **451** – operação proibida por política do sistema  

---

# 8. Boas Práticas para Documentação da API
- Sempre atualizar este arquivo quando qualquer rota mudar.  
- Todos os exemplos devem ser funcionais.  
- Toda nova feature deve vir acompanhada de suas rotas documentadas.  
- Rotas obsoletas devem ser marcadas como **deprecated** antes de serem removidas.  

---

# ✔ Fim do documento
