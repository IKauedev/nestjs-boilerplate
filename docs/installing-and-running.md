# Instalação

O NestJS Boilerplate suporta [TypeORM](https://www.npmjs.com/package/typeorm) e [Mongoose](https://www.npmjs.com/package/mongoose) para trabalhar com bancos de dados. Por padrão, o TypeORM usa [PostgreSQL](https://www.postgresql.org/) como banco principal, mas você pode usar qualquer banco relacional.

A troca entre TypeORM e Mongoose é implementada com base na [Arquitetura Hexagonal](architecture.md#hexagonal-architecture). Isso facilita a escolha do banco ideal para sua aplicação.

---

## Índice <!-- omit in toc -->

- [Desenvolvimento confortável (PostgreSQL + TypeORM)](#desenvolvimento-confortavel-postgresql--typeorm)
  - [Vídeo tutorial (PostgreSQL + TypeORM)](#video-tutorial-postgresql--typeorm)
- [Desenvolvimento confortável (MongoDB + Mongoose)](#desenvolvimento-confortavel-mongodb--mongoose)
- [Execução rápida (PostgreSQL + TypeORM)](#execucao-rapida-postgresql--typeorm)
- [Execução rápida (MongoDB + Mongoose)](#execucao-rapida-mongodb--mongoose)
- [Links](#links)

---

## Desenvolvimento confortável (PostgreSQL + TypeORM)

1. Clone o repositório

   ```bash
   git clone --depth 1 https://github.com/brocoders/nestjs-boilerplate.git my-app
   ```

1. Vá para a pasta e copie `env-example-relational` como `.env`.

   ```bash
   cd my-app/
   cp env-example-relational .env
   ```

1. Altere `DATABASE_HOST=postgres` para `DATABASE_HOST=localhost`

   Altere `MAIL_HOST=maildev` para `MAIL_HOST=localhost`

1. Execute o container adicional:

   ```bash
   docker compose up -d postgres adminer maildev
   ```

1. Instale as dependências

   ```bash
   npm install
   ```

1. Execute a configuração do app

   > Execute este comando apenas na primeira inicialização do projeto, nas próximas vezes pule este passo.

   > Se você for contribuir com o boilerplate, NÃO execute este comando.

   ```bash
   npm run app:config
   ```

1. Execute as migrações

   ```bash
   npm run migration:run
   ```

1. Execute os seeds

   ```bash
   npm run seed:run:relational
   ```

1. Execute o app em modo dev

   ```bash
   npm run start:dev
   ```

1. Abra <http://localhost:3000>

### Vídeo tutorial (PostgreSQL + TypeORM)

<https://github.com/user-attachments/assets/136a16aa-f94a-4b20-8eaf-6b4262964315>

---

## Desenvolvimento confortável (MongoDB + Mongoose)

1. Clone o repositório

   ```bash
   git clone --depth 1 https://github.com/brocoders/nestjs-boilerplate.git my-app
   ```

1. Vá para a pasta e copie `env-example-document` como `.env`.

   ```bash
   cd my-app/
   cp env-example-document .env
   ```

1. Altere `DATABASE_URL=mongodb://mongo:27017` para `DATABASE_URL=mongodb://localhost:27017`

1. Execute o container adicional:

   ```bash
   docker compose -f docker-compose.document.yaml up -d mongo mongo-express maildev
   ```

1. Instale as dependências

   ```bash
   npm install
   ```

1. Execute a configuração do app

   > Execute este comando apenas na primeira inicialização do projeto, nas próximas vezes pule este passo.

   > Se você for contribuir com o boilerplate, NÃO execute este comando.

   ```bash
   npm run app:config
   ```

1. Execute os seeds

   ```bash
   npm run seed:run:document
   ```

1. Execute o app em modo dev

   ```bash
   npm run start:dev
   ```

1. Abra <http://localhost:3000>

---

## Execução rápida (PostgreSQL + TypeORM)

Se quiser rodar rapidamente sua aplicação, use os comandos abaixo:

1. Clone o repositório

   ```bash
   git clone --depth 1 https://github.com/brocoders/nestjs-boilerplate.git my-app
   ```

1. Vá para a pasta e copie `env-example-relational` como `.env`.

   ```bash
   cd my-app/
   cp env-example-relational .env
   ```

1. Execute os containers

   ```bash
   docker compose up -d
   ```

1. Para checar o status

   ```bash
   docker compose logs
   ```

1. Abra <http://localhost:3000>

---

## Execução rápida (MongoDB + Mongoose)

Se quiser rodar rapidamente sua aplicação, use os comandos abaixo:

1. Clone o repositório

   ```bash
   git clone --depth 1 https://github.com/brocoders/nestjs-boilerplate.git my-app
   ```

1. Vá para a pasta e copie `env-example-document` como `.env`.

   ```bash
   cd my-app/
   cp env-example-document .env
   ```

1. Execute os containers

   ```bash
   docker compose -f docker-compose.document.yaml up -d
   ```

1. Para checar o status

   ```bash
   docker compose -f docker-compose.document.yaml logs
   ```

1. Abra <http://localhost:3000>

---

## Links

- Swagger (API docs): <http://localhost:3000/docs>
- Adminer (cliente para o BD): <http://localhost:8080>
- MongoDB Express (cliente para o BD): <http://localhost:8081/>
- Maildev: <http://localhost:1080>

---

Anterior: [Introdução](introduction.md)

Próximo: [Arquitetura](architecture.md)
