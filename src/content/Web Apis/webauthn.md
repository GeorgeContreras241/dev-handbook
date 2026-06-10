---
title: "WebAuthn: Autenticación con Criptografía Asimétrica"
description: "API del navegador para autenticar usuarios sin contraseñas usando claves criptográficas"
date: 2026-06-09
tags: ["web-apis", "seguridad", "autenticacion", "criptografia"]
---

**WebAuthn** (Web Authentication API) es un estándar del W3C que permite autenticar usuarios mediante **criptografía asimétrica** en lugar de contraseñas. La idea central: el servidor nunca almacena un secreto compartido. En su lugar, el dispositivo del usuario guarda una **clave privada** y el servidor solo conoce la **clave pública** correspondiente.

> **Nota**: Esta tecnología evoluciona rápido. Las implementaciones de librerías y los detalles del protocolo pueden cambiar. Revisar siempre la documentación oficial antes de llevar a producción.

## Cómo funciona el protocolo

El flujo se basa en un **challenge**: el servidor genera un valor aleatorio único por sesión, el dispositivo lo firma con la clave privada, y el servidor verifica la firma con la clave pública que guardó en el registro. Si la firma es válida, la identidad queda probada.

```
Registro
  Cliente  →  "quiero registrarme"          →  Servidor
  Cliente  ←  challenge + opciones           ←  Servidor
  Cliente     genera par de claves (dispositivo)
  Cliente  →  clave pública + firma           →  Servidor
  Servidor    guarda clave pública

Autenticación
  Cliente  →  "quiero entrar"               →  Servidor
  Cliente  ←  nuevo challenge               ←  Servidor
  Cliente     firma el challenge con clave privada
  Cliente  →  firma                         →  Servidor
  Servidor    verifica con clave pública guardada ✓
```

La clave privada **nunca sale del dispositivo**. El servidor no puede reconstruirla aunque le roben la base de datos.

## Opciones de autenticador

Cuando se llama a `navigator.credentials.create`, el navegador presenta al usuario tres tipos de autenticador:

- **Clave de hardware** (USB, NFC): YubiKey y similares.
- **Código QR**: escanear con otro dispositivo que sí tiene biométrica.
- **Autenticador de plataforma**: huella dactilar, Face ID, Windows Hello — integrado en el propio dispositivo.

## Flujo de registro con @simplewebauthn

La librería `@simplewebauthn/browser` abstrae la llamada a `navigator.credentials` y el formateo de los datos. El flujo tiene dos roundtrips al servidor.

### 1. Obtener opciones del servidor

```js
import { startRegistration } from '@simplewebauthn/browser';

const optsRes = await fetch(`${API}/generate-registration-options`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ username }),
});

const opts = await optsRes.json();
```

El servidor devuelve las opciones configuradas: algoritmos permitidos, timeout, datos del usuario, y el `challenge` generado para esta sesión.

### 2. Invocar el autenticador

```js
let credential;
try {
  credential = await startRegistration(opts);
} catch (err) {
  // El usuario canceló, el autenticador no está disponible, etc.
  console.error(err);
  return;
}
```

`startRegistration` llama internamente a `navigator.credentials.create({ publicKey: ... })` ya formateado. El navegador abre el diálogo para elegir autenticador.

### 3. Enviar la credencial al servidor para verificar

```js
const verRes = await fetch(`${API}/verify-registration`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ username, credential }),
});

const verification = await verRes.json();
// { verified: true } si todo fue correcto
```

El servidor valida la firma del challenge, comprueba el origen (`rpID`), y si pasa guarda la clave pública asociada al usuario.

## Lo que el servidor necesita gestionar

El backend tiene dos responsabilidades clave que la librería del navegador no cubre:

```
1. Generar y guardar el challenge por sesión (expira en segundos)
2. Guardar la clave pública + credentialID por usuario tras el registro
```

En el flujo de autenticación (login), el proceso es análogo: el servidor genera un nuevo challenge, el cliente lo firma con `startAuthentication`, y el servidor verifica la firma contra la clave pública almacenada.

## Cuándo tiene sentido usarlo

- Aplicaciones donde la seguridad de la cuenta es crítica y quieres eliminar contraseñas.
- Como segundo factor (2FA) además de contraseña.
- Cuando el público objetivo usa dispositivos modernos con biométrica integrada.

No tiene sentido para prototipos rápidos o cuando el soporte de navegadores antiguos es un requisito duro, aunque el soporte actual es amplio (Chrome, Firefox, Safari, Edge).

> WebAuthn desplaza el problema de "proteger una contraseña" a "proteger el dispositivo". Es un modelo más robusto para la mayoría de ataques remotos, pero no elimina el riesgo físico.
