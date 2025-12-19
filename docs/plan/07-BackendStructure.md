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

O backend deve ser executável via **Docker** e **Docker Compose**, garantindo que qualquer desenvolvedor ou agente de IA consiga subir o ambiente completo com um único comando.

### 9.1 Objetivos

- Padronizar o ambiente de execução do backend.
- Facilitar o setup local (`docker-compose up`).
- Isolar a API e o banco em containers distintos, mas interligados.
- Preparar o projeto para futura orquestração (Kubernetes, ECS, etc.), se necessário.

### 9.2 Arquivo `Dockerfile` do backend

O projeto deve conter um `Dockerfile` na raiz (ou na pasta do backend) com:

- Imagem base: Node.js (versão LTS definida na Tech Stack).
- Diretório de trabalho (`WORKDIR`) configurado.
- Cópia do `package.json` e `package-lock.json` (ou `pnpm-lock`, `yarn.lock`) antes do restante do código, para otimizar cache de dependências.
- Instalação de dependências (`npm ci` ou comando equivalente).
- Cópia do código fonte.
- Exposição da porta da API (ex.: `EXPOSE 3000`).
- Comando padrão de execução (`CMD ["npm", "run", "start"]` ou script equivalente configurado pelo projeto).

Em ambiente de desenvolvimento, o `Dockerfile` pode ser ajustado para suportar `npm run dev` com nodemon ou ferramenta similar, se definido nas convenções do projeto.

### 9.3 Arquivo `docker-compose.yml`

O projeto deve incluir um arquivo `docker-compose.yml` com, no mínimo:

- Serviço `api`:
  - Build baseado no `Dockerfile` do backend.
  - Mapeamento de porta externa → interna (ex.: `3000:3000`).
  - Variáveis de ambiente necessárias (carregadas de `.env` quando possível).
  - Dependência explícita do serviço de banco (`depends_on: ["db"]`).

- Serviço `db`:
  - Imagem oficial do PostgreSQL.
  - Configuração de usuário, senha e banco padrão via variáveis de ambiente.
  - Volume nomeado para persistência dos dados (ex.: `pgdata:/var/lib/postgresql/data`).
  - Porta exposta para acesso local quando necessário (ex.: `5432:5432` em ambiente de desenvolvimento).

- Definição de `volumes`:
  - `pgdata` ou outro nome padrão definido pelo projeto para dados do banco.

### 9.4 Variáveis de ambiente e conexão com o banco

A conexão do backend com o banco deve utilizar o host do serviço Docker (ex.: `db`) em vez de `localhost`, por exemplo:

- `DATABASE_URL=postgres://usuario:senha@db:5432/nome_banco`

Essas variáveis devem ser centralizadas em um arquivo `.env` (não versionado, ou com `.env.example` de referência) e lidas pela camada `/config` do backend.

### 9.5 Fluxo recomendado de uso

- Subir o ambiente local:
  - `docker-compose up` ou `docker-compose up --build` quando houver alterações de dependências.
- Derrubar o ambiente:
  - `docker-compose down` (mantendo volume).
  - `docker-compose down -v` para remover volumes (apenas em casos específicos).

### 9.6 Responsabilidades dos agentes e do ASSESS

- O agente de código (DELEGATE) deve:
  - Garantir que o `Dockerfile` e o `docker-compose.yml` estejam alinhados à arquitetura e à Tech Stack.
  - Atualizar os arquivos de containerização ao incluir novos serviços que dependam de infraestrutura (ex.: cache, filas).

- A fase ASSESS deve:
  - Verificar se o backend sobe corretamente via Docker.
  - Confirmar se as variáveis de ambiente usadas no código estão coerentes com as definidas no `docker-compose.yml`.
  - Validar se o schema de banco (migrações) está aplicável no container de banco configurado.

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
