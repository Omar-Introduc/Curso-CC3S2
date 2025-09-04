---
bibliography:FUENTES.md
---
# Introducción a DevOps y DevSecOps
**Fecha:** 01/09/2025
**Tiempo invertido:** 04:00

## Entorno Utilizado
* **Sistema Operativo:** Windows 11
* **Terminal:** Windows Terminal con PowerShell
* **Navegador Web:** Microsoft Edge
* **Editor de Código:** Visual Studio Code
* **Tipo de Red:** Red publica UNI

## 4.1 DevOps vs. cascada tradicional

![DevOps vs. cascada tradicional](/imagenes/devops-vs-cascada.png)
_DevOps vs. cascada tradicional_

Explica por qué DevOps acelera y reduce riesgo en software para la nube frente a cascada (feedback continuo, pequeños lotes, automatización).

* Respecto a la detección de errores detectar un error en pleno proceso de integración de desarrollo es mucho mas rentable que detectarlo depues de meses en las fases de prueba.
En el apartado de pequeños lotes, encontrar una falla en código con poca estructura resulta mas óptimo que en el desarrollo completo que se da en el método cascada.
Dar paso a la automatización nos genere una eficacia mayor y reduce la probabilidad de error humano.

Pregunta retadora: señala un contexto real donde un enfoque cercano a cascada sigue siendo razonable (por ejemplo, sistemas con certificaciones regulatorias estrictas o fuerte acoplamiento hardware). Expón dos criterios verificables y los trade-offs (velocidad vs. conformidad/seguridad).

* Cuando el software es sometido a un proceso de certificación gubernamental debe cumplir cierto parámetro regulatorios, esto hace que un modelo iterativo como devOps sea impráctico, ya que se requiere una extensa documentación exhaustiva que el modelo cascada si ofrece.
El software adaptado a hardware que no puede cambiar y no se pueden realizar actualizaciones post-despliegue, si el hardware no puede cambiar los requisitos son necesariamente fijos centrándose en la perfección de la primera versión.

# 4.2 Ciclo tradicional de dos pasos y silos
![imagenes/silos-equipos](/imagenes/silos-equipos.jpg)
_silos-equipos_

Identifica dos limitaciones del ciclo "construcción -> operación" sin integración continua (por ejemplo, grandes lotes, colas de defectos).

* Sin integraciones el código se desarrolla durante semanas o meses antes de una fusión, creando grandes lotes de código que hacen de la integración una tarea compleja. y cuando se detecta fallos de QA, este es reportado a un sistema de colas, donde a través del paso de tiempo cuando un desarrollador lo atiende, ha perdido el contexto del trabajo original. Se genera retraso y aumento en el esfuerzo de reanálisis creando ineficiencias en este ciclo.

Pregunta retadora: define dos anti-patrones ("throw over the wall", seguridad como auditoría tardía) y explica cómo agravan incidentes (mayor MTTR, retrabajos, degradaciones repetitivas).

* "Throw over the wall" (Lanzar sobre el muro):
    * Es la práctica de completar una fase de trabajo y transferir toda la responsabilidad al siguiente equipo de forma impersonal, como si se lanzara un paquete sobre un muro. Este handoff crea asimetrías de información: el equipo que recibe carece del contexto que tenía el equipo de desarrollo.
    * Durante un incidente, se desata un "juego de culpas". Operaciones no entiende el código y Desarrollo no conoce las particularidades del entorno productivo causando problemas.

* Seguridad como auditoría tardía:
    * Ocurre cuando el equipo de seguridad interviene solo al final del ciclo para realizar una auditoría, en lugar de integrarse desde el diseño. Su rol se vuelve en encontrar fallos en vez de prevenirlos. 
    * Si se descubre una falla de seguridad crítica justo antes del despliegue, la solución a menudo requiere un retrabajo masivo, retrasando todo el lanzamiento.

# 4.3 Principios y beneficios de DevOps
Describe CI y CD destacando tamaño de cambios, pruebas automatizadas cercanas al código y colaboración.
* CI y CD describen cambios por lotes pequeños integrandose al proyecto completo bajo las normas de calidad las cuales pueden estar descritas bajo normas de logica en base al codigo o en funcion de problemas  comunes del grupo de desarrollo

Explica cómo una práctica Agile (reuniones diarias, retrospectivas) alimenta decisiones del pipeline (qué se promueve, qué se bloquea).
* Un pipeline se fundamenta y se regula bajo la perspectiva de una metodologia de desarrollo agil, donde se promueve la retroalimentacion diaria de los equipos sobre el estado del proyecto, de esta forma asginando que se promueve o bloquea en base a las resoluciones grupales


Propón un indicador observable (no financiero) para medir mejora de colaboración Dev-Ops (por ejemplo, tiempo desde PR listo hasta despliegue en entorno de pruebas; proporción de rollbacks sin downtime).
* El MTTR mide el tiempo promedio que tarda el equipo en restaurar el servicio por completo después de que se detecta un incidente en producción, no se puede tener un MTTR bajo si Dev le lanza el problema a Ops. La velocidad de recuperación depende directamente de cuán rápido y eficazmente puedan colaborar para diagnosticar, corregir y desplegar una solución.

# 4.4 Evolución a DevSecOps (seguridad desde el inicio: SAST/DAST; cambio cultural)
Diferencia SAST (estático, temprano) y DAST (dinámico, en 
ejecución), y ubícalos en el pipeline.

* Principal diferencial el SAST realiza las pruebas tanto lógicas como de seguridad en un entorno estático, se realiza de manera temprana después del commit y antes de su integración a la rama principal, mientras que el DAST realiza pruebas de seguridad a su vez lógicos, si estos resultan en vulnerabilidades, en tiempo real cunaod el código ya ha sido desplegado y en ejecución.

Define un gate mínimo de seguridad con dos umbrales cuantitativos (por ejemplo, "cualquier hallazgo crítico en componentes expuestos bloquea la promoción"; "cobertura mínima de pruebas de seguridad del X%").

* Cero (0) vulnerabilidades de severidad 'Crítica' o 'Alta' detectadas por el escaneo SAST, la cobertura del escaneo DAST debe ser de al menos el 85% de los endpoint de la API

Incluye una política de excepción con caducidad, responsable y plan de corrección.

* Una excepción puede ser solicitada por el líder técnico, debe ser aprobada por el Director de Seguridad (CISO), tendrá una caducidad de 14 días, y debe incluir un plan de corrección detallado con un ticket asignado en Jira para su seguimiento.

Pregunta retadora: ¿cómo evitar el "teatro de seguridad" (cumplir checklist sin reducir riesgo)? Propón dos señales de eficacia (disminución de hallazgos repetidos; reducción en tiempo de remediación) y cómo medirlas.
* Se evita enfocándose en métricas basadas en el riesgo y no en el cumplimiento. Por ejemplo, en lugar de un checklist de '100% de escaneos completados', se prioriza la corrección del 'Top 10 de vulnerabilidades' en las aplicaciones críticas.
    * Se mide comparando los informes de SAST de un trimestre a otro y calculando el porcentaje de vulnerabilidades recurrentes del mismo tipo
    * Se mide calculando el tiempo promedio desde que una vulnerabilidad es detectada por una herramienta en el pipeline hasta que el commit con la solución es fusionado a la rama principal.

# 4.5 CI/CD y estrategias de despliegue (sandbox, canary, azul/verde)
![imagenes/pipeline_canary](/imagenes/pipeline_canary.png)
_En ingeniería de software, la implementación de Canary es la práctica de realizar lanzamientos por etapas. Primero implementamos una actualización de software para una pequeña parte de los usuarios, para que puedan probarla y proporcionar comentarios. Una vez aceptado el cambio, la actualización se extiende al resto de usuarios._

Elige una estrategia para un microservicio crítico (por ejemplo, autenticación) y justifica.

* Una estrategia para un microservicio crítico que puede resultar fatal si logra pasar las pruebsa con un fallo daria muchos problemas,por el ende el Canary Development con el cual se libera a un porcentual pequeño de usuarios se vuelve la mejor opción.

Crea una tabla breve de riesgos vs. mitigaciones (al menos tres filas), por ejemplo:

### Riesgos y Mitigación en el Despliegue de Software

| Riesgo | Mitigación |
| :--- | :--- |
| **Falla total de la nueva versión** | Despliegue Canario para limitar el radio de impacto. |
| **Lentitud inesperada (latencia)** | Monitoreo de latencia p99 con `rollback` automático. |
| **Sesiones de usuario rotas** | Asegurar compatibilidad de `tokens` y drenado de conexiones. |

Define un KPI primario (p. ej., error 5xx, latencia p95) y un umbral numérico con ventana de observación para promoción/abortado.

* KPI Primario: Tasa de errores de autenticación (respuestas 4xx).
* Umbral: Abortar el despliegue si la tasa de errores aumenta un 1% sobre la línea base durante una ventana de 5 minutos. 

Pregunta retadora: si el KPI técnico se mantiene, pero cae una métrica de producto (conversión), explica por qué ambos tipos de métricas deben coexistir en el gate.

* Ambos deben coexistir porque un KPI técnico mide la salud de la máquina, mientras que una métrica de producto mide el éxito del negocio. Un sistema técnicamente perfecto que no cumple su objetivo es un fracaso.
 