# 06 – DataBase Schema — Guia de Estrutura Física dos Dados

## 📌 O que este arquivo deve conter
Este documento descreve a **estrutura física dos dados** do projeto, normalmente em um banco relacional (como PostgreSQL, MySQL etc.) ou, se for o caso, em outro tipo de banco (NoSQL, grafos, chave-valor).

Ele deve responder claramente:

- **Quais tabelas/coleções existem?**
- **Quais campos cada uma possui?**
- **Como as entidades se relacionam?**
- **Quais restrições e regras de integridade são impostas pelo banco?**

Nada aqui fala de regras de tela ou fluxo de usuário:  
o foco é **modelagem de dados**, **tipos**, **chaves** e **relacionamentos**.

---

## 🔍 Pontos de atenção

- O schema deve estar **alinhado à SPEC e à Arquitetura**.
- Toda entidade importante descrita na SPEC deve ter um reflexo aqui.
- Definir tipos adequados evita problemas de consistência e performance.
- Chaves primárias, estrangeiras e índices precisam estar documentados.
- Sempre indicar quando algo for **opcional** (`NULL` / `NOT NULL`) e quando houver **valores padrão**.

---

# 1. Visão Geral do Banco de Dados

Descreva, em alto nível:

- Tipo de banco de dados (ex.: PostgreSQL, MySQL, MongoDB).
- Estratégia geral:
  - Banco relacional com tabelas normalizadas?
  - Estrutura híbrida com cache ou documentos?
- Convenções gerais:
  - Padrão de nomes (snake_case, singular/plural).
  - Schema usado (ex.: `public`, `app`, etc.).

Exemplo:
> O sistema utiliza PostgreSQL, com um schema principal chamado `app`.  
> Todas as tabelas seguem o padrão `snake_case` e nomes no singular.

---

# 2. Tabelas (ou Coleções) Principais

Liste todas as tabelas principais do projeto e sua função.

Exemplo:
- `usuarios` — armazena informações de usuários da aplicação.  
- `projetos` — representa projetos criados pelos usuários.  
- `tarefas` — itens associados a um projeto específico.  

---

# 3. Definição Detalhada de Cada Tabela

Para cada tabela, defina:

- Nome da tabela
- Campos (nome, tipo, obrigatoriedade, default)
- Chave primária
- Chaves estrangeiras
- Índices relevantes
- Regras/constraints

## 3.1 Exemplo de tabela genérica

### Tabela: `usuarios`

Objetivo: armazenar informações dos usuários do sistema.

DDL de exemplo (PostgreSQL):
```sql
CREATE TABLE IF NOT EXISTS usuarios (
    id              SERIAL PRIMARY KEY,
    nome            VARCHAR(150) NOT NULL,
    email           VARCHAR(150) NOT NULL UNIQUE,
    senha_hash      VARCHAR(255) NOT NULL,
    perfil          VARCHAR(30)  NOT NULL,
    ativo           BOOLEAN      NOT NULL DEFAULT TRUE,
    created_at      TIMESTAMP    NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMP    NOT NULL DEFAULT NOW()
);
```

---

### Tabela: `projetos`

Objetivo: representar projetos que pertencem a usuários.

```sql
CREATE TABLE IF NOT EXISTS projetos (
    id              SERIAL PRIMARY KEY,
    id_usuario      INTEGER      NOT NULL REFERENCES usuarios(id),
    nome            VARCHAR(150) NOT NULL,
    descricao       TEXT,
    status          VARCHAR(20)  NOT NULL DEFAULT 'ativo',
    created_at      TIMESTAMP    NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMP    NOT NULL DEFAULT NOW()
);
```

---

# 4. Campos: Tipos, Nulidade e Defaults

Importante indicar:

- Tipo (VARCHAR, TEXT, INTEGER, NUMERIC, BOOLEAN, TIMESTAMP, etc.).
- Se aceita NULL.
- Se tem default.
- Se possui validação via CHECK.

Exemplo:
```sql
ALTER TABLE projetos
ADD CONSTRAINT chk_projetos_status
CHECK (status IN ('ativo', 'inativo', 'arquivado'));
```

---

# 5. Relacionamentos (Foreign Keys)

Descrever relações entre tabelas:

- 1:1  
- 1:N  
- N:N  

Exemplo 1:N:
```sql
id_usuario INTEGER NOT NULL REFERENCES usuarios(id)
```

Exemplo N:N:
```sql
CREATE TABLE usuarios_grupos (
    id_usuario  INTEGER NOT NULL REFERENCES usuarios(id),
    id_grupo    INTEGER NOT NULL REFERENCES grupos(id),
    PRIMARY KEY (id_usuario, id_grupo)
);
```

---

# 6. Índices

Documentar onde existem índices e por quê.

Exemplo:
```sql
CREATE INDEX idx_usuarios_email ON usuarios (email);
CREATE INDEX idx_projetos_status ON projetos (status);
```

---

# 7. Views (Visões)

Exemplo:
```sql
CREATE OR REPLACE VIEW vw_projetos_ativos AS
SELECT
    p.id,
    p.nome,
    p.status,
    u.nome AS nome_usuario
FROM projetos p
JOIN usuarios u ON u.id = p.id_usuario
WHERE p.status = 'ativo';
```

---

# 8. Regras de Integridade e Cascata

Exemplo:
```sql
CREATE TABLE tarefas (
    id          SERIAL PRIMARY KEY,
    id_projeto  INTEGER NOT NULL REFERENCES projetos(id) ON DELETE CASCADE,
    titulo      VARCHAR(200) NOT NULL,
    concluida   BOOLEAN      NOT NULL DEFAULT FALSE
);
```

---

# 9. Considerações de Performance

Exemplos:
- Particionamento por data
- Arquivamento periódico
- Indexação adicional
- Cache de consultas

---

# 10. Migrações

Documentar ferramentas e convenções:

Exemplo:
- Flyway (`V001__create_usuarios.sql`)
- Prisma migrations
- Sequelize migrations

---

# 11. Checklist por Tabela

- [ ] Nome da tabela  
- [ ] Objetivo  
- [ ] Campos  
- [ ] Chave primária  
- [ ] FKs  
- [ ] Índices  
- [ ] Constraints  
- [ ] Views  
- [ ] Observações  
