# 📘 Traefik — Service Specification v1.0

**Capa:** 🔴 Fundación | **Enfoque:** Spec-First / Infrastructure Contract | **Estado:** Living Spec

---

## 1. Propósito

Reverse proxy y punto único de entrada HTTP del nodo `branch-lab`. **Todo el tráfico web del nodo pasa por Traefik.** Sin él, ningún servicio es accesible desde el navegador vía tailnet.

A diferencia de `docker-rpi4b`, aquí Traefik vive en un **stack independiente** (`infrastructure/traefik/`) que posee la red `edge`. Los servicios la consumen como red externa: `docker compose down` en un servicio jamás tumba el proxy ni a sí mismo.

---

## 2. Definición del Servicio

> Traefik v3 (major pineado, no `latest`) con descubrimiento dinámico vía Docker labels y middlewares reutilizables en archivos YAML. Solo expone HTTP (puerto 80). El cifrado lo aporta Tailscale (WireGuard); **no hay TLS en Traefik** y **no se expone a internet**.

### 🔑 Principios

| Principio | Regla |
|---|---|
| **Proxy intocable** | Solo gobernanza toca este stack. Los agentes nunca lo modifican |
| **Ruteo por path** | `PathPrefix(/servicio)` — Magic DNS da un solo hostname |
| **Descubrimiento explícito** | `exposedbydefault=false`: solo servicios con `traefik.enable=true` se descubren |
| **Red externa `edge`** | Traefik posee la red; los servicios la referencian como `external: true` |
| **HTTP plano interno** | Tailscale (WireGuard) cifra en tránsito. No hay TLS local |

---

## 3. Contrato de Infraestructura

### 🔹 Entrada

| Recurso | Especificación | Obligatorio |
|---|---|---|
| **CPU** | x86_64 (amd64) | Sí |
| **RAM** | Mínimo 128 MB | Sí |
| **Docker socket** | `/var/run/docker.sock` (read-only) | **Sí** — sin esto no descubre servicios |
| **Directorio dynamic/** | `./dynamic/` con archivos YAML | **Sí** — middlewares file-based |
| **Red** | Crea y posee la red `edge` (bridge) | Sí |

### 🔹 Salida

| Recurso | Valor |
|---|---|
| **Puerto host** | `80` (HTTP) |
| **Endpoint dashboard** | `http://bildung.tail1a3dd6.ts.net/dashboard/` |
| **API interna** | `:8080` (no publicado; accesible vía router `PathPrefix(/api)`) |
| **Red `edge`** | `bridge`, consumida por los servicios como externa |

### 🔹 Dependencias

```text
traefik
  ├── docker.sock (descubre servicios vía labels)
  └── red edge (que él mismo crea y posee)
```

Sin dependencia de `cloudflared` (no existe en branch-lab).

---

## 4. Archivos de Configuración

| Path | Propósito |
|---|---|
| `./dynamic/security-headers.yml` | Middleware reutilizable `security-headers` (frameDeny, XSS filter, nosniff, no-referrer, SAMEORIGIN) |

Los middlewares se referencian desde labels como `security-headers@file`.

---

## 5. Variables de Entorno

**Ninguna.** A diferencia de rpi4b (que usaba `DOMINIO`, `TZ`, `TRAEFIK_AUTH`), la configuración de branch-lab vive íntegramente en:

| Fuente | Ejemplo |
|---|---|
| `command` (flags CLI) | `--providers.docker.exposedbydefault=false`, `--entrypoints.web.address=:80` |
| Labels Docker | `traefik.http.routers.dashboard.rule=PathPrefix(...)` |
| File provider | `./dynamic/security-headers.yml` |

No hay variable `DOMINIO` porque el ruteo es por `PathPrefix`, no por `Host()`.

---

## 6. Criterios de Aceptación

| # | Criterio | Verificación |
|---|---|---|
| **CA-1** | Contenedor inicia | `docker compose ps traefik` → Up |
| **CA-2** | Dashboard accesible | `curl -s http://bildung.tail1a3dd6.ts.net/dashboard/` → HTML |
| **CA-3** | Descubre servicios etiquetados | `curl -s http://bildung.tail1a3dd6.ts.net/api/http/routers` → aparece `whoami@docker` |
| **CA-4** | Solo se descubren servicios explícitos | Servicios sin `traefik.enable=true` no aparecen en el dashboard |
| **CA-5** | Reinicio no rompe ruteo | `restart` → los servicios siguen accesibles |
| **CA-6** | La red `edge` existe | `docker network ls` → `edge` |

---

## 7. Límites y Restricciones

| Restricción | Valor |
|---|---|
| **Arquitectura** | x86_64 (amd64) |
| **Solo HTTP** | Sin TLS. Tailscale (WireGuard) cifra el tránsito |
| **Sin alta disponibilidad** | Una instancia. Si cae, todo el nodo es inaccesible |
| **Docker socket** | Acceso read-only. No puede crear/eliminar contenedores |
| **Solo tailnet** | Sin exposición a internet. Sin `cloudflared` |
| **Sin `Host()`** | Un solo hostname vía Magic DNS; el ruteo es por path |

---

## 8. Por qué HTTP y no HTTPS (en el nodo)

El cifrado lo aporta Tailscale en el borde de la red:

```text
[Navegador] ── tailnet (WireGuard) ──→ [Tailscale] ── HTTP ──→ [Traefik :80]
```

Ventajas:
- Traefik no gestiona certificados (simplicidad)
- Magic DNS ya resuelve `bildung.tail1a3dd6.ts.net` dentro del tailnet
- No hay exposición a internet que justifique TLS local ni `cloudflared`

---

📌 **Nota SDD:** Traefik es el servicio más difícil de reemplazar del nodo. Cada label `traefik.*` en los servicios depende de su contrato (ruteo por `PathPrefix`, red externa `edge`, `security-headers@file`). Cualquier cambio en la API de configuración de Traefik debe probarse contra todos los servicios antes de aplicar.
