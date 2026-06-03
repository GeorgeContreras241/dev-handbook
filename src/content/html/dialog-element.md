---
title: "El elemento <dialog> nativo"
description: "Modales accesibles sin librerías, gracias al HTML moderno."
date: 2026-05-26
tags: ["html", "accesibilidad"]
---

`<dialog>` es el elemento HTML estándar para modales. Soporte universal desde 2022. Antes de instalar una librería, considera esto.

## Modal básico

```html
<dialog id="confirm">
  <form method="dialog">
    <h2>¿Eliminar registro?</h2>
    <p>Esta acción no se puede deshacer.</p>
    <button value="cancel">Cancelar</button>
    <button value="ok">Eliminar</button>
  </form>
</dialog>

<button onclick="document.getElementById('confirm').showModal()">
  Eliminar
</button>
```

`method="dialog"` en el `<form>` cierra automáticamente el diálogo al enviar, exponiendo el `value` del botón pulsado.

<figure class="demo">
  <figcaption>Live: diálogo nativo en acción</figcaption>
  <div class="flex items-center gap-3">
    <button type="button" id="demo-dialog-trigger" class="px-3 py-1.5 text-sm bg-emerald-600 hover:bg-emerald-700 text-white rounded">
      Abrir diálogo
    </button>
    <span id="demo-dialog-result" class="text-xs font-mono text-zinc-500 dark:text-zinc-400"></span>
  </div>
  <dialog id="demo-dialog" class="rounded-lg shadow-xl p-6 bg-white dark:bg-zinc-900 text-zinc-900 dark:text-zinc-100 max-w-sm backdrop:bg-black/50 backdrop:backdrop-blur-sm">
    <form method="dialog" class="m-0">
      <h3 class="text-lg font-semibold m-0 mb-2">¿Confirmar acción?</h3>
      <p class="text-sm text-zinc-600 dark:text-zinc-400 m-0 mb-4">
        Diálogo nativo. Cierra con <kbd class="px-1.5 py-0.5 text-xs font-mono bg-zinc-100 dark:bg-zinc-800 rounded border border-zinc-200 dark:border-zinc-700">Esc</kbd> o un botón.
      </p>
      <div class="flex gap-2 justify-end">
        <button type="submit" value="cancel" class="px-3 py-1.5 text-sm border border-zinc-300 dark:border-zinc-700 rounded">Cancelar</button>
        <button type="submit" value="ok" class="px-3 py-1.5 text-sm bg-emerald-600 hover:bg-emerald-700 text-white rounded">Confirmar</button>
      </div>
    </form>
  </dialog>
</figure>

<script>
  (() => {
    const dlg = document.getElementById('demo-dialog');
    const trigger = document.getElementById('demo-dialog-trigger');
    const result = document.getElementById('demo-dialog-result');
    if (!dlg || !trigger || !result) return;
    trigger.addEventListener('click', () => dlg.showModal());
    dlg.addEventListener('close', () => {
      result.textContent = `returnValue: "${dlg.returnValue || ''}"`;
    });
  })();
</script>

## API mínima

```js
const dlg = document.getElementById('confirm');

dlg.showModal();   // abre como modal (bloquea fondo)
dlg.show();        // abre como no-modal (sin backdrop)
dlg.close('ok');   // cierra con valor de retorno

dlg.addEventListener('close', () => {
  console.log(dlg.returnValue); // 'ok' | 'cancel'
});
```

## Lo que me da gratis

- **Focus trap** automático.
- **Cierre con `Esc`**.
- Pseudo-elemento **`::backdrop`** para estilar el fondo.
- Rol ARIA correcto sin esfuerzo.
- `inert` automático en el resto del documento.

## Estilando el backdrop

```css
dialog::backdrop {
  background: rgb(0 0 0 / 0.5);
  backdrop-filter: blur(4px);
}
```

> Para el 90% de los casos `<dialog>` reemplaza cualquier librería de modales. Solo necesitas más si vas a hacer animaciones de entrada/salida complejas o stacking de varios modales.
