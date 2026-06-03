---
title: "Métodos de array que más uso"
description: "map, filter, reduce, flatMap y compañía. Con casos reales."
date: 2026-05-30
tags: ["javascript", "arrays"]
---

Los métodos funcionales de `Array.prototype` reemplazan el 95% de mis bucles `for`.

<figure class="demo">
  <figcaption>Live: aplica un método sobre [1, 2, 3, 4, 5]</figcaption>
  <div class="space-y-3 text-sm">
    <div class="flex flex-wrap items-center gap-2">
      <code class="font-mono px-2 py-1 bg-white dark:bg-zinc-950 border border-zinc-200 dark:border-zinc-800 rounded">[1, 2, 3, 4, 5]</code>
      <span class="text-zinc-500">.</span>
      <select id="am-method" class="px-2 py-1 bg-white dark:bg-zinc-950 border border-zinc-300 dark:border-zinc-700 rounded font-mono text-xs text-zinc-800 dark:text-zinc-200">
        <option value="map">map(x =&gt; x * 2)</option>
        <option value="filter">filter(x =&gt; x % 2 === 0)</option>
        <option value="reduce">reduce((s, x) =&gt; s + x, 0)</option>
        <option value="find">find(x =&gt; x &gt; 3)</option>
        <option value="some">some(x =&gt; x &gt; 4)</option>
        <option value="flatMap">flatMap(x =&gt; [x, x])</option>
      </select>
    </div>
    <div class="flex items-center gap-2">
      <span class="text-xs uppercase tracking-wider text-zinc-500">resultado</span>
      <code id="am-result" class="font-mono px-2 py-1 bg-white dark:bg-zinc-950 border border-zinc-200 dark:border-zinc-800 rounded text-emerald-600 dark:text-emerald-400"></code>
    </div>
  </div>
</figure>

<script>
  (() => {
    const select = document.getElementById('am-method');
    const result = document.getElementById('am-result');
    if (!select || !result) return;
    const arr = [1, 2, 3, 4, 5];
    const ops = {
      map: () => arr.map(x => x * 2),
      filter: () => arr.filter(x => x % 2 === 0),
      reduce: () => arr.reduce((s, x) => s + x, 0),
      find: () => arr.find(x => x > 3),
      some: () => arr.some(x => x > 4),
      flatMap: () => arr.flatMap(x => [x, x]),
    };
    const run = () => {
      const out = ops[select.value]();
      result.textContent = JSON.stringify(out);
    };
    select.addEventListener('change', run);
    run();
  })();
</script>

## `map`: transformar

```js
const ids = users.map(u => u.id);
```

Uno a uno. La longitud se conserva.

## `filter`: descartar

```js
const activos = users.filter(u => u.active);
```

Devuelve los que cumplen la condición.

## `reduce`: acumular

```js
const total = items.reduce((sum, item) => sum + item.price, 0);
```

El segundo argumento (`0`) es el valor inicial. **Siempre lo pongo**, evita sorpresas con arrays vacíos.

## `flatMap`: mapear + aplanar

```js
const tags = posts.flatMap(p => p.tags);
// equivale a posts.map(...).flat() pero más eficiente
```

Útil cuando cada elemento produce 0..N resultados.

## `find` vs `filter`

```js
const user = users.find(u => u.id === '42');         // primer match o undefined
const admins = users.filter(u => u.role === 'admin'); // todos los matches
```

`find` corta en cuanto encuentra. Si solo necesitas uno, no uses `filter()[0]`.

## `some` y `every`

```js
const hayAdmin     = users.some(u => u.role === 'admin');
const todosActivos = users.every(u => u.active);
```

Cortocircuitan. Más expresivos que un `for` con `break`.

## Tip mental

> Si la operación entra en una expresión, va con método funcional.
> Si necesita ramificaciones complejas, vuelve al `for...of`. No te cases con un estilo.
