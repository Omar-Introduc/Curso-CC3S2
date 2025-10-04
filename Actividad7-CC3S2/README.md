### A) Evitar (o no) --ff

**Ejecución de una fusión FF real**
Una fusión fast-forward (--ff) ocurre automáticamente cuando la rama que vas a fusionar es un descendiente directo de la rama principal, sin commits intermedios.

**Respuesta a la pregunta:**
Evitarías una fusión fast-forward (--no-ff) en un equipo cuando quieres que tu historial de commits sea más explícito y legible. Un merge con --no-ff siempre crea un commit de fusión que sirve como un registro claro de que una rama se integró. Esto ayuda a la **trazabilidad** y facilita la **reversión** de una característica completa si algo sale mal.

---

### B) Trabajo en equipo con --no-ff

**Ejecución de una fusión no-ff**
Una fusión no-fast-forward (--no-ff) ocurre cuando las ramas divergieron y cada una tiene commits únicos. Se puede forzar incluso cuando un fast-forward es posible.

**Respuesta a las preguntas:**
* **Ventajas de trazabilidad:** Un commit de fusión sirve como un hito claro que muestra cuándo y quién integró una rama completa de una característica. Esto facilita la depuración y el seguimiento de las versiones.
* **Problemas con exceso de merges:** Un historial con demasiados commits de fusión puede volverse desordenado y difícil de leer. Puede ser complicado ver la secuencia real de cambios.

---

### C) Squash con muchos commits

**Ejecución de una fusión squash**
Una fusión squash comprime todos los commits de una rama en un único commit en la rama principal.

**Respuesta a las preguntas:**
* **¿Cuándo conviene?:** Conviene usar squash cuando una rama de desarrollo tiene muchos commits pequeños o irrelevantes (como "fix", "typo", "WIP") y quieres que el historial en la rama principal sea limpio y tenga un solo commit significativo.
* **¿Qué se pierde?:** Se pierde el historial de los commits individuales. No puedes ver los pasos intermedios de cómo se desarrolló la función, solo el resultado final.

### Conflictos reales con no-fast-forward

**Ejecución de un conflicto de fusión**
Un conflicto ocurre cuando dos ramas modifican la misma parte de un archivo. Al intentar fusionarlas, Git no sabe qué versión mantener y requiere que lo resuelvas manualmente. 

**Respuesta a las preguntas:**
* **Pasos adicionales:** Para resolver el conflicto, tuve que **editar el archivo manualmente** para elegir qué versión de la línea quería mantener. Luego, usé **git add para marcar el conflicto como resuelto** y **git commit para finalizar la fusión**.
* **Cómo evitarlo:** La mejor forma de evitar conflictos es con **peticiones de fusión (PRs) pequeñas** y bien definidas. También ayuda que el equipo tenga una buena comunicación para no trabajar en las mismas líneas al mismo tiempo, usar **git pull --rebase** para mantener las ramas actualizadas antes de hacer cambios, y tener **pruebas automáticas** que detecten errores.

### Comparación de Historias de Git

**Ejecución de las vistas de historial**

**Pasos:**
1.  **Generar el log de Fast-forward:**
    git log --graph --oneline --decorate --all --first-parent > evidencias/05-compare-fastforward.log
2.  **Generar el log solo de merges:**
    git log --graph --oneline --merges --decorate > evidencias/06-compare-noff.log
3.  **Generar el log completo:**
    git log --graph --oneline --decorate --all > evidencias/07-compare-squash.log

---

### Análisis y Preferencias

**¿Cómo se ve el DAG en cada caso?**

* **Fast-forward**: El historial es una **línea recta y simple**. Los commits de la rama fusionada se añaden directamente al final de la rama principal sin ningún commit de fusión. La rama principal simplemente "avanza" hasta el último commit de la rama fusionada.
* **--no-ff (No-fast-forward)**: El historial muestra un **commit de fusión** que une las dos ramas. El grafo del historial no es lineal; muestra un punto donde las ramas se bifurcan y luego se unen. Esto deja un registro claro de que se realizó una fusión.
* **Squash**: El historial también es una **línea recta**, similar al fast-forward, pero la rama de origen (la que se fusionó) **no aparece** en el historial. El trabajo de múltiples commits se comprime en un único commit en la rama principal.

---

**¿Qué método prefieres para cada escenario?**

* **Trabajo individual (proyectos pequeños):** Prefiero el **fast-forward** o el **rebase**. Mantienen el historial lineal y limpio, lo que es ideal cuando no se necesita una trazabilidad compleja y se busca simplicidad.
* **Equipo grande (proyectos colaborativos):** Prefiero el **no-fast-forward**. El commit de fusión es invaluable para la **trazabilidad**. Sirve como un hito claro que muestra cuándo y quién fusionó una característica. Esto facilita la depuración y la revisión del código en entornos complejos.
* **Repositorios con auditoría estricta (o de código abierto):** Prefiero el **squash**. Permite que los revisores vean un historial de trabajo detallado en la rama de funcionalidad, pero mantiene el historial principal (main) limpio y lleno de commits lógicos y de alta calidad. Esto asegura que el main sea fácil de auditar.

### Revertir una Fusión

**Ejecución de un revert de un merge**
Se utiliza git revert para deshacer los cambios de un merge commit y crear un nuevo commit que revierta esos cambios. Este método es seguro porque no reescribe el historial.

**Pasos:**
1.  **Asegurar merge commit reciente:** Asegúrate de estar en una rama donde el último commit sea una fusión no-fast-forward (ej. del ejercicio B).
2.  **Verificar el merge commit:**
    git show -s --format=%P HEAD
    La salida debe mostrar dos hashes de commit, confirmando que es un merge commit con dos padres.
3.  **Revertir la fusión:**
    git revert -m 1 HEAD
    El flag -m 1 le dice a Git que mantenga la rama principal (parent 1) y revierta los cambios de la otra rama (parent 2), creando un nuevo commit de reversión.
4.  **Generar la evidencia:**
    mkdir -p evidencias
    git log --graph --oneline --decorate --all > evidencias/08-revert-merge.log

---

### Preguntas

**¿Cuándo usar git revert en vez de git reset?**
Usa **git revert** cuando quieras deshacer commits en un repositorio **compartido y público**. Revert no reescribe el historial, sino que crea un nuevo commit que deshace los cambios. Es un método seguro que no afecta el trabajo de otros colaboradores. Usa **git reset** solo en tu rama local si aún no has subido tus commits al repositorio público. Reset reescribe el historial, lo que puede causar problemas para el resto del equipo.

**¿Impacto en un repo compartido con historial público?**
El impacto de git revert es mínimo y seguro. Revert mantiene el historial completo y crea un commit nuevo. La reversión queda registrada, lo que facilita el seguimiento de los cambios y asegura la integridad del historial.

---

### Fusión remota con Pull Request

**Comentarios de la captura:**

* **Estrategia:** La plataforma de GitHub por defecto suele usar una estrategia de **merge commit (--no-ff)** para las Pull Requests. Sin embargo, también ofrece opciones para hacer Squash and Merge (para un commit limpio) o Rebase and Merge (para un historial lineal). La captura muestra que se usó una de estas estrategias, lo que resulta en un commit de fusión o en un único commit de squash.
* **Historial local tras git pull:** Al hacer git pull después de la fusión en GitHub, el historial local se actualiza para reflejar el merge commit o el squash commit creado en la plataforma. Tu historial local se sincroniza con el historial del repositorio remoto. La apariencia de la rama de la Pull Request dependerá de la estrategia de fusión que se haya elegido en GitHub.Línea original desde feature-conflict
Línea definitiva desde main
