# Estructura

Ahora debe ser mas simple, la complejidad genera friccion.

# Bildug

```
Buldung
├── gobernanza/
│   ├── notebook/
│   └── buzon
├── proyectos/
│   ├── 
│   └── 
├── codex/
├── praxis/
│   ├── ontos/
│   ├── hypostasis
│   ├── taxis
│   └── tekne
```

en preparacion a las etapas futuras, branch lab de tener exito, se combertira en division de software

**Estructura propuesta de Praxis:**
| Capa               | Nombre        | Función                                                                                                                                                                                                                                        |
| ------------------ | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Proyecto           | **Praxis**    | sistema agéntico completo (contr                                                                                                                                                                                                               |
| Ontología          | **Ontos**     | constituido por contratos establecidos organizados por division/rol                                                                                                                                                                            |
| Leyes/Constitución | Nomos         | Leyes reglas, principios, límites normativos del sistema, isomorfos de agent_constitution.md organizados por globales/nombre_del_articulo.md<br>division/nombre_del_articulo.md # las globales<br>division/departamento/nombre_del_articulo.md |
| Entidades          | **Hypostasis* | registro de cada agente instanciado                                                                                                                                                                                                            |
| Jerarquía          | **Taxis**     | roles, capacidades, límites, isomorfos de la agent_hierarchy.md, seria como el organigrama agentico de bildung                                                                                                                                 |
| Biblioteca         | **Tekhne**    | skills utiles pa                                                                                                                                                                                                                               |
| Capa               | Nombre        | Función                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Proyecto           | **Praxis**    | sistema agéntico completo (contraparte del Codex)                                                                                                                                                                                                                                                                               |
| Ontología          | **Ontos**     | constituido por contratos, protocolos establecidos organizados por division/rol                                                                                                                                                                                                                                                 |
| Leyes/Constitución | Nomos         | Leyes reglas, principios, límites normativos del sistema, isomorfos de agent_constitution.md organizados por globales/nombre_del_articulo.md, division/nombre_del_articulo.md # las globales de division, division/rol/nombre_del_articulo.md si aplica                                                                         |
| Entidades          | **Hypostasis* | registro de cada agente instanciado organizados por division/rol/\|, todas las demas son referenciables aqui, cada paquete constitulle una identidad agentica, configuracion, modelo, system prompt, skill, contratos de ontos asociados, leyes de nomos aplicables, jerarquia de taxis a la que pertenece, artifacts de tekhne |
| Jerarquía          | **Taxis**     | roles, capacidades, límites, isomorfos de la agent_hierarchy.md, seria como el organigrama agentico de bildung                                                                                                                                                                                                                  |
| Biblioteca         | **Tekhne**    | skills utiles para los agentes definidos en hypostasis, destiladas de alchemy                                                                                                                                                                                                                                                   |


| Capa               | Nombre        | Función                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Proyecto           | **Praxis**    | sistema agéntico completo (contraparte del Codex)                                                                                                                                                                                                                                                                               |
| Ontología          | **Ontos**     | constituido por contratos, protocolos establecidos organizados por division/rol                                                                                                                                                                                                                                                 |
| Leyes/Constitución | Nomos         | Leyes reglas, principios, límites normativos del sistema, isomorfos de agent_constitution.md organizados por globales/nombre_del_articulo.md, division/nombre_del_articulo.md # las globales de division, division/rol/nombre_del_articulo.md si aplica                                                                         |
| Entidades          | **Hypostasis* | registro de cada agente instanciado organizados por division/rol/\|, todas las demas son referenciables aqui, cada paquete constitulle una identidad agentica, configuracion, modelo, system prompt, skill, contratos de ontos asociados, leyes de nomos aplicables, jerarquia de taxis a la que pertenece, artifacts de tekhne |
| Jerarquía          | **Taxis**     | roles, capacidades, límites, isomorfos de la agent_hierarchy.md, seria como el organigrama agentico de bildung                                                                                                                                                                                                                  |
| Biblioteca         | **Tekhne**    | skills utiles para los agentes definidos en hypostasis, destiladas de alchemy                                                                                                                                                                                                                                                   |
**Nomos** son las reglas generales del sistema (constitución), mientras que **Taxis** es cómo esas reglas se traducen en capacidades/límites específicos por rol. Uno es la ley, el otro es la jurisdicción.
## Branch Lab

```
~/.bildung-branch/
├── buzon/
│   ├── gobernanza/
│   ├── 
│   └── tesh-lead/
├── proyectos/
```

~/architect/      ← INBOX (carpeta Syncthing nueva, para cuando sincronicemos) se va a sincronizar al server-rpi4b 24/7

~/architect/                ← WORKSPACE del Arquitecto-Ejecutor (cptr) — ya existe

  ├── plan/                ← el ANCLA: items del plan aprobado (normalizados)

  ├── briefs/              ← briefs que genero para el Tech Lead

  ├── reportes/            ← reportes de hito que devuelve el Tech Lead

  └── cierre/                ← reporte de cierre del arquitecto

~/.bildung/                    ← WORKSPACE del Tech Lead (OpenCode) — código real

  └── <proyecto>/          ← un directorio por proyecto activo

por ahora no usaremos las automatizaciones hasta saber si encaja con los ciclos de vida que planeamos implementar

un item invalido de parte de gobernanza crea reporte de rechazo con motivos y propuestas y cierra ciclo de vida, pero eso es lo que vamos a resolver, voy a tomar todo lo que estoy aprendiendo de esto y lo convertire en documentacion ejecutable, unificando el proyecto Praxis una fractalidad emergente de codex, para moldear el ecosistema agentico

Estructura propuesta de Praxis:







Capa



Nombre



Función





Proyecto



Praxis



sistema agéntico completo (contraparte de Codex)





Ontología



Ontos



constituido por contratos





Leyes/Constitución



Nomos



reglas, principios, límites normativos del sistema, isomorfos de agent_constitution.md





Entidades



Hypostasis



registro de cada agente instanciado





Jerarquía



Taxis



roles, capacidades, límites, isomorfos de la agent_hierarchy.md





Biblioteca



Tekhne



skills utiles para los agentes definidos en hypostasis, destiladas de alchemy

Nomos son las reglas generales del sistema (constitución), mientras que Taxis es cómo esas reglas se traducen en capacidades/límites específicos por rol. Uno es la ley, el otro es la jurisdicción.