---
title: "Type vs Interface en TypeScript"
description: "Cuándo usar type alias y cuándo interface. Diferencias prácticas que aplico en mi día a día."
date: 2026-06-01
tags: ["typescript", "tipos"]
---

En TypeScript existen dos formas principales de describir la forma de un objeto: `type` e `interface`. El 90% del tiempo son intercambiables, pero hay matices que importan cuando un proyecto crece.

## Diferencias clave

| Característica            | `type` | `interface` |
| ------------------------- | ------ | ----------- |
| Composición               | `&`    | `extends`   |
| Re-declaración (merging)  | No     | Sí          |
| Uniones / primitivos      | Sí     | No          |
| Tuplas, mapped, condic.   | Sí     | No          |

## Cuándo uso `interface`

Cuando modelo un **contrato público** que espero que alguien (incluido yo en el futuro) pueda extender.

```ts
interface User {
  id: string;
  email: string;
}

interface AdminUser extends User {
  permissions: string[];
}
```

## Cuándo uso `type`

Cuando necesito uniones, tuplas, mapped types o cualquier transformación que `interface` no soporta.

```ts
type Status = 'idle' | 'loading' | 'done';
type Pair<T> = [T, T];
type Nullable<T> = T | null;
```

## Regla práctica

> `interface` para objetos que forman parte de un API y pueden crecer.
> `type` para todo lo demás (uniones, helpers, alias).

No es dogma. Si el equipo ya tiene una convención, sígola.
