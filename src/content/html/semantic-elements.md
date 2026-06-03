---
title: "Elementos semánticos en HTML"
description: "Por qué importa usar <article>, <section>, <nav> en lugar de divs por todas partes."
date: 2026-05-25
tags: ["html", "semantica", "accesibilidad"]
---

Un `<div>` no significa nada. Un `<article>` sí. La diferencia es **accesibilidad, SEO y mantenibilidad**.

## Los que más uso

| Elemento     | Uso                                              |
| ------------ | ------------------------------------------------ |
| `<header>`   | Cabecera de página o de sección                  |
| `<nav>`      | Bloque de navegación principal                   |
| `<main>`     | Contenido principal, único por página            |
| `<article>`  | Contenido autónomo (post, card, comentario)      |
| `<section>`  | Agrupación temática con encabezado propio        |
| `<aside>`    | Contenido tangencial (sidebar, callouts)         |
| `<footer>`   | Pie de página o de sección                       |
| `<figure>`   | Imagen, diagrama o code block con leyenda        |

## Estructura típica

```html
<body>
  <header>
    <nav><!-- enlaces principales --></nav>
  </header>
  <main>
    <article>
      <h1>Título del apunte</h1>
      <section>
        <h2>Subtema</h2>
        <p>...</p>
      </section>
    </article>
    <aside><!-- TOC, related --></aside>
  </main>
  <footer></footer>
</body>
```

<figure class="demo">
  <figcaption>Visualización de landmarks</figcaption>
  <div class="space-y-1 text-[10px] font-mono">
    <div class="border-2 border-blue-400 dark:border-blue-600 p-2 rounded">
      <span class="text-blue-600 dark:text-blue-400">&lt;header&gt;</span>
      <div class="border-2 border-purple-400 dark:border-purple-600 mt-1 p-1.5 rounded">
        <span class="text-purple-600 dark:text-purple-400">&lt;nav&gt;</span>
      </div>
    </div>
    <div class="border-2 border-emerald-400 dark:border-emerald-600 p-2 rounded">
      <span class="text-emerald-600 dark:text-emerald-400">&lt;main&gt;</span>
      <div class="grid grid-cols-3 gap-1.5 mt-1">
        <div class="col-span-2 border-2 border-orange-400 dark:border-orange-600 p-3 rounded">
          <span class="text-orange-600 dark:text-orange-400">&lt;article&gt;</span>
        </div>
        <div class="border-2 border-pink-400 dark:border-pink-600 p-3 rounded">
          <span class="text-pink-600 dark:text-pink-400">&lt;aside&gt;</span>
        </div>
      </div>
    </div>
    <div class="border-2 border-zinc-400 dark:border-zinc-500 p-2 rounded">
      <span class="text-zinc-600 dark:text-zinc-400">&lt;footer&gt;</span>
    </div>
  </div>
</figure>

## Beneficios reales

- Los **lectores de pantalla** anuncian landmarks (`main`, `nav`, `aside`).
- Algunos navegadores ofrecen **reader mode** basándose en `<article>`.
- Buscadores entienden mejor la jerarquía del documento.
- Tu CSS no se llena de `.container-wrapper-inner`.

> Si dudo, `<section>` casi siempre es válido. `<div>` queda solo para agrupaciones puramente visuales.
