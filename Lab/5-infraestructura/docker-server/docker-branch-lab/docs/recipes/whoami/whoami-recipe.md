# whoami — Recipe v1.0

## 🎯 Propósito

Servicio de **prueba** que valida el patrón de integración del nodo `branch-lab`: red externa `edge` + labels `traefik.*`. **No es producción.** Devuelve los headers de la petición para confirmar que Traefik enruta correctamente.

---

## 📦 Imagen

| Campo | Valor |
|---|---|
| Image | `traefik/whoami` |
| Puerto interno | `80` (vía `loadbalancer.server.port=80`) |
| Puerto host | Ninguno (solo vía Traefik) |
| Reinicio | `unless-stopped` |
| Red | `edge` (**external: true**) |

---

## 🔗 Dependencias

| Recurso | Tipo | Obligatorio |
|---|---|---|
| red `edge` | Red externa (poseída por el stack de Traefik) | **Sí** — debe existir antes |
| traefik | Reverse proxy (stack `infrastructure/`) | **Sí** |

---

## 🔐 Variables de entorno

**Ninguna.**

---

## 📂 Volúmenes

**Ninguno.** Servicio efímero.

---

## 🚀 Deploy

```bash
# Requisito previo: la red `edge` existe (desplegada por Traefik)
docker network ls | grep edge

# Levantar whoami
cd services/whoami && docker compose up -d
```

---

## 🩺 Health check

```bash
# Responde vía Traefik (debe devolver JSON con headers y el host)
curl -s http://bildung.tail1a3dd6.ts.net/whoami/

# Pertenece a la red edge
docker network inspect edge --format '{{range .Containers}}{{.Name}} {{end}}'

# No publica puerto al host
docker compose ps whoami
```

---

## 🧹 Backup

No aplica. Sin estado, sin volúmenes.

---

## 🔄 Restore

No aplica. Basta con `docker compose up -d` para recrearlo.

---

## 🐛 Troubleshooting

| Síntoma | Causa probable | Solución |
|---|---|---|
| 404 en `/whoami/` | Router `PathPrefix(/whoami)` no activo o label ausente | Verificar labels en compose |
| 502 Bad Gateway | Contenedor no está en `edge` o puerto interno distinto de 80 | `docker network inspect edge`; revisar `loadbalancer.server.port` |
| "network edge not found" | Traefik no desplegado | Desplegar `infrastructure/traefik` primero |
| No responde por IP:80 directa | whoami no publica puerto al host (por diseño) | Usar la URL vía Traefik |
