---
title: "Content Collections en Astro"
description: "Cómo tipar y consultar contenido Markdown con el content layer de Astro."
date: 2026-06-02
tags: ["astro", "content"]
---

Las **Content Collections** son la forma idiomática de Astro de gestionar contenido tipado (Markdown, MDX, JSON, YAML…). Te dan validación con Zod, autocompletado y un único punto de consulta.

## Definir el schema

En `src/content.config.ts`:

```ts
import { defineCollection, z } from 'astro:content';
import { glob } from 'astro/loaders';

const notes = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content' }),
  schema: z.object({
    title: z.string(),
    date: z.coerce.date(),
    draft: z.boolean().default(false),
  }),
});

export const collections = { notes };
```

El `glob` loader recorre el directorio y usa la ruta relativa (sin extensión) como `id` de cada entrada. Eso me da slugs gratis del tipo `astro/content-collections`.

## Consultar entradas

```ts
import { getCollection } from 'astro:content';

const notes = await getCollection('notes', ({ data }) => !data.draft);
```

El segundo argumento es un filtro opcional. Lo uso para excluir borradores.

## Renderizar una entrada

```astro
---
import { getEntry, render } from 'astro:content';
const entry = await getEntry('notes', 'astro/content-collections');
const { Content } = await render(entry!);
---

<Content />
```

`Content` es un componente que pinta el Markdown ya procesado (con Shiki para code blocks incluido).

## Por qué me gusta

- **Validación temprana**: si olvido un campo del frontmatter, el build falla.
- **Tipos automáticos**: `entry.data.title` está tipado sin esfuerzo.
- **Una sola fuente**: nada de mezclar `import.meta.glob` con loaders custom.
