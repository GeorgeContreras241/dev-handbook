---
title: "Flexbox vs Grid: cuándo usar cuál"
description: "Mi criterio para elegir entre Flexbox y CSS Grid sin volverme loco."
date: 2026-05-28
tags: ["css", "layout"]
---

La regla mental que uso:

- **Flexbox** → una dimensión (fila o columna).
- **Grid** → dos dimensiones (filas Y columnas a la vez).

## Flexbox: alineación lineal

Perfecto para barras de navegación, listas en fila, centrado.

```css
.toolbar {
  display: flex;
  gap: 0.5rem;
  align-items: center;
  justify-content: space-between;
}
```

<figure class="demo">
  <figcaption>Live: toolbar con flexbox</figcaption>
  <div class="flex items-center justify-between gap-2 p-3 bg-white dark:bg-zinc-950 border border-zinc-200 dark:border-zinc-800 rounded-md">
    <span class="font-semibold text-zinc-900 dark:text-zinc-50 text-sm">App</span>
    <div class="flex gap-2">
      <button type="button" class="px-3 py-1 text-xs border border-zinc-300 dark:border-zinc-700 rounded text-zinc-700 dark:text-zinc-300">Login</button>
      <button type="button" class="px-3 py-1 text-xs bg-emerald-600 text-white rounded">Signup</button>
    </div>
  </div>
</figure>

## Grid: layouts complejos

Cuando necesito definir tanto el flujo horizontal como el vertical.

```css
.page {
  display: grid;
  grid-template-columns: 16rem 1fr;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
}
```

<figure class="demo">
  <figcaption>Live: sidebar + main + footer en grid</figcaption>
  <div class="grid gap-2 text-xs font-mono" style="grid-template-columns: 7rem 1fr; grid-template-rows: 2rem 5rem 2rem;">
    <div class="col-span-2 flex items-center px-3 bg-zinc-200 dark:bg-zinc-800 text-zinc-700 dark:text-zinc-300 rounded">header</div>
    <div class="flex items-center px-3 bg-zinc-100 dark:bg-zinc-900 text-zinc-600 dark:text-zinc-400 rounded">sidebar</div>
    <div class="flex items-center px-3 bg-white dark:bg-zinc-950 border border-zinc-200 dark:border-zinc-800 text-zinc-700 dark:text-zinc-300 rounded">main</div>
    <div class="col-span-2 flex items-center px-3 bg-zinc-200 dark:bg-zinc-800 text-zinc-700 dark:text-zinc-300 rounded">footer</div>
  </div>
</figure>

## Tabla rápida

| Escenario                         | Mejor opción |
| --------------------------------- | ------------ |
| Navbar / toolbar                  | Flexbox      |
| Centrar contenido en pantalla     | Flexbox      |
| Layout sidebar + main + footer    | Grid         |
| Galería tipo "masonry" responsive | Grid         |
| Wrappear botones en línea         | Flexbox      |

> No es excluyente. Lo normal es **Grid en el padre + Flexbox en hijos**.
