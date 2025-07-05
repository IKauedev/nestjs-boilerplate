# Traduções

## Índice <!-- omit in toc -->

- [Como adicionar uma nova tradução](#como-adicionar-uma-nova-traducao)
- [Como usar traduções no frontend](#como-usar-traducoes-no-frontend)
- [Como usar traduções no código](#como-usar-traducoes-no-codigo)

## Como adicionar uma nova tradução

1. Copie a pasta `en` e renomeie para o idioma que deseja adicionar.
2. Traduza os arquivos na nova pasta.

## Como usar traduções no frontend

1. Adicione o header `x-custom-lang` na requisição com o idioma que deseja usar.

## Como usar traduções no código

```typescript
import { I18nContext } from 'nestjs-i18n';

// ...código...

@Injectable()
export class SomeService {
  // ...código...

  async someMethod(): Promise<void> {
    const i18n = I18nContext.current();

    if (!i18n) {
      throw new Error('I18nContext não está disponível');
    }

    const emailConfirmTitle = await i18n.t('common.confirmEmail');

    // ...código...
  }
}
```

---

Anterior: [Atualização automática de dependências](automatic-update-dependencies.md)
