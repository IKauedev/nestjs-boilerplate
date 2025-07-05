# Banco de Dados

## Índice <!-- omit in toc -->

- [Sobre bancos de dados](#sobre-bancos-de-dados)
- [Trabalhando com schema do banco (TypeORM)](#trabalhando-com-schema-do-banco-typeorm)
  - [Gerar migração](#gerar-migracao)
  - [Executar migração](#executar-migracao)
  - [Reverter migração](#reverter-migracao)
  - [Remover todas as tabelas do banco](#remover-todas-as-tabelas-do-banco)
- [Trabalhando com schema do banco (Mongoose)](#trabalhando-com-schema-do-banco-mongoose)
  - [Criar schema](#criar-schema)
- [Seed (TypeORM)](#seed-typeorm)
  - [Criando seeds (TypeORM)](#criando-seeds-typeorm)
  - [Executar seed (TypeORM)](#executar-seed-typeorm)
  - [Factory e Faker (TypeORM)](#factory-e-faker-typeorm)
- [Seed (Mongoose)](#seed-mongoose)
  - [Criando seeds (Mongoose)](#criando-seeds-mongoose)
  - [Executar seed (Mongoose)](#executar-seed-mongoose)
- [Otimização de performance (PostgreSQL + TypeORM)](#otimizacao-de-performance-postgresql--typeorm)
  - [Índices e Chaves Estrangeiras](#indices-e-chaves-estrangeiras)
  - [Máximo de conexões](#maximo-de-conexoes)
- [Otimização de performance (MongoDB + Mongoose)](#otimizacao-de-performance-mongodb--mongoose)
  - [Desenhar schema](#desenhar-schema)
- [Trocar PostgreSQL por MySQL](#trocar-postgresql-por-mysql)

---

## Sobre bancos de dados

O boilerplate suporta dois tipos de bancos: PostgreSQL com TypeORM e MongoDB com Mongoose. Você pode escolher um deles ou usar ambos no seu projeto. A escolha depende dos requisitos do seu projeto.

Para suportar ambos os bancos é usada a [Arquitetura Hexagonal](architecture.md#hexagonal-architecture).

## Trabalhando com schema do banco (TypeORM)

### Gerar migração

1. Crie o arquivo da entidade com extensão `.entity.ts`. Por exemplo `post.entity.ts`:

   ```ts
   // /src/posts/infrastructure/persistence/relational/entities/post.entity.ts

   import { Column, Entity, PrimaryGeneratedColumn } from 'typeorm';
   import { EntityRelationalHelper } from '../../../../../utils/relational-entity-helper';

   @Entity()
   export class Post extends EntityRelationalHelper {
     @PrimaryGeneratedColumn()
     id: number;

     @Column()
     title: string;

     @Column()
     body: string;

     // Aqui qualquer campo que você precisar
   }
   ```

1. Em seguida, gere o arquivo de migração:

   ```bash
   npm run migration:generate -- src/database/migrations/CreatePostTable
   ```

1. Aplique esta migração ao banco via [npm run migration:run](#executar-migracao).

### Executar migração

```bash
npm run migration:run
```

### Reverter migração

```bash
npm run migration:revert
```

### Remover todas as tabelas do banco

```bash
npm run schema:drop
```

---

## Trabalhando com schema do banco (Mongoose)

### Criar schema

1. Crie o arquivo da entidade com extensão `.schema.ts`. Por exemplo `post.schema.ts`:

   ```ts
   // /src/posts/infrastructure/persistence/document/entities/post.schema.ts

   import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
   import { HydratedDocument } from 'mongoose';

   export type PostSchemaDocument = HydratedDocument<PostSchemaClass>;

   @Schema({
     timestamps: true,
     toJSON: {
       virtuals: true,
       getters: true,
     },
   })
   export class PostSchemaClass extends EntityDocumentHelper {
     @Prop()
     title: string;

     @Prop()
     body: string;

     // Aqui qualquer campo que você precisar
   }

   export const PostSchema = SchemaFactory.createForClass(PostSchemaClass);
   ```

---

## Seed (TypeORM)

### Criando seeds (TypeORM)

1. Crie o arquivo seed com `npm run seed:create:relational -- --name Post`. Onde `Post` é o nome da entidade.
1. Vá para `src/database/seeds/relational/post/post-seed.service.ts`.
1. No método `run` adicione sua lógica.
1. Execute [npm run seed:run:relational](#executar-seed-typeorm)

### Executar seed (TypeORM)

```bash
npm run seed:run:relational
```

### Factory e Faker (TypeORM)

1. Instale o faker:

    ```bash
    npm i --save-dev @faker-js/faker
    ```

1. Crie `src/database/seeds/relational/user/user.factory.ts`:

    ```ts
    import { faker } from '@faker-js/faker';
    import { RoleEnum } from '../../../../roles/roles.enum';
    import { StatusEnum } from '../../../../statuses/statuses.enum';
    import { Injectable } from '@nestjs/common';
    import { InjectRepository } from '@nestjs/typeorm';
    import { Repository } from 'typeorm';
    import { RoleEntity } from '../../../../roles/infrastructure/persistence/relational/entities/role.entity';
    import { UserEntity } from '../../../../users/infrastructure/persistence/relational/entities/user.entity';
    import { StatusEntity } from '../../../../statuses/infrastructure/persistence/relational/entities/status.entity';

    @Injectable()
    export class UserFactory {
      constructor(
        @InjectRepository(UserEntity)
        private repositoryUser: Repository<UserEntity>,
        @InjectRepository(RoleEntity)
        private repositoryRole: Repository<RoleEntity>,
        @InjectRepository(StatusEntity)
        private repositoryStatus: Repository<StatusEntity>,
      ) {}

      createRandomUser() {
        // Necessário para manter o contexto do "this"
        return () => {
          return this.repositoryUser.create({
            firstName: faker.person.firstName(),
            lastName: faker.person.lastName(),
            email: faker.internet.email(),
            password: faker.internet.password(),
            role: this.repositoryRole.create({
              id: RoleEnum.user,
              name: 'User',
            }),
            status: this.repositoryStatus.create({
              id: StatusEnum.active,
              name: 'Active',
            }),
          });
        };
      }
    }
    ```

1. Faça alterações em `src/database/seeds/relational/user/user-seed.service.ts`:

    ```ts
    // Algum código aqui...
    import { UserFactory } from './user.factory';
    import { faker } from '@faker-js/faker';

    @Injectable()
    export class UserSeedService {
      constructor(
        // Algum código aqui...
        private userFactory: UserFactory,
      ) {}

      async run() {
        // Algum código aqui...

        await this.repository.save(
          faker.helpers.multiple(this.userFactory.createRandomUser(), {
            count: 5,
          }),
        );
      }
    }
    ```

1. Faça alterações em `src/database/seeds/relational/user/user-seed.module.ts`:

    ```ts
    import { Module } from '@nestjs/common';
    import { TypeOrmModule } from '@nestjs/typeorm';
    
    import { UserSeedService } from './user-seed.service';
    import { UserFactory } from './user.factory';

    import { UserEntity } from '../../../../users/infrastructure/persistence/relational/entities/user.entity';
    import { RoleEntity } from '../../../../roles/infrastructure/persistence/relational/entities/role.entity';
    import { StatusEntity } from '../../../../statuses/infrastructure/persistence/relational/entities/status.entity';

    @Module({
      imports: [TypeOrmModule.forFeature([UserEntity, Role, Status])],
      providers: [UserSeedService, UserFactory],
      exports: [UserSeedService, UserFactory],
    })
    export class UserSeedModule {}

    ```

1. Execute o seed:

    ```bash
    npm run seed:run
    ```

---

## Seed (Mongoose)

### Criando seeds (Mongoose)

1. Crie o arquivo seed com `npm run seed:create:document -- --name Post`. Onde `Post` é o nome da entidade.
1. Vá para `src/database/seeds/document/post/post-seed.service.ts`.
1. No método `run` adicione sua lógica.
1. Execute [npm run seed:run:document](#executar-seed-mongoose)

### Executar seed (Mongoose)

```bash
npm run seed:run:document
```

---

## Otimização de performance (PostgreSQL + TypeORM)

### Índices e Chaves Estrangeiras

Não esqueça de criar `índices` nas colunas de Chave Estrangeira (FK) (se necessário), pois por padrão o PostgreSQL [não adiciona índices automaticamente nas FKs](https://stackoverflow.com/a/970605/18140714).

### Máximo de conexões

Defina o número ideal de [máximo de conexões](https://node-postgres.com/apis/pool) para o banco no seu `/.env`:

```txt
DATABASE_MAX_CONNECTIONS=100
```

Você pode pensar nesse parâmetro como o número de conexões simultâneas que sua aplicação pode lidar.

## Otimização de performance (MongoDB + Mongoose)

### Desenhar schema

Desenhar schema para MongoDB é completamente diferente de bancos relacionais. Para melhor performance, desenhe seu schema de acordo com:

1. [Anti-padrões de schema do MongoDB](https://www.mongodb.com/developer/products/mongodb/schema-design-anti-pattern-massive-arrays)
1. [Boas práticas de schema do MongoDB](https://www.mongodb.com/developer/products/mongodb/mongodb-schema-design-best-practices/)

## Trocar PostgreSQL por MySQL

Se quiser usar `MySQL` ao invés de `PostgreSQL`, siga o guia completo [aqui](installing-and-running.md).

Depois de completar todos os passos, você terá o app rodando.
![image](https://github.com/user-attachments/assets/ec60b61a-65e6-43e2-9bcf-72dad4c8a9fa)

Se chegou até aqui, só faltam algumas mudanças para trocar de `PostgreSQL` para `MySQL`.

**Altere o arquivo `.env` para:**

```env
DATABASE_TYPE=mysql
# use "localhost" se rodar localmente
# use "mysql" se rodar no docker
DATABASE_HOST=localhost
DATABASE_PORT=3306
DATABASE_USERNAME=root
DATABASE_PASSWORD=secret
DATABASE_NAME=app
```

**Altere o `docker-compose.yml` para:**

```yml
services:
  mysql:
    image: mysql:9.2.0
    ports:
      - ${DATABASE_PORT}:3306
    volumes:
      - mysql-boilerplate-db:/var/lib/mysql
    environment:
      MYSQL_USER: ${DATABASE_USERNAME}
      MYSQL_PASSWORD: ${DATABASE_PASSWORD}
      MYSQL_ROOT_PASSWORD: ${DATABASE_PASSWORD}
      MYSQL_DATABASE: ${DATABASE_NAME}

  # outros serviços aqui...

volumes:
  # outros volumes aqui...
  mysql-boilerplate-db:
```

Depois disso, rode o Docker com:

```bash
docker compose up -d mysql adminer maildev
```

Todos os três serviços devem estar rodando como abaixo:

![image](https://github.com/user-attachments/assets/73e10325-66ed-46ca-a0c5-45791ef0750f)

Agora instale o cliente MySQL:

```bash
npm i mysql2 --save
```

**Delete a migração existente e gere uma nova:**

```bash
npm run migration:generate -- src/database/migrations/newMigration --pretty=true
```

Rode as migrações:

```bash
npm run migration:run
```

Rode os seeds:

```bash
npm run seed:run:relational
```

Rode o app em modo dev:

```bash
npm run start:dev
```

Abra <http://localhost:3000>

Para acessar o Adminer:

Abra a porta no navegador.
Abra <http://localhost:8080>

![image](https://github.com/user-attachments/assets/f4b86daa-d93f-4ae9-a9e3-3c29bb3bba9d)

App rodando:
![image](https://github.com/user-attachments/assets/5dc0609d-5f6d-4176-918d-1744906f4f88)
![image](https://github.com/user-attachments/assets/ff2201a6-d834-4c8b-9ab7-b9413a0a95c1)

---

Anterior: [Interface de Linha de Comando](cli.md)

Próximo: [Autenticação](auth.md)
