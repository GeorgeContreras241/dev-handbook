---
title: "Debounce en JavaScript"
description: "Retrasar la ejecución de una función hasta que el usuario deje de disparar eventos"
date: 2026-06-04
tags: ["javascript", "performance", "eventos", "react"]
---

El **debounce** es una técnica para evitar que una función se ejecute en cada disparo de un evento rápido (como `input`, `resize` o `keydown`). En lugar de ejecutar en cada evento, espera a que pasen X milisegundos sin nuevos disparos y solo entonces ejecuta. Lo uso principalmente para no saturar una API con una petición por cada tecla.

Una aclaración importante: en el `submit` de un formulario no tiene sentido aplicarlo. El submit lo activa el usuario conscientemente con un botón, no es un evento continuo.

## Implementación vanilla reutilizable

La forma más limpia es una función de orden superior que devuelve la versión "debounciada":

```js
function debounce(fn, ms = 300) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), ms);
  };
}
```

El truco está en que `timer` vive en el closure compartido entre todas las llamadas. Cada nueva llamada cancela el timeout anterior y crea uno nuevo.

<figure class="demo">
  <figcaption>Live: escribe rápido y compara los contadores</figcaption>
  <div class="space-y-3">
    <input
      id="db-input"
      type="text"
      placeholder="Escribe aquí..."
      class="w-full px-3 py-1.5 text-sm bg-white dark:bg-zinc-950 border border-zinc-300 dark:border-zinc-700 rounded"
    />
    <div class="text-xs font-mono text-zinc-500 dark:text-zinc-400 flex flex-wrap gap-6">
      <span>disparos totales: <span id="db-total" class="text-zinc-700 dark:text-zinc-300">0</span></span>
      <span>ejecuciones debounced (500 ms): <span id="db-debounced" class="text-emerald-600 dark:text-emerald-400">0</span></span>
    </div>
  </div>
</figure>

<script>
  (() => {
    const input = document.getElementById('db-input');
    const total = document.getElementById('db-total');
    const debounced = document.getElementById('db-debounced');
    if (!input || !total || !debounced) return;

    const debounce = (fn, ms) => {
      let t;
      return (...args) => { clearTimeout(t); t = setTimeout(() => fn(...args), ms); };
    };

    let totalCount = 0;
    let debouncedCount = 0;

    const onSettle = debounce(() => {
      debouncedCount++;
      debounced.textContent = debouncedCount;
    }, 500);

    input.addEventListener('input', () => {
      totalCount++;
      total.textContent = totalCount;
      onSettle();
    });
  })();
</script>

## Uso en JS vanilla sin función reutilizable

Cuando el caso es puntual y no vale la pena abstraer, declaro el timer fuera del handler:

```js
let debounceTimer;

document.getElementById('search').addEventListener('input', (e) => {
  const value = e.target.value;

  if (debounceTimer) clearTimeout(debounceTimer);

  debounceTimer = setTimeout(() => {
    document.getElementById('output').textContent = value;
  }, 500);
});
```

La variable `debounceTimer` en scope externo cumple el mismo papel que el closure de la versión reutilizable.

## Uso en React con `useRef`

En React no puedo usar una variable suelta fuera del componente porque se reiniciaría en cada render. Uso `useRef` para persistir el timer sin causar re-renders:

```tsx
import { useRef } from 'react';

function SearchInput() {
  const debounceTimer = useRef<ReturnType<typeof setTimeout> | null>(null);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value;

    if (debounceTimer.current) {
      clearTimeout(debounceTimer.current);
    }

    debounceTimer.current = setTimeout(() => {
      console.log('valor estabilizado:', value);
      // aquí va la llamada a la API
    }, 500);
  };

  return <input onChange={handleChange} placeholder="Buscar..." />;
}
```

`useRef` devuelve un objeto `{ current: ... }` que React no observa para re-renders. Es el lugar correcto para guardar referencias mutables que no afectan la UI directamente.

## Debounce vs throttle

| Patrón   | Comportamiento                              | Caso típico                    |
| -------- | ------------------------------------------- | ------------------------------ |
| Debounce | Ejecuta cuando el usuario deja de disparar  | Búsqueda en vivo, autosave     |
| Throttle | Ejecuta como máximo 1 vez cada X ms         | Scroll, mousemove, resize      |

La diferencia clave: debounce espera silencio, throttle garantiza cadencia.

## Cuándo lo aplico

- Input de búsqueda que llama a una API (evito una petición por tecla).
- Autosave en formularios de edición.
- Validación asíncrona de campos (comprobar si un username ya existe).

> Si la operación es cara y tiene sentido esperar a que el usuario "termine", debounce. Si necesitas feedback continuo pero capado, throttle.
