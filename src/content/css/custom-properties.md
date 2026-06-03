---
title: "CSS Custom Properties (variables)"
description: "Variables CSS nativas para theming, sin preprocesadores."
date: 2026-05-29
tags: ["css", "theming"]
---

Las **custom properties** (alias "variables CSS") son nativas, dinámicas y se cascadean. A diferencia de las variables de SASS/LESS, existen en runtime y pueden cambiar.

## Definir

```css
:root {
  --color-bg: #ffffff;
  --color-fg: #18181b;
  --spacing-unit: 0.25rem;
}
```

`:root` equivale a `<html>` pero con mayor especificidad. Lo uso para tokens globales.

## Usar

```css
.card {
  background: var(--color-bg);
  color: var(--color-fg);
  padding: calc(var(--spacing-unit) * 4);
}
```

## Cambiar en runtime

Aquí está su magia: puedes redefinirlas en cualquier scope.

```css
[data-theme="dark"] {
  --color-bg: #09090b;
  --color-fg: #fafafa;
}
```

O desde JS:

```js
document.documentElement.style.setProperty('--color-accent', '#10b981');
```

<figure class="demo">
  <figcaption>Live: pulsa para cambiar el acento</figcaption>
  <div id="cp-card" style="--cp-accent: #10b981; border-color: #10b981;" class="p-4 bg-white dark:bg-zinc-950 border-2 rounded-md">
    <p class="text-sm font-medium m-0" style="color: var(--cp-accent);">
      Este texto y el borde usan <code class="font-mono text-xs px-1 py-0.5 bg-zinc-100 dark:bg-zinc-900 rounded">var(--cp-accent)</code>.
    </p>
    <button type="button" id="cp-btn" class="mt-3 px-3 py-1 text-xs border border-zinc-300 dark:border-zinc-700 rounded text-zinc-700 dark:text-zinc-300 hover:bg-zinc-100 dark:hover:bg-zinc-800">
      Cambiar color
    </button>
  </div>
</figure>

<script>
  (() => {
    const colors = ['#10b981', '#3b82f6', '#f59e0b', '#ef4444', '#8b5cf6', '#ec4899'];
    let i = 0;
    const card = document.getElementById('cp-card');
    const btn = document.getElementById('cp-btn');
    if (!card || !btn) return;
    btn.addEventListener('click', () => {
      i = (i + 1) % colors.length;
      card.style.setProperty('--cp-accent', colors[i]);
      card.style.borderColor = colors[i];
    });
  })();
</script>

## Fallback

```css
.btn {
  background: var(--color-accent, #3b82f6);
}
```

Si `--color-accent` no existe, usa el azul.

> Las uso para theming claro/oscuro, escala de espaciado consistente y para que JS manipule estilos sin tocar clases.
