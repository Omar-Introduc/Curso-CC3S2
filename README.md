# Introducción a DevOps y DevSecOps
**Fecha:** 01/09/2025
**Tiempo invertido:** 04:00

---

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






