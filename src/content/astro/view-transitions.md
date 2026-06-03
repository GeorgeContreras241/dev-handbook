---
title: "View Transitions en Astro"
description: "Transiciones suaves entre páginas con la API nativa del navegador."
date: 2026-06-03
tags: ["astro", "animaciones"]
---

Astro tiene soporte de primera clase para la **View Transitions API** del navegador. Activarlo es una línea.

## Activar el router transicional

En el `<head>` del layout:

```astro
---
import { ClientRouter } from 'astro:transitions';
---
<html>
  <head>
    <ClientRouter />
  </head>
  ...
</html>
```

Eso convierte la navegación en SPA-like, con cross-fade por defecto y sin necesidad de framework.

## La API nativa: `document.startViewTransition`

Por debajo, Astro usa la API estándar del navegador. Puedes usarla directamente para animar cualquier cambio de DOM, no solo navegaciones.

```js
document.startViewTransition(() => {
  // mutación del DOM aquí
});
```

El navegador hace snapshot del estado actual, ejecuta la mutación, y anima la diferencia.

<figure class="demo">
  <figcaption>Live: morph entre dos estados (requiere Chrome/Edge moderno)</figcaption>
  <div class="space-y-3">
    <div id="vt-stage" class="flex items-center justify-start min-h-[5rem]">
      <div id="vt-box" class="rounded text-white text-xs font-mono flex items-center justify-center" style="view-transition-name: vt-box; width: 4rem; height: 4rem; background: #10b981;">
        caja
      </div>
    </div>
    <button type="button" id="vt-btn" class="px-3 py-1.5 text-sm border border-zinc-300 dark:border-zinc-700 rounded text-zinc-700 dark:text-zinc-300 hover:bg-zinc-100 dark:hover:bg-zinc-800">
      Toggle estado
    </button>
  </div>
</figure>

<script>
  (() => {
    const box = document.getElementById('vt-box');
    const btn = document.getElementById('vt-btn');
    if (!box || !btn) return;
    let big = false;
    const update = () => {
      big = !big;
      box.style.width = big ? '100%' : '4rem';
      box.style.height = big ? '6rem' : '4rem';
      box.style.background = big ? '#8b5cf6' : '#10b981';
      box.textContent = big ? 'expandida' : 'caja';
    };
    btn.addEventListener('click', () => {
      if (document.startViewTransition) {
        document.startViewTransition(update);
      } else {
        update();
      }
    });
  })();
</script>

## Nombrar elementos para morphing

Si un elemento existe en ambas páginas (logo, título), puedes hacer que **morphee** de una a otra:

```astro
<h1 transition:name="page-title">{title}</h1>
```

El navegador interpola posición y tamaño entre páginas.

## Personalizar la animación

```astro
<main transition:animate="slide">
  <slot />
</main>
```

Valores nativos: `fade` (default), `slide`, `none`. También puedes definir animaciones custom con CSS y las pseudo `::view-transition-old/new`.

## Persistir estado

```astro
<audio transition:persist controls src="..." />
```

`transition:persist` mantiene el nodo entre navegaciones — útil para players, widgets de chat o estado de formularios.

## Fallback

En navegadores sin soporte, Astro hace una navegación normal del documento. **Mejora progresiva**, sin penalización.

> Para una doc personal queda fino. Para apps grandes evalúa si la complejidad de coordinación de transiciones compensa.
