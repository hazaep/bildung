# 🏗️ Docker Branch Lab — Arquitectura v1.0

> Documento de decisiones para el stack de contenedores del nodo `branch-lab`.
> Derivado de `docker-rpi4b/docs/architecture.md`, adaptado a un nodo delegado.

---

## 1. La decisión fundacional: proxy aislado

En el RPi, Traefik comparte el mismo ecosistema de composes con los servicios
(una sola red `services`, un compose principal que extiende capas). Eso es
correcto para un nodo **operado por humano**.

`branch-lab` es un nodo **delegado a agentes**. Un agente que hace
`docker compose down` para revisar un servicio no debe tumbarse a sí mismo ni
al proxy que lo enruta. Por eso aquí la arquitectura se invierte:

```
┌─────────────────────────────────────────────┐
│ infrastructure/traefik/  (RED `edge` — posee) │  ← intocable
│   traefik                                    │
└──────────────────┬──────────────────────────┘
                   │ red externa `edge`
   ┌───────────────┼───────────────┐
   ▼               ▼               ▼
 services/     services/      services/
 whoami        open-webui     ...
 (edge:        (edge:         (edge:
  external)     external)      external)
```

**Regla:** los stacks de `services/` declaran la red como `external: true`.
No la crean, no la poseen. `docker compose down` en un servicio solo elimina
su contenedor, no la red ni a Traefik.

---

## 2. Diferencias con docker-rpi4b

| Aspecto | rpi4b | branch-lab |
|---|---|---|
| **Terminación TLS** | Cloudflare (edge) | Tailscale (WireGuard) |
| **Acceso** | Internet vía túnel cloudflared | Solo tailnet (Magic DNS) |
| **Ruteo** | `Host(subdominio.dominio)` | `PathPrefix(/servicio)` |
| **Hostname** | Múltiples subdominios | Uno solo: `bildung.tail1a3dd6.ts.net` |
| **Estructura** | Compose principal + capas `extends` | Stacks independientes + red externa |
| **Cloudflared** | Sí | No (no se expone a internet) |
| **Quién toca el proxy** | Humano | Nadie (solo gobernanza) |

---

## 3. Ruteo por PathPrefix (consecuencia de Magic DNS)

Magic DNS asigna **un solo hostname** al nodo (`bildung.tail1a3dd6.ts.net`).
No hay subdominios arbitrarios. Por eso los servicios se distinguen por **path**:

```
http://bildung.tail1a3dd6.ts.net/whoami/   → contenedor whoami
http://bildung.tail1a3dd6.ts.net/dashboard/ → dashboard de Traefik
http://bildung.tail1a3dd6.ts.net/open-webui/ → (futuro) open-webui/computer
```

Si en el futuro se necesita `Host()` (subdominios), requeriría un dominio
propio + DNS — decisión pendiente de gobernanza, no bloqueante.

---

## 4. Contrato de Traefik

| Campo | Valor |
|---|---|
| Imagen | `traefik:v3` (major pineado) |
| Entrypoint | `web` → `:80` (HTTP) |
| Red | `edge` (bridge, externa para servicios) |
| Descubrimiento | Docker provider, `exposedbydefault=false` |
| File provider | `./dynamic/` (middlewares reutilizables) |
| Dashboard | vía router `PathPrefix(/dashboard)` → `api@internal` |
| Docker socket | `ro` (read-only) |

**Exposición de puerto:** solo `80:80`. El API `:8080` no se publica al host;
queda interno y se accede vía el router del dashboard.

---

## 5. Red

```yaml
# definida por infrastructure/traefik/docker-compose.yml
networks:
  edge:
    name: edge
    driver: bridge
```

Los servicios la referencian como:

```yaml
networks:
  edge:
    external: true
```

Resolución DNS automática por `container_name` dentro de `edge`.

---

## 6. Principios de evolución

| Principio | Regla |
|---|---|
| **Agregar servicio** | Nuevo dir en `services/` → compose con red `edge` external + labels → `docker compose up -d` |
| **Tocar Traefik** | Solo gobernanza. Añadir middleware → `infrastructure/traefik/dynamic/` |
| **No exponer a internet** | Sin cloudflared. Si cambia, es decisión de gobernanza |
| **Servicios sin label** | No se descubren (`exposedbydefault=false`) — son internos a la red |

---

## 7. Pendientes

- [ ] Auth del dashboard (basic auth `TRAEFIK_AUTH`) antes de cualquier exposición pública.
- [ ] open-webui/computer (Fase 3, siguiente).
- [ ] Política de TLS interno si se desea HTTPS dentro del tailnet (Tailscale `cert`).
