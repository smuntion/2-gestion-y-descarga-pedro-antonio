# Sesión II - Gestión avanzada y descarga de datos

Herramientas computacionales para bioinformática: UNIX, expresiones regulares y shell script

======================================

* * *

Antes de empezar, clona este repositorio a tu directorio personal, luego vete a ese directorio y ejecuta:

`cp -r /home/gtfs .` 


### 1. Más tareas en UNIX
#### 1.1 Copia, mueve y borra

En la anterior sesión, vimos algunos comandos útiles para navegador por el sistema de ficheros de UNIX y realizar algunas tareas básicas. Vamos a ver algunos comandos más.

Imagina que quieres hacer un copia de un fichero de anotaciones genómicas ([GTF](https://bioinformaticaupf.crg.eu/2005/projectes05/3.8/gtf.html)) para poder modificarlo de manera segura sin miedo a "cargártelo". Muy sencillo, puedes usar la orden `cp` (copy) para ello. 

```
abenito@cpg3:~/sesion-ii/gtfs$ cp Drosophila_melanogaster.BDGP6.28.102.gtf Drosophila_melanogaster.BDGP6.28.102-copy.gtf
abenito@cpg3:~/sesion-ii/gtfs$ ls -l
total 322144
-rw-r--r-- 1 abenito abenito 140785169 ene  4 10:40 Drosophila_melanogaster.BDGP6.28.102-copy.gtf
-rw-r--r-- 1 abenito abenito 140785169 ene  4 10:19 Drosophila_melanogaster.BDGP6.28.102.gtf
-rw-r--r-- 1 abenito abenito  48295448 ene  4 10:18 Homo_sapiens.GRCh38.102.gtf.gz
```

Como ves, se ha creado una copia de igual tamaño (140785169 bytes, por cierto, ¿cuántos megas es esto? PistaUtiliza `ls -lh` para saberlo) y con los mismos permisos en el mismo directorio, aunque podríamos usar una ruta absoluta o relativa para copiarlo en otro sitio. 

Si resultase que no estamos satisfechos con el nombre que hemos elegido, podríamos usar la orden `mv` (move) para cambiarlo de nombre (y sitio). **Las tareas de renombrar y mover en UNIX son idénticas**. 

```
abenito@cpg3:~/sesion-ii/gtfs$ mv Drosophila_melanogaster.BDGP6.28.102-copy.gtf Drosophila_melanogaster.BDGP6.28.102-bueno.gtf
abenito@cpg3:~/sesion-ii/gtfs$ ls -lh
total 315M
-rw-r--r-- 1 abenito abenito 135M ene  4 10:40 Drosophila_melanogaster.BDGP6.28.102-bueno.gtf
-rw-r--r-- 1 abenito abenito 135M ene  4 10:19 Drosophila_melanogaster.BDGP6.28.102.gtf
-rw-r--r-- 1 abenito abenito  47M ene  4 10:18 Homo_sapiens.GRCh38.102.gtf.gz
```

**Cuidado:** En UNIX, por defecto, no hay comprobación de sobreescritura. Esto es, si mueves o copias un fichero y ya existe otro con el mismo nombre, el fichero se sobreescribe y el contenido antiguo se pierde para siempre, como ["lágrimas en la lluvia"](https://youtu.be/qKpMFMiRkBI). 


Ahora nos damos cuenta de que queremos copiar un directorio completo. ¿Cómo lo haríamos? Exactamente igual, pero añadiendo el flag `-r` (recursive), para que copie el directorio y **recursivamente**, todos los contenidos que hay en él. 

```
abenito@cpg3:~/sesion-ii$ ls -lh gtfs-2/
total 315M
-rw-r--r-- 1 abenito abenito 135M ene  4 10:56 Drosophila_melanogaster.BDGP6.28.102-bueno.gtf
-rw-r--r-- 1 abenito abenito 135M ene  4 10:56 Drosophila_melanogaster.BDGP6.28.102.gtf
-rw-r--r-- 1 abenito abenito  47M ene  4 10:56 Homo_sapiens.GRCh38.102.gtf.gz
```
Si prestas atención, `ls -lh` además de convertir el tamaño de los ficheros individuales a un formato legible por humanos (de ahí la "`h`"), nos da el tamaño total de los elementos en el directorio (en este caso 315M). Existe otra orden, `du -h`, que nos permite obtener información sobre el tamaño de los contenidos de un directorio (en este caso en el directorio actual, `.`). Prueba a lanzar este comando para ver cuánto ocupa tu directorio home personal.

```
abenito@cpg3:~$ cd
abenito@cpg3:~$ du -h
<muchas cosas>
<muchas cosas>
<muchas cosas>
```

Veras que aparecen mucha información. Esto es debido a que por defecto, `du` va a mostrar, recursivamente, información para todos los directorios que cuelguen del actual. Si no queremos esto (que será lo común), podremos especificar la profundidad hasta la que `du` debería mirar en el árbol.

```
abenito@cpg3:~$ du -h --max-depth=0
8,5G	.
```

```
abenito@cpg3:~$ du -h --max-depth=1
7,7M	./materiales-informatica-i
12K	./.ssh
4,0K	./PRUEBA
8,0K	./.nano
259M	./python-bio
20K	./certs
360K	./informatica-i-old
348K	./.ipynb_checkpoints
8,6M	./.ipython
84M	./informatica-i
83M	./.cargo
52K	./.jupyter
5,2M	./visualization-curriculum
8,0K	./dir
8,0K	./.config
328M	./.local
70M	./materiales-bio-python
32K	./ps1
56K	./cpg3.der.usal.es.ssl
176K	./sorteo
1,2M	./epub2txt2
380K	./notebooks
630M	./sesion-ii
13M	./genomes
153M	./.cache
8,5G	.
```

Mucho mejor! Ahora podemos saber qué está ocupando espacio en el disco y podremos borrarlo llegado el caso. 
  

Finalmente (hoy estamos un poco indecis@s, la verdad), abortamos la tarea de manipulación del fichero .gtf, por el motivo que sea. Como somos reticentes a ocupar espacio en un servidor compartido innecesariamente, borramos todo lo que hemos creado con la orden `rm`. 

Empezamos por alguno de los ficheros copiados en el directorio `gtf-2`: 

```
abenito@cpg3:~/sesion-ii$ rm gtfs-2/Drosophila_melanogaster.BDGP6.28.102-bueno.gtf
abenito@cpg3:~/sesion-ii$ ls gtfs-2/
Drosophila_melanogaster.BDGP6.28.102.gtf  Homo_sapiens.GRCh38.102.gtf.gz
```

Borrado! Perfecto. Pero también existe la orden `rmdir`, para eliminar un directorio...

```
abenito@cpg3:~/sesion-ii$ rmdir gtfs-2/
rmdir: fallo al borrar 'gtfs-2/': El directorio no está vacío
```

... pero no nos deja! Claro, es que `rmdir` es sólo para directorios vacíos, lo podemos ver con `man rmdir`:

```
abenito@cpg3:~/sesion-ii$ man rmdir
RMDIR(1)                                                                                                                                                                                      RMDIR(1)

NOMBRE
       rmdir - borra directorios vacíos
...
```
Entonces, estamos avocad@s a tener que vaciar los directorios manualmente antes de eliminarlos? Menudo rollo... 
Pues no! Podemos utilizar también el flag para eliminar recursivamente con `rm`: 

```
abenito@cpg3:~/sesion-ii$ rm -r gtfs-2/
abenito@cpg3:~/sesion-ii$ ls -l
total 4
drwxr-xr-x 2 abenito abenito 4096 ene  4 10:52 gtfs
```

Muy conveniente! Pero cuidado con `rm -r`, ya que elimina el directorio y **absolutamente todo** lo que haya dentro, sin dejar rastro. Así que piensalo dos veces antes de usarlo...

Por cierto, la shell también nos permite crear _alias_ para órdenes con argumentos. Imagina que la orden `ls -lh` te encanta y la usas muy a menudo. Podrías hacer:

```
abenito@cpg3:~$ alias lista="ls -lh"
abenito@cpg3:~/sesion-ii$ lista
total 4,0K
drwxr-xr-x 2 abenito abenito 4,0K ene  4 10:52 gtfs
```

Y `lista` produciría el resultado esperado. Oye, te has fijado? `ls -lh` reporta el total de la carpeta, que dice que es 4 kilobyes (4K). Pero si hacemos lo mismo dentro de la carpeta `gtfs` (que está incluida en la anterior):

```
abenito@cpg3:~/sesion-ii/gtfs$ lista
total 315M
-rw-r--r-- 1 abenito abenito 135M ene  4 10:40 Drosophila_melanogaster.BDGP6.28.102-bueno.gtf
-rw-r--r-- 1 abenito abenito 135M ene  4 10:19 Drosophila_melanogaster.BDGP6.28.102.gtf
-rw-r--r-- 1 abenito abenito  47M ene  4 10:18 Homo_sapiens.GRCh38.102.gtf.gz
```

Nos da 315M. ¿No debería habernos dado 315M + 4K en el primer caso? 

**¿Sabes por qué ocurre esto?** Te lo dejo de deberes 


#### 1.2 Enlaces, duros y blandos.
En UNIX, los ficheros se organizan de manera que los datos y el nombre del fichero están separados. En realidad, lo único que entiende el sistema de ficheros son los [inodos](https://es.wikipedia.org/wiki/Inodo), que son referencias a datos en un espacio del disco. El inodo de cualquier fichero (o directorio, ya que los directorios son, en UNIX, un tipo de fichero especial) se puede consultar con `ls -i`: 

```
abenito@cpg3:~/sesion-ii/gtfs$ ls -li
total 322144
150209046 -rw-r--r-- 1 abenito abenito 140785169 ene  4 10:40 Drosophila_melanogaster.BDGP6.28.102-bueno.gtf
150209045 -rw-r--r-- 1 abenito abenito 140785169 ene  4 10:19 Drosophila_melanogaster.BDGP6.28.102.gtf
150209043 -rw-r--r-- 1 abenito abenito  48295448 ene  4 10:18 Homo_sapiens.GRCh38.102.gtf.gz
```
Como se puede ver, el .gtf de la _Drosophila Melanogaster_ y la copia que hicimos antes, tienen inodos contiguos. Esto quiere decir que son ficheros diferentes (en total ocupan 270M, 135+135, en disco). Por lo tanto, si desease utilizar el mismo .gtf desde otro sitio en el disco duro (por ejemplo, desde una carpeta "hermana" llamada "pruebas"), lo primero que se me podría ocurrir es mover esa copia a dicha carpeta. Pero esto tiene varios problemas:

1. El primero, y más obvio, es que estaré malgastando espacio en disco. 
2. El segundo, que está relacionado con el anterior, es que si hago 20 pruebas, con este método gastare 20*135M = 2700M = 2.64GB! 
3. El tercero, no menos importante, es que si modifico el fichero, tengo que volver a copiarlo a todas las carpetas "prueba" (de las cuales puede que no me acuerde), lo cual me puede llevar bastante tiempo. 

Para solucionar esta incómoda situación, existen en UNIX un tipo de ficheros especiales llamados __enlaces__, que son eso, referencias a ficheros, y se declaran con la orden `ln <fichero_a_enlazar> <enlace>`. El enlace por defecto es el enlace *duro*: vamos a crear uno. 

```
abenito@cpg3:~/sesion-ii$ mkdir pruebas-1
abenito@cpg3:~/sesion-ii$ mkdir pruebas-2
abenito@cpg3:~/sesion-ii$ ln gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf pruebas-1/
abenito@cpg3:~/sesion-ii$ ls -li pruebas-1/
total 137492
150209045 -rw-r--r-- 2 abenito abenito 140785169 ene  4 10:19 Drosophila_melanogaster.BDGP6.28.102.gtf
```

Como ves, se ha creado otro fichero idéntico al original en otra carpeta, pero con un tamaño mucho menor. Además, el inodo parece ser el mismo al del fichero original, y después de los permisos ahora aparece "2" en vez de "1". ¿Qué significará este número? 

También podemos crear un enlace *blando*, cuya funcionalidad es muy parecida al del enlace _duro_, pero funciona de manera diferente. En los enlaces duros, ninguna parte (enlace o enlazado) es más importante que la otra. Todas comparten los mismos datos. Sin embargo, en un enlace blando, la parte enlazada es _más importante_ que el enlace. Cuando se van a mirar los datos del enlace, el sistema operativo ve que está apuntando a la parte enlazada y nos da los datos de ésta. Crearemos enlaces _simbólicos o blandos_ usando el flag `-s` (soft) Ejemplo:

```
abenito@cpg3:~/sesion-ii$ ln -s gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf pruebas-2/
abenito@cpg3:~/sesion-ii$ ls -li pruebas-2/
total 0
150209051 lrwxrwxrwx 1 abenito abenito 45 ene  5 13:25 Drosophila_melanogaster.BDGP6.28.102.gtf -> gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf
```

Diferencias con el caso anterior: ahora el número de inodo ha cambiado, y antes de los permisos aparece la letra "l" (link), para indicar que se trata de un enlace. Además, el comando ls nos muestra la ruta al fichero enlazado pero...no ha actualizado el número "1" que aparece después de los permisos, cosa que sí ocurría con los enlaces duros! Para entender mejor el funcionamiento de los enlaces, realiza el siguiente ejercicio:

### Hacer ejercicio 1

Normalmente emplearemos enlaces blandos ya que nos permiten enlazar a directorios y enlazar entre sistemas de ficheros diferentes (con los enlaces duros no). 


### 2. Variables
#### 2.1. Variables de entorno

Como cualquier lenguaje de programación (la shell es programable en lenguaje BASH), también vamos a contar con variables para almacenar los datos que consideremos oportuno. A estas variables se las conoce como _variables de entorno_. Podemos definir cualquier variable siguiendo las siguientes reglas:

1. __El nombre de la variable puede contener__:
	1. Letras del alfabeto inglés (mayúsculas y minúsculas) 
	2. Números 
	3. Guión bajo `_`
2. __No puede comenzar por un número__
3. Las variables __almacenan únicamente caracteres__. Incluso si almacenamos números, estaremos guardando la conversión a caracter de dichos números.
3. En la shell tradicionalmente se __utilizan las mayúsculas__, para diferenciar las variables de los comandos.

Así que, como ejemplos de variable nombrada correctamente, tendremos `MI_NOMBRE`, `apellid0`, `__CIUDAD_4`, pero no `0nombre` o `apellido!`.

Para definir una variable emplearemos, como es habitual, el símbolo de igualdad `=` (sin espacios entre el nombre y el valor), así;

```
abenito@cpg3:~/sesion-ii$ NOMBRE=Juan
abenito@cpg3:~/sesion-ii$ APELLIDO=Lanas
```

Sin embargo, si tratamos de almacenar una cadena con espacios...

```
abenito@cpg3:~/sesion-ii$ NOMBRE_COMPLETO=Juan Lanas
-bash: Lanas: no se encontró la orden
```

...falla. Esto es debido a que la shell cree que la cadena termina en "Juan", ya que luego hay un espacio en blanco. Después, trata de ejecutar "Lanas" como si de una orden se tratase lo cual, evidentemente, falla porque no existe tal orden. Para evitar el error, debemos indicarle los límites de la cadena que deseamos almacenar usando el apóstrofo (en el teclado español se encuentra debajo del cierre de interrogación) o las comillas dobles, así:


```
abenito@cpg3:~/sesion-ii$ NOMBRE_COMPLETO='Juan Lanas'
```

Para acceder a los contenidos de una variable, usaremos el símbolo del dolar `$`, seguido del nombre de la variable:

```
abenito@cpg3:~/sesion-ii$ $NOMBRE
-bash: Juan: no se encontró la orden
```
Obtenemos un fallo también ahora, aunque es fácil adivinar el porqué. Antes de ejecutar nada, la shell hace las sustituciones de variables pertinentes, cambiando `$NOMBRE` por `Juan`. Luego evalúa la línea, tratando de ejecutar la orden `Juan`, que no se encuentra. 

Si queremos ver los contenidos de dicha variable, utilizaremos la orden `echo`: 

```
abenito@cpg3:~/sesion-ii$ echo $NOMBRE
Juan
abenito@cpg3:~/sesion-ii$ echo $NOMBRE_COMPLETO
Juan Lanas
```

Para evitar confusiones en algunos contextos, podemos rodear el nombre de la variable en llaves (`{` y `}`) para indicarle exactamente dónde acaba y termina un nombre de variable:

```
abenito@cpg3:~/sesion-ii$ echo ${NOMBRE}
Juan
```

Finalmente, para desdefinir variables usaremos `unset`. 

```
abenito@cpg3:~/sesion-ii$ unset NOMBRE_COMPLETO
abenito@cpg3:~/sesion-ii$ echo $NOMBRE_COMPLETO

```

En realidad, y a diferencia de lo que ocurre en otros lenguajes como Python, el apóstrofe o las comillas tienen diferente función en bash:

1. Todo lo que vaya entre apóstrofes, preserva el valor literal de cada carácter encontrado.
2. Todo lo que vaya entre comillas, preserva el valor literal de cada carácter, con la excepción de `$`, `` ` ``, `\`, y `!`. 

```
abenito@cpg3:~/sesion-ii$ FRUTA=manzana
abenito@cpg3:~/sesion-ii$ echo "$FRUTA"
manzana
abenito@cpg3:~/sesion-ii$ echo '$FRUTA'
$FRUTA
abenito@cpg3:~/sesion-ii$ echo "'$FRUTA'"
'manzana'
abenito@cpg3:~/sesion-ii$ echo '"$FRUTA"'
"$FRUTA"
```


**Intenta adivinar qué resultado dará el comando ``echo `ls` ``**

**Cierra la sesión y vuelve a conectarte. ¿Qué valor tienen las variables que habías definido (NOMBRE, APPELIDO, etc.)?**

#### 2.2. Variables de entorno del sistema

Algunas variables tienen significado especial y son asignadas por la shell u otros programas por defecto, aquí van algunas (prueba a ver qué contienen).

- HOSTNAME: almacena el nombre del ordenador
- HOSTTYPE: tipo de servidor (tipo de procesador)
- OLDPWD: último directorio en que hemos estado
- OSTYPE: tipo de sistema operativo
- PWD: ruta del directorio de trabajo
- RANDOM: cada vez que se lee, devuelve un número al azar de 0 a 32767
- SECONDS: nos dice cuántos segundos llevamos conectados
- HOME: ruta del directorio de conexión
- LANG y LC_...: usadas para establecer el locale (idioma por defecto)
- PATH: lista de directorios donde se buscan las órdenes y programas que teclea el usuario
- PSx: permite establecer los distintos indicadores (prompts)

Una de las más importantes es la variable `PATH`. Los programas que usamos en UNIX normalmente han sido escritos por otras personas y colocados en el disco duro para nuestro disfrute. ¿Pero dónde exactamente? ¿Cómo sabe la shell encontrar un comando cuando lo escribimos? La respuesta es en aquellos directorios especificados por la variable de entorno `PATH` (separados por `:`). Para saber dónde se encuentra un comando en concreto podemos usar la orden `which`. Probemos con `ls` y con `python`. 

```
abenito@cpg3:~/sesion-ii$ echo $PATH
/usr/local/ncbi/sra-tools/bin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/home/abenito/.local/bin
abenito@cpg3:~/sesion-ii$ which ls
/bin/ls
abenito@cpg3:~/sesion-ii$ which python3
/usr/bin/python3
```

Como ves, dependiendo del tipo de binario, es colocado en un sitio o en otro, como vimos en la sesión anterior. 
Para conocer todas las variables que están actualmente definidas, puedes emplear la orden `set`. 

### 3. Operando

La shell también permite ejecutar operaciones aritméticas. Algunos ejemplos:

```
abenito@cpg3:~/sesion-ii$ echo 1+1
1+1
abenito@cpg3:~/sesion-ii$ echo $((1+1))
2
abenito@cpg3:~/sesion-ii$ CUATRO=4
abenito@cpg3:~/sesion-ii$ echo $(($CUATRO * $CUATRO))
16
abenito@cpg3:~/sesion-ii$ echo $(($CUATRO ** 4))
256
```
**Ojo: Los operandos y el resultado están limitadas a números enteros.**


### 4. Expansión de argumentos y expresiones regulares

Hemos visto que normalmente, a los comandos les proporcionaremos un número variable de argumentos. Estos suelen ser rutas, relativas o absolutas, y se suelen colocar después del comando separados por espacios. Imagina ahora que hacemos más enlaces a nuestro fichero .gtf:

```
abenito@cpg3:~/sesion-ii$ ln -s gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf ..^C
abenito@cpg3:~/sesion-ii$ ln -s gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf pruebas-1/Drosophila_1.gtf
abenito@cpg3:~/sesion-ii$ ln -s gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf pruebas-1/Drosophila_2.gtf
abenito@cpg3:~/sesion-ii$ ln -s gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf pruebas-1/Drosophila_3.gtf
abenito@cpg3:~/sesion-ii$ ln -s gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf pruebas-1/Drosophila_4.gtf
abenito@cpg3:~/sesion-ii$ ln -s gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf pruebas-1/Drosophila_bueno.gtf
```

Si ahora queremos borrar todos los enlaces terminados en un número, tendríamos que hacer algo así como (no ejecutar):

```
abenito@cpg3:~/sesion-ii$ rm pruebas-1/Drosophila_1.gtf pruebas-1/Drosophila_2.gtf pruebas-1/Drosophila_3.gtf pruebas-1/Drosophila_4.gtf
```

Esto es muy ineficiente! Para automatizar esta pesada tarea vamos a introducir un concepto clave en este curso llamado _expresiones regulares_. 
Una expresión regular es una cadena de caracteres que contiene unos símbolos de significado especial que al interpretarse casarán con diferentes variaciones de texto. Estas expresiones regulares o bien casarán con un texto dado o no, de acuerdo a unas reglas. En el terminal, usaremos las siguientes expresiones regulares:

1. *: este carácter casa con cualquier número de caracteres de la cadena, incluso cero
2. ?: este carácter casa con justo un carácter de la cadena, aunque puede ser cualquiera
3. [abc]: esta expresión casa con cualquiera de los caracteres que pongamos dentro de los corchetes, en el ejemplo a, b o c. También se admiten rangos de caracteres, separados por un guion. Por ejemplo, [a-z] casa con cualquier letra minúscula del alfabeto.
4. [!abc] o [^abc]: igual que el caso anterior, pero al contrario. En el ejemplo, casa con cualquier carácter que no sea ni a, ni b, ni c.
5. Cualquier otro carácter: solamente casa con él mismo

Aquí van unos ejemplos:

```
abenito@cpg3:~/sesion-ii$ ls pruebas-1/Drosophila_*
pruebas-1/Drosophila_1.gtf  pruebas-1/Drosophila_3.gtf	pruebas-1/Drosophila_bueno.gtf
pruebas-1/Drosophila_2.gtf  pruebas-1/Drosophila_4.gtf	pruebas-1/Drosophila_melanogaster.BDGP6.28.102.gtf

abenito@cpg3:~/sesion-ii$ ls pruebas-1/Drosophila_[1-5]*
pruebas-1/Drosophila_1.gtf  pruebas-1/Drosophila_2.gtf	pruebas-1/Drosophila_3.gtf  pruebas-1/Drosophila_4.gtf

abenito@cpg3:~/sesion-ii$ ls pruebas-1/Drosophila_[1-4]*
pruebas-1/Drosophila_1.gtf  pruebas-1/Drosophila_2.gtf	pruebas-1/Drosophila_3.gtf  pruebas-1/Drosophila_4.gtf

abenito@cpg3:~/sesion-ii$ ls pruebas-1/Drosophila_[1-2]*
pruebas-1/Drosophila_1.gtf  pruebas-1/Drosophila_2.gtf

abenito@cpg3:~/sesion-ii$ ls pruebas-1/Drosophila_[3-4]*
pruebas-1/Drosophila_3.gtf  pruebas-1/Drosophila_4.gtf

abenito@cpg3:~/sesion-ii$ ls pruebas-1/Drosophila_?.gtf
pruebas-1/Drosophila_1.gtf  pruebas-1/Drosophila_2.gtf	pruebas-1/Drosophila_3.gtf  pruebas-1/Drosophila_4.gtf

abenito@cpg3:~/sesion-ii$ ls gtfs/D*
gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf

abenito@cpg3:~/sesion-ii$ ls gtfs/*.gtf
gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf

abenito@cpg3:~/sesion-ii$ ls pruebas-1/Drosophila_[!3-4]*
pruebas-1/Drosophila_1.gtf  pruebas-1/Drosophila_bueno.gtf
pruebas-1/Drosophila_2.gtf  pruebas-1/Drosophila_melanogaster.BDGP6.28.102.gtf
```


### 5. Algunos trucos

#### 5.1. Valor devuelto

Cualquier proceso en UNIX devuelve un valor mayor o igual a cero cuando acaba su ejecución (en caso de fallo, el valor suele venir especificado en la página del manual correspondiente). Esto nos permite controlar la ejecución de los mismos y actuar en consecuencia. Para ello, emplearemos la variable del sistema `$?`, que devuelve el valor devuelto por la última orden que se ejecutó: 

```
abenito@cpg3:~/sesion-ii$ ls gtfs/
Drosophila_melanogaster.BDGP6.28.102.gtf  Homo_sapiens.GRCh38.102.gtf.gz
abenito@cpg3:~/sesion-ii$ ls no_existo
ls: no se puede acceder a 'no_existo': No existe el fichero o el directorio
abenito@cpg3:~/sesion-ii$ echo $?
2
abenito@cpg3:~/sesion-ii$ echo $?
0

```

#### 5.2. Secuencias de órdenes en una misma línea

Usando los valores devueltos por nuestros comandos, podemos construir secuencias de órdenes en la misma línea. 
Por ejemplo, todo lo que se separe con punto y coma (`;`) se va a ejecutar de manera secuencial, sin importar el resultado del comando. A todos los efectos es como si los comandos hubiesen sido introducidos en líneas diferentes: 

```
abenito@cpg3:~/sesion-ii$ cd pruebas-1; ls H*; cd ..
ls: no se puede acceder a 'H*': No existe el fichero o el directorio
abenito@cpg3:~/sesion-ii$
```

Sin embargo, si separamos los comandos mediante `&&`, estaremos diciéndole a la shell que para continuar al siguiente comando, el valor devuelto por el anterior ha de ser cero:

```
abenito@cpg3:~/sesion-ii$ cd pruebas-1 && ls H* && cd ..
ls: no se puede acceder a 'H*': No existe el fichero o el directorio
abenito@cpg3:~/sesion-ii/pruebas-1$
```

En este caso, `ls H*` falla debido a que no existe ningún fichero que case con la expresión regular `H*`. Por lo tanto, el siguiente comando `cd`, que nos devolvería al directorio inicial no se ejecuta y por tanto nos quedamos dentro del subdirectorio `prueba-1`. 

Veremos formas más complejas de enlazar comandos (usando su entrada y salida estándar, `stdin` y `stdout`) en las próximas sesiones. 


#### 5.3. Expansión de llaves
Otra opción que nos ofrece la shell para expandir patrones consiste en usar llaves: Existen dos formas, una numérica y otra alfabética. En la numérica, vamos a indicar un rango y la shell completa los argumentos para cada uno de los valores de este rango. Por ejemplo, si quisiéramos referirnos a 10 copias diferentes que hemos hecho de un fichero .md con la memoria de nuestro proyecto haríamos:

```
abenito@cpg3:~/sesion-ii$ echo memoria-{1..10}.md
memoria-1.md memoria-2.md memoria-3.md memoria-4.md memoria-5.md memoria-6.md memoria-7.md memoria-8.md memoria-9.md memoria-10.md
```


En la segunda forma, la alfabética, podemos usar cadenas separadas por comas y la shell construye una palabra por cada una de las posibilidades:
```
abenito@cpg3:~/sesion-ii$ echo ante{cesor,puesto,rior,ojos,ayer}
antecesor antepuesto anterior anteojos anteayer
```


#### 5.4. touch
`touch` es un comando que nos permite cambiar la fecha de modificación de un fichero que se pase como argumento. Si el fichero que se pasa como argumento no existe, creará uno nuevo:

```
abenito@cpg3:~/sesion-ii$ touch recien_creado
abenito@cpg3:~/sesion-ii$ ls -l recien_creado
-rw-r--r-- 1 abenito abenito 0 ene  8 13:42 recien_creado
```

Combinando la expansión de llaves 

### 6. Buscando

Además de para expandir argumentos, podemos usar el poder de las expresiones regulares para realizar una de las tareas más comunes en un sistema operativo: buscar ficheros. Para esto, UNIX cuenta con la herramienta `find`. Por defecto, a `find` le pasaremos dos argumentos: Uno, el lugar donde tiene que buscar (una ruta). Dos, qué tiene que buscar (una expresión). 

Si omitimos el primer argumento, `find` simplemente mostrará (a diferencia de `ls`), de manera recursiva, todos los contenidos que cuelguen del directorio indicado, por ejemplo:

```
abenito@cpg3:~/sesion-ii$ find .
.
./pruebas-2
./pruebas-2/Drosophila_melanogaster.BDGP6.28.102.gtf
./pruebas-1
./pruebas-1/Drosophila_3.gtf
./pruebas-1/Drosophila_1.gtf
./pruebas-1/Drosophila_bueno.gtf
./pruebas-1/Drosophila_melanogaster.BDGP6.28.102.gtf
./pruebas-1/Drosophila_2.gtf
./pruebas-1/Drosophila_4.gtf
./gtfs
./gtfs/Drosophila_melanogaster.BDGP6.28.102.gtf
./gtfs/Homo_sapiens.GRCh38.102.gtf.gz
```

Es habitual buscar por nombre de fichero, pasando la opción `-name`, que admitirá también expresiones regulares como las vistas en el anterior apartado.
Un ejemplo: queremos encontrar todos los ficheros .gtf que empiecen por H: 

```
abenito@cpg3:~/sesion-ii$ find . -name "H*.gtf*"
./gtfs/Homo_sapiens.GRCh38.102.gtf.gz
```


### 2. Descargando datos

Supón que estas trabajando en un proyecto, y el equipo encargado de la secuenciación te dice que ya tienes los datos disponibles en el centro de descargas de tu instituto/universidad. Descargar esta cantidad de datos a través de un navegador web no es factible: los navegadores no están diseñados para descargar estos conjuntos de datos tan grandes. Además, necesitas descargar estos datos al servidor de trabajo, no a tu portátil. 

Como es lógico, abrirás una sesión ssh a tu servidor y descargarás allí los datos usando comandos del shell. Vamos a ver cómo se hace esto.

#### 2.1. Descargando datos
Dos programas de línea de comandos para descargar datos de la web son `wget` y `curl`. Dependiendo de tu sistema, puede que dichos programas no estén aún instalados: tendrás que instalarlos usando el manejador de paquetes (por ejemplo, `apt-get` en Debian/Ubuntu o Homebrew en MacOS). Aunque la funcionalidad básica de ambos es similar, cada uno tiene distintos puntos fuertes que vamos a mostrar en los siguientes apartados.

#### 2.2. wget
`wget` es útil para descargar rápidamente un fichero usando la línea de comandos: por ejemplo, el cromosoma humano 22 del assembly GRCh37:

```
abenito@cpg3:~/sesion-ii$ wget http://hgdownload.soe.ucsc.edu/goldenPath/hg19/chromosomes/chr22.fa.gz
--2021-02-08 15:06:21--  http://hgdownload.soe.ucsc.edu/goldenPath/hg19/chromosomes/chr22.fa.gz
Resolviendo hgdownload.soe.ucsc.edu (hgdownload.soe.ucsc.edu)... 128.114.119.163
Conectando con hgdownload.soe.ucsc.edu (hgdownload.soe.ucsc.edu)[128.114.119.163]:80... conectado.
Petición HTTP enviada, esperando respuesta... 200 OK
Longitud: 11327826 (11M) [application/x-gzip]
Grabando a: “chr22.fa.gz”

chr22.fa.gz                         100%[==================================================================>]  10,80M   951KB/s    in 14s

2021-02-08 15:06:36 (817 KB/s) - “chr22.fa.gz” guardado [11327826/11327826]
```

`wget` descarga este fichero al directorio actual mostrando un indicador de progreso. En este caso, se uso el protocolo http para descargar los datos. `wget` también permite emplear links FTP (`ftp://`), que es habitual encontrar en repositorio como UCSC. 

Como estos datos son públicos, no es necesario autenticarse en el servidor (aunque esto será, en muchos casos, lo habitual). Si quisiéramos hacerlo, usaremos las opciones `--user=` y `--ask-password`. 

Una de las ventajas de usar `wget` es que puede descargar datos recursivamente (con la opción `-r` o `--recursive`). En este caso, `wget` también descargará los enlaces contenidos en la página indicada: enlaces, imágenes, ficheros...
Por defecto, para evitar acabando descargar todo Internet, `wget` está limitado a una profundidad de 5 enlaces (puedes especificar esta profundidad con `--level` o `-l`). 

Las descargas recursivas puede ser útiles para descargar todos los ficheros de un cierto tipo desde una página (el backslash `\` parte la línea en dos):

```
wget --accept "*.gtf" --no-directories --recursive --no-parent \
 http://genomics.miuniversidad.es/laboratorio/anotaciones.html
```

En este ejemplo, descargamos todos los ficheros .gtf de la página web dada. Pero cuidado! esta orden puede ser bastante agresiva: si te despistas, podrías descargar muchísimos datos y ocupar todo el espacio en disco en tu servidor (para disgusto de la persona encargada de administrarlo). Además, puede usar un montón de recursos de red, dejando a tus compañer@s de laboratorio sin acceso al servidor. Finalmente, el host remoto puede bloquear tu IP si descargas _demasiados_ datos sin respetar sus límites. Por eso es importante que antes de ponerte a descargar datos a lo loco, busques en el sitio de destino algún tipo de anuncio de buenas prácticas y límites de descarga. Por ejemplo, puedes usar `--limit-rate` para poner un tope máximo de velocidad de descarga y evitar enfadar a algún _sysadmin_.

Para evitar este tipo de cosas, en el anterior ejemplo evitamos que `wget` descargue contenido de posibles enlaces a directorios más arriba en el árbol (otras cosas que pudieran estar colgando de `http://genomics.miuniversidad.es/laboratorio/`) con la opción `--no-parent`. Además, usamos `--accept` para marcar que sólo queremos archivos .gtf. `--no-directories` le dice que no replique la estructura de carpetas que encuentre en remoto, sino que descargue todo al mismo directorio actual. Finalmente, aunque no la hemos usado, la opción `-o` le indica a wget que descargue el fichero con un nombre en concreto y con `-c/"--continue"` podemos continuar una descarga parcial (que no terminó, bien porque hubo una caída de la red o porque la cancelamos nosotras mismas). 

#### 2.3. curl
`curl` tiene un propósito ligeramente diferente a `wget`: emplea la salida estándar por defecto, así que tendremos que usar `-O` para especificar el nombre que queremos darle en nuestro sistema. `curl` soporta muchos más protocolos (sftp, scp) y además puede seguir redireccionamientos (códigos HTTP 301 y 308). Para volver a descargar el cromosoma 22 con `curl` haremos (sobreescribiendo la anterior descarga):

```
abenito@cpg3:~/sesion-ii$ curl http://hgdownload.soe.ucsc.edu/goldenPath/hg19/chromosomes/chr22.fa.gz > chr22.fa.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 10.8M  100 10.8M    0     0   722k      0  0:00:15  0:00:15 --:--:-- 1353k
```


En definitiva, si `wget` no te funciona para descargar algo, prueba con `curl`. 

#### 2.4. scp
`scp` es como el comando `cp` pero entre máquinas diferentes. Para especificar las localizaciones en diferentes máquinas usaremos la sintaxis `usuario@host:/ruta/al/fichero`. Si quiero copiar el fichero que me acabo de descargar a mi portátil, haría (el caracter `~` se sustituye por mi directorio HOME):

```
➜  ~ scp abenito@cpg3.der.usal.es:~/sesion-ii/chr22.fa.gz .
abenito@cpg3.der.usal.es's password:
chr22.fa.gz                                   100%   11MB  11.2MB/s   00:00
```

`scp` me pide mi password, y descarga el fichero al directorio actual desde el que se invoque en mi portatil. Además, si invierto el orden de los argumentos, _subiré_ ficheros al servidor en vez de descargarlos. 

Si quiero descargar todo un directorio, puedo hacerlo con el flag `-r`: 

```
scp -r abenito@cpg3.der.usal.es:~/sesion-ii/ .
```

### 2. Integridad de los datos
Cuando descarguemos grandes conjuntos de datos, ocuparemos la red por períodos mas largos de tiempo. Esto se traduce en que existirá un mayor riesgo de que los datos se corrompan debido a posibles cortes en la conexión u otros infortunios. Por tanto, además de asegurarnos de que descargamos los datos correctamente (el proceso finaliza sin errores), tenemos que comprobar la integridad de los mismos con los llamados __checksums__.

Los _checksums_ son resúmenes comprimidos de los datos que se computan de tal manera que aunque cambie un solo bit en los datos, el checksum será diferente.
Esto tiene muchísimas aplicaciones, como por ejemplo documentar correctamente nuestros análisis, asegurándonos de que son reproducibles por una tercera persona: un ejemplo es que es habitual que los datos cambien entre diferentes versiones de los mismos, con lo cual nuestros análisis puede que sólo sean válidos para una de ellas. Si no expresamos esto de manera unívoca en nuestras memorias, esto puede llevar a problemas en el futuro. 

#### 2.1. Tipos de checksums
Existen varios algoritmos para calcular checksums, entre los cuales destacan `CRC`, `MD5` y `SHA-1`. Por ejemplo `SHA-1` es el tipo de hash que genera git para distinguir un commit de otro. `SHA-1` es más moderno y potente que `MD5` y `CRC`, pero estos últimos sigue siendo aún muy usados, por lo que también los veremos en esta sesión. 

En la carpeta `genes` tienes dos ficheros .bed de prueba, vamos a calcular los tres tipos de checksums de estos ficheros. Lanza estos comandos en tu terminal y comprueba que los hashes casan con los que aparecen en tu pantalla. 

`SHA-1`, con el comando `shasum`:

```
abenito@cpg3:~/sesion-ii/genes$ shasum gen-1.bed gen-2.bed
30819eebf61439abc88142e19a3fad4c15f39303  gen-1.bed
10aaafc883c5b2e0c31136c6d9eb01dca0d04950  gen-2.bed
```

`MD5`, con el comando `md5sum` (md5 si usas OSX):

```
abenito@cpg3:~/sesion-ii/genes$ md5sum gen-1.bed gen-2.bed
d0554d9acd90e16f68f20dea62af6963  gen-1.bed
dcc78b56a1c4b050c2c8af0171f9cfd6  gen-2.bed
```

y finalmente `CRC`, con `sum`:

```
abenito@cpg3:~/sesion-ii/genes$ sum ./*
53230     1 ./gen-1.bed
05754     1 ./gen-2.bed
```

Para automatizar este proceso, `shasum` y `md5sum` permiten generar un archivo de texto con los checksums de lo que le pasemos. Luego, podremos usar este fichero para verificar si los checksums casan o no con los ficheros que tenemos. El archivo de checksums se generará así (no te preocupes si no entiendes aún qué hace el caracter `>`, más sobre esto próximamente, quédate de momento con que es necesario para crear el fichero).

```
abenito@cpg3:~/sesion-ii/genes$ shasum ./*.bed > bed_checksums.sha
abenito@cpg3:~/sesion-ii/genes$ cat bed_checksums.sha
30819eebf61439abc88142e19a3fad4c15f39303  ./gen-1.bed
10aaafc883c5b2e0c31136c6d9eb01dca0d04950  ./gen-2.bed
abenito@cpg3:~/sesion-ii/genes$ shasum -c bed_checksums.sha
./gene-1.bed: OK
./gene-2.bed: OK
```

Como ves, la opción `-c` (check) le dice a shasum que compruebe los checksums.  Prueba a editar un bed (haz una copia antes para poder restaurarla después) con nano cambiando un caracter y lanza el comando para comprobar los checksums de nuevo. ¿Qué pasa ahora?


En fin, normalmente, en cualquier servidor de datos que se precie, encontraremos un fichero con los checksums de los ficheros disponibles para descargar. Esto nos permitirá comprobar la integridad de los datos y aseguridad la reproducibilidad de nuestros experimentos.

 
#### 2.2. Encontrando diferencias con diff
Para terminar esta sesión, vamos a ver el comando `diff`. 

Imagina que alguien con el que te encuentras colaborando, ha estado trabajando en una copia de un fichero .bed. Su versión es `gen-2.bed`, mientras que la tuya es `gen-1.bed`. Cuando ponéis los resultados en común, como es de esperar, véis que los suyos y los tuyos son diferentes. Lo primero es asegurarse de que los ficheros son de hecho diferentes calculando un checksum (como así es, ver apartado anterior). Antes de utilizar los datos de tu compañera para correr tu análisis, te gustaría ver cómo de diferentes son ambas copias. Esto lo conseguimos con `diff <fichero1> <fichero2>` (he anotado la salida con ----> (número) para explicarla debajo).


```
abenito@cpg3:~/sesion-ii/genes$ diff -u gene-1.bed gene-2.bed
--- gene-1.bed	2021-01-08 16:32:40.529398475 +0100 -----> (1)
+++ gene-2.bed	2021-01-08 16:32:50.457306376 +0100
@@ -1,22 +1,19 @@ -----> (2)
 1	6206197	6206270	GENE00000025907
 1	6223599	6223745	GENE00000025907 -----> (3)
 1	6227940	6228049	GENE00000025907
+1	6222341	6228319	GENE00000025907 -----> (4)
 1	6229959	6230073	GENE00000025907
-1	6230003	6230005	GENE00000025907 -----> (5)
 1	6233961	6234087	GENE00000025907
 1	6234229	6234311	GENE00000025907
 1	6206227	6206270	GENE00000025907
 1	6227940	6228049	GENE00000025907
 1	6229959	6230073	GENE00000025907
-1	6230003	6230073	GENE00000025907 -----> (6)
+1	6230133	6230191	GENE00000025907
 1	6233961	6234087	GENE00000025907
 1	6234229	6234399	GENE00000025907
 1	6238262	6238384	GENE00000025907
-1	6214645	6214957	GENE00000025907
 1	6227940	6228049	GENE00000025907
 1	6229959	6230073	GENE00000025907
-1	6230003	6230073	GENE00000025907
 1	6233961	6234087	GENE00000025907
 1	6234229	6234399	GENE00000025907
-1	6238262	6238464	GENE00000025907
 1	6239952	6240378	GENE00000025907
```

 
Te suena? Dónde lo has visto antes? :) 

En primer lugar, la opción `-u` le dice a diff que saque la salida en el formato diff unificado, que es casi idéntico al que se usa en git. 
Este formato saca "hunks", o trozos con diferencias entre los dos ficheros, para facilitar la exploración.

1. Las dos líneas del comienzo son la cabecera. El primer fichero (la base de la comparación) se marca con `---`, mientras que el segundo (la versión modificada), se marca con `+++`. Además, se muestra la fecha de modificación de los dos ficheros.
2. Esta línea indica el comienzo de un "hunk" con cambios. Los números entre `@@` y `@@` indican dónde empieza el "hunk", y cómo de largo es, en los dos ficheros, respectivamente. 
3. Las líneas del diff que comienzan con un espacio en blanco indican que el fichero modificado no presenta cambios respecto al primero.
4. Las líneas del diff que comienzan con un `+`, indican que se ha añadido una línea al fichero modificado. 
5. Las líneas del diff que comienzan con un `-`, indican que se ha quitado una línea al fichero modificado.
6. Una línea del diff con un `-` seguida de otra con un `+`, indica que la línea ha cambiado en el fichero modificado. 



### Órdenes de la shell vistas en esta sesión:

`cp`: copia ficheros

`mv`: mueve o renombra un fichero

`rm`: borra un fichero

`du`: muestra lo que ocupa un directorio y su contenido en disco

`ln`: construye un enlace

`echo`: imprime en la pantalla sus argumentos

`unset`: desdefine una variable

`set`: muestra las variables de entorno

`which`: muestra la orden del fichero que se ejecuta con una orden

`find`: busca ficheros o directorios

`wget`: descargador no interactivo

`curl`: transfiere URLs

`scp`: copia ficheros entre máquinas distintas

`shasum`: genera el checksum de los ficheros usando el algoritmo sha-1

`md5sum`: genera el checksum de los ficheros usando el algoritmo md5

`sum`: genera el checksum de los ficheros usando el algoritmo crc

`diff`: compara dos ficheros de texto, mostrando las diferencias
