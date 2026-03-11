# ForumFlow API — API RESTful de Fórum de Perguntas e Respostas

> API RESTful para um sistema de fórum Q&A, desenvolvida com NestJS e Prisma ORM, com autenticação JWT, documentação Swagger e suporte a Docker.

[![NestJS](https://img.shields.io/badge/NestJS-11-E0234E?logo=nestjs&logoColor=white)](https://nestjs.com/)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Prisma](https://img.shields.io/badge/Prisma-6-2D3748?logo=prisma&logoColor=white)](https://www.prisma.io/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/compose/)
[![Swagger](https://img.shields.io/badge/Swagger-API_Docs-85EA2D?logo=swagger&logoColor=black)](https://swagger.io/)

---

## Sumário

- [Sobre o Projeto](#sobre-o-projeto)
- [Funcionalidades](#funcionalidades)
- [Tecnologias](#tecnologias)
- [Endpoints da API](#endpoints-da-api)
- [Modelos de Dados](#modelos-de-dados)
- [Instalação](#instalação)
- [Estrutura de Pastas](#estrutura-de-pastas)
- [Comandos Úteis](#comandos-úteis)

---

## Sobre o Projeto

**ForumFlow API** (nome sugerido para substituir "forum_project_api") é uma API REST para um fórum de perguntas e respostas. Usuários podem se registrar, autenticar via JWT, criar perguntas e submeter respostas. As relações entre usuários, perguntas e respostas formam um sistema de Q&A completo.

---

## Funcionalidades

- Cadastro e autenticação de usuários com JWT
- CRUD completo de perguntas e respostas
- Relacionamentos entre usuários, perguntas e respostas
- Validações com class-validator
- Documentação automática via Swagger
- Suporte a Docker e Docker Compose

---

## Tecnologias

| Categoria | Tecnologia |
|---|---|
| **Framework** | NestJS v11 |
| **Linguagem** | TypeScript (ES2023) |
| **ORM** | Prisma v6.6 |
| **Banco de Dados** | PostgreSQL 15 |
| **Autenticação** | JWT (`@nestjs/jwt`) |
| **Validação** | class-validator + class-transformer |
| **Documentação** | Swagger (`@nestjs/swagger`) |
| **Hash** | bcrypt |
| **Containerização** | Docker (multi-stage Node 20 Alpine) + Docker Compose |
| **Build** | SWC (compilação rápida) |
| **Testes** | Jest + Supertest |

---

## Endpoints da API

### Autenticação (`/auth`)

| Método | Rota | Auth | Descrição |
|---|---|---|---|
| `POST` | `/auth/login` | Pública | Login (retorna `access_token`) |

### Usuários (`/user`)

| Método | Rota | Auth | Descrição |
|---|---|---|---|
| `POST` | `/user` | Pública | Registrar usuário |
| `GET` | `/user` | JWT | Listar todos os usuários |
| `GET` | `/user/:id` | JWT | Buscar usuário por ID |
| `PUT` | `/user/:id` | JWT | Atualizar usuário |
| `DELETE` | `/user/:id` | JWT | Remover usuário |

### Perguntas (`/questions`)

| Método | Rota | Auth | Descrição |
|---|---|---|---|
| `POST` | `/questions` | JWT | Criar pergunta |
| `GET` | `/questions` | JWT | Listar perguntas (com respostas e autores) |
| `GET` | `/questions/:id` | JWT | Buscar pergunta por ID |
| `PATCH` | `/questions/:id` | JWT | Atualizar pergunta |
| `DELETE` | `/questions/:id` | JWT | Remover pergunta |

### Respostas (`/answers`)

| Método | Rota | Auth | Descrição |
|---|---|---|---|
| `POST` | `/answers/:questionId` | JWT | Responder uma pergunta |
| `GET` | `/answers` | JWT | Listar todas as respostas |
| `GET` | `/answers/:id` | JWT | Buscar resposta por ID |
| `PATCH` | `/answers/:id` | JWT | Atualizar resposta |
| `DELETE` | `/answers/:id` | JWT | Remover resposta |

---

## Modelos de Dados

```
User
├── id, email (unique), name?, password
├── createdAt, updatedAt
├── → Questions[]
└── → Answers[]

Questions
├── id, title, body, userId (FK)
├── createdAt, updatedAt
├── → User
└── → Answers[]

Answers
├── id, body, userId (FK), questionId (FK)
├── createdAt, updatedAt
├── → User
└── → Question
```

---

## Instalação sem Docker

### 1. Clone o projeto

```bash
git clone <url-do-repositorio>
cd forum_project_api
```

### 2. Instale as dependências

```bash
npm install
```

### 3. Configure o ambiente

Crie um arquivo `.env` na raiz com o seguinte conteúdo:

```env
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/forum_db?schema=public"
```

> Certifique-se de que o PostgreSQL esteja rodando localmente e o banco `forum_db` exista.

### 4. Rode as migrações e gere o cliente do Prisma

```bash
npx prisma migrate dev --name init
npx prisma generate
```

### 5. Inicie o servidor

```bash
npm run start:dev
```

A API estará disponível em:  
📍 `http://localhost:3000`

A documentação Swagger estará em:  
📘 `http://localhost:3000/api`

---

## Instalação com Docker

### 1. Configure seu `.env`

```env
DATABASE_URL="postgresql://postgres:postgres@db:5432/forum_db?schema=public"
```

### 2. Execute os containers

```bash
docker compose up --build
```

- API: `http://localhost:4000`
- Swagger: `http://localhost:4000/api`
- PostgreSQL: `localhost:5432`

---

## Estrutura de Pastas

```
forum_project_api/
├── src/
│   ├── main.ts                    # Bootstrap + Swagger (porta 3000)
│   ├── app.module.ts              # Root module (global ValidationPipe)
│   ├── auth/
│   │   ├── auth.controller.ts     # POST /auth/login
│   │   ├── auth.service.ts        # bcrypt + JWT sign
│   │   └── auth.guard.ts          # Bearer token guard
│   ├── user/
│   │   ├── user.controller.ts     # CRUD endpoints
│   │   ├── user.service.ts        # Prisma queries + hash
│   │   └── dto/                   # CreateUserDto, UpdateUserDto
│   ├── questions/
│   │   ├── questions.controller.ts
│   │   ├── questions.service.ts   # Includes nested answers + user
│   │   └── dto/
│   ├── answers/
│   │   ├── answers.controller.ts
│   │   ├── answers.service.ts
│   │   └── dto/
│   └── database/
│       └── prisma.service.ts      # PrismaClient extends
├── prisma/
│   ├── schema.prisma              # User, Questions, Answers
│   └── migrations/
├── docker-compose.yml             # PostgreSQL 15 + API (4000→3000)
├── Dockerfile                     # Multi-stage (Node 20 Alpine)
├── .env.example                   # DATABASE_URL + SECRET_KEY
└── package.json
```

---

## Comandos Úteis

```bash
npm run start           # Inicia a aplicação
npm run start:dev       # Inicia em modo de desenvolvimento
npm run build           # Compila o projeto
npm run format          # Formata o código
npm run test            # Executa os testes unitários
```

---

## Requisitos

- Node.js >= 20
- PostgreSQL (se rodar localmente)
- Docker (se preferir containerizar)

---

## Prisma

Alguns comandos úteis:

```bash
npx prisma generate          # Gera cliente Prisma
npx prisma migrate dev       # Executa migrações no banco local
npx prisma studio            # Abre interface visual do Prisma
```

---

## Swagger

Após iniciar o projeto, acesse a documentação da API interativa via Swagger em:

```
http://localhost:3000/api
```

Se estiver usando Docker:

```
http://localhost:4000/api
```

---

## Variáveis de Ambiente

| Variável | Descrição | Exemplo |
|---|---|---|
| `DATABASE_URL` | String de conexão PostgreSQL | `postgresql://postgres:postgres@db:5432/forum_db?schema=public` |
| `SECRET_KEY` | Chave secreta para assinatura JWT | `minha-chave-secreta` |

---

## Licença

Distribuído sob a licença [MIT](./LICENSE).

---

## Contribuições

Pull requests são bem-vindos. Para grandes mudanças, por favor abra uma issue primeiro para discutir o que você gostaria de mudar.