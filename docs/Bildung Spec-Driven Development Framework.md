# 📘 Bildung Spec-Driven Development Framework
**Versión:** 0.1 | **Enfoque:** Spec-First / Contract-Driven / Distributed

## 🔑 Principios SDD aplicados a Bildung
| Principio Bildung | Traducción SDD |
|--------------|----------------|
| `El cliente declara, no decide` | Validación estricta de capacidades declaradas vs permisos asignados |
| `Comunicación como núcleo` | Contrato JSON estandarizado como único punto de validación |
| `Evolución sin ruptura` | Versionado semántico de contratos + pruebas de compatibilidad |
| `Servicio ejecuta, no expone` | Aislamiento de respuestas + validación de esquemas de salida |
| `Reemplazabilidad` | Interfaces definidas por esquemas, no por implementaciones |

---

## 📐 1. Core Contract (Fuente de Verdad)
Todo request/response debe validarse contra este esquema. Es el **contrato inquebrantable** del sistema.

### 🔹 Request Schema (`bildung-request.schema.json`)
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Bildung Request",
  "type": "object",
  "required": ["action", "client"],
  "properties": {
    "action": { "type": "string", "pattern": "^[a-z_]+$" },
    "params": { "type": "object" },
    "client": { "$ref": "#/definitions/client" }
  },
  "definitions": {
    "client": {
      "type": "object",
      "required": ["name", "login_id", "version", "capabilities"],
      "properties": {
        "name": { "type": "string" },
        "login_id": { "type": "string" },
        "version": { "type": "string" },
        "capabilities": {
          "type": "object",
          "properties": {
            "text": { "type": "boolean" },
            "image": { "type": "boolean" },
            "display_animation": { "type": "boolean" },
            "sound": { "type": "boolean" },
            "touch_input": { "type": "boolean" },
            "physical_buttons": { "type": "boolean" },
            "mic": { "type": "boolean" },
            "keyboard": { "type": "boolean" },
            "low_power": { "type": "boolean" },
            "battery": { "type": "boolean" },
            "always_on": { "type": "boolean" },
            "wifi": { "type": "boolean" },
            "bluetooth": { "type": "boolean" },
            "offline_mode": { "type": "boolean" },
            "nlp": { "type": "boolean" },
            "decision_making": { "type": "boolean" },
            "memory": { "type": "boolean" },
            "local_cache": { "type": "boolean" },
            "json_parsing": { "type": "boolean" },
            "streaming": { "type": "boolean" }
          },
          "additionalProperties": false
        },
        "meta": { "type": "object" }
      }
    }
  }
}
```

### 🔹 Response Schema (`bildung-response.schema.json`)
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Bildung Response",
  "type": "object",
  "required": ["success", "action", "state", "data", "error"],
  "properties": {
    "success": { "type": "boolean" },
    "action": { "type": "string" },
    "state": { 
      "type": "string", 
      "enum": ["ok", "error", "idle", "no_content", "no_device", "playing", "paused", "loading", "offline"] 
    },
    "data": { "type": "object" },
    "error": { "type": ["string", "null"] }
  },
  "additionalProperties": false
}
```

> ✅ **Regla SDD:** Toda librería cliente o servicio debe validar contra estos esquemas antes de serializar o deserializar.

---

## 👤 2. Client Spec & Adaptative Routing
El backend usa `client.capabilities` para adaptar la respuesta. Esto se modela como un **motor de decisión basado en specs**.

### 🔹 Tabla de Mapeo de Capacidades → Adaptación
| Capacidad declarada | Comportamiento del Backend |
|---------------------|----------------------------|
| `text: true` | Incluye campos legibles (`track_name`, `artist`, etc.) |
| `image: true` | Añade `image_url` o `thumbnail` si está disponible |
| `display_animation: true` | Devuelve payload de animación/frames |
| `offline_mode: true` | Reduce payload, prioriza `local_cache` o fallbacks |
| `nlp: true` | Permite `params.intent` o respuestas estructuradas para LLMs |

### 🔹 Validación de Permisos vs Capacidades
```text
IF client.capabilities.X == true
AND backend.permissions[login_id].capabilities.X != true
THEN FORBIDDEN → state: "error", error: "capability_not_authorized"
```

---

## ⚙️ 3. Service Spec Template
Cada servicio debe declarar su contrato en un archivo `.bildung-service.json`.

```json
{
  "service": "spotify",
  "version": "1.0.0",
  "actions": {
    "play": {
      "description": "Inicia reproducción",
      "params": { "type": "object" },
      "idempotent": true,
      "response_data_shape": {
        "track_name": "string",
        "artist": "string"
      },
      "possible_states": ["ok", "error", "no_device", "playing"]
    },
    "status": {
      "description": "Estado actual del reproductor",
      "params": { "type": "object" },
      "idempotent": true,
      "response_data_shape": {
        "track_name": "string",
        "artist": "string",
        "progress_ms": "number",
        "is_playing": "boolean"
      },
      "possible_states": ["ok", "error", "idle", "no_device"]
    }
  },
  "errors": {
    "no_active_device": "No hay dispositivo Spotify activo",
    "external_api_error": "Fallo en comunicación con Spotify API",
    "validation_error": "Parámetros inválidos o incompletos"
  }
}
```

> ✅ **Regla SDD:** Un servicio solo puede exponer `data` que coincida con `response_data_shape`. Si una API externa devuelve más campos, el adaptador los filtra.

---

## 🌐 4. Arquitectura & Capas de Validación
```
[ Cliente ] → Validación Request Schema → [ Bildung Comm Layer ] → Routing → [ Servicio/Adaptador ] → Validación Response Schema → [ Cliente ]
```

| Capa | Validación SDD | Herramienta sugerida |
|------|----------------|----------------------|
| Request Entry | `bildung-request.schema.json` + permisos | `ajv` / `spectral` / middleware |
| Routing | Acción registrada en algún servicio | Registry JSON |
| Execution | Lógica idempotente, timeouts, retries | Service spec |
| Response Exit | `bildung-response.schema.json` + capability filter | `ajv` + adaptador de capacidades |

---

## 🧪 5. Estrategia de Testing (Contract Testing)
En SDD, **las pruebas nacen de la spec**, no al revés.

| Tipo de Test | Objetivo | Herramienta |
|--------------|----------|-------------|
| Schema Validation | Request/Response cumplen contrato | `ajv`, `jest-ajv` |
| Contract Testing | Cliente y servicio hablan el mismo idioma | `Pact`, `OpenAPI Mock` |
| Capability Filtering | Backend adapta respuesta según `capabilities` | Tests unitarios con mocks |
| Idempotency | Mismo `action` → mismo `state` esperado | Tests de integración |
| Error Resilience | Respuesta nunca rompe estructura | Fuzzing + schema validation |

### 📦 Ejemplo de test en CI (Pseudocódigo)
```bash
# 1. Validar schema request
ajv validate -s bildung-request.schema.json -d request.json
# 2. Mock service response
mock-server --spec bildung-response.schema.json --port 8080
# 3. Contract test
pact-verifier --provider-base-url http://localhost:8080 --pact-url ./client-service.pact.json
```

---

## 🛠️ 6. Workflow Spec-Driven Development (Paso a paso)
1. **Escribir Spec primero** → Crear/actualizar `*.schema.json`, `*.bildung-service.json`, `capabilities-registry.json`
2. **Generar Código Base** → Usar generadores (`openapi-generator`, `json-schema-to-typescript`)
3. **Implementar Lógica** → Solo lo necesario para cumplir la spec
4. **Validar Automáticamente** → CI/CD corre schema + contract tests
5. **Mock para Clientes** → Servidor mock basado en la spec para desarrollo paralelo
6. **Desplegar & Auditar** → Logs de validación + métricas de `state` y `error`

---

## 📦 7. Tooling Recomendado
| Propósito | Herramienta |
|-----------|-------------|
| Validación JSON Schema | `ajv` (JS), `jsonschema` (Python), `serde_json` (Rust) |
| Contract Testing | `Pact`, `Specmatic`, `Spring Cloud Contract` |
| Mock Servers | `WireMock`, `Mockoon`, `Prism` (OpenAPI) |
| Generación de código | `json-schema-to-typescript`, `quicktype`, `datamodel-code-generator` |
| Linter de Specs | `Spectral` (con reglas custom Bildung) |
| CI/CD | GitHub Actions / GitLab CI con etapas `validate-specs` → `test-contracts` → `deploy` |

---

## 📖 8. Cómo mantener la documentación viva
- 📁 **Repo Structure:**
  ```
  /specs
    /core/          bildung-request.schema.json, bildung-response.schema.json
    /clients/       capabilities-registry.json
    /services/      spotify.bildung-service.json, ai.bildung-service.json
    /rules/         spectral-rules.yaml, versioning-policy.md
  /tests/           contract-tests/, schema-tests/
  /docs/            adr/, sdd-workflow.md, client-examples/
  ```
- 🔒 **Branch Protection:** No merge sin `validate-specs` passing.
- 📈 **Metrics:** `% de requests válidos`, `error_type_distribution`, `capability_usage`
- 🔄 **Review Cycle:** Cada cambio en spec requiere `ADR` (Architectural Decision Record).
