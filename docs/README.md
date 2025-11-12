# Proyecto de pruebas para Codex en entorno Liferay:

Este repositorio está destinado a **probar y entrenar a Codex** (u otros agentes de inteligencia artificial orientados al desarrollo) en un entorno basado en **Liferay DXP 7.4 GA132**.  
El objetivo principal es permitir que Codex genere, interprete y documente componentes de Liferay de forma correcta dentro de un proyecto realista.

---

## Descripción general:

Este proyecto replica un entorno de desarrollo **Liferay 7.4 GA132** estándar, con estructura modular OSGi, configuraciones de Gradle, y despliegue en entorno local o remoto (Docker, Tomcat o Liferay bundle).

El repositorio sirve como punto de referencia para que un agente como **Codex** pueda:
- Comprender la estructura típica de un workspace de Liferay.
- Generar código compatible (módulos, servicios, portlets, fragmentos, etc.).
- Producir documentación técnica contextual (por ejemplo, `agents.md`).
- Asistir en tareas de mantenimiento, refactorización y despliegue.

---

## Notas adicionales:

- Este repositorio no contiene datos sensibles ni dependencias propietarias.

- Puede ser clonado y modificado libremente para pruebas de agentes.

- El archivo agents.md se genera como documento auxiliar, no debe versionarse de forma obligatoria.