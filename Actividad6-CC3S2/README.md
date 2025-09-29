README

Bienvenido al proyecto

## Reflexión sobre Git

Git es una herramienta fundamental que mantiene un historial de cambios claro y organizado a través de **commits atómicos**, cada uno con un mensaje descriptivo que documenta la evolución del proyecto. El comando `git log` es clave para navegar este historial, permitiendo entender qué cambios se hicieron, quién los hizo y por qué.

El uso de **ramas (branches)** es crucial para el desarrollo, ya que aísla el trabajo en nuevas características o correcciones de errores, protegiendo la estabilidad de la rama principal (`main`). Esto facilita el desarrollo paralelo y la experimentación segura. Una vez que el trabajo en una rama está completo, se integra de forma controlada mediante `Pull Requests`.

Para revisar el historial y el manejo de múltiples líneas de desarrollo, el comando `git log --oneline --graph` ofrece una vista visual y compacta de las ramas y fusiones. Esto demuestra cómo Git gestiona eficientemente el desarrollo no lineal, permitiendo que las líneas de trabajo diverjan y converjan de manera ordenada y trazable. Finalmente, `git status` se usa para asegurar que no queden cambios sin registrar.
