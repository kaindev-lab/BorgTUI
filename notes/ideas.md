
**Ideas para la proximas Versión:**

- **Multiples Repositorios:** Añadir un nuevo campo en las tareas para especificar el repositorio objetivo y así poder manejar multiples repositorios, que pueden por si mismos tener o no tener encriptación, ser o no ser locales. Esto potencia enormemente el sistema, lo hace más flexible y da la posibilidad de manejar multiples repositorios, tener o no tener encriptaciónd e los datos, segun la configuraciónd el repositorio objetivo y permite manejar repositorios tanto locales como externos.

- Usar un NIVEL de log = WARN cuando se detecta un PID huerfano en el LOCKFILE usado para idnetificar cuando hay un script orquetsador ya en proceso.

- La informaciónd e las valdiaciones inciales de los archivos como logs, state, config no es clara. Resuleven los problemas creando y configurando los archivos cuando no existen, pero no regflejan la ausencia d elos archivos en los logs claramente.

- Sería util añadir validaciones para un ID mal escrito en las configuraciones. IDs como [AB100] el sistema los omite, no los procesa, lo que no es un error, pero sería útil para el usuario validaciones y mensajes que le indiquen el error.

- Podría ser aconsejable validar las frecuencias de 0 en las tareas. Estas frecuencias hacen que el sistema haga el proceso de backups y limpieza (prune) cen cada ejecución del script orquetador. Aunque no es necesario quitarlo, podría ser útil avisar de esto en un log.

- El sistema de backups que depende de la ejecución con 'cron' no es preciso. Pueden haber retrasos por ejemplo si hay backups por realizar cada 5 minutos, pero el cron se activa cada hora, la tarea se trasa siempre 11 veces más que la frecuencia asiganda (5 mintos se hacen cada 55 minutos). Para no depender tanto de la configuración del usuario, podríamos convertir el sistema en un 'daemon' que una condigurado, siempre este corriendo en segundo plano y validando cuando es tiempo de ejecutarase.

- Si el sistema detecta si dentro de un directorio a respaldar hay algunas carpetas o archivos a los que no tiene acceso, debería de respaldar el directorio omitiendo los archivos y carpetas a los que tiene acceso y omitir los archivos y carpetas a los que no tiene acceso, pues de 1000 archivos no dbería por ejemplo omitir un backup por 1 solo archivo. Aunque habría que verificar como maneja BorgBackups estos casos, si hace un "todo o nada", o simplemente omite los archivos y directorios a los que no tiene acceso.
 
- La información sobre las tareas que no son validas no es clara en la TUI. La TUI conculta en los estados para mostrar información sobre la tarea, peor en el archivo de estados, solo estan los estados de las tareas validas, por ejemplo una tarea nueva con ritas invaldias, o una tarea vieja que se le cambio las rutas por unas no validas, no estará en el archivo de estados, por lo que las tareas no validas desde el incio o que dejan de ser valdias, en el panel de tareas del TUI no se muestran y para el usuario no será claro por que no se muestran, por lo que es necesario añadir validaciones en el panel del TUI que validen estos casos y a estas tareas invalidas les indique su invalidez, por ejemplo poniendo en el estado algo como: "NO-VALIDA"

- La limpieza (borg prune) no se esta ejecutando para ningun ID. Probablemente tenga relación con el prefix que uso en el 'prune', probablemente por como esta escrito no encuentra coincidencias. Debería añdir valdiaciones extra, no solo validar si hay errores o no durante el prune, sino validar el numero de backups antes y después del prune.

- Añadir la opción de especificar el tipo de compresión para los backups en vez de solo tener un valor predeterminado que se activa o no (True|False).

- Más detallles del repositoprio, por ejemplo usando sus opciones como: `borg info --json repo | jq`, `borg list --long repo`, `borg list repo --format`. También sería adecuado mostrar la compresión del abckups, sea en el listado de los backups, o sea en el nombre con el que se guarda.

- Más detalles en el estado de las tareas, por ejemplo agregar una columna de notas, donde indicar cosas como que se omitieron politicas o rutas que no eran validas. Esto es muy importante para poder saber. También sería util ver los demás datos como las politicas y la frecuencia si esto no hace la caja de infromación demasiado grande. 

- Me gustaría poder seleccionar una tarea especifica y expandir la información sobre esta.

- En la TUI resultaría muy util un submenu, o formulario intemedio antes de lanzar los logs con lnav o tail, donde se filtren los logs que se deseen ver o se indique que opciones ejecutar o que consultas realizar para ver datos especificos como logs de error, de infromación, de advertencia, en vez de ver todos los logs.

- En el panel de la TUI convendría tener en el resumen un conteo de NO-PASAN o algo así, junto a los demás estados. Las tareas sin ubicaciones validas que respladar no pasan a los estados, por lo que no son contadas. Si hay 7 tareas y 1 invalida, en el resumen no debería aparecer que hay 6 tareas, sino 7 tareas y especificar que una no se cuenta, no es valida, no pasa, no se le realiza backups.

- Sería util tener la opción de recarga las ventanas del TUI, para poder cargar nuevos valores, por ejemplo de los estados (PENDIENTE, REALIZADO, EN-PROCESO, TAREAS TOTALES, TAREAS QUE NO PASAN) o por ejemplo en la ventada de estados que cambian con frecuencia.

- Sería util usar una opción que permita ver los estados de las tareas en tiempo real, por ejemplo tener la ventana de estados dentro de un bucle que consulte continuamente los cambios en los estados y en el caso de los datos del tiempo faltante y hora actual (formato UNIX) que calcule los nuevos tiempos que el script orqustador aún no haya actualizado y meustre estos estos tiemos calculados que cuando el script orquestador se activa deberá calcular y quedaran sincronizados en ese momento. Los demás datos como los estados, frecuencia, deberá de estarlos consultando en el archivo. Solo los datos del tiempo se calculan desde el TUI, pues estos no dependen del script orquestador, sino de la hora actual y los valores de frecuencia y uñtimo exito leidos desde el rachivo de estados.

- Crear test de automaticos para testear el sistema rapidamente.

- Dejar claro la licencia bajo la que se usará el proyecto, por ejemplo una licencia MIT ("usa el proyecto como gustes, pero respeta el copyrigth")

- Para que el sistema no se llegue a relentizar cuando se realizan los backups, estos deberían de ejecutarse con el comando nice y con una prioridad baja que no use recursos que el sistema esta suando para otras cosas, así eso implique que los abckups tarden más en realizarse.

- En el sistema se sigue usando mucho la obtención de datos con 'grep A' que es poco segura, flexible y eficiente, asíq ue sería mejor remplazarla por funciones más robustas como las definidas al incio del orquestador.

- El problema de awk vs cut con los espacios

    Observación: En sync_state_times extraes valores usando awk '{print $3}'.

    Peligro: Si por alguna razón una ruta o un valor tiene espacios (ejemplo: compresion = zstd 6), awk solo agarrará "zstd".

    Regla de oro: Usa siempre cut -d'=' -f2- | xargs para todos los campos. Es más lento por milisegundos, pero es 100% seguro contra espacios.

- El sistema no esta detectando cuando hay cambios en la ifnromación de un grupo por respaldar, por ejemplo si añado una carpeta nueva a la lista de rutas. Debería de hacer una validación extra que compare la información de los grupos del archivo de configuraciones y el archivo de estados y si detecta un cambio, que actualice el archivo de estado y lo pase a estado PENDIENTE para que el sistema procese el nuevo cambio.


**Mejoras TUI**

- La opción de actualizar la ventana de estados, podría no solo consultar el archivo de estados, sino realizar calculos propios basado en la hora actual, pues el archivo de estados se actualiza solo cuando se ejecuta el script orquestador.

Para simplificar el uso a perfiles no técnicos, se planea implementar:

- **Selección Visual:** Integración con gestores de archivos para añadir rutas de respaldo mediante selección directa.

- **Formularios Guiados:** Creación y edición de tareas (IDs) mediante asistentes que eliminen la necesidad de editar archivos de texto manualmente.


**Mejoras para el test:**

- Validar el manejo ante la falta de dependencias NO criticas, solos se valido depencias criticas como 'borg'.

- Validar como actua el sistema cuando las politicas de retención tienen algunas politicas validas y otras invalidas, solo valida cuando que hace el sistema cuando no hay politicas validas.

- Validar como actua el sistema con falta de permisos de acceso sobre los archivos y directorios dentro de un directorio a respaldar definidos en las rutas configuradas por respaldar.

- Validar que el sistema verifique tener permiso de lectura para las rutas a respaldar y todos los archivos y carpetas que este contiene.

- Añadir pruebas sobre el manejo del repositorio cunaod en las ocnfiguraciones no existe, no es accesible o no esta creado.

- Validar el comportamiento del sistema cuando un archivo, un directorio, el contenido de un directorio de una tarea no es accesible (permisos nombre mal escrito).


