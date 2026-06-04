---
title: "useMemo en React"
description: "Memorizar cálculos costosos para que no se recalculen en cada render"
date: 2026-06-04
tags: ["react", "performance", "hooks", "memoization"]
---

**`useMemo`** es un hook de React que memoriza el resultado de una función y solo lo recalcula cuando cambian sus dependencias. Lo uso cuando tengo una operación cara (filtrado, mapeo, ordenación sobre arrays grandes) que se ejecutaría en cada render aunque los datos relevantes no hayan cambiado.

El caso de dolor que lo justifica: un componente tiene dos estados, uno para búsqueda y otro que cambia por otras interacciones (un contador, un toggle). Sin `useMemo`, el filtrado se recalcula aunque solo cambie el contador. Con `useMemo`, solo se ejecuta cuando cambia el término de búsqueda.

## Forma básica

```tsx
import { useMemo } from 'react';

const resultado = useMemo(() => {
  // cálculo costoso
  return datos.filter(item => item.activo);
}, [datos]); // solo recalcula si `datos` cambia
```

El primer argumento es una función que devuelve el valor a memorizar. El segundo es el array de dependencias, igual que en `useEffect`.

## Ejemplo: filtrado con y sin memo

```tsx
const [searchTerm, setSearchTerm] = useState('');
const [count, setCount] = useState(0);

// Sin memo: se ejecuta en cada render, aunque solo cambie `count`
const filteredWithout = usersArray.filter(user =>
  user.name.toLowerCase().includes(searchTerm.toLowerCase())
);

// Con memo: solo se ejecuta cuando cambia `searchTerm`
const filteredWith = useMemo(() => {
  console.log('filtrando...');
  return usersArray.filter(user =>
    user.name.toLowerCase().includes(searchTerm.toLowerCase())
  );
}, [searchTerm]);
```

El `console.log` dentro del memo deja claro cuándo se recalcula de verdad. Lo uso en desarrollo para verificar que las dependencias están bien definidas.

## Qué pasa si no pongo la dependencia correcta

Si omito una dependencia que sí se usa dentro, React no recalculará cuando cambie y el valor quedará desactualizado (stale). Los linters con `eslint-plugin-react-hooks` detectan esto automáticamente con la regla `exhaustive-deps`.

```tsx
// ❌ Mal: searchTerm se usa pero no está en dependencias
const filtered = useMemo(() => {
  return users.filter(u => u.name.includes(searchTerm));
}, []); // stale: nunca se actualiza

// ✅ Bien
const filtered = useMemo(() => {
  return users.filter(u => u.name.includes(searchTerm));
}, [searchTerm]);
```

## Cuándo tiene sentido usarlo

| Situación                                          | ¿Usar useMemo? |
| -------------------------------------------------- | -------------- |
| Filtrar/ordenar arrays de cientos o miles de items | Sí             |
| Cálculo matemático complejo                        | Sí             |
| Mapeo simple sobre array pequeño                   | No             |
| Formatear una fecha o string                       | No             |
| Pasar un objeto como prop estable (para `memo`)    | Sí             |

La regla que aplico: si no noto el lag, no memoizo. `useMemo` tiene su propio coste (comparar dependencias, guardar el valor anterior). Para operaciones baratas, el overhead puede superar el ahorro.

## Diferencia con `useCallback`

`useMemo` memoriza un **valor**. `useCallback` memoriza una **función**. Internamente son casi lo mismo:

```tsx
// Equivalentes
const fn = useCallback(() => doSomething(a, b), [a, b]);
const fn = useMemo(() => () => doSomething(a, b), [a, b]);
```

Uso `useCallback` cuando necesito estabilizar una función para pasarla a un componente hijo envuelto en `React.memo` o como dependencia de otro hook.

> `useMemo` no es optimización prematura si el problema ya existe; sí lo es si lo aplicas por defecto a todo. Primero mide, luego memoiza.
