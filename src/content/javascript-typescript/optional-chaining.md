---
title: "Optional chaining (?.) y nullish coalescing (??)"
description: "Acceso seguro a propiedades y defaults sensatos en JavaScript moderno."
date: 2026-05-31
tags: ["javascript", "typescript"]
---

Dos operadores que cambiaron cómo escribo JavaScript moderno. Bien combinados, eliminan la mitad de los `if (x && x.y && x.y.z)`.

## Optional chaining `?.`

Cortocircuita si el valor a la izquierda es `null` o `undefined` y devuelve `undefined`.

```js
const city = user?.address?.city;
// undefined si user o address son null/undefined
```

También funciona con:

```js
arr?.[0]            // acceso por índice
fn?.()              // llamada a función que puede no existir
obj?.method?.()     // método opcional
```

## Nullish coalescing `??`

Devuelve el operando derecho **solo si el izquierdo es `null` o `undefined`**.

```js
const port = process.env.PORT ?? 3000;
```

A diferencia de `||`, no se activa con valores falsy válidos como `0`, `''` o `false`:

<figure class="demo">
  <figcaption>Comparativa: || vs ??</figcaption>
  <div class="grid grid-cols-3 gap-x-4 gap-y-1 text-sm font-mono">
    <div class="text-[0.7rem] uppercase tracking-wider text-zinc-500 not-italic">input</div>
    <div class="text-[0.7rem] uppercase tracking-wider text-zinc-500 not-italic">x || 'def'</div>
    <div class="text-[0.7rem] uppercase tracking-wider text-zinc-500 not-italic">x ?? 'def'</div>

    <div class="text-zinc-700 dark:text-zinc-300">0</div>
    <div class="text-amber-600 dark:text-amber-400">'def'</div>
    <div class="text-emerald-600 dark:text-emerald-400">0</div>

    <div class="text-zinc-700 dark:text-zinc-300">''</div>
    <div class="text-amber-600 dark:text-amber-400">'def'</div>
    <div class="text-emerald-600 dark:text-emerald-400">''</div>

    <div class="text-zinc-700 dark:text-zinc-300">false</div>
    <div class="text-amber-600 dark:text-amber-400">'def'</div>
    <div class="text-emerald-600 dark:text-emerald-400">false</div>

    <div class="text-zinc-700 dark:text-zinc-300">null</div>
    <div class="text-emerald-600 dark:text-emerald-400">'def'</div>
    <div class="text-emerald-600 dark:text-emerald-400">'def'</div>

    <div class="text-zinc-700 dark:text-zinc-300">undefined</div>
    <div class="text-emerald-600 dark:text-emerald-400">'def'</div>
    <div class="text-emerald-600 dark:text-emerald-400">'def'</div>

    <div class="text-zinc-700 dark:text-zinc-300">'hola'</div>
    <div class="text-zinc-700 dark:text-zinc-300">'hola'</div>
    <div class="text-zinc-700 dark:text-zinc-300">'hola'</div>
  </div>
  <p class="text-xs text-zinc-500 dark:text-zinc-400 mt-3 m-0">
    En amarillo: <code class="text-[0.7rem] px-1 bg-zinc-100 dark:bg-zinc-900 rounded">||</code> se activa con falsy y rompe la intención original.
  </p>
</figure>

## Combo típico

```js
const nombre = data?.user?.name ?? 'Anónimo';
```

Lee: "intenta acceder al nombre; si en cualquier paso es null/undefined, usa 'Anónimo'".

## Asignación con `??=`

```js
config.timeout ??= 5000;
// asigna solo si config.timeout es null/undefined
```

Lo uso para defaults en objetos de configuración sin pisar valores explícitos.

> Antes hacía `obj && obj.prop && obj.prop.value`. Ya no.
