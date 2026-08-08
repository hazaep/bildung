# 📘 Bildung Architecture Blueprint

### Versión 0.1 — Foundation Layer

---

# 🧠 1. Propósito del documento

Este documento define:

* La **arquitectura base de Bildung**
* El **modelo de comunicación entre componentes**
* Las **reglas que garantizan escalabilidad**
* El **lenguaje común del sistema**

---

## 🎯 Objetivo principal

> Definir un sistema distribuido donde múltiples clientes y servicios interactúan mediante un contrato común, desacoplado y evolutivo.

---

# 🌐 2. Definición de Bildung

Bildung es un:

> **Sistema operativo distribuido basado en mensajes, diseñado para coordinar dispositivos, servicios e inteligencia artificial mediante un estándar de comunicación unificado.**

---

## 🧠 Conceptos clave

* No hay un backend único
* No hay lógica central obligatoria
* El sistema vive en la **comunicación**

---

# 🧩 3. Modelo conceptual

```text
Clientes → Comunicación → Servicios
```

Pero realmente es:

```text
[ Clients ]
    ↓
[ Bildung Communication Layer ]
    ↓
[ Distributed Services ]
```

---

## 🔥 Insight estructural

> Bildung no es una aplicación
> Bildung es un **lenguaje operativo entre sistemas**

---

# ⚙️ 4. Principios arquitectónicos

Estos no son sugerencias. Son reglas.

---

## 4.1 Desacoplamiento total

* Ningún cliente conoce implementación interna
* Ningún servicio depende de otro directamente

👉 Comunicación SIEMPRE vía contrato

---

## 4.2 Responsabilidad única por nodo

Cada nodo:

* hace una cosa
* la hace bien
* es reemplazable

Esto sigue el principio de modularidad en sistemas distribuidos

---

## 4.3 Comunicación como núcleo

> El sistema NO está definido por servicios
> Está definido por **mensajes**

---

## 4.4 Evolución sin ruptura

* cambios no deben romper clientes existentes
* versionado obligatorio (`v1`, `v2`, etc)

---

## 4.5 Cliente como entidad declarativa

El cliente:

* no ejecuta lógica compleja
* describe intención
* declara capacidades

---

## 4.6 Backend como ejecutor

El backend:

* interpreta intención
* decide ejecución
* adapta respuesta

---

## 4.7 Distribución por diseño

El sistema:

* está diseñado para múltiples nodos
* cada nodo puede vivir en distinta infraestructura

Esto es base de sistemas distribuidos modernos donde múltiples nodos colaboran para un objetivo común

---

# 🧠 5. Componentes del sistema

---

## 🔷 5.1 Cliente

Ejemplos:

* ESP32
* UI Web
* CLI
* IA Agent

---

### Responsabilidades

* enviar requests
* renderizar respuestas
* manejar interacción

---

### No responsabilidades

* lógica compleja, salvo casos especiales
* decisiones globales
* conocimiento del sistema

---

## 🔷 5.2 Communication Layer

Esto es EL estándar.

---

### Función

* transportar mensajes
* definir formato
* garantizar compatibilidad

---

### No es

* lógica de negocio
* orquestador pesado

---

## 🔷 5.3 Servicios

Ejemplos:

* Spotify Service
* IA Service
* Domótica Service
* Media Service

---

### Responsabilidades

* ejecutar acciones
* interactuar con APIs externas
* devolver resultados normalizados

---

## 🔷 5.4 Adaptadores (Bridge Layer)

Encargados de:

* traducir APIs externas
* normalizar respuestas
* aislar cambios externos

---

# 🧠 6. Modelo de comunicación

---

## 🔷 Forma estándar

```json
{
  "action": "string",
  "params": {},
  "client": {}
}
```

---

## 🔥 Este es el núcleo del sistema

Todo en Bildung gira alrededor de:

> **action-based communication**

---

## 🧠 6.1 Acción = intención

No describe:

❌ cómo hacerlo
✅ qué se quiere lograr

---

## 🧠 6.2 Params = contexto

* datos necesarios
* configuración

---

## 🧠 6.3 Client = perfil

Define:

* capacidades
* identidad

---

# 🧠 7. Modelo de respuesta

---

## 🔷 Respuesta estándar

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

## 🧠 Significado

| Campo   | Función            |
| ------- | ------------------ |
| success | resultado lógico   |
| state   | estado del sistema |
| data    | payload            |
| error   | diagnóstico        |

---

## 🔥 Regla crítica

> El cliente NUNCA interpreta raw data
> solo interpreta este formato

---

# 🧠 8. Flujo de ejecución

---

## 🔁 Flujo general

```text
Cliente → Request → Backend → Servicio → Respuesta → Cliente
```

---

## 🧠 Detalle

1. Cliente envía intención
2. Backend interpreta
3. Servicio ejecuta
4. Respuesta se normaliza
5. Cliente renderiza

---

## 🔥 Importante

> El cliente NO sabe qué backend resolvió la acción

---

# 🧠 9. Tipos de comunicación

---

## 🔷 9.1 Sincrónica (actual)

* HTTP
* respuesta inmediata

---

## 🔷 9.2 Asíncrona (futuro)

Para escalar (reduce acoplamiento)
* eventos
* colas
* pub/sub

---

# 🧠 10. Versionado

---

## 🔷 Regla

Toda API debe tener:

```text
/v1/
/v2/
```

---

## 🔥 Nunca romper contratos

* agregar → OK
* modificar → versión nueva
* eliminar → versión nueva

---

# 🧠 11. Naming conventions

---

## 🔷 Endpoint único por módulo de backend

```http
POST /v1/{module}
```


## 🔷 Actions

```text
play
pause
status
volume
generate
execute
```

---

## 🔷 Estados

```text
ok
error
idle
no_device
no_content
```

---

## 🔷 Reglas

* verbos simples
* consistencia global y legible
* sin ambigüedad

---

# 🧠 12. Filosofía del sistema

---

## 🔥 Regla central

> "El cliente no ejecuta acciones, solicita operaciones."

---

## 🔥 Segunda regla

> "Los servicios no se conocen entre sí, solo hablan el mismo lenguaje."

---

## 🔥 Tercera regla

> "El valor está en el contrato, no en la implementación."

---

# 🧠 13. Propiedades del sistema

---

## ✔ Escalable

* múltiples clientes
* múltiples servicios

---

## ✔ Extensible

* nuevos módulos sin romper existentes

---

## ✔ Reemplazable

* cambiar backend sin tocar cliente

---

## ✔ Interoperable

* cualquier cliente puede hablar con cualquier servicio

---

# 🧠 14. Riesgos conocidos

---

## ⚠️ 1. Ruptura de contrato

Solución:

* disciplina en el estándar

---

## ⚠️ 2. Fragmentación

Solución:

* naming consistente
* documentación viva

---

## ⚠️ 3. complejidad distribuida

Solución:

* boundaries claros
* responsabilidades definidas
