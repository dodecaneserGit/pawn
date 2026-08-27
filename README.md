# [ PAWN ]

> Conexión directa. Cifrado extremo a extremo. Sin servidores. Sin logs. Sin rastro.

Chat peer-to-peer encriptado en un único archivo HTML. Comunicación directa entre navegadores mediante WebRTC DataChannel con cifrado de extremo a extremo usando ECDH + AES-256-GCM a través de la Web Crypto API nativa del navegador.

![Pantalla de inicio](img/setup.jpg)

---

## ⚡ Características

- **🔒 Double Ratchet real (Signal Protocol)** — Ratchet asimétrico (ECDH P-256) + Ratchet simétrico (HKDF + HMAC-SHA256) + AES-256-GCM por cada mensaje.
- **🛡️ Forward Secrecy & Break-in Recovery** — Claves efímeras de un solo uso que rotan continuamente; si una clave se compromete, los mensajes pasados y futuros quedan protegidos.
- **🔗 P2P directo** — WebRTC DataChannel, sin servidores de aplicación intermedios.
- **📄 Un solo archivo** — Todo en `p2p-chat.html`, sin dependencias externas ni librerías de terceros.
- **🛡️ Verificación SAS (emojis)** — 7 emojis derivados criptográficamente del secreto compartido para confirmación visual anti-MITM.
- **🔍 Anti-tampering en tiempo real** — Cálculo automático del hash SHA-256 del código HTML al cargarse.
- **💓 Heartbeat Keep-Alive (15s)** — Latido silencioso en segundo plano que mantiene abiertos permanentemente los puertos NAT y el relay TURN sin cortes por inactividad.
- **💀 Mensajes efímeros** — Autodestrucción por defecto (30s) y configurable (10s / 30s / 1min / 5min).
- **✓✓ Estado de entrega** — Confirmación de recepción en tiempo real con acuses de recibo cifrados.
- **🗜️ Tokens comprimidos** — Compresión Deflate nativa para tokens de conexión más cortos.
- **🔄 Reconexión inteligente** — ICE restart automático + reconexión manual como respaldo conservando historial.
- **⚙️ Soporte TURN dedicado** — Superación de NAT simétrico (redes 4G/5G, hotspots y corporativas) con opción de ocultar IP.
- **📋 Telemetría en pantalla** — Consola de diagnóstico `[ 📋 DEBUG LOGS ]` con eventos WebRTC e ICE en tiempo real.
- **📱 Responsive** — Optimizado para móviles y escritorio.
- **🖤 Estética underground** — UI terminal monospace, fondo negro `#0a0a0a`, sin distracciones ni animaciones.

---

## 🖥️ Capturas de Pantalla

![Chat encriptado activo](img/chat.jpg)

---

## 🚀 Cómo Usar

### Requisitos
- Navegador moderno (Chrome, Firefox, Edge, Safari).
- Contexto seguro (`https://`, `localhost`, o `file://` en navegadores compatibles).

### Conexión paso a paso

1. **Peer A** abre `p2p-chat.html` → pulsa `[ CREAR SALA ]`.
2. Espera a que se genere el token → lo copia y envía a **Peer B** por cualquier canal externo seguro.
3. **Peer B** abre `p2p-chat.html` → pulsa `[ UNIRSE ]` → pega el token → pulsa `[ PROCESAR ]`.
4. **Peer B** copia su token de respuesta generado → lo envía de vuelta a **Peer A**.
5. **Peer A** pega la respuesta → pulsa `[ CONECTAR ]`.
6. 🔐 **Chat cifrado activo** — verificad que el fingerprint y los **emojis SAS** coinciden en ambos lados.

### Redes con NAT Simétrico (4G/5G / Hotspot móvil / Redes corporativas)
Si estás en una red celular o compartiendo datos desde el móvil (donde el proveedor bloquea el paso directo P2P):
1. Pulsa `[ ⚙ CONFIG RED ]` en la pantalla de inicio.
2. Introduce la URL de tu servidor TURN y credenciales (ejemplo con coturn o servidor propio):
   ```
   URL:      turn:tu-servidor-turn.com:3478
   Usuario:  tu_usuario
   Password: tu_password
   ```
3. Opcionalmente marca `FORZAR RELAY` si deseas ocultar tu IP real (todo el tráfico viaja cifrado a través del relay).
4. Ambos contactos deben configurar el mismo TURN antes de iniciar el intercambio.

---

## 📖 Manual de Funciones

### Cifrado e Integridad
| Función | Descripción |
|:---|:---|
| **Double Ratchet** | Algoritmo criptográfico de rotación continua de claves (KDF Chain + DH Ratchet). Genera una clave AES única e irrepetible para cada mensaje. |
| **Forward Secrecy** | Destrucción instantánea de cada clave de mensaje tras su uso. Si una clave fuese comprometida, ningún mensaje anterior puede descifrarse. |
| **Break-in Recovery** | Recuperación post-compromiso mediante nuevos pares de claves ECDH generados en cada cambio de turno de conversación. |
| **ECDH P-256** | Genera pares de claves asimétricas efímeras en RAM (Web Crypto API). Las claves privadas nunca viajan por la red. |
| **AES-256-GCM** | Cifra cada mensaje con IV único de 96 bits y autenticación de integridad (AEAD). |
| **HKDF-SHA256** | Expansión y derivación de la cadena raíz y cadenas simétricas de envío y recepción. |
| **Fingerprint** | Hash hexadecimal SHA-256 del secreto compartido inicial (primeros 8 bytes). |
| **Verificación SAS** | Secuencia de 7 emojis visuales derivados de los bytes 8-14 del secreto compartido para validación verbal anti-MITM. |
| **Anti-tampering** | Hash SHA-256 calculado en tiempo real del código HTML de la aplicación para certificar que no ha sido modificado. |

### Mensajes efímeros
- Activado por defecto con temporizador de 30 segundos.
- Selector configurable: 10s / 30s / 1min / 5min o desactivable mediante `[ EFÍMERO: ON/OFF ]`.
- Countdown visible en cada mensaje `[28s]`. Al llegar a 0, el mensaje se destruye en ambos extremos.

### Estado de entrega
| Indicador | Significado |
|:---|:---|
| `✓` | Mensaje cifrado y enviado al canal de datos. |
| `✓✓` (verde) | Confirmación ACK recibida del peer receptor. |

### Heartbeat Keep-Alive
- Envía un paquete microscópico silencioso (`PING`/`PONG`) cada 15 segundos.
- Mantiene vivas las tablas NAT de routers/antenas móviles y el relay TURN, evitando desconexiones tras periodos de inactividad.

### Consola de Diagnóstico (`[ 📋 DEBUG LOGS ]`)
- Muestra en tiempo real la recolección de candidatos ICE (`[HOST]`, `[SRFLX]`, `[RELAY]`).
- Registra cambios de estado de la conexión y errores de autenticación o red.

### Reconexión
- **Nivel 1 — Automática**: Detecta inestabilidades y ejecuta un ICE restart transparente vía DataChannel.
- **Nivel 2 — Manual**: Si el canal se corta totalmente, despliega el panel de emergencia `> CONEXIÓN PERDIDA` para re-establecer el enlace manteniendo el historial de la conversación.

---

## 🔐 Arquitectura de Seguridad

```
Mensaje de texto plano
    ↓
Capa 1: Cifrado aplicación (AES-256-GCM — Web Crypto API)
    ↓
Capa 2: Cifrado transporte (DTLS 1.3 — nativo WebRTC)
    ↓
Transmisión P2P directa (UDP / TURN Relay)
```

### ¿Por qué doble capa?
- **Capa 1** protege contra: servidores TURN comprometidos, señalización interceptada o espionaje en relays.
- **Capa 2** protege contra: interceptación de red local, sniffing de paquetes y análisis de tráfico.

### Filosofía zero-server
- Sin servidor de señalización → sin registro de quién habla con quién.
- Sin servidor de mensajes → sin almacenamiento ni metadatos.
- Sin servidor de identidad → sin cuentas ni números de teléfono.
- Las claves viven solo en la memoria RAM del navegador y se purgan al cerrar la pestaña.

---

## 📋 Historial de Releases

### v0.4 — Double Ratchet (Fase 4 Completa)
- 🔐 **Double Ratchet nativo (Signal Protocol)**: Implementación completa de rotación de claves en Web Crypto API (KDF Chain + DH Ratchet asimétrico).
- 🛡️ **Forward Secrecy**: Clave AES-256-GCM efímera generada y destruida con cada mensaje enviado/recibido.
- 🔄 **Break-in Recovery**: Autocuración post-compromiso mediante nuevos pares de claves ECDH generados dinámicamente en cada turno de conversación.

### v0.35 — Heartbeat Keep-Alive
- 💓 Latido silencioso de 15s (`PING`/`PONG`) para mantener puertos NAT y TURN abiertos indefinidamente.
- Prevención de desconexiones por inactividad en reposo.

### v0.34 — Protección de Handshake Inicial
- Control de estado `sessionActive` para evitar falsas alarmas de reconexión durante el intercambio de tokens.

### v0.33 — Optimización ICE y Relay Inmediato
- Priorización en el milisegundo 0 del servidor TURN propio.
- Detección y empaquetado inteligente del candidato `[RELAY]`.

### v0.32 — Consola de Diagnóstico en Pantalla
- Panel `[ 📋 DEBUG LOGS ]` con telemetría de eventos WebRTC e ICE en vivo.

### v0.31 — Corrección de Binding en Coturn
- Habilitación de STUN/TURN binding en el VPS y soporte multiruta UDP/TCP.

### v0.3 — Seguridad Avanzada (Fase 4 Inicial)
- 🛡️ **Verificación SAS**: 7 emojis criptográficos anti-MITM.
- 🔍 **Anti-tampering**: Cálculo y visualización en tiempo real del hash SHA-256 de integridad.

### v0.24 — Servidor TURN Propio
- Despliegue y configuración de `coturn` en VPS privado con política zero-logs.

### v0.23 — Renombrado Oficial a PAWN
- Rebranding completo de la aplicación a **`[ PAWN ]`**.

### v0.22 — Documentación y Capturas
- Creación de `README.md` exhaustivo con arquitectura, manual y capturas.

### v0.21 — Soporte TURN
- Panel `[ ⚙ CONFIG RED ]` para configurar servidor TURN y modo forzar relay.

### v0.2 — Fase 2 Completa
- 🗜️ Compresión de tokens SDP (Deflate nativo, ~40-60% más cortos).
- ✓✓ Estado de entrega (enviado / recibido).
- 🔄 Reconexión inteligente en 2 niveles (automática + manual).
- 💀 Mensajes efímeros con countdown (30s por defecto).
- ✏️ Indicador de escritura.
- 📌 Notificación en título de pestaña.
- 🖤 UI underground: monospace, tema oscuro, sin animaciones.

### v0.1 — Versión Beta
- Conexión WebRTC P2P con señalización manual (copy-paste).
- Cifrado E2E: ECDH P-256 + AES-256-GCM + HKDF-SHA256.
- Verificación visual por fingerprint.

---

## 🗺️ Estado del Proyecto y Roadmap

### Funcionalidades Implementadas y Validadas:
- [x] **Conexión P2P Serverless**: WebRTC DataChannel por copy-paste de tokens.
- [x] **Cifrado Double Ratchet**: Rotación per-message de claves con Forward Secrecy y Break-in Recovery (Signal Protocol).
- [x] **Cifrado E2E Autenticado**: ECDH P-256 + HKDF-SHA256 + HMAC + AES-256-GCM.
- [x] **Compresión de tokens**: Reducción del tamaño del SDP con Deflate nativo.
- [x] **Mensajes efímeros**: Autodestrucción automática y temporizador configurable.
- [x] **Confirmación de entrega**: Checks `✓` y `✓✓` en tiempo real.
- [x] **Verificación SAS**: Secuencia de 7 emojis anti-MITM derivados del hash de sesión.
- [x] **Anti-tampering**: Hash SHA-256 del código fuente en tiempo real.
- [x] **Relay TURN Propio**: Superación de NAT simétrico (Hotspot/5G/Fibra) en VPS privado.
- [x] **Heartbeat Keep-Alive**: Prevención de cortes de conexión tras periodos de inactividad.
- [x] **Telemetría en vivo**: Consola de logs de diagnóstico en pantalla.
- [x] **Licenciamiento libre**: GNU General Public License v3.0 (GPLv3).

---

## 📜 Licencia

Este proyecto está bajo la licencia **GNU General Public License v3.0 (GPLv3)**. Consulta el archivo [LICENSE](LICENSE) para más detalles.

```
[ PAWN ] - Peer-to-Peer Encrypted Zero-Server Chat
Copyright (C) 2026 David Osuna Calle (dodecaneser)

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.
```
