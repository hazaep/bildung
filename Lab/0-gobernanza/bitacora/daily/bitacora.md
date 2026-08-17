# Bitácora de gobernanza

- 2026-08-07: se añadio estructura_interna_proyecto_experimental.md propuesta por el proyecto self-driving-lab
- 2026-08-08: inicia versionado con git de bildung
- 2026-08-09: comienza prototipado de sistema agentico
- 2026-08-10: EAL cierra Fase II con 9 ciclos completados. Stack Bildung completo: docs→BD→git→OS→archivos→contenedores. 6 adapters. Validación en producción RPi4B con 16 contenedores reales. Smoke test 6/6 PASS.
- 2026-08-10: Fase III autorizada: Ciclo 10 (code + deps adapters) y Ciclo 11 (lint + diff adapters).
- 2026-08-10: Patrón Mariposa detectado — modifica Bildung mismo. Entropía alta con rutas complicadas. Necesidad de anclas de contexto para agentes.
- 2026-08-10: decisión arquitectónica: Branch Lab con Debian 13 SSH server (sin desktop). Se abolirá `.gob` pero no su contenido. Separación de código vivo vs sistema administrativo.
- 2026-08-10: Codex agentico pendiente: prototipo basado en gradientes de resolución, no solo definiciones.
- 2026-08-10: nuevo gradiente: diseñar el proyecto genera ancla y referencias. Abolir referencias a documentación de Bildung en contextos de agentes — solo necesitan su tarea.
- 2026-08-13: Branch Lab bootstrap. Fase 0 (timeshift, ufw, hardening SSH, unattended-upgrades, NTP) y Fase 1 (Tailscale, Syncthing). Decisiones: Debian 13 headless, firewall cerrado sin IP fija, claves una por agente (pendiente), sudo NOPASSWD, grupo docker (delegación absoluta).
- 2026-08-13: Fase 2 (Docker CE + compose + containerd, git + SSH key en GitHub). Traefik desplegado en stack independiente (red 'edge') + whoami de prueba. Ruteo por PathPrefix vía Magic DNS, sin cloudflared (solo Tailscale).
- 2026-08-13: cptr (Open WebUI Computer) instalado HOST NATIVO como infraestructura de administración. systemd user service + linger. Puerto 8000 solo tailnet. Se entiende el Gateway API (workspace como modelo OpenAI-compatible) → futuro flujo Arquitecto→Tech Lead.
- 2026-08-16: reboot test. Test #1 el nodo se apagó físicamente (fuente sin batería, pico de demanda). Test #2 PASS: vuelve solo ~75s, WiFi reconecta, todos los servicios activos. Caveat de hardware documentado.
- 2026-08-16: timeshift periodicidad programada (diario + timer horario Persistent). El paquete Debian no trae disparador; JSON usa espacios antes de ':' (sed falla, usar python).
- 2026-08-16: OpenCode instalado y validado como code tool del Tech Lead (deepseek-v4-pro). Sub-agents con contexto aislado confirmados (build delegó a Explore Agent). Gotchas: requiere TTY (script -qec), --auto para no-interactivo. Arquitectura de agentes de cptr definida (Gobernanza→Arquitecto-Diseñador Open WebUI→Arquitecto-Ejecutor cptr→Tech Lead OpenCode). Pendiente: definir workspaces.