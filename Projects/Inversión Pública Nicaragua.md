# La inversión pública en Nicaragua
## Un análisis a partir de la información publicada por el Gobierno de Nicaragua

### **Introducción**

En Nicaragua, la información generada por el sector público es limitada. El Banco Central de Nicaragua y el Ministerio de Hacienda y Crédito Público (MHCP) son de las instituciones que más publican información cuantitativa sobre la economía del país; sin embargo, en cuanto a datos sociodemográficos, es notable la falta de información actualizada. El censo poblacional no se realiza desde más de 15 años (última vez en 2005); encuestas sobre el nivel de vida (pobreza) se dejaron de publicar desde 2017. La única publicación periódica que se publica al respecto son los resultados de la Encuesta Continua de Hogares (ECH) que se ha estado publicando trimestralmente por el Instituto Nacional de Información de Desarrollo (INIDE). 

Dependiendo de la temática que se quiera estudiar sobre políticas públicas en Nicaragua, la información será mayor o menor. Durante ya casi 10 años he estado realizando análisis sobre las finanzas públicas de Nicaragua y algunos países centroamericano y me he encontrado con estas dificultades. Nicaragua está en la cola de la transparencia fiscal, así que cuando se desean hacer este tipo de análisis nos encontramos con muchas dificultades. 

Las autoridades nicaragüenses que rigen las finanzas públicas hasta la fecha no han logrado implementar un sistema de publicación de información que sea de fácil acceso para estudiar los datos respecto a este tema. Por ejemplo, el BCN publica la mayoría de sus datos en formatos de hojas de cálculo, implementando una política de “datos abiertos” para dar a conocer los indicadores económicos del país;  sin embargo, esto no se ha logrado trasladar al MHCP. 

El proyecto reciente que trabajé fue el de construir una base de datos sobre la inversión pública en los diferentes departamentos y municipios del país. Para esto utilicé los datos que publica el MHCP en la página web del Sistema Nacional de Inversión Pública (SNIP). Estos son los únicos datos que este ministerio publica en cierta forma de “datos abiertos”[^1]. 

En esta web se puede encontrar información sobre los proyectos de inversión pública que el Sector Público no Financiero implementa en el país. Hay datos sobre las asignaciones (aprobado para los proyectos), así como lo realmente ejecutado al finalizar cada año. Muestra las instituciones, los proyectos, los sectores, los departamentos y municipios, así como las fuentes de financiamiento. 

Pero no todo es fácil; para poder construir una base de datos completa, es necesario unir la información de los diferentes archivos que se publican en este sitio[^2].

### **Objetivos y Metodología**

El objetivo de este proyecto es **analizar cómo ha evolucionado la inversión pública en Nicaragua entre los años 2017 y 2021**, conociendo lo siguiente:

1. **qué instituciones ejecutan**, 
2. **hacia qué sectores ha estado dirigida la inversión,** 
3. **quiénes financian los proyectos, y sobre todo,** 
4. **hacia qué departamentos y municipios se están enfocando los recursos**.

Para esto, hice uso de tres archivos que publica el SNIP. En el primero se presenta información de los departamentos, municipios, proyecto y obra y la institución encargada de ejecutarla. En el segundo se muestra información sobre las instituciones y los proyectos y las fuentes de financiamiento. El tercero incluye datos sobre los proyectos, el sector y la institución de ejecución[^3].

Tanto el segundo archivo como el tercero, los datos publicados son los ejecutados, pero con el primero, son datos de presupuestos actualizados, es decir, lo último que se aprueba (o se modifica) durante el año. Esto no es lo que realmente se ejecuta. Por lo tanto, fue necesario unir los datos de los archivos donde se muestra la información departamental y municipal.

![Archivo 1](https://user-images.githubusercontent.com/112327873/189492022-570004f1-294e-4cc8-a6e8-886f66583e38.png) 

![Archivo 2](https://user-images.githubusercontent.com/112327873/189492041-49625f65-216c-4a0c-b721-3d63edc9db72.png)

![Archivo 3](https://user-images.githubusercontent.com/112327873/189492068-1e392e96-318d-4346-867e-5311c8f30e38.png)

### **Construcción de las bases de datos**

Para poder consolidar toda la información en una sola base de datos fue necesario unir la información de los tres archivos; sin embargo, la estructura de estas bases de datos lo primero que hice fue estructurar de manera ordenada cada uno de los archivos, para luego conectarlos. En este sentido era necesario crear una columna para cada tipo de datos. Por ejemplo, en el primer archivo fueron: departamento, municipio, proyecto, obra, institución; en el segundo: Ente, institución, proyecto, obra, fuente.

Esta parte es la más complicada pues no encontré una forma automática de re-estructurar la base de datos. Por lo tanto lo hice de una manera más rústica, apoyado porque cada dato estaba rellenado por un color diferente: 
1)	Cree una columna para cada nivel de información.
2)	Borré los registros que no pertenecían a cada columna, guiado por el color.
3)	Rellené automáticamente cada columna con el registro al que pertenecía para que cada fila coincidiera al final con el último elemento, que podía ser la obra o la fuente de financiamiento.










[^1]: www.snip.gob.ni
[^2]: http://snip.gob.ni/Portada/PipAnual
[^3]: Es necesario destacar que la información que se publica en este sitio web es de toda la inversión pública del sector público no financiero: gobierno central, empresas públicas. Incluye parte de la inversión pública que hacen los gobiernos municipales, pero la que es financiada a través de las transferencias del gobierno central; no toma en cuenta las posibles inversiones que estos hacen con sus propios recursos, porque en muchos casos hay una deficiente contabilidad
