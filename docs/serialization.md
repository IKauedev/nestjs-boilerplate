# Serialização

Para serialização, utilize [class-transformer](https://www.npmjs.com/package/class-transformer) e o interceptor global `ClassSerializerInterceptor`.

---

## Índice <!-- omit in toc -->

- [Ocultar propriedade privada](#ocultar-propriedade-privada)
- [Exibir propriedade privada para admins](#exibir-propriedade-privada-para-admins)

---

## Ocultar propriedade privada

Se precisar ocultar alguma propriedade na entidade, use `@Exclude({ toPlainOnly: true })` na coluna.

```ts
// /src/users/entities/user.entity.ts

import { Exclude } from 'class-transformer';

@Entity()
export class User extends EntityRelationalHelper {
  // Some code here...

  @Column({ nullable: true })
  @Exclude({ toPlainOnly: true })
  password: string;

  // Some code here...
}
```

## Exibir propriedade privada para admins

1. Crie um controller que retorna dados apenas para admin e adicione `@SerializeOptions({ groups: ['admin'] })` ao método:

   ```ts
   // /src/users/users.controller.ts

   // Some code here...

   @ApiBearerAuth()
   @Roles(RoleEnum.admin)
   @UseGuards(AuthGuard('jwt'), RolesGuard)
   @Controller({
     path: 'users',
     version: '1',
   })
   export class UsersController {
     constructor(private readonly usersService: UsersService) {}

     // Some code here...

     @SerializeOptions({
       groups: ['admin'],
     })
     @Get(':id')
     @HttpCode(HttpStatus.OK)
     findOne(@Param('id') id: string) {
       return this.usersService.findOne({ id: +id });
     }

     // Some code here...
   }
   ```

1. Na entidade, adicione `@Expose({ groups: ['admin'] })` à coluna que deve ser exibida para admin:

   ```ts
   // /src/users/entities/user.entity.ts

   // Some code here...

   import { Expose } from 'class-transformer';

   @Entity()
   export class User extends EntityRelationalHelper {
     // Some code here...

     @Column({ unique: true, nullable: true })
     @Expose({ groups: ['admin'] })
     email: string | null;

     // Some code here...
   }
   ```

---

Anterior: [Autenticação](auth.md)

Próximo: [Upload de Arquivos](file-uploading.md)
