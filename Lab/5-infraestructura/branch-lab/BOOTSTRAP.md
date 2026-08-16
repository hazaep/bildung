# Branch Lab — Manual de Replicación (Bootstrap)

> **Objetivo:** que un agente pueda replicar un nodo idéntico en la red, sabiendo
> exactamente dónde interviene Hazael (auths) y qué valida al final.
>
> Nodo de referencia: `bildung` (Acer E15, Debian 13). Creado 2026-08-13.

---

## 0. El flujo en una frase

> Hazael prepara el equipo y hace **4 autenticaciones manuales**.
> El agente ejecuta **todo lo demás** (fases 0→3b) y valida con pruebas.

Nada más que eso. El agente no puede hacer las auths (requieren cuentas de
Hazael); todo el resto es automatizable.

---

## 1. Variables por nodo (lo que cambia)

Antes de empezar, fijar estos valores. Los de referencia son los del nodo actual:

| Variable | Valor de referencia | Nota |
|---|---|---|
| `NODO_HOSTNAME` | `bildung` | el que aparece en `hostname` |
| `NODO_USER` | `branch-lab` | usuario del sistema |
| `NODO_IP_LAN` | `192.168.100.45` | asignada por DHCP/router |
| `NODO_IP_TAILSCALE` | `100.71.214.45` | asignada al autenticar Tailscale |
| `TAILNET` | `tail1a3dd6` | nombre del tailnet |
| `MAGIC_DNS` | `bildung.tail1a3dd6.ts.net` | `{hostname}.{tailnet}.ts.net` |
| `ZONA_HORARIA` | `America/Denver` | del instalador |
| `RED_LAN` | `192.168.100.0/24` | subred local |
| `RED_TAILSCALE` | `100.64.0.0/10` | rango CGNAT estándar de Tailscale |

---

## 2. Intervención humana (los únicos 4 momentos)

Estos pasos **no los puede hacer el agente**. Hazael debe estar presente:

### 2.1 Preparación previa (antes de entregar al agente)

1. Instalar Debian 13 (Trixie) desde ISO **netinstall**, no desktop.
   En `tasksel` marcar solo **SSH server** (nada de desktop environment).
   - Usuario: `branch-lab`
   - Si por error carga GUI (ISO desktop), corregir: `systemctl set-default multi-user.target`.
2. Conectarlo a la WiFi (configura NetworkManager en la instalación).
3. Añadir la **clave pública SSH del agente** a `/home/branch-lab/.ssh/authorized_keys`
   para que el agente pueda entrar sin password.
4. Confirmar que Hazael puede entrar: `ssh branch-lab@192.168.100.45`.

### 2.2 Auth 1 — GitHub (clave SSH)

El agente genera la clave y muestra la pública. Hazael la agrega a GitHub
(Settings → SSH keys). Sirve para que el nodo interactúe con repos.

### 2.3 Auth 2 — Tailscale

El agente instala `tailscaled`. Hazael ejecuta la autenticación:

```bash
sudo tailscale up
```

Abre la URL que imprime, autentica con su cuenta. El nodo queda en el tailnet
y recibe su IP (`100.71.214.45`).

### 2.4 Auth 3 — Syncthing (handshake de dispositivos)

El agente instala Syncthing y entrega el **device ID del nodo**. Hazael:

1. Agrega ese device ID en **su** Syncthing.
2. Acepta la carpeta compartida de prueba.

### 2.5 Auth 4 — cptr (primer login)

El agente arranca cptr y entrega la URL con `?token=...` (startup token).
Hazael la abre desde su teléfono (o dispositivo en tailnet) y crea el
**usuario/contraseña** definitivos. El token es de un solo uso.

---

## 3. Estándar operativo (aplicar en TODA la ejecución)

1. **Redirigir output a log.** Cada instalación/operación va a
   `~/.bootstrap/logs/NN-descripcion.log`, reporta exit code, y solo se hace
   `tail`/`grep` en fallo. Reduce entropía de contexto.
2. **`grep` por patrón de éxito, no `tail` ciego.** Procesos con barra de progreso
   (timeshift, apt, pip) inundan el log. Buscar `saved successfully`, `EXIT:0`,
   `Setting up`, `Successfully installed`.
3. **Snapshot timeshift antes de cambios grandes.**
4. **Verificar exit code después de cada paso** (`echo $?` → `0`).

---

## 4. Fases de ejecución (todo automatizable por el agente)

### Fase 0 — Línea base segura

```bash
# 0.1 snapshot baseline (ANTES de tocar nada)
sudo timeshift --create --comments "baseline-limpio-pre-docker" --tags D

# 0.2 firewall: cerrar inbound, permitir SSH
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from 192.168.100.0/24 to any port 22 proto tcp   # LAN
sudo ufw allow from 100.64.0.0/10 to any port 22 proto tcp       # Tailscale
sudo ufw --force enable

# 0.3 hardening SSH (drop-in, no tocar sshd_config principal)
printf "PermitRootLogin no\n" | sudo tee /etc/ssh/sshd_config.d/99-hardening.conf
sudo sshd -t && sudo systemctl reload ssh

# 0.4 parches de seguridad automáticos
printf 'APT::Periodic::Update-Package-Lists "1";\nAPT::Periodic::Unattended-Upgrade "1";\nAPT::Periodic::AutocleanInterval "7";\n' \
  | sudo tee /etc/apt/apt.conf.d/20auto-upgrades
```

**Nota:** `passwordauthentication no` y `pubkeyauthentication yes` vienen por
defecto en Debian 13. Solo hace falta endurecer `PermitRootLogin`. NTP usa
`systemd-timesyncd` (viene activo).

### Fase 1 — Acceso y persistencia

```bash
# 1.1 Tailscale (instalación; la auth es de Hazael — §2.3)
curl -fsSL https://tailscale.com/install.sh | sudo sh

# 1.2 Syncthing (repo oficial)
sudo mkdir -p /etc/apt/keyrings
sudo curl -fsSL -o /etc/apt/keyrings/syncthing-archive-keyring.gpg \
  https://syncthing.net/release-key.gpg
echo "deb [signed-by=/etc/apt/keyrings/syncthing-archive-keyring.gpg] https://apt.syncthing.net/ syncthing stable" \
  | sudo tee /etc/apt/sources.list.d/syncthing.list
sudo apt-get update && sudo apt-get install -y syncthing
sudo systemctl enable --now syncthing@branch-lab

# 1.3 firewall para Syncthing (puertos 22000 y 21027)
sudo ufw allow from 192.168.100.0/24 to any port 22000 proto tcp
sudo ufw allow from 192.168.100.0/24 to any port 22000 proto udp
sudo ufw allow from 192.168.100.0/24 to any port 21027 proto udp
sudo ufw allow from 100.64.0.0/10 to any port 22000 proto tcp
sudo ufw allow from 100.64.0.0/10 to any port 22000 proto udp
sudo ufw allow from 100.64.0.0/10 to any port 21027 proto udp
```

### Fase 2 — Runtime

```bash
# 2.1 Docker CE (repo oficial, NO docker.io de Debian)
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg \
  -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian trixie stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# 2.2 usuario al grupo docker (delegación absoluta)
sudo usermod -aG docker branch-lab

# 2.3 git
sudo apt-get install -y git
git config --global user.name "Branch Lab"
git config --global user.email "branch-lab@bildung"
```

### Fase 3 — Traefik (reverse proxy aislado)

El stack vive en el repo `Lab/5-infraestructura/docker-server/docker-branch-lab/`.
Se copia al nodo y se despliega **en dos stacks separados**.

```bash
# 3.1 abrir puerto 80 (HTTP)
sudo ufw allow from 100.64.0.0/10 to any port 80 proto tcp
sudo ufw allow from 192.168.100.0/24 to any port 80 proto tcp

# 3.2 Traefik (stack INDEPENDIENTE — crea la red 'edge')
cd ~/docker/docker-branch-lab/infrastructure/traefik
docker compose up -d

# 3.3 whoami (stack SEPARADO — red 'edge' external, no la posee)
cd ~/docker/docker-branch-lab/services/whoami
docker compose up -d
```

**Regla de arquitectura:** Traefik es **intocable**. Vive en su propio stack y
posee la red `edge`. Los servicios la referencian como `external: true`. Un
`docker compose down` en un servicio jamás tumba el proxy.

### Fase 3b — Open WebUI Computer (cptr)

Instalación **HOST NATIVO** (única excepción a "todo en contenedores", porque
cptr es infraestructura de administración, análoga a SSH — sirve la máquina entera).

```bash
# 3b.1 dependencias Python (Debian 13 trae Python sin pip por PEP 668)
sudo apt-get install -y python3-pip python3-venv

# 3b.2 venv dedicado + cptr con todas las opciones
mkdir -p ~/venvs
python3 -m venv ~/venvs/cptr
~/venvs/cptr/bin/pip install --upgrade pip
~/venvs/cptr/bin/pip install 'cptr[all]'

# 3b.3 systemd user service + linger (persiste sin login)
mkdir -p ~/.config/systemd/user
cat > ~/.config/systemd/user/cptr.service <<'EOF'
[Unit]
Description=Open WebUI Computer
After=network.target

[Service]
ExecStart=/home/branch-lab/venvs/cptr/bin/cptr run --host 0.0.0.0 --port 8000 --headless
Restart=on-failure
RestartSec=5

[Install]
WantedBy=default.target
EOF
systemctl --user daemon-reload
loginctl enable-linger branch-lab
systemctl --user enable --now cptr.service

# 3b.4 puerto 8000 SOLO tailnet (nunca LAN, nunca internet)
sudo ufw allow from 100.64.0.0/10 to any port 8000 proto tcp

# 3b.5 obtener el startup token para Hazael (§2.5)
journalctl --user -u cptr.service --no-pager | grep -oE "token=[a-f0-9]+" | head -1
```

**Claves de cptr:**
- `--headless` = "no abrir navegador al arrancar" (la web se sirve siempre).
- `--host 0.0.0.0` = escuchar en todas las interfaces; **ufw** es quien filtra
  el acceso (no el bind). Por eso `127.0.0.1` NO serviría para tailnet.
- El control de acceso entre nodos del tailnet mono-identidad vive en **ufw**
  de cada nodo, no en las ACLs de Tailscale.

---

## 5. Pruebas de validación (checklist final)

Ejecutar y verificar. Todas deben pasar.

### Fase 0
```bash
sudo ufw status | head -1                          # → "Status: active"
sudo sshd -T | grep -i permitrootlogin             # → "no"
timedatectl | grep synchronized                     # → "yes"
sudo timeshift --list                               # → 1 snapshot
systemctl is-enabled ssh tailscaled docker ufw     # → todos "enabled"
```

### Fase 1
```bash
tailscale ip -4                                     # → IP 100.x.x.x asignada
syncthing cli show connections | grep connected     # → "connected": true (tras §2.4)
```

### Fase 2
```bash
docker run --rm hello-world | grep "Hello"          # → "Hello from Docker!"
docker ps                                           # → sin error de permission denied
```

### Fase 3
```bash
docker network ls | grep edge                       # → red "edge" creada
curl -s -o /dev/null -w "%{http_code}" http://localhost/whoami    # → 200
curl -s -o /dev/null -w "%{http_code}" http://100.71.214.45/whoami  # → 200 (tailnet)
```

### Fase 3b
```bash
ss -tlnp | grep :8000                               # → LISTEN 0.0.0.0:8000
curl -s -o /dev/null -w "%{http_code}" http://100.71.214.45:8000/  # → 200 (tailnet)
# Desde un cliente LAN externo (NO desde el propio nodo):
curl -s -o /dev/null -w "%{http_code}" http://192.168.100.45:8000/  # → 000 (denegado, correcto)
```

### Prueba definitiva (persistencia)
```bash
sudo reboot
# tras reiniciar, verificar sin intervención:
#   - SSH responde
#   - tailscale ip -4 devuelve la IP
#   - systemctl --user is-active cptr.service → active
#   - docker ps → traefik + whoami corriendo
```

**Resultado (2026-08-16):** ✅ PASS en el nodo de referencia.
- reboot → vuelve solo (~75s), WiFi reconecta sola (conexión de sistema vía NetworkManager)
- ssh, tailscaled (IP intacta), docker, ufw, cptr: todos `active` sin intervención
- traefik + whoami: auto-reiniciados por `restart: unless-stopped`

> ⚠️ **Caveat de hardware (nodos SIN batería):** si el equipo **se apaga** en lugar
> de reiniciar (no vuelve a la red), es un problema de **fuente de alimentación**
> (pico de demanda sin fallback a batería), **no** de configuración. Verificar
> físicamente: ¿encendió? ¿LED de power? Si no volvió, encender manualmente y
> re-ejecutar. La config del server es determinista y ya quedó validada.

---

## 6. Datos operativos del nodo de referencia

| Campo                | Valor                                                             |
| -------------------- | ----------------------------------------------------------------- |
| Hostname             | `bildung`                                                         |
| OS                   | Debian 13 (Trixie), kernel 6.12, x86_64                           |
| Hardware             | 2 vCPU, 3.7 GiB RAM, 912 GB disco                                 |
| Tailscale IP         | `100.71.214.45`                                                   |
| LAN IP               | `192.168.100.45` (WiFi `wlp2s0`)                                  |
| Magic DNS            | `bildung.tail1a3dd6.ts.net`                                       |
| Syncthing device ID  | `2C24BNU-F6MSUTS-HIF4TJ7-AZCRWDJ-XCHGKH6-YH6Y5JL-XCPTYRQ-WSBLJAG` |
| cptr                 | `http://bildung:8000`                                             |
| Logs de bootstrap    | `~/.bootstrap/logs/`                                              |
| Repo de contenedores | `~/docker/docker-branch-lab/`                                     |

---

## 7. Pendientes (decisión de gobernanza)

- [ ] **Backup offsite**: timeshift cubre el SO, NO los volúmenes Docker. Evaluar MEGA 50GB vía mega-cli.
- [ ] **Fuente de alimentación**: el nodo de referencia funciona SIN batería (solo fuente). Observar si hay apagones en picos de demanda; si se repiten, conseguir batería o fuente de mayor capacidad.
- [ ] **Esquema definitivo de claves SSH** (una por agente; hoy es una común).
- [ ] **Gateway API**: conectar cptr con Open WebUI — el flujo Arquitecto→Tech Lead.
- [ ] **open-webui/computer como workspace** formal del Tech Lead.
- [ ] **Telemetría** del nodo → proyecto `telemetry`.
- [ ] **code tool**: instalar `aider` + Cline CLI (comparación).
