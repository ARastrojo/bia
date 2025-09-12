
Introducción a R y RStudio.
Instalación de R y RStudio Para utilizar R mediante RStudio tendremos que instalar la versión más actualizada de R previamente a la instalación de RStudio.
Windows: Dirigirse a y descargar el instalador haciendo click en el link que dice “Download R 4.5.1 for Windows” (o la última versión que aparezca).
Una vez que descargado, hacer doble clic en el archivo y seguir las instrucciones del instalador.
Una vez instalado R, dirigirse a y descargar el instalador.
Una vez que descargado, hacer doble clic en el archivo y seguir las instrucciones del instalador.
GNU/Linux (Ubuntu y derivados): Para descargar la versión de R disponible en los repositorios de la propia distribución de Linux:¿Qué es R?
R es un lenguaje de programación orientado a objetos y un entorno de trabajo donde se puede implementar distintos procedimientos que permiten hacer tareas muy diversas.
Dichas tareas van desde el análisis estadístico hasta el aprendizaje automático (machine learning), minería de datos, econometría, investigación biomédica o la bioinformática.
A esto contribuye la posibilidad de cargar diferentes bibliotecas o paquetes con funcionalidades de cálculo y representación de gráficos.
La interfaz de R se reduce a poco más de una consola en la que se escriben líneas de comandos que el programa ejecuta, devolviendo el resultado en la misma consola.
La imagen típica de la consola de R se muestra a continuación:

El cursor, que por defecto es el símbolo ‘\>’, indica que R está listo para recibir un comando.
Por ejemplo, podemos escribir en la consola presionando luego Enter:

```{r}
5+3
```

Y R nos responderá: [1] 8

¿Qué es RStudio?
Existen softwares que facilitan la interacción con R.
Es el caso de RStudio, un entorno de desarrollo integrado (IDE) que combina todas las herramientas necesarias para el desarrollo de software en una sola interfaz gráfica (editor de código, compilador, depurador y herramientas de automatización de compilaciones).
Las principales ventanas que podemos encontrar en RStudio son las siguientes:

Código: En esta ventana se escriben las líneas de comandos.
Para ejecutarlas se coloca el cursor en la línea a ejecutar y se presiona CTR + Enter.
El conjunto de líneas de comandos para la realización de un proceso se denomina programa o “script”.
Los scripts se pueden guardar y abrir de nuevo en otro momento para realizar el mismo análisis.
También es donde visualizarás determinados objetos como las tablas de datos.
Consola: Es donde se visualizará la ejecución de los comandos y los resultados.
Es lo mismo que lo que se vería si se usara R sin RStudio, de forma que también se pueden escribir aquí directamente la línea de comandos a ejecutar.
Sin embargo, las órdenes aquí dadas no se guardarán en el script.
Entorno/Historial: Aquí se mostrarán los conjuntos de datos y los objetos creados, y que se encuentran en la memoria.
También se pueden ver algunas de las propiedades de dichos datos u objetos como el tipo de objeto y sus dimensiones.
En la pestaña “History” se puede consultar el histórico de los comandos ejecutados.
Archivos/Gráficos/Paquetes/Ayuda/Visor: Con la pestaña “Files” se puede navegar por las carpetas del ordenador y determinar el directorio de trabajo.
En la pestaña “Plots” se mostrarán los gráficos generados.
En “Packages” se puede consultar los paquetes activos en R en ese momento.
En la pestaña “Help” se desplegarán los archivos de ayuda de los paquetes y funciones de R.
En “Viewer” se podrán ver los contenidos web locales (ej: HTML outputs).
CRAN y paquetes Cuando se instala R se instalan también una serie de paquetes básicos, que traen implementadas múltiples funciones para la realización de tareas rutinarias, como la representación gráfica, la manipulación de datos, análisis estadísticos, etc.
Dichos paquetes básicos están mantenidos por un reducido grupo de especialistas que conforman el “R Core Team”, asegurando la actualización y la mejora continua de R.
Pero el verdadero potencial de R viene dado por la posibilidad de expandir sus capacidades mediante otros paquetes desarrollados por especialistas de distintos campos para abordar tareas específicas.
Todos los paquetes cumplen una serie de requisitos y contienen un conjunto de funciones preprogramadas, junto con un archivo de ayuda que documenta sus capacidades.
Los paquetes se pueden instalar a partir de distintos repositorios.
El repositorio oficial de R es el CRAN (Comprehensive R Archive Network), una red de servidores espejo () repartidos por todo el mundo que almacenan versiones idénticas y actualizadas del código junto con la documentación de R.
La recomendación es elegir el servidor de más cercano para minimizar la carga de la red (en España hay dos).
Todos los paquetes en CRAN han pasado un riguroso proceso de revisión por parte del “CRAN Team”, pero la responsabilidad de mantener y mejorar los paquetes de R es de sus autores.
En RStudio, el mirror será seleccionado por defecto.
No obstante, se puede cambiar a otro mirror yendo a Tools \> Global Options \> Packages y haciendo clic en el botón Change.
Para minimizar la carga de la red, la recomendación es elegir el servidor más cercano (en España hay dos).
Mediante código podemos hacerlo con la función chooseCRANmirror() o directamente al instalar un paquete determinado dentro de la función install.packages() mediante el argumento “repos”.
Por ejemplo, usando el mirror de la Red Nacional Española de Investigación (Madrid):

```{r}
install.packages("vegan", repos = "https://cran.rediris.es/", dep = T)
```

El argumento dep = T indica a R que instale las dependencias, es decir, cualquier otro paquete que sea requerido para el correcto funcionamiento del paquete que queremos instalar.
Los paquetes hay que instalarlos una única vez, pero para utilizarlos hay que cargarlos con la función library() siempre que se inicie una nueva sesión en R:

```{r}
library("vegan")
```

Una vez cargados, se pueden utilizar todas sus funciones y ver los contenidos de su archivo de ayuda.
Para obtener ayuda sobre una función determinada del paquete se puede usar la función help() o, alternativamente, el símbolo “?”
seguido del nombre de la función:

```{r}
help(metaMDS)
?metaMDS
```

También podemos obtener información de cuáles son las funciones que contiene el paquete:

```{r}
help(package = "vegan")
```

Algunos paquetes incluyen una o varias “viñetas”, donde se explica en detalle su funcionalidad combinando las distintas funciones que contiene.
Para ver las viñetas incluidas en un paquete usaremos:

```{r}
vignette(package = "vegan")
```

Y para ver una viñeta determinada la nombraremos con el mismo comando:

```{r}
vignette("diversity-vegan")
```

Otros recursos de ayuda que pueden tener los paquetes de R se pueden consultar con demo() y example():

```{r}
demo(package = "vegan")
demo(package="graphics")
demo("persp")
example("vegan")
```

Por último, para saber en un momento determinado qué paquetes tenemos cargados podemos ejecutar:

```{r}
search()
```

Otros repositorios.
Bioconductor.
No todos los paquetes que se pueden implementar en R están disponibles en CRAN.
Para los paquetes relacionados con la bioinformática en particular, existe otro repositorio que cuenta con muchos paquetes potentes.
Se llama y es un repositorio centrado específicamente en paquetes de bioinformática.
Esto significa que muchos, si no todos, los paquetes disponibles en Bioconductor se centran en el análisis de datos biológicos, pudiendo ser un lugar ideal para buscar herramientas para analizar conjuntos de datos -ómicos.
Para buscar y consultar los paquetes que se pueden instalar desde este repositorio hay que ir a la propia página de .
La primera vez que se quiera instalar un paquete de Bioconductor se tendrá que instalar primero el gestor del repositorio a través de CRAN:

```{r}
install.packages("BiocManager")
```

Para comprobar que se ha instalado correctamente:

```{r}
BiocManager::version()
```

Una vez realizado esto, ya se podrá instalar cualquier paquete de Bioconductor:

```{r}
BiocManager::install("dada2")
library("dada2")
help(package = "dada2")
```

Tipos de objetos en R Antes se ha comentado que R es un lenguaje orientado a objetos.
Esto significa que las variables, datos, funciones o los resultados se guardan en la memoria activa del ordenador en forma de objetos con un nombre específico.
Para nombrar (y crear) un objeto nuevo, se utiliza el símbolo de asignación “\<-“.
La asignación de un objeto se puede hacer con el símbolo “=” pero puede ser confuso ya que corresponde con el operador de la igualdad.

```{r}
a <- 3
a
```

Los objetos se pueden modificar o manipular con operadores (aritméticos, lógicos y comparativos) y funciones (que a su vez son objetos).
Pero vamos por partes: - Operadores.
Con R podemos hacer cálculos matemáticos con operadores aritméticos.

```{r}
2+3 			# suma
sum(2, 3) 		# otra forma de sumar con la función sum()
2-3			# resta
2*3 			# multiplicación
10/5 			# división
2^3 			# potencias
8^(1/3) 		# las raíces se consiguen como potencias de fracciones
log(10) 		# logaritmo natural
log10(10) 		# logaritmo en base 10
round(log(15),2) 	# redondeo a dos decimales
```

También tenemos operadores comparativos o relacionales, cuyo resultado es TRUE o FALSE con valores numéricos asociados 1 y 0, respectivamente.

```{r}
3>2 	# mayor que
3<=2 	# menor o igual que
3==2 	# exactamente igual que 
3!=2 	# distinto de
```

Otro tipo de operadores son los lógicos, usados para álgebra Booleana, y que también devuelven valores de TRUE o FALSE.
Pueden ser útiles para seleccionar datos.
!x ; significa no “x” x & y, significa “x” e “y” x\|y, significa “x” o “y” Por ejemplo, para indicar intervalos numéricos tendremos que usar estos operadores.

```{r}
x<-34
x>20 & x<40
x>20 & x<30
```

-   Funciones. Las funciones son aplicaciones para realizar distintas operaciones sobre los objetos. Hay muchas funciones que vienen con la distribución de R o en los distintos paquetes disponibles. Tienen una serie de argumentos que, si no vienen especificados por defecto, deberemos de aportar. Ya hemos visto alguna, como help(), install.packages(), sum(), etc. Para saber los argumentos que podemos o debemos introducir en una función ya definida usaremos el comando help(). Pero también podemos definir nuestras propias funciones usando function() según la siguiente estructura: nombre \<- function(argumentos) {operaciones} Por ejemplo:

```{r}
restar2numeros <-function(a,b) {a-b} 	# Ojo con los acentos y las mayúsculas!
restar2numeros(a=5, b=7) 			# Aquí los argumentos son “a” y “b2
```

-   Vectores. Un vector es una colección de uno o más datos del mismo tipo. Cuando decimos del mismo tipo, nos referimos a que todos los datos introducidos en el vector han de ser numéricos, valores lógicos (TRUE o FALSE) o cadena de caracteres, este último tipo siempre entre comillas.

```{r}
vector.numerico <-c(31,23,25,42,25,37)		# La función c() es para combinar
vector.numerico
vector.cadena <- c("Juan", "Laura", "Ivan", "Sergio", "Maria", "Eva")
vector.cadena
```

Otro tipo de datos pueden ser los factores.
Podemos convertir cualquier vector en un factor.

```{r}
vector.factor <- c("hombre", "mujer", "hombre", "hombre", "mujer", "mujer")
vector.factor <- as.factor(vector.factor)
vector.factor 		# Fíjate que en los niveles de este factor
```

La función str() nos va a dar información sobre la estructura de los objetos.

```{r}
str(vector.factor)		
```

O podemos preguntar directamente a R si nuestro objeto es un vector.

```{r}
is.vector(vector.cadena)
is.vector(vector.factor) 	# Para R ya no es un vector sino un factor
is.factor(vector.factor)
```

Podemos averiguar la longitud de un vector con la función length().

```{r}
length(vector.cadena)
```

-   Matrices y arrays. Las matrices y los arrays pueden considerarse vectores multidimensionales. Las matrices son un caso particular de arrays con sólo dos dimensiones: el largo, o número de filas; y el ancho, o número de columnas. Al igual que los vectores, no se pueden mezclar distintos tipos de datos.

```{r}
mi.matriz <- matrix(1:12, nrow = 4, ncol = 3)		#1:12 crea secuencias de 1 en 1
mi.matriz		# Fíjate que cada elemento tiene unas coordenadas asignadas
str(mi.matriz) 	# Fíjate que el tipo de dato es integer
mi.matriz <- matrix(1.5:12.5, nrow = 4, ncol = 3)
str(mi.matriz)	# Fíjate que el tipo de dato ha cambiado a numeric
mi.array <- array(1:24, dim = c(4,3,2))	# Más de 2 dimensiones
mi.array
str(mi.matriz)	# Es un vector, de longitud 24 doblado en 3 dimensiones
```

Se pueden crear matrices combinando vectores por columnas, con cbind(), o por filas, con rbind()

```{r}
mi.matriz <- cbind(vector.cadena, vector.numerico, vector.factor)
mi.matriz
str(mi.matriz)	# Fíjate que todos los datos se han vuelto cadenas de caracteres
```

Cuando un vector no tiene la misma longitud que el resto, R recicla los valores hasta completar.

```{r}
vector.corto <- c(3,7,4,1)
mi.matriz <- cbind(vector.cadena, vector.numerico, vector.factor, vector.corto)
mi.matriz 		# Fíjate que pasa en la cuarta columna
```

-   Tablas de datos (data frames). Las tablas de datos tienen también dos dimensiones como las matrices, pero pueden contener datos de distinto tipo. Con la función data.frame() le indicaremos tantos vectores como columnas deseemos.

```{r}
mi.tabla <-data.frame(vector.cadena, vector.numerico, vector.factor)
mi.tabla
str(mi.tabla)
```

Los vectores deberán tener la misma longitud.
En los data frame, R no recicla los valores.

```{r}
mi.tabla <- data.frame(vector.cadena, vector.numerico, vector.factor, vector.corto)
```

A no ser que la longitud sea un múltiplo de la longitud del vector corto.

```{r}
vector.corto <- c(3,7)
mi.tabla <- data.frame(vector.cadena, vector.numerico, vector.factor, vector.corto)
mi.tabla		# Fíjate que pasa en la cuarta columna
```

Se puede asignar nombres a cada vector cuando lo estamos creando.

```{r}
mi.tabla <- data.frame("Nombre" = vector.cadena,

			    "Edad" = vector.numerico,
			    "Sexo" = vector.factor
    )

mi.tabla
```

También podemos nombrar las columnas de nuestro data frame o matriz con la función colnames().

```{r}
colnames(mi.matriz)
colnames(mi.matriz) <- c("Nombre", "Edad", "Sexo", "Curso", "Otro")
mi.matriz
```

-   Listas. Las listas, al igual que los vectores, son estructuras de datos unidimensionales, sólo tienen largo. Pero a diferencia de los vectores, permiten el almacenamiento de objetos de distinta naturaleza (vectores, funciones, matrices, tablas…, incluso otras listas). Cada uno de los objetos guardados serán sus elementos y su longitud será el número de elementos que contiene. Podemos crear una lista con la función list() donde se encuentren los objetos que hemos creado hasta ahora:

```{r}
ls()		# Con esta función obtenemos la lista de objetos del entorno
mi.lista <- list(x, restar2numeros, vector.cadena, mi.matriz, mi.tabla)
length(mi.lista)	# Hemos guardado 5 objetos
mi.lista
str(mi.lista)
```

Y, al igual que los data frames, se les puede dar un nombre cuando la creamos.

```{r}
mi.lista <- list("numerico" = x,

"funcion" = restar2numeros,
        		"cadena" = vector.cadena,
        		"matriz" = mi.matriz,
       		 "tabla" = mi.tabla
)
mi.lista
str(mi.lista)
```

-   Otros tipos de objetos. Existen otros tipos de objetos de distinta naturaleza que servirán para almacenar los resultados de muchos de los análisis que realizaremos con las funciones de los distintos paquetes. Su estructura puede ser muy compleja, pero existen funciones para extraer la información principal como la función summary().

```{r}
reg1 <- lm(dist~speed, data=cars)		# lm() para hacer regresión
str(reg1)
summary(reg1)
```

Manejo de los objetos En muchas ocasiones vamos a querer sacar elementos de los objetos, bien para operar con ellos o para obtener un resultado tras aplicar alguna operación.
Esto se puede realizar mediante índices, los cuales representan la posición de los elementos dentro de la estructura del objeto.
Los índices los especificamos mediante corchetes (“[]”).
Hemos visto que los vectores son unidimensionales de forma que sabiendo la posición de los datos que nos interesan los sacaremos fácilmente.

```{r}
vector.cadena
vector.cadena[3]		# Devuelve un vector con el elemento 3
```

También podemos extraer un subconjunto de los datos mediante secuencias numéricas creadas con “:”.

```{r}
vector.cadena[3:6]		# Devuelve un vector con los elementos del 3 al 6
```

Pero no se puede usar comas dentro de los corchetes pues estaríamos indicando más de una dimensión.

```{r}
vector.cadena[1,5]		# Devuelve un error, dos dimensiones en un vector
vector.cadena[c(1,5)]	# Devuelve un vector con los elementos de 1 y 5
vector.cadena[c(1,3:6)]	# Devuelve un vector con todos los elementos menos el 2
```

También se pueden usar índices negativos.

```{r}
vector.cadena[-2]		# Devuelve un vector con todos los elementos menos el 2
```

En las matrices y en los data frames hay dos dimensiones.
Por tanto, tendremos que indicar la fila y la columna del elemento deseado.

```{r}
mi.matriz
mi.matriz[5,2]		# Posición fila 5 y columna 2
```

Si indicamos sólo la fila, nos devolverá los valores de todas las columnas en esa fila y viceversa.

```{r}
mi.matriz[5,] 		# Todos los valores de la fila 5
mi.matriz[,2]			# Todos los valores de la columna 2
```

En los arrays, con n dimensiones tendremos que indicar n coordenadas.

```{r}
mi.array
mi.array[1,2,1]		# Este array tiene tres coordenada: fila, columna, nivel
mi.array[1, , ]			# Devuelve la primera fila de los dos niveles
mi.array[, , 1]			# Devuelve todas las filas y columnas del primer nivel
```

Pero como las matrices y los arrays son vectores plegados en varias dimensiones, también podemos indicar la posición del elemento en el vector (no así en los data frames).

```{r}
mi.matriz[11]		# El elemento 11 tiene las coordenadas [5,2]
mi.array[5]			# El elemento 5 tiene las coordenadas [1,2,1]
```

Podemos seleccionar varias filas o columnas a la vez.

```{r}
mi.matriz[3:5,] 		# Filas 3, 4 y 5
mi.matriz[,c(2,3)] 		# Columnas 2 y 5
```

En los data frames, si las columnas están nombradas, podemos hacer selecciones con los nombres.

```{r}
mi.tabla
mi.tabla$Nombre		# Selecciona toda la columna llamada "Nombre"
```

Y podemos combinar índices y nombres, a la vez que usamos operadores lógicos.

```{r}
mi.tabla[mi.tabla$Sexo=="mujer",]		# Todas las filas y columnas de mujeres
```

Como leer datos en R Gráficos Como guardar el trabajo (scripts, objetos y espacio de trabajo) Bibliografía y otros recursos de autoaprendizaje: .
Francisco Martínez del Río, Universidad de Jaén.
. J.
B. Mendoza Vega, Universidad Nacional Autónoma de México.
. Emmanuel Paradis, Université Montpellier II.
. W.
N. Venables, D. M. Smith and the R Core Team.
. Un paquete de gr##áficos muy potente.
. Documentos varios, algunos disponibles en distintos idiomas.
. Libro enfocado a la Ciencia de Datos con R.
. Proyecto para el análisis y la comprensión de datos de alto rendimiento en genómica y biología molecular.
. Foro de discusión para preguntas generales de programación, incluido R.
. Foro de discusión para preguntas específicas de bioinformática.
