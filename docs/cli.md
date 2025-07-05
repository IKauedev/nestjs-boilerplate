# Interface de Linha de Comando (CLI)

---

## Índice <!-- omit in toc -->

- [Gerar recurso](#gerar-recurso)
  - [Para banco orientado a documentos (MongoDB + Mongoose)](#para-banco-orientado-a-documentos-mongodb--mongoose)
  - [Para banco relacional (PostgreSQL + TypeORM)](#para-banco-relacional-postgresql--typeorm)
    - [Vídeo tutorial para banco relacional (PostgreSQL + TypeORM)](#video-tutorial-para-banco-relacional-postgresql--typeorm)
  - [Para ambos os bancos](#para-ambos-os-bancos)
- [Adicionar propriedade ao recurso](#adicionar-propriedade-ao-recurso)
  - [Propriedade para banco orientado a documentos (MongoDB + Mongoose)](#propriedade-para-banco-orientado-a-documentos-mongodb--mongoose)
  - [Propriedade para banco relacional (PostgreSQL + TypeORM)](#propriedade-para-banco-relacional-postgresql--typeorm)
    - [Vídeo tutorial de como adicionar propriedade para banco relacional (PostgreSQL + TypeORM)](#video-tutorial-de-como-adicionar-propriedade-para-banco-relacional-postgresql--typeorm)
  - [Propriedade para ambos os bancos](#propriedade-para-ambos-os-bancos)

---

## Gerar recurso

Gere um recurso com os comandos abaixo:

### Para banco orientado a documentos (MongoDB + Mongoose)
  
```bash
npm run generate:resource:document -- --name NomeDoRecurso
```

Exemplo:

```bash
npm run generate:resource:document -- --name Categoria
```

### Para banco relacional (PostgreSQL + TypeORM)

```bash
npm run generate:resource:relational -- --name NomeDoRecurso
```

Exemplo:

```bash
npm run generate:resource:relational -- --name Categoria
```

#### Vídeo tutorial para banco relacional (PostgreSQL + TypeORM)

<https://github.com/user-attachments/assets/f7f91a7d-f9ff-4653-a78a-152ac5e7a95d>

### Para ambos os bancos

```bash
npm run generate:resource:all-db -- --name NomeDoRecurso
```

Exemplo:

```bash
npm run generate:resource:all-db -- --name Categoria
```

## Adicionar propriedade ao recurso

### Propriedade para banco orientado a documentos (MongoDB + Mongoose)

```bash
npm run add:property:to-document
```

### Propriedade para banco relacional (PostgreSQL + TypeORM)

```bash
npm run add:property:to-relational
```

#### Vídeo tutorial de como adicionar propriedade para banco relacional (PostgreSQL + TypeORM)

<https://github.com/user-attachments/assets/95b9d70a-70cf-442a-b8bf-a73d32810e0c>

### Propriedade para ambos os bancos

```bash
npm run add:property:to-all-db
```

---

Anterior: [Arquitetura](architecture.md)

Próximo: [Banco de Dados](database.md)
