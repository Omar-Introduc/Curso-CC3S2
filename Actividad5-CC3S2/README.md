Ejecuta `make help` y guarda la salida para análisis. Luego inspecciona `.DEFAULT_GOAL` y `.PHONY` dentro del Makefile. Comandos:
# Parte 1: Construir -Makefile y Bash desde 0 
## EJERCICIOS 1
#### 1. Ejecuta make help y guarda la salida para análisis. Luego inspecciona .DEFAULT_GOAL y .PHONY dentro del Makefile. Comandos:
```
omar@DESKTOP-2GPELEH:~/Curso-CC3S2/Actividad5-CC3S2$ make help
  all           Construir, testear y empaquetar el proyecto
  build         Generar out/hello.txt
  clean         Limpiar archivos generados
  help          Mostrar esta ayuda
```
La automatización del Makefile es fundamental para la robustez del proyecto. La función principal de **`make help`** es generar un **reporte dinámico y específico** del funcionamiento de cada *target*, junto con su descripción. Esto transforma el `Makefile` en una herramienta auto-documentada. La directiva **`DEFAULT_GOAL := help`** especifica el objetivo por defecto, lo que garantiza que, al ejecutar `make` sin un *target* explícito, el sistema ejecute `help` en lugar del primer *target* que encuentre (que podría ser `build` o `run`). Finalmente, la directiva **`.PHONY`** se utiliza para declarar que un *target* no representa un archivo físico. Esto es crucial porque asegura que los *targets* como `clean` o `test` se ejecuten siempre como una **acción**, incluso si existe un archivo con ese nombre que, de otro modo, impediría que el comando se ejecutara.

#### 2. Comprueba la generación e idempotencia de build. Limpia salidas previas, ejecuta build, verifica el contenido y repite build para constatar que no rehace nada si no cambió la fuente. Comandos:

El primer comando elimina cualquier salida previa para asegurar que la construcción comienza desde cero.  
El primer `make build` genera los archivos de salida (por ejemplo, `out/hello.txt`).  
El segundo `make build` no rehace nada porque los archivos de salida ya existen y las fuentes no han cambiado. Esto demuestra la **idempotencia**: `make` solo reconstruye si detecta cambios en las dependencias.  
Si el archivo fuente no se modifica, la segunda ejecución de `make build` simplemente informa que todo está actualizado y no ejecuta el script de Python nuevamente.

#### 3. Fuerza un fallo controlado para observar el modo estricto del shell y .DELETE_ON_ERROR. Sobrescribe PYTHON con un intérprete inexistente y verifica que no quede artefacto corrupto. Comandos:

El uso de las opciones `-e -u -o pipefail` en el shell del Makefile garantiza que cualquier error en los comandos, variables no definidas o fallos en una tubería provocan la detención inmediata de la ejecución. Esto evita que se creen archivos incompletos o corruptos. Además, la directiva `.DELETE_ON_ERROR:` asegura que si una regla falla durante la construcción (por ejemplo, al usar un intérprete Python inexistente), cualquier archivo de salida generado parcialmente se elimina automáticamente. Así, el sistema nunca deja artefactos inconsistentes en el directorio de salida, manteniendo la integridad del proyecto y facilitando la depuración de errores.

#### 4. Realiza un "ensayo" (dry-run) y una depuración detallada para observar el razonamiento de Make al decidir si rehacer o no. Comandos:

El comando `make -n build` muestra los pasos que Make ejecutaría para construir el objetivo, pero sin realizar ninguna acción real
El comando `make -d build` activa el modo depuración, mostrando cómo Make analiza cada objetivo y sus dependencias.  
En el log, el fragmento "Considerando target file 'out/hello.txt'" indica que Make evalúa si debe generar ese archivo.  
Luego, "File 'out/hello.txt' does not exist" muestra que el archivo aún no está presente, por lo que Make decide que debe crearlo ("Must remake target 'out/hello.txt'").  
Make verifica también las dependencias, como `src/hello.py`, y si no requieren actualización, procede a ejecutar el comando de construcción.  
Finalmente, el log confirma que el archivo se ha generado exitosamente ("Successfully remade target file 'out/hello.txt'").  
Este proceso evidencia cómo Make decide rehacer objetivos solo si faltan o están desactualizados, optimizando la reconstrucción del proyecto.

#### 5. Demuestra la incrementalidad con marcas de tiempo. Primero toca la fuente y luego el target para comparar comportamientos. Comandos:

Cuando se ejecuta `touch src/hello.py`, se actualiza la marca de tiempo del archivo fuente, lo que hace que Make detecte que la dependencia ha cambiado y, por tanto, rehaga el archivo de salida (`out/hello.txt`). En cambio, al ejecutar `touch out/hello.txt`, solo se actualiza la marca de tiempo del archivo objetivo, pero no de sus dependencias. Make compara las fechas y, al ver que el objetivo no es más antiguo que la fuente, no realiza ninguna acción adicional. Esto demuestra la incrementalidad: solo se rehace el trabajo si las dependencias han cambiado, logrando evitar tareas innecesarias.

#### 6. Ejecuta verificación de estilo/formato manual (sin objetivos lint/tools). Si las herramientas están instaladas, muestra sus diagnósticos; si no, deja evidencia de su ausencia. Comandos:

Las salidas muestran que ni `shellcheck` ni `shfmt` están instalados en el entorno, por lo que no se pudo realizar la verificación de estilo ni el formateo de los scripts. Estas herramientas son útiles para detectar errores comunes, advertencias y mejorar la legibilidad de los scripts de shell. Para instalarlas en Linux, se pueden usar los comandos `sudo apt install shellcheck` y `sudo apt install shfmt`. Una vez instaladas, sus diagnósticos ayudarán a mantener la calidad y consistencia del código de los scripts.

Ahora que `shellcheck` está instalado, la herramienta ha detectado el error SC2148 en `scripts/run_tests.sh`. Este error indica que el script no tiene una línea shebang (`#!/bin/bash` o similar) al inicio, por lo que `shellcheck` no puede saber qué shell debe analizar. Para corregirlo, simplemente añadimos una línea shebang al principio del script. 

#### 7. Construye un paquete reproducible de forma manual, fijando metadatos para que el hash no cambie entre corridas idénticas. Repite el empaquetado y compara hashes. Comandos:

Las opciones `--sort=name` y `--numeric-owner` en `tar` aseguran que los archivos se empaqueten siempre en el mismo orden y con los mismos identificadores de propietario, eliminando diferencias entre corridas. La opción `--mtime='@0'` fija la fecha de modificación de todos los archivos del paquete a la época Unix (1970-01-01), evitando variaciones por marcas de tiempo. Al comprimir con `gzip -n`, se omite la inclusión del nombre de archivo y la fecha en el encabezado del archivo comprimido, eliminando más fuentes de variabilidad. Así, el hash SHA256 del paquete generado es idéntico en ejecuciones repetidas, garantizando la reproducibilidad.
 Se verifica la invariabilidad con 
```
diff -u logs/sha256-1.txt logs/sha256-2.txt | tee logs/sha256-diff.txt || true
```
#### 8. Reproduce el error clásico "missing separator" sin tocar el Makefile original. Crea una copia, cambia el TAB inicial de una receta por espacios, y confirma el error. Comandos:

Make exige que cada línea de receta bajo un target comience con un carácter TAB, no con espacios, para distinguir claramente entre reglas y comandos. Si se usan espacios, Make no reconoce la línea como una receta y lanza el error "missing separator". Este error es fácil de diagnosticar: basta revisar la línea indicada en el mensaje y verificar que el inicio sea un TAB.

## EJERCICIOS 2

#### 1. Ejecuta ./scripts/run_tests.sh en un repositorio limpio. Observa las líneas "Demostrando pipefail": primero sin y luego con pipefail. Verifica que imprime "Test pasó" y termina exitosamente con código 0 (echo $?).
```
omar@DESKTOP-2GPELEH:~/Curso-CC3S2/Actividad5-CC3S2$ ./scripts/run_tests.sh
echo $?
Demostrando pipefail:
Sin pipefail: el pipe se considera exitoso (status 0).
Con pipefail: se detecta el fallo (status != 0).
Test pasó: Hello, World!
0
```

#### 2. Edita src/hello.py para que no imprima "Hello, World!". Ejecuta el script: verás "Test falló", moverá hello.py a hello.py.bak, y el trap lo restaurará. Confirma código 2 y ausencia de .bak.

```
omar@DESKTOP-2GPELEH:~/Curso-CC3S2/Actividad5-CC3S2$ ./scripts/run_tests.sh
echo $?
Demostrando pipefail:
Sin pipefail: el pipe se considera exitoso (status 0).
Con pipefail: se detecta el fallo (status != 0).
Test falló: salida inesperada
2
```

#### 3. Ejecuta bash -x scripts/run_tests.sh. Revisa el trace: expansión de tmp y PY, llamadas a funciones, here-doc y tuberías. Observa el trap armado al inicio y ejecutándose al final; estado 0.

```
omar@DESKTOP-2GPELEH:~/Curso-CC3S2/Actividad5-CC3S2$ bash -x scripts/run_tests.sh
+ set -euo pipefail
+ IFS='
        '
+ umask 027
+ set -o noclobber
+ PY=python3
+ SRC_DIR=src
++ mktemp
+ tmp=/tmp/tmp.Rx5lSgUAet
+ trap 'cleanup $?' EXIT INT TERM
+ echo 'Demostrando pipefail:'
Demostrando pipefail:
+ set +o pipefail
+ false
+ true
+ echo 'Sin pipefail: el pipe se considera exitoso (status 0).'
Sin pipefail: el pipe se considera exitoso (status 0).
+ set -o pipefail
+ false
+ true
+ echo 'Con pipefail: se detecta el fallo (status != 0).'
Con pipefail: se detecta el fallo (status != 0).
+ cat
+ check_deps
+ deps=('python3' 'grep')
+ local -a deps
+ for dep in "${deps[@]}"
+ command -v python3
+ for dep in "${deps[@]}"
+ command -v grep
+ run_tests src/hello.py
+ local script=src/hello.py
+ local output
++ python3 src/hello.py
+ output='Hello, World!'
+ echo 'Hello, World!'
+ grep -Fq 'Hello, World!'
+ echo 'Test pasó: Hello, World!'
Test pasó: Hello, World!
+ cleanup 0
+ rc=0
+ rm -f /tmp/tmp.Rx5lSgUAet
+ '[' -f src/hello.py.bak ']'
+ exit 0
```

#### 4. Sustituye output=$("$PY" "$script") por ("$PY" "$script"). Ejecuta script. output queda indefinida; con set -u, al referenciarla en echo aborta antes de grep. El trap limpia y devuelve código distinto no-cero.

Si sustituyes `output=$("$PY" "$script")` por simplemente `("$PY" "$script")`, la variable `output` nunca se define. Con `set -u` activo, al intentar usar `echo "$output"` el script aborta inmediatamente por referenciar una variable indefinida. El `trap` ejecuta la función `cleanup`, eliminando el archivo temporal y restaurando el estado si es necesario. El script termina con un código de error distinto de cero, indicando el fallo por variable no inicializada.
```
+ cleanup 127
+ rc=127
+ rm -f /tmp/tmp.JSa0hly1OD
```

# Parte 2: Leer - Analizar un repositorio completo

#### 1. Ejecuta make -n all para un dry-run que muestre comandos sin ejecutarlos; identifica expansiones $@ y $<, el orden de objetivos y cómo all encadena tools, lint, build, test, package.

El comando `make -n all` realiza un dry-run, mostrando los comandos que Make ejecutaría para construir el objetivo `all`, pero sin ejecutarlos realmente.
```
omar@DESKTOP-2GPELEH:~/Curso-CC3S2/Actividad5-CC3S2$ make -n all
command -v python3 >/dev/null || { echo "Falta python3"; exit 1; }
command -v shellcheck >/dev/null || { echo "Falta shellcheck"; exit 1; }
command -v shfmt >/dev/null || { echo "Falta shfmt"; exit 1; }
command -v grep >/dev/null || { echo "Falta grep"; exit 1; }
command -v awk >/dev/null || { echo "Falta awk"; exit 1; }
command -v tar >/dev/null || { echo "Falta tar"; exit 1; }
tar --version 2>/dev/null | grep -q 'GNU tar' || { echo "Se requiere GNU tar"; exit 1; }
command -v sha256sum >/dev/null || { echo "Falta sha256sum"; exit 1; }
shellcheck scripts/run_tests.sh
shfmt -d scripts/run_tests.sh
command -v ruff >/dev/null 2>&1 && ruff check src || echo "ruff no instalado; omitiendo lint Python"
mkdir -p out
python3 src/hello.py > out/hello.txt
scripts/run_tests.sh
python3 -m unittest discover -s tests -v
mkdir -p dist
tar --sort=name --owner=0 --group=0 --numeric-owner --mtime='UTC 1970-01-01' -czf dist/app.tar.gz -C out hello.txt
```
 En el proceso, se observa cómo `all` encadena los objetivos `tools`, `lint`, `build`, `test` y `package` en ese orden, siguiendo la dependencia declarada en el Makefile. Las expansiones `$@` representan el nombre del objetivo actual (por ejemplo, `out/hello.txt`), mientras que `$<` corresponde al primer prerequisito (por ejemplo, `src/hello.py`). Así, en la receta de build, `mkdir -p $(@D)` crea el directorio de destino y `$(PYTHON) $< > $@`, ejecuta el script fuente (src/hello.py) y guarda la salida en el archivo objetivo (out/hello.txt)

#### 2. Ejecuta make -d build y localiza líneas "Considerando el archivo objetivo" y "Debe deshacerse", explica por qué recompila o no out/hello.txt usando marcas de tiempo y cómo mkdir -p $(@D) garantiza el directorio.

Al ejecutar `make -d build`, Make muestra en detalle cómo decide si recompilar `out/hello.txt`. Las líneas "Considerando el archivo objetivo 'out/hello.txt'"
```
   File 'out/hello.txt' does not exist.
    Considering target file 'src/hello.py'.
     Looking for an implicit rule for 'src/hello.py'.
     No implicit rule found for 'src/hello.py'.
     Finished prerequisites of target file 'src/hello.py'.
    No need to remake target 'src/hello.py'.
   Finished prerequisites of target file 'out/hello.txt'.
```
 indican que Make está evaluando ese archivo. Si la marca de tiempo de `src/hello.py` (la dependencia) es más reciente que la de `out/hello.txt`, o si el archivo de salida no existe, Make muestra "Debe deshacerse" y recompila el objetivo. Si no hay cambios, Make informa que no es necesario rehacerlo. El comando `mkdir -p $(@D)` en la receta garantiza que el directorio de destino (`out/`) exista antes de crear el archivo, evitando errores si el directorio no está presente.

#### 3. Fuerza un entorno con BSD tar en PATH y corre make tools; comprueba el fallo con "Se requiere GNU tar" y razona por qué --sort, --numeric-owner y --mtime son imprescindibles para reproducibilidad determinista.

Si se fuerza el uso de BSD tar en el PATH y se ejecuta `make tools`, el Makefile falla con el mensaje "Se requiere GNU tar". Esto ocurre porque BSD tar no soporta las opciones `--sort=name`, `--numeric-owner` y `--mtime`, que son exclusivas de GNU tar. Estas opciones son imprescindibles para reproducibilidad determinista: `--sort=name` ordena los archivos en el paquete, `--numeric-owner` fija los identificadores de propietario y grupo, y `--mtime` establece una marca de tiempo fija para todos los archivos.

#### 4. Ejecuta make verify-repro; observa que genera dos artefactos y compara SHA256_1 y SHA256_2. Si difieren, hipótesis: zona horaria, versión de tar, contenido no determinista o variables de entorno no fijadas.
```

omar@DESKTOP-2GPELEH:~/Curso-CC3S2/Actividad5-CC3S2$ make verify-repro
SHA256_1=ad917bfc2c042f07c8de4ef70fcbff91a49977d589de88932da474592ba1c545
SHA256_2=ad917bfc2c042f07c8de4ef70fcbff91a49977d589de88932da474592ba1c545
OK: reproducible
```
Si los hashes SHA256_1 y SHA256_2 fueran diferentes, indicaría que la construcción no es determinista.

#### 5. Corre make clean && make all, cronometrando; repite make all sin cambios y compara tiempos y logs. Explica por qué la segunda es más rápida gracias a timestamps y relaciones de dependencia bien declaradas.
```
make clean && make all
rm -rf out dist
shellcheck scripts/run_tests.sh
shfmt -d scripts/run_tests.sh
ruff no instalado; omitiendo lint Python
mkdir -p out
python3 src/hello.py > out/hello.txt
scripts/run_tests.sh
Demostrando pipefail:
Sin pipefail: el pipe se considera exitoso (status 0).
Con pipefail: se detecta el fallo (status != 0).
Test pasó: Hello, World!
python3 -m unittest discover -s tests -v
test_greet (test_hello.TestGreet.test_greet) ... ok

----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
mkdir -p dist
tar --sort=name --owner=0 --group=0 --numeric-owner --mtime='UTC 1970-01-01' -czf dist/app.tar.gz -C out hello.txt

----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```
Aunque para este caso el tiempo es igual, se fuerza una reconstrucción completa del proyecto desde cero en al primera a segunda ejecución de time make all es casi instantánea. Esto se debe a que make comprueba las marcas de tiempo (timestamps) de los archivos. Al ver que los objetivos ya existen y son más recientes que sus dependencias, make determina que no hay nada que reconstruir y omite los comandos de build y package.
#### 6. Ejecuta PYTHON=python3.12 make test (si existe). Verifica con python3.12 --version y mensajes que el override funciona gracias a ?= y a PY="${PYTHON:-python3}" en el script; confirma que el artefacto final no cambia respecto al intérprete por defecto.
Al ejecutar PYTHON=python3.12 make test, se sobreescribe la variable PYTHON solo para esa corrida.
El Makefile permite esto gracias al operador ?=, que respeta la variable externa en lugar de su valor por defecto. A su vez, los scripts de shell usan la sintaxis PY="${PYTHON:-python3}" para heredar y utilizar esta misma versión de Python.

#### 7. Ejecuta make test; describe cómo primero corre scripts/run_tests.sh y luego python -m unittest. Determina el comportamiento si el script de pruebas falla y cómo se propaga el error a la tarea global.

#### 8. Ejecuta touch src/hello.py y luego make all; identifica qué objetivos se rehacen (build, test, package) y relaciona el comportamiento con el timestamp actualizado y la cadena de dependencias especificada.

#### 9. Ejecuta make -j4 all y observa ejecución concurrente de objetivos independientes; confirma resultados idénticos a modo secuencial y explica cómo mkdir -p $(@D) y dependencias precisas evitan condiciones de carrera.

#### 10. Ejecuta make lint y luego make format; interpreta diagnósticos de shellcheck, revisa diferencias aplicadas por shfmt y, si está disponible, considera la salida de ruff sobre src/ antes de empaquetar.
