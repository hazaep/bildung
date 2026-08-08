# 📘 Bildung System Specification (v0.1)
**Enfoque:** Spec-Driven Development | **Arquitectura:** Distribuida, basada en contratos | **Estado:** Living Spec

## 1. Alcance y Principios Fundamentales
| Principio | Regla Inmutable |
|---|---|
| **Desacoplamiento total** | Ningún cliente conoce la implementación interna. Ningún servicio depende de otro directamente. |
| **Comunicación como núcleo** | El sistema se define por mensajes, no por código. `Action = Intención`, no `How`. |
| **Responsabilidad única** | Cada nodo hace una cosa, la hace bien, y es reemplazable. |
| **Cliente declarativo** | El cliente solo declara intención y capacidades. No ejecuta lógica de negocio. |
| **Backend ejecutor** | Interpreta intención, valida permisos, adapta respuesta. |
| **Evolución sin ruptura** | Cambios aditivos permitidos. Modificaciones/eliminaciones requieren nueva versión. |

---

## 2. Contrato de Comunicación (Core Spec)
### 🔹 Request Estándar
```json
{
  "action": "string",
  "params": { "key": "value" },
  "client": {
    "name": "string",
    "login_id": "string",
    "version": "string",
    "capabilities": { "text": true, "image": false, ... },
    "meta": { "location": "home", "network": "wifi" }
  }
}
```
- **Endpoint:** `POST /v1/{module}`
- **Validación obligatoria:** `action` debe existir, `client` debe estar presente, `capabilities` debe ser objeto.

### 🔹 Response Estándar (Obligatoria)
```json
{
  "success": boolean,
  "action": "string",
  "state": "string",
  "data": { "key": "value" },
  "error": null | "machine_readable_code"
}
```
- **Estados base:** `ok`, `error`, `idle`, `no_content`, `no_device` (+ extendidos por servicio).
- **Regla crítica:** `state` describe el estado del sistema, **NO** el HTTP status. Nunca romper la estructura por error.

---

## 3. Modelo de Cliente y Capacidades
### 🔹 Identidad
- `name`: Identificador único del dispositivo/instancia.
- `login_id`: Plantilla de permisos y capacidades autorizadas por el backend.
- **Regla de permisos:** El backend es la fuente de verdad. Un cliente solo puede declarar capacidades previamente autorizadas. `false` por defecto si no hay permiso explícito.

### 🔹 Esquema de Capacidades (`capabilities`)
| Categoría | Ejemplos |
|---|---|
| **Output** | `text`, `image`, `display_animation`, `sound` |
| **Input** | `touch_input`, `physical_buttons`, `mic`, `keyboard` |
| **Hardware** | `low_power`, `battery`, `always_on` |
| **Red** | `wifi`, `bluetooth`, `offline_mode` |
| **Inteligencia** | `nlp`, `decision_making`, `memory` |
| **Procesamiento** | `local_cache`, `json_parsing`, `streaming` |

### 🔹 Tipos de Cliente
`Embedded` (recursos limitados) | `UI` (render complejo) | `IA` (contexto/decisiones) | `Automation` (event-driven)

---

## 4. Modelo de Servicio y Ejecución
### 🔹 Tipos de Servicio
`Wrapper` (APIs externas) | `Nativo` (lógica interna) | `Inteligente` (IA/decisión) | `Agregador` (multi-servicio)

### 🔹 Flujo Interno Obligatorio
`Request → Validación → Ejecución → Normalización → Response`
1. **Validación:** Verifica `action`, `params`, `client` + permisos.
2. **Ejecución:** Lógica propia o llamada a API externa.
3. **Normalización:** Traduce respuesta externa al contrato Bildung. **Nunca expone raw data externa.**
4. **Adaptación:** Recorta/añade campos en `data` según `client.capabilities`.
5. **Respuesta:** Devuelve esquema estándar.

### 🔹 Idempotencia
Acciones como `play`, `pause`, `status` deben ser tolerantes a llamadas duplicadas sin fallar ni cambiar estado inesperadamente.

---

## 5. Flujo de Ejecución End-to-End
```
[Cliente] → Declara identidad + capacidades
    ↓
[Com. Layer] → Enruta a POST /v1/{module}
    ↓
[Backend] → Valida contrato + permisos + capacidades
    ↓
[Servicio] → Ejecuta action → Normaliza → Devuelve data base
    ↓
[Backend] → Adapta data según capabilities → Envía Response estándar
    ↓
[Cliente] → Renderiza según su UI/capacidad → Espera siguiente ciclo
```
**Ciclo de vida cliente:** `Inicialización → Identificación → Request → Render → Espera`

---

## 6. Versionado y Estrategia de Evolución
- **Rutas:** `/v1/`, `/v2/`, etc. Obligatorio desde día 1.
- **Política de cambios:**
  - ✅ Agregar campos/actions: Permitido (compatibilidad hacia atrás).
  - ⚠️ Modificar/eliminar: Requiere nueva versión.
  - 🚫 Romper contrato: Prohibido.
- **Compatibilidad:** El backend debe responder correctamente a clientes antiguos incluso cuando existen capacidades nuevas.

---

## 7. Criterios de Validación y Aceptación (SDD Tests)
| Categoría | Criterio de Aceptación |
|---|---|
| **Contrato** | Todo Request/Response pasa validación JSON Schema estricta. |
| **Estructura** | Response siempre contiene `success`, `action`, `state`, `data`, `error`. |
| **Adaptación** | Si `capabilities.image: false`, `data.image` nunca se envía. |
| **Permisos** | Capacidades no autorizadas son ignoradas o rechazadas con `error: "unauthorized_capability"`. |
| **Aislamiento** | Nunca se retorna `spotify_raw`, `openai_response`, etc. Solo datos normalizados. |
| **Errores** | Fallos nunca rompen JSON. `success: false` + `state: "error"` + código legible. |
| **Idempotencia** | Llamar `play` 2x seguidas devuelve `success: true` + `state: "playing"` sin error. |

---

## 8. Glosario Rápido
- **Action:** Qué se quiere lograr (intención). No describe implementación.
- **State:** Estado actual del sistema/servicio. Independiente de HTTP codes.
- **Capabilities:** Declaración de habilidades del cliente. Motor de adaptación del backend.
- **Login_ID:** Identificador de plantilla de permisos. Vincula cliente a políticas de seguridad.
- **Normalización:** Proceso de traducir cualquier respuesta externa al contrato Bildung.

---

📌 **Nota SDD:** Este documento es la **única fuente de verdad** para desarrollo, QA y documentación. Cualquier implementación debe validarse contra estos contratos antes de merging. Las modificaciones requieren actualización de versión y migración documentada.
