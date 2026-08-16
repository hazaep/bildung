# Traefik — Recipe v1.0

## 🎯 Propósito

Reverse proxy del nodo `branch-lab`. **Todo el tráfico HTTP del nodo pasa por aquí.** Vive en un stack independiente (`infrastructure/traefik/`) que posee la red `edge`. Solo gobernanza lo toca.

---

## 📦 Imagen

| Campo | Valor |
|---|---|
| Image | `traefik:v3` (major pineado, **no** `latest`) |
| Puerto host | `80` (HTTP) |
| API / dashboard | `:8080` interno (no publicado; accesible vía router `/api` y `/dashboard`) |
| Reinicio | `unless-stopped` |
| Red | `edge` (bridge, **creada y poseída por este stack**) |

---

## 🔗 Dependencias

| Recurso | Tipo | Obligatorio |
|---|---|---|
| docker.sock | Descubrimiento de servicios | **Sí** |
| `./dynamic/` | Middlewares file-based | **Sí** |
| cloudflared | — | **No existe** en branch-lab |

---

## 🔐 Variables de entorno

**Ninguna.** La configuración va en `command` (flags CLI) y en el file provider. No hay `DOMINIO` (ruteo por `PathPrefix`).

Flags principales (`command`):

| Flag | Efecto |
|---|---|
| `--api.dashboard=true` | Activa el dashboard |
| `--providers.docker=true` | Descubre servicios vía labels |
| `--providers.docker.exposedbydefault=false` | Solo `traefik.enable=true` se descubren |
| `--providers.docker.network=edge` | Red donde vive todo |
| `--providers.file.directory=/dynamic` + `--watch=true` | Middlewares file-based |
| `--entrypoints.web.address=:80` | Entrypoint HTTP |
| `--accesslog=true` | Access log legible |

---

## 📂 Volúmenes

| Path contenedor | Path host | Contenido |
|---|---|---|
| `/var/run/docker.sock` (ro) | Host | Descubrimiento de servicios |
| `/dynamic` (ro) | `./dynamic/` | Middlewares YAML (`security-headers.yml`) |

---

## 🚀 Deploy

```bash
# 1. Fundación (crea la red edge)
cd infrastructure/traefik && docker compose up -d

# 2. Verificar la red
docker network ls | grep edge
```

---

## 🩺 Health check

```bash
# Dashboard (vía router, no puerto directo)
curl -s http://bildung.tail1a3dd6.ts.net/dashboard/ | head -3

# API interna (accesible por el router PathPrefix /api)
curl -s http://bildung.tail1a3dd6.ts.net/api/rawdata | jq '.routers | keys'

# Verificar que un servicio está registrado
curl -s http://bildung.tail1a3dd6.ts.net/api/http/routers | jq '.[] | select(.service=="whoami@docker")'

# Estado del contenedor
docker compose ps traefik
```

---

## 🧹 Backup

```bash
# Solo los middlewares file-based (lo único con estado)
cp ./dynamic/security-headers.yml ./dynamic/security-headers.yml.backup
```

---

## 🔄 Restore

```bash
cp ./dynamic/security-headers.yml.backup ./dynamic/security-headers.yml
docker compose restart traefik
```

---

## 🐛 Troubleshooting

| Síntoma | Causa probable | Solución |
|---|---|---|
| 502 Bad Gateway | El servicio backend no está corriendo o no está en `edge` | `docker compose ps <servicio>` y `docker network inspect edge` |
| 404 en ruta | Label `traefik.http.routers.*.rule` (PathPrefix) mal configurado | Revisar compose del servicio |
| Dashboard no carga | `--api.dashboard=true` ausente o path distinto | Verificar `command` en compose |
| Servicio no aparece | `traefik.enable=true` ausente | Agregar label al servicio |
| "network edge not found" | La red no existe (traefik no desplegado) | Desplegar traefik primero |
| Error de middlewares | `security-headers.yml` no encontrado | Verificar `--providers.file.directory=/dynamic` y el bind mount |
