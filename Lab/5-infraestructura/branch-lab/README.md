# Branch Lab — branch-lab

> **Primera rama independiente del Laboratorio Bildung.**
> Nodo headless delegado por completo a agentes. Hazael no lo toca una vez estable.

## Identidad

| Campo | Valor |
|---|---|
| Hostname | `bildung` |
| Tailscale | `100.71.214.45` · Magic DNS `bildung.tail1a3dd6.ts.net` |
| LAN | `192.168.100.45` (WiFi `wlp2s0`) |
| OS | Debian 13 (Trixie), x86_64 |
| Hardware | 2 vCPU, 3.7 GiB RAM, 912 GB disco |
| Propósito | Ejecución delegada: contenedores, code tools, agentes |

## Estado

| Fase | Componentes | Estado |
|---|---|---|
| 0 | timeshift, ufw, hardening SSH, unattended-upgrades, NTP | ✅ |
| 1 | tailscale, syncthing | ✅ |
| 2 | docker + compose + containerd, git + ssh | ✅ |
| 3 | Traefik (proxy aislado) + whoami (validación) | ✅ |
| 3b | Open WebUI Computer (cptr) — administración del nodo | ✅ |
| 4 | code tools (aider + Cline), telemetría | ⏳ pendiente |

## Acceso

| Recurso | URL |
|---|---|
| cptr (administración) | `http://bildung.tail1a3dd6.ts.net:8000` |
| Traefik dashboard | `http://bildung.tail1a3dd6.ts.net/dashboard/` |
| whoami (prueba) | `http://bildung.tail1a3dd6.ts.net/whoami/` |

Todo **solo por Tailscale**. Nada expuesto a internet.

## Documentación

- [`BOOTSTRAP.md`](BOOTSTRAP.md) — manual de replicación: qué automatiza el
  agente, dónde interviene Hazael (4 auths), y el checklist de pruebas.
- El stack de contenedores vive en
  [`docker-server/docker-branch-lab/`](../docker-server/docker-branch-lab/).

## Nota de gobernanza

Este nodo opera bajo **delegación absoluta**: SSH solo clave, `sudo` sin
password, usuario en grupo `docker`. Cualquier agente con acceso SSH es
efectivamente root del nodo. La red de seguridad es `timeshift` + firewall
cerrado + Tailscale controlado por Hazael. cptr (equivalente a un SSH con
interfaz web) hereda ese mismo nivel de confianza.
