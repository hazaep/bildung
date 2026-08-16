# 🐳 Docker Branch Lab — Stack de producción del nodo `branch-lab`

> **Versión de producción** del entorno de contenedores para el nodo `branch-lab`
> (Debian 13 headless, primera rama independiente del Laboratorio Bildung).
>
> Derivado de la arquitectura `docker-rpi4b`, pero **independiente** de sus stacks
> de producción. Adaptado a Tailscale + Magic DNS (sin Cloudflare).

## Principio rector

> **El reverse proxy es intocable. El resto del entorno es del agente.**

Traefik vive en su **propio stack** (`infrastructure/traefik/`), aislado de los
servicios. Los servicios del agente viven en `services/`, cada uno con su propio
`docker-compose.yml`, conectados a una red externa `edge` que **no poseen**.

Consecuencia: un agente puede hacer `docker compose down` en su servicio para
revisarlo **sin apagarse a sí mismo ni al proxy**. Traefik solo se toca desde
`infrastructure/traefik/`.

## Estructura

```
docker-branch-lab/
├── README.md                     ← este archivo
├── docs/
│   └── architecture.md           ← decisiones de arquitectura y diferencias con rpi4b
├── infrastructure/
│   └── traefik/                  ← 🔴 FUNDACIÓN (intocable)
│       ├── docker-compose.yml    ← stack independiente, crea red `edge`
│       └── dynamic/              ← middlewares file-based
│           └── security-headers.yml
└── services/
    └── whoami/                   ← servicio de prueba (validación del patrón)
        └── docker-compose.yml    ← stack separado, red `edge` externa
```

## Acceso (Magic DNS)

| Servicio | URL |
|---|---|
| Traefik dashboard | `http://bildung.tail1a3dd6.ts.net/dashboard/` |
| whoami (prueba) | `http://bildung.tail1a3dd6.ts.net/whoami/` |

Acceso **solo por Tailscale** (WireGuard cifra el tránsito). Sin exposición a
internet. Sin Cloudflare.

## Deploy

```bash
# 1. Fundación (crea la red edge)
cd infrastructure/traefik && docker compose up -d

# 2. Servicios (se unen a la red edge)
cd services/whoami && docker compose up -d
```

## Estado

| Stack | Servicio | Estado |
|---|---|---|
| infrastructure/traefik | traefik | ✅ desplegado |
| services/whoami | whoami | ✅ desplegado (prueba) |
