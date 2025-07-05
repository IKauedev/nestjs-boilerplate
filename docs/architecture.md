# Arquitetura

---

## Índice <!-- omit in toc -->

- [Arquitetura Hexagonal](#arquitetura-hexagonal)
- [Motivação](#motivacao)
- [Descrição da estrutura dos módulos](#descricao-da-estrutura-dos-modulos)
- [Recomendações](#recomendacoes)
  - [Repositório](#repositorio)
- [FAQ](#faq)
  - [Existe uma forma de gerar um novo recurso (controller, service, DTOs, etc) com Arquitetura Hexagonal?](#existe-uma-forma-de-gerar-um-novo-recurso-controller-service-dtos-etc-com-arquitetura-hexagonal)
- [Links](#links)

---

## Arquitetura Hexagonal

O NestJS Boilerplate é baseado na [Arquitetura Hexagonal](https://en.wikipedia.org/wiki/Hexagonal_architecture_(software)). Essa arquitetura também é conhecida como Ports and Adapters.

![Diagrama da Arquitetura Hexagonal](https://github.com/brocoders/nestjs-boilerplate/assets/6001723/6a6a763e-d1c9-43cc-910a-617cda3a71db)

## Motivação

O principal motivo para usar a Arquitetura Hexagonal é separar a lógica de negócio da infraestrutura. Essa separação permite trocar facilmente o banco de dados, a forma de upload de arquivos ou qualquer outra infraestrutura sem alterar a lógica de negócio.

## Descrição da estrutura dos módulos

```txt
.
├── domain
│   └── [DOMAIN_ENTITY].ts
├── dto
│   ├── create.dto.ts
│   ├── find-all.dto.ts
│   └── update.dto.ts
├── infrastructure
│   └── persistence
│       ├── document
│       │   ├── document-persistence.module.ts
│       │   ├── entities
│       │   │   └── [SCHEMA].ts
│       │   ├── mappers
│       │   │   └── [MAPPER].ts
│       │   └── repositories
│       │       └── [ADAPTER].repository.ts
│       ├── relational
│       │   ├── entities
│       │   │   └── [ENTITY].ts
│       │   ├── mappers
│       │   │   └── [MAPPER].ts
│       │   ├── relational-persistence.module.ts
│       │   └── repositories
│       │       └── [ADAPTER].repository.ts
│       └── [PORT].repository.ts
├── controller.ts
├── module.ts
└── service.ts
```

`[DOMAIN ENTITY].ts` representa uma entidade usada na lógica de negócio. A entidade de domínio não tem dependências de banco de dados ou qualquer outra infraestrutura.

`[SCHEMA].ts` representa a **estrutura do banco de dados**. É usado no banco orientado a documentos (MongoDB).

`[ENTITY].ts` representa a **estrutura do banco de dados**. É usado no banco relacional (PostgreSQL).

`[MAPPER].ts` é um mapper que converte **entidade do banco** para **entidade de domínio** e vice-versa.

`[PORT].repository.ts` é um repositório **port** que define os métodos para interação com o banco.

`[ADAPTER].repository.ts` é um repositório que implementa o `[PORT].repository.ts`. É usado para interagir com o banco.

A pasta `infrastructure` contém todos os componentes relacionados à infraestrutura, como `persistence`, `uploader`, `senders`, etc.

Cada componente tem `port` e `adapters`. `Port` é a interface que define os métodos para interação com a infraestrutura. `Adapters` são as implementações do `port`.

## Recomendações

### Repositório

Não tente criar métodos universais no repositório, pois são difíceis de estender durante a vida do projeto. Em vez disso, crie métodos com responsabilidade única.

```typescript
// ❌
export class UsersRelationalRepository implements UserRepository {
  async find(condition: UniversalConditionInterface): Promise<User> {
    // ...
  }
}

// ✅
export class UsersRelationalRepository implements UserRepository {
  async findByEmail(email: string): Promise<User> {
    // ...
  }
  
  async findByRoles(roles: string[]): Promise<User> {
    // ...
  }
  
  async findByIds(ids: string[]): Promise<User> {
    // ...
  }
}
```

---

## FAQ

### Existe uma forma de gerar um novo recurso (controller, service, DTOs, etc) com Arquitetura Hexagonal?

Sim, você pode usar a [CLI](cli.md) para gerar um novo recurso com Arquitetura Hexagonal.

---

## Links

- [Princípio da Inversão de Dependência](https://trilon.io/blog/dependency-inversion-principle) com NestJS.

---

Anterior: [Instalação e Execução](installing-and-running.md)

Próximo: [Interface de Linha de Comando](cli.md)
