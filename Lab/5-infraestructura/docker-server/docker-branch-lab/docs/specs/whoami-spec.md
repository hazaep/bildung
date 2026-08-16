# 📘 whoami — Service Specification v1.0

**Capa:** 🧪 Prueba | **Enfoque:** Spec-First / Infrastructure Contract | **Estado:** Living Spec

---

## 1. Propósito

Servicio de **prueba** que valida el patrón de integración del nodo `branch-lab`: un servicio de `services/` que se conecta a la red externa `edge` y se anuncia con labels `traefik.*`. **No es un servicio de producción.** Su único fin es confirmar que el patrón "label + red externa" funciona antes de desplegar servicios reales (open-webui/computer, etc.).

---

## 2. Definición del Servicio

> `traefik/whoami`: contenedor mínimo que devuelve los headers de la petición y el host que la atiende. Perfecto para verificar que Traefik enruta por `PathPrefix(/whoami)` y que el contenedor responde en el puerto interno `80`.

### 🔑 Principios

| Principio | Regla |
|---|---|
| **Patrón de referencia** | Es el template que copiarán los servicios reales |
| **Efímero** | Sin volúmenes, sin estado, sin variables |
| **Sin puerto publicado** | No expone puerto al host; solo es alcanzable vía Traefik |

---

## 3. Contrato de Infraestructura

### 🔹 Entrada

| Recurso | Especificación | Obligatorio |
|---|---|---|
| **CPU** | x86_64 (amd64) | Sí |
| **RAM** | Mínima (decenas de MB) | Sí |
| **Red** | `edge` (bridge), **externa** — la posee el stack de Traefik | **Sí** — sin ella no hay ruta |

### 🔹 Salida

| Recurso | Valor |
|---|---|
| **Endpoint** | `http://bildung.tail1a3dd6.ts.net/whoami/` |
| **Puerto interno** | `80` (vía `loadbalancer.server.port=80`) |
| **Puerto host** | Ninguno (solo Traefik) |

### 🔹 Dependencias

```text
whoami
  ├── traefik (stack infrastructure/) — posee la red `edge`
  └── red `edge` (external: true)
```

---

## 4. Sin volúmenes persistentes

whoami es completamente efímero. No almacena nada.

---

## 5. Variables de Entorno

**Ninguna.** Toda la configuración está en las labels Docker del compose.

---

## 6. Criterios de Aceptación

| # | Criterio | Verificación |
|---|---|---|
| **CA-1** | Contenedor inicia | `docker compose ps whoami` → Up |
| **CA-2** | Responde vía Traefik | `curl -s http://bildung.tail1a3dd6.ts.net/whoami/` → JSON con headers |
| **CA-3** | Está en la red `edge` | `docker network inspect edge` → contiene `whoami` |
| **CA-4** | No publica puerto al host | `docker compose ps` → sin `ports` mapeados |
| **CA-5** | `down` no afecta al proxy | `docker compose down` en `services/whoami` → Traefik sigue Up |

---

## 7. Límites y Restricciones

| Restricción | Valor |
|---|---|
| **No producción** | No persiste datos; no se le debe asignar carga real |
| **Solo tailnet** | Accesible únicamente por Tailscale, vía Traefik |
| **Sin auth** | No tiene login (es una prueba) |

---

📌 **Nota SDD:** whoami es la prueba de que el contrato funciona. Si un futuro servicio real no responde, la primera comprobación es comparar su compose contra este: red `edge` externa + `traefik.enable=true` + router con `PathPrefix` + `loadbalancer.server.port`.
