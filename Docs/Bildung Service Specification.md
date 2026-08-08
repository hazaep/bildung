# 📘 Bildung Service Specification

### Versión 0.1 — Modular Service Layer

---

# 🧠 1. Propósito

Definir:

* Qué es un servicio dentro de Bildung
* Cómo debe comportarse
* Cómo se integra al sistema
* Cómo responde de forma estandarizada

---

## 🎯 Objetivo

> Permitir que cualquier servicio sea intercambiable, escalable y compatible con todos los clientes Bildung.

---

# 🧩 2. Definición de Servicio

## 🔷 Servicio (Service)

> Componente que ejecuta una acción solicitada por un cliente, y devuelve un resultado normalizado según el estándar Bildung.

---

## 🔥 Regla fundamental

> Un servicio NO define el sistema
> Un servicio IMPLEMENTA una capacidad del sistema

---

# 🧠 3. Tipos de servicios

---

## 🔷 3.1 Service Wrapper

Encapsula APIs externas

Ejemplos:

* Spotify
* OpenAI / DeepSeek
* APIs externas

---

### Función

* traducir request Bildung → API externa
* normalizar respuesta → Bildung

---

---

## 🔷 3.2 Service Nativo

Lógica propia del sistema

Ejemplos:

* domótica
* reglas internas
* automatización

---

---

## 🔷 3.3 Service Inteligente

Servicios con lógica avanzada

Ejemplos:

* agentes IA
* sistemas de decisión
* motores de recomendación

---

---

## 🔷 3.4 Service Agregador

Combina múltiples servicios

Ejemplo:

* IA que usa Spotify + contexto + memoria

---

# 🧠 4. Contrato del servicio

---

## 🔷 Entrada (Request)

El servicio recibe SIEMPRE:

```json
{
  "action": "string",
  "params": {},
  "client": {}
}
```

---

## 🔷 Salida (Response obligatoria)

```json
{
  "success": true,
  "action": "string",
  "state": "string",
  "data": {},
  "error": null
}
```

---

## 🔥 Regla crítica

> TODO servicio debe responder en formato Bildung
> sin excepciones

---

# 🧠 5. Modelo de ejecución

---

## 🔁 Flujo interno

```text
Request → Validación → Ejecución → Normalización → Response
```

---

## 🔷 Etapas

---

### 5.1 Validación

* action válida
* params correctos
* client presente

---

---

### 5.2 Ejecución

* lógica del servicio
* llamadas externas si aplica

---

---

### 5.3 Normalización

* adaptar respuesta al estándar Bildung
* eliminar formato externo

---

---

# 🧠 6. Actions (núcleo del servicio)

---

## 🔥 Regla

> Un servicio se define por las actions que soporta

---

## 🔷 Spotify (Ya implementado)

`https://hooks.bildung-system.info/webhook/{id}/v1/player`

```text
play
pause
next
prev
status
volume
```

---

---

## 🔷 Ejemplo IA 

```text
generate
chat
summarize
execute
```

---

---

## 🔷 Ejemplo domótica

```text
turn_on
turn_off
toggle
status
```

---

# 🧠 7. Estados (`state`)

---

## 🔷 Estados estándar

```text
ok
error
idle
no_content
no_device
```

---

---

## 🔷 Estados extendidos (por servicio)

```text
playing
paused
loading
offline
```

---

---

## 🔥 Regla

> state describe el sistema
> no el HTTP status

---

# 🧠 8. Manejo de errores

---

## 🔥 Regla crítica

> Nunca romper la estructura por error

---

## 🔷 Ejemplo

```json
{
  "success": false,
  "action": "play",
  "state": "error",
  "data": {},
  "error": "no_active_device"
}
```

---

---

## 🔷 Tipos de error (PTE implementacion Actual basico debug)

```text
validation_error
external_api_error
timeout
unknown_error
```

---

---

## 🔥 Recomendación

Siempre incluir:

```json
"error": "machine_readable_code"
```

---

# 🧠 9. Adaptación por cliente

---

## 🔥 Clave del sistema

> El servicio debe adaptarse al cliente

---

## 🔷 Ejemplo

---

### Cliente ESP32

```json
{
  "action": "status",
  "params": {},
  "client": {
    "name": "BILDUNG_HUB",
    "login_id": "ESP32_001",
    "capabilities": {
      "text": true,
      "image": false
    }
  }
}
```

---

### Backend
Loguea API Unica con headers
Compara si la KEY esta asignada al name
El login_id es la de la configuracion de capabilities y permisos del cliente
Verifica que ESP32_001 este asignado
ejemplo client
```json
{
  "client": "BILDUNG_HUB",
  "API_KEY": "string",
  "login_id": "ESP32_001",
  "data": {
    "hardware": {},
    "software": {}
  }
}
```
ejemplo login_id 
```json
{
  "ESP32_001": {
    "capabilities": {
      "type": "infrastructure",
      "input": {},
      "output": { 
        "text": true, 
        "image": true
      }
    },
    "permisos": {
      "level": "client",
      "specific_scopes": ["service_consumer", "read"],
      "spotify": {
        "read_estatus": true
      },
      "ai": {},
      "domotica": {}
    }
  }
}
```
Ejecuta de acuerdo a verificación
Normaliza respuesta

### Respuesta:

```json
{
  "track_name": "...",
  "artist": "..."
}
```

---

---

### Cliente Web

```json
{
  "WEB_001": {
    "capabilities": {
      "output": { 
        "text": true, 
        "image": true
      },
    "permisos": {}
    }
  }
}
```

---

### Respuesta:

```json
{
  "track_name": "...",
  "artist": "...",
  "image": "..."
}
```

---

# 🧠 10. Aislamiento de APIs externas

---

## 🔥 Regla de oro

> El sistema nunca expone directamente APIs externas

---

## 🔷 Ejemplo incorrecto

```json
{
  "spotify_raw": {...}
}
```

---

## 🔷 Correcto
Respuesta solo con la data necesaria

```json
{
  "success": true,
  "action": "status",
  "state": "ok",
  "data": {
    "track_name": "...",
    "artist": "..."
  },
  "error": null
```

---

---

## Beneficio

* cambios externos no rompen clientes
* independencia total

---

# 🧠 11. Idempotencia

---

## 🔷 Definición

Misma acción → mismo resultado esperado

---

## 🔥 Importante para:

```text
play
pause
status
```

---

---

## Ejemplo

* llamar `play` cuando ya está reproduciendo
  → no debe fallar

---

# 🧠 12. Escalabilidad

---

## 🔷 Horizontal

* múltiples instancias del mismo servicio

---

---

## 🔷 Vertical

* optimizar lógica interna

---

---

## 🔷 Distribución

* servicios en distintos nodos

---

---

## 🔥 Regla

> Ningún servicio es obligatorio para el sistema completo

---

# 🧠 13. Registro de servicios (futuro)

---

## 🔷 Posible evolución

```json
{
  "service": "spotify",
  "actions": ["play", "pause", "status"]
}
```

---

---

## Beneficio

* descubrimiento dinámico
* clientes adaptativos

---

# 🧠 14. Ejemplo real

---

## 🔷 Request

```json
{
  "action": "status",
  "params": {},
  "client": {
    "name": "BILDUNG_HUB",
    "login_id": "ESP32_001",
    "capabilities": {
      "text": true,
      "image": true
    }
  }
}
```

---

---

## 🔷 Response

```json
{
  "success": true,
  "action": "status",
  "state": "ok",
  "data": {
    "track_name": "Guy In The Window",
    "artist": "NOMARKMORE",
    "image_link": "www..."
  },
  "error": null
}
```

---

---

## 🔷 Response (web)

```json
{
  "success": true,
  "action": "status",
  "state": "ok",
  "data": {
    "track_name": "Guy In The Window",
    "artist": "NOMARKMORE",
    "image": "https://..."
  },
  "error": null
}
```

---

# 🧠 15. Filosofía del servicio

---

## 🔥 Regla 1

> El servicio ejecuta, no decide el sistema

---

## 🔥 Regla 2

> El servicio traduce, no expone APIs externas

---

## 🔥 Regla 3

> El servicio es reemplazable
