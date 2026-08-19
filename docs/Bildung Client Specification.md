# 📘 Bildung Client Specification

### Versión 0.1 — Adaptive Client Model

---

# 🧠 1. Propósito

Definir:

* Qué es un cliente dentro de Bildung
* Cómo se identifica
* Qué capacidades puede declarar
* Cómo el sistema debe adaptarse a él

---

## 🎯 Objetivo

> Permitir que múltiples tipos de clientes interactúen con el sistema sin romper la lógica ni duplicar endpoints.

---

# 🧩 2. Definición de Cliente

## 🔷 Cliente (Client)

> Entidad que solicita operaciones al sistema Bildung mediante el estándar de comunicación.

---

## 🔥 Regla fundamental

> El cliente **no ejecuta lógica de negocio**
> El cliente **declara intención y capacidades**

---

# 🧠 3. Tipos de cliente

---

## 🔷 3.1 Cliente físico (Embedded)

Ejemplos:

* ESP32
* ESP32-C3
* Raspberry Pi (modo cliente)

---

### Características

* recursos limitados
* UI básica
* bajo consumo
* conectividad variable

---

---

## 🔷 3.2 Cliente UI (Frontend)

Ejemplos:

* Web App
* App móvil
* Dashboard

---

### Características

* UI rica
* mayor capacidad de render
* interacción compleja

---

---

## 🔷 3.3 Cliente IA

Ejemplos:

* Agente conversacional
* LLM wrapper
* automatización inteligente

---

### Características

* puede interpretar lenguaje natural
* puede tomar decisiones
* puede encadenar acciones

---

---

## 🔷 3.4 Cliente sistema (Automation)

Ejemplos:

* n8n workflows
* cron jobs
* triggers externos

---

### Características

* no interactivo
* orientado a eventos
* ejecución automática

---

# 🧠 4. Identidad del cliente

---

## 🔷 Estructura base

```json
{
  "client": {
    "name": "string",
    "login_id": "string",
    "version": "string",
    "capabilities": {},
    "meta": {}
  }
}
```

---

## 🧠 Campos

---

### 🔹 name

Identificador único del cliente

Ejemplo:

```json
"esp32_panel_01"
```

---

### 🔹 login_id

capa de permisos y capacidades del cliente se puede usar como plantilla pero debe estar especificada para el identificador del cliente

```json
"esp32"
"web"
"ia"
"automation"
```

---

---

### 🔹 version

Versión del cliente

```json
"1.0"
```

---

---

### 🔹 capabilities

Capacidades declaradas

---

---

### 🔹 meta (opcional)

Datos adicionales

```json
{
  "location": "home",
  "network": "wifi"
}
```

---

# 🧠 5. Modelo de capacidades

---

## 🔥 Este es el punto clave del sistema

> El backend adapta su comportamiento basado en capacidades

---
test
## 🔷 Ejemplo

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

## 🧠 5.1 Tipos de capacidades

---

### Output

```text
text
image
display_animation
sound
```

---

---

### Input

```text
touch_input
physical_buttons
mic
keyboard
```

---

---

### ⚡ Hardware

```text
low_power
battery
always_on
```

---

---

### 🌐 Red

```text
wifi
bluetooth
offline_mode
```

---

---

### 🧠 Inteligencia

```text
nlp
decision_making
memory
```

---

---

### 📦 Procesamiento

```text
local_cache
json_parsing
streaming
```

---

# 🧠 6. Comportamiento adaptativo

---

## 🔥 Regla principal

> El backend debe adaptar la respuesta al cliente

---

## 🧠 Ejemplo real

---

### Cliente ESP32

```json
"capabilities": {"text": true}
```

---

### Respuesta:

```json
"data": {
  "track_name": "Song",
  "artist": "Artist"
}
```

---

---

### Cliente Web o ESP32 con display

```json
"capabilities": {"image": true, "text": true}
```

---

### Respuesta:

```json
"data": {
  "track_name": "Song",
  "artist": "Artist",
  "image": "url"
}
```

---

# 🧠 7. Niveles de complejidad

---

## 🔷 Nivel 1 — Simple

* cliente básico
* respuesta mínima

---

## 🔷 Nivel 2 — Intermedio

* cliente con UI
* respuesta enriquecida

---

## 🔷 Nivel 3 — Avanzado

* cliente IA
* respuesta contextual
* acciones sugeridas

---

# 🧠 8. Reglas de diseño para clientes

---

## ✔ 8.1 El backend administra los permisos y capacidades

El cliente puede declarar capacidades en function de las que tiene autorizadas en el backend, lo que sirve para anular las capacidades innecesarias "false" si no tiene permisos no puede declarar "true" una capacidad que no esta especificada como "true" en el backend

---

## ✔ 8.2 Mantener simplicidad

Especialmente en embedded

---

## ✔ 8.3 No acoplarse a respuestas específicas

Solo usar:

* success
* state
* data

---

## ✔ 8.4 Manejar estados, no lógica

---

## ✔ 8.5 No puede sobrepasar las capacidades sin la autorización especificada

Aun teniendo permiso asignado para modificarlas, debe modificarlas antes de declararlas

---

## ✔ 8.6 Los permisos los administra el backend

Bildung es un entorno distribuido, pensado en la variedad de dispositivos, por lo que en el futuro los voy a centralizar para administrar dispositivos dentro de la infraestructura

---
# 🧠 9. Ciclo de vida del cliente

---

```text
Inicialización → Identificación → Request → Render → Espera
```

---

## 🔁 Flujo

1. Cliente inicia
2. Declara identidad
3. Envía acción
4. Recibe respuesta
5. Renderiza

---

# 🧠 10. Estrategias de optimización

---

## 🔷 Embedded

* minimizar payload
* evitar imágenes grandes
* usar polling inteligente

---

---

## 🔷 Web

* usar datos enriquecidos
* cache
* render dinámico

---

---

## 🔷 IA

* usar contexto
* mantener historial
* encadenar acciones

---

# 🧠 11. Compatibilidad futura

---

## 🔥 Regla

> Nuevas capacidades no deben romper clientes antiguos

---

## Ejemplo

Cliente viejo:

```json
{"text": true}
```

Cliente nuevo:

```json
{"text": true, "image":true}
```

---

Backend:

* responde acorde a cada uno
* sin romper ninguno

---

# 🧠 12. Seguridad básica

---

## 🔷

* client_id validado
* token por cliente
* rate limit

---

# 🧠 13. Ejemplo completo

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
      "text": true
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
    "track_name": "Song",
    "artist": "Artist"
  },
  "error": null
}
```

---

# 🧠 14. Filosofía del cliente

---

## 🔥 Regla 1

> El cliente no decide, solicita

---

## 🔥 Regla 2

> El cliente no conoce el sistema, solo el contrato

---

## 🔥 Regla 3

> El cliente es reemplazable
