# 07 – Backend Structure — Guia de Organização do Backend

## 📌 O que este arquivo deve conter
Este documento define **como o backend deve ser organizado fisicamente**, incluindo:
- Estrutura de diretórios
- Padrões de organização de código
- Fluxo de inicialização
- Boas práticas obrigatórias
- Convenções para rotas, controllers, services e repositórios
- Regras de autenticação, logs, erros e middlewares

Ele orienta qualquer desenvolvedor — humano ou IA — a implementar o backend do projeto de forma padronizada e consistente.

---

## 🔍 Pontos de atenção
- Toda estrutura deve refletir diretamente a SPEC e a API Routes.
- A organização deve ser simples, padronizada e escalável.
- Código repetido deve ser proibido: **usar services e utils**.
- Middlewares globais devem ser declarados no início da aplicação.
- Controllers não devem conter regras de negócio — apenas orquestrar.
- Repositórios não devem conter lógica além de leitura/escrita.

---

# 1. Estrutura de Diretórios

A estrutura abaixo é o **modelo padrão recomendado**:

```
/src
  /routes
  /controllers
  /services
  /repositories
  /middlewares
  /utils
  /config
app.ts
server.ts
```

### Função de cada diretório:

#### `/routes`
- Define os endpoints da API.
- Deve ser separado por domínio funcional.
- Cada arquivo deve importar seu controller correspondente.

Exemplo de arquivo:
```
/routes/users.routes.ts
```

---

#### `/controllers`
Responsáveis apenas por:
- Receber e validar entrada
- Chamar o service apropriado
- Retornar respostas em JSON
- Nunca aplicar regra de negócio

Exemplo:
```
/controllers/users.controller.ts
```

---

#### `/services`
- Contém as regras de negócio
- Implementa fluxos definidos na SPEC
- Pode chamar múltiplos repositórios
- Nunca acessa diretamente o `req` ou `res`

Exemplo:
```
/services/users.service.ts
```

---

#### `/repositories`
- Funções de acesso ao banco de dados
- Um repositório por entidade principal
- Apenas CRUD + queries específicas

Exemplo:
```
/repositories/users.repository.ts
```

---

#### `/middlewares`
Contém middlewares como:
- Autenticação
- Autorização
- Validação
- Logs
- Tratamento de erros

Exemplo:
```
/middlewares/auth.middleware.ts
```

---

#### `/utils`
Funções utilitárias e helpers como:
- Formatação de dados
- Geração de tokens
- Hash de senhas
- Validadores genéricos

---

#### `/config`
Arquivos de configuração do projeto:
- Variáveis de ambiente
- Conexão com banco
- Configuração de logs
- Configuração de serviços externos

---

# 2. Fluxo de Inicialização

### `server.ts`
Ponto inicial que:
- Carrega variáveis do `.env`
- Inicializa o app
- Liga a porta do servidor

### `app.ts`
- Registra middlewares globais
- Registra rotas
- Registra tratamento global de erros

Fluxo típico:

```
server.ts → app.ts → middlewares globais → rotas → controllers → services → repositories
```

---

# 3. Padrões de Rotas

As rotas devem seguir o padrão REST documentado em **API Routes**.

Exemplo de rota:

```ts
router.get("/", controller.list);
router.post("/", controller.create);
router.get("/:id", controller.get);
router.put("/:id", controller.update);
router.delete("/:id", controller.remove);
```

Regras:
- Nome dos arquivos deve respeitar o domínio: `users.routes.ts`
- Toda rota deve ter controller correspondente

---

# 4. Padrões de Controllers

Os controllers devem:
- Validar inputs básicos
- Chamar métodos dos serviços
- Nunca aplicar regras de negócio
- Retornar JSON padronizado
- Tratar erros de forma unificada via middleware global

Exemplo simplificado:

```ts
async function create(req, res, next) {
  try {
    const result = await userService.create(req.body);
    res.status(201).json(result);
  } catch (err) {
    next(err);
  }
}
```

---

# 5. Padrões de Services

Os services implementam:
- Regras de negócio
- Fluxos definidos na SPEC
- Processamentos intermediários
- Chamadas a múltiplos repositórios

Exemplo:

```ts
async function create(data) {
  await validateUser(data);
  return userRepository.create(data);
}
```

---

# 6. Padrões de Repositórios

Os repositórios fazem:
- CRUD completo
- Queries específicas
- Conexão com o banco via ORM ou query builder

Exemplo:

```ts
async function findById(id) {
  return db.query("SELECT * FROM usuarios WHERE id = $1", [id]);
}
```

---

# 7. Middlewares Globais

Middlewares recomendados:

### 7.1 Autenticação
- Valida token
- Anexa usuário ao `req.user`

### 7.2 Autorização
- Verifica permissões do usuário

### 7.3 Tratamento Global de Erros
Centraliza mensagens e evita código repetido.

### 7.4 Logs
Registra:
- Requisições
- Respostas
- Erros

### 7.5 CORS
Liberar ou restringir origens.

---

# 8. Conexão com Banco de Dados

No diretório `/config`, deve existir:

```
/config/database.ts
```

Exemplo genérico:

```ts
import { Pool } from "pg";

export const db = new Pool({
  connectionString: process.env.DATABASE_URL
});
```

---

# 9. Docker e docker-compose

Arquivo `docker-compose.yml` deve conter:
- Serviço do backend
- Serviço do banco de dados
- Volume para persistência
- Rede interna

Exemplo simplificado:

```yaml
services:
  api:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db

  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: example
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

---

# 10. Boas Práticas e Padrões Internos

- Usar ESLint + Prettier
- Padrão de commits (Conventional Commits)
- Padronização de erros
- Proibir código duplicado
- Variáveis de ambiente documentadas
- Não acessar banco diretamente em controllers
- Não aplicar regras de negócio em rotas ou controllers
- Testes unitários para services

---

# 11. Checklist do Backend

- [ ] Estrutura de pastas criada  
- [ ] Rotas padronizadas  
- [ ] Controllers limpos  
- [ ] Services com lógica de negócio  
- [ ] Repositórios independentes  
- [ ] Middlewares globais instalados  
- [ ] Tratamento de erros centralizado  
- [ ] Docker funcionando  
- [ ] Conexão com banco isolada  
- [ ] Utils bem categorizados  
