# chapter-05-s02

## QML y gráficas

## Introducción

El objetivo de este capítulo es mostrar la forma de dibujar gráficas usando QML + JavaScript en una aplicación para Ubuntu Touch \(UBPorts\).

## Requisitos

Para seguir este capítulo hay que tener configurado el kit "Desktop" en el SDK de Ubuntu y tener un conocimiento básico de QML y JavaScript. Para tener más información puedes consultar los primeros capítulos del curso.

También es necesario tener unos conocimientos básicos del lenguaje SQL y de la forma de acceder a una base de datos SQLite desde QML. Si no conoces la forma de hacerlo puedes consultar el capítulo anterior.

## Premisas

Este capítulo usa la aplicación de ejemplo que se creó en el capítulo anterior relacionada con QML y el acceso a una base de datos SQlite. De forma resumida, la aplicación de ejemplo se llamaba "WeatherRecorder" y se programó para guardar y gestionar los valores de temperatura de una ciudad.

Ahora, en este capítulo, se mejorará la aplicación para añadir una función de analítica: mostrar las temperaturas de un mes en una gráfica.

## Gráficas con QML

La solución presentada utiliza la [librería JavaScript de código abierto Chart.js](http://www.chartjs.org/) como librería de dibujo que utiliza los nuevos elementos HTML5. Para usarla hay que usar un "binding", que es una librería intermedia entre Charts.js y el código QML. Este binding utiliza otra librería de código abierto llamada [qchart.js](https://github.com/jwintz/qchart.js). qchart.js define un componente QML personalizado que muestra una gráfica que envuelve el objeto Chart.js

Hay que agradecer a Jwintz, el autor de **qchart.js**, el gran trabajo que ha realizado. No es necesario conocer a bajo nivel el funcionamiento de la librería para poder usarla.

## Configuración del proyecto para usar gráficas

Si importas la aplicación de ejemplo en el IDE de Ubuntu se pueden saltar los siguientes pasos. En el caso de crear una aplicación nueva de QML desde cero hay que añadir los archivos para poder representar gráficas.

Para dibujar gráficas desde la aplicación QML es necesario incluir dos archivos \(se pueden encontrar todos los archivos necesarios en [https://github.com/jwintz/qchart.js](https://github.com/jwintz/qchart.js)\):

### Qchart.js

Es la librería de dibujo de JavaScript que contiene las funciones que permiten dibujar la gráfica, calcular la escala de los ejes, etc. Es una versión de la librería Chart.js empaquetada con la librería "qchart.js" \(seguramente con alguna modificación adicional\).

Para usar la librería hay que importarla:

```javascript
import "QChart.js" as Charts
```

Nota: la sintaxis de importación anterior es la misma que se usa para importar un archivo externo de JavaScript en QML.

### QChart.qml

Define un nuevo componente QML \(llamado "QChart"\) que se puede usar en el código QML para dibujar una gráfica. Se usa en un bloque de código parecido al siguiente:

```javascript
QChart {                  
  //chart configuration options
}
```

Como último paso hay que incluir los nuevos archivos en el archivo **.rqc** \(es un archivo del proyecto que define todos los archivos que componen el proyecto\). A partir de este punto la aplicación QML puede dibujar gráficas.

## Introducción a la aplicación de ejemplo

Como se ha comentado antes, se usará como base la aplicación programada en el capítulo anterior y se mejorará. Para mantener separados los códigos fuentes de ambas aplicaciones, y evitar confusiones, se ha creado una aplicación nueva llamada "WeatherRecorderChart". Esta aplicación usa como base la aplicación "WeatherRecorder".

Si importas el proyecto "WeatherRecorderChart" en el IDE de Ubuntu, se verá una imagen similar a la siguiente:

![Archivos del proyecto de la aplicaci&#xF3;n](../.gitbook/assets/01_application_source%20%282%29.png)

Al ejecutar la aplicación por primera vez \(pulsando el botón verde con el icono de "Play"\) se mostrará la pantalla de configuración de la aplicación:

![Pantalla de configuraci&#xF3;n de la aplicaci&#xF3;n](../.gitbook/assets/02_application.png)

Al aceptar \(o actualizar\) la configuración que propone la aplicación se llegará a la nueva pantalla inicial:

![Nuevo dise&#xF1;o de la pantalla inicial](../.gitbook/assets/03_application_new_design.png)

Nota: la aplicación sólo muestra la pantalla de configuración en el primer inicio \(se ha descrito lo que ocurre cuando la aplicación se inicia en el capítulo anterior\).

**Importante**: tanto la aplicación nueva como la antigua usan una base de datos SQLite para guardar los valores de temperatura. La localización del archivo de la base de datos es diferente, ya que la ruta está relacionada con el campo "applicationName" que hay en el archivo Main.qml. Por favor, revisa el capítulo anterior para conocer más detalles.

Si se compara con la aplicación "WeatherRecorder", se puede distinguir un nuevo apartado al final de la pantalla llamado "Analytic section". Al pulsar en el botón "Chart" aparece una nueva pantalla que permite elegir el mes y visualizar una gráfica de barras con los valores de las temperaturas:

![Pantalla de anal&#xED;tica](../.gitbook/assets/04_chart.png)

En la parte derecha se encuentra la leyenda de la gráfica, que muestra los valores que se han usado para dibujar la gráfica. Si no se encuentra un valor, la gráfica se muestra vacía en ese día \(éste es el caso cuando el mes no tiene ninguna temperatura guardada\). Introduce algún valor de temperatura e intenta visualizar de nuevo la gráfica.

## Detalles de la nueva aplicación

En el apartado anterior se ha mostrado la nueva pantalla que contiene la gráfica. En este apartado se describirán las modificaciones en el código original de forma más detallada.

En el interfaz de usuario hay una nueva sección, cuyo código se puede encontrar en el archivo Main.qml. Esta sección está formada por dos componentes Row con identificadores "chartSectionHeader" y "chartRow" \(se pueden buscar con CTRL + F\) que se han añadido al componente que ya existía.

Para tener una pantalla separada para la gráfica, y su leyenda, se ha añadido un componente nuevo: [PageStack](https://docs.ubuntu.com/phone/en/apps/api-qml-current/Ubuntu.Components.PageStack). Este componente permite navegar entre pantallas, es decir, actúa como una pila \(stack\). La pantalla que está en la parte superior de la pila es la que se muestra. Para añadir o eliminar una pantalla de la pila se usan los métodos "push" y "pop".

Cuando se inicia la aplicación, es necesario añadir \(push\) la primera pantalla en el componente PageStack. Ésto se realiza en el evento "onComplete" del propio componente. Puedes consultar el capítulo anterior para tener más información sobre el funcionamiento del evento "onComplete".

La inicialización se hace con el siguiente código:

```javascript
// Listing 01
PageStack {
  id: pageStack

  /* set the firts page of the application */
  Component.onCompleted: {
    pageStack.push(mainPage);
  }

    // a set of Page components
 }
```

Con el código anterior se añade \(push\) en la pila la pantalla con el id "mainPage". Este identificador es el que tiene la pantalla inicial de la aplicación. Prueba a buscar el id en el código.

Ahora se puede buscar el punto de entrada a la pantalla de la gráfica: el botón "Chart". Su código, que se encuentra en el archivo Main.qml es el siguiente:

```javascript
// Listing 02
Button {
  id:showChartButton
  text: i18n.tr("Chart")
  width: units.gu(14)
  color: UbuntuColors.green
  onClicked: {
    pageStack.push(chartPage)
  }
}
```

Cuando el usuario pulsa el botón \(es decir, se dispara el evento "onClick"\) se añade una nueva pantalla a la parte superior de la pila del componente PageStack. Al hacerlo se remplaza la pantalla que tuviera por la pantalla nueva. ¿Qué pantalla se muestra? Es la pantalla con el id "chartPage". Prueba a pulsar CTRL sobre la variable pageStack para acceder al código. Otra opción es buscar el código con CTRL + F\).

Al usar cualquiera de los dos métodos anteriores se llegará al siguiente código:

```javascript
// Listing 03
Component {
    id: chartPage
    ChartPage{}
}
```

Esa sentencia define e importa un componente personalizado que está contenido en el archivo ChartPage.qml \(prueba a pulsar CTRL + botón izquierdo sobre ChartPage{} para abrir el archivo\).

Nota: el listado 03 muestra el componente personalizado, que está definido en un archivo externo y se puede importar en otros archivos QML. En nuestro caso, el componente QML es la pantalla \(Page\) con el id "chartPage".

En ChartPage.qml se encuentra la siguiente implementación:

```javascript
// Listing 04
Page {
     id: chartPage

     //... other code omitted for shortness
 }
```

## La pantalla chartPage en detalle

El flujo de navegación de la aplicación ya está explicado. Ahora nos podemos centrar en la pantalla que crea la gráfica, cuyo código fuente se encuentra en el archivo ChartPage.qml.

Nota: se ha omitido parte del código porque contiene componentes que ya se han presentado antes en el curso \(selector de fecha, botones, etc.\).

La lógica de ejecución comienza cuando el usuario pulsa el botón "Show Chart". El código que se ejecuta cuando el evento "onClick" se dispara es el siguiente:

```javascript
// Listing 05
onClicked: {
  /* extract the year, month, day from the variable ‘targetDate’ */
  var dateParts = chartPage.targetDate.split("-");
  /* build a JS Date object using string tokens (month is 0-based) */
  var date = new Date(dateParts[0], dateParts[1] - 1, dateParts[2]);
  /* calculates first and last day of the month */
  var firstDayMonth = new Date( date.getFullYear(),date.getMonth(), 1);
  var lastDayMonth = new Date( date.getFullYear(), date.getMonth() + 1, 0);
  /* set the data-set at the chart and make visible the chart and legend */
  temperatureChart.chartData = ChartUtils.getChartData(firstDayMonth,lastDayMonth);

  /* make visible the chart and the legend */
  temperatureChartContainer.visible = true;     
 }
```

Cuando el usuario elige un mes en el selector de fechas, se guarda este valor en la variable de JavaScript llamada "chartPage". La variable guarda la fecha en el formato yyyy-mm-dd \(año, mes, día\). Puedes consultar los detalles en el componente "popoverTargetMonthPicker".

Después de dividir la fecha \(ver el listado 05\), se crea un objeto Date de JavaScript. Se usarán los métodos nativos de este objeto para calcular el primer y el último día del mes elegido. Estos cálculos se tienen que hacer en tiempo de ejecución porque no se conoce inicialmente la elección del usuario.

Los días primero y último del mes se pasan como parámetros en el método de JavaScript "getChartData", que crea el dataset XY para la gráfica:

```javascript
// Listing 06
temperatureChart.chartData= ChartUtils.getChartData(firstDayMonth,lastDayMonth);
```

El método devuelve un dataset que se guarda en el campo "chartData" del componente QML "temperatureChart" \(que corresponde a la gráfica\):

```javascript
// Listing 07
/* The monthly temperature chart */
QChart{
  id: temperatureChart;
  width: parent.width
  height: parent.height;
  chartAnimated: false;

  /* for all the options see: QChart.js */
  chartOptions: {"barStrokeWidth": 0};

  /* chartData: set when the user press 'Show Chart' button*/
  chartType: Charts.ChartType.BAR;
}
```

El listado 07 muestra la declaración del componente QML de la gráfica, que está definido en Qchart.js y se ha descrito al principio del capítulo. Observa cómo se ha indicado que es una gráfica de barras.

## Creación de la gráfica en detalle

Lo que ocurre a nivel de componente QML se ha descrito en el apartado anterior. Ahora se estudiará el código JavaScript que configura el dataset de la gráfica.

El archivo ChartUtils.js \(que se ha importado en el archivo QML\) contiene todos los métodos que se han usado para crear la gráfica de temperatura. Observa que el archivo ChartUtils.js se ha creado en esta aplicación y NO pertenece a ninguna librería.

Revisemos el archivo.

El punto de entrada es el método `function getChartData(fromDate, toDate)`, que se llama cuando se pulsa el botón "Show Chart". En ese método se calculan el día inicial y el día final del mes.

El método se divide en varios pasos:

### Paso 1

En el primer paso se calculan los días que tiene el mes elegido \(de 28 a 31\) usando el método "getDifferenceInDays" que está definido en el archivo DateUtils.js:

```javascript
// Listing 08

/* the amount of days for the target month (e.g.: 30) */
var monthDays = DateUtils.getDifferenceInDays(fromDate,toDate);
```

### Paso 2

Se prepara un array de JavaScript \(con una pareja clave - valor\) cuyo tamaño es el número de días. Ésta será la base para el dataset de la gráfica. La clave en el array es la fecha del mes elegido mientras que el valor es el valor de la temperatura. Todos los valores de entrada se inicializan a 0 \(cero\). Esta decisión es necesaria para gestionar los casos en los que no hay un valor, por ejemplo, el usuario ha olvidado introducirlo. Los valores que no tienen temperatura se mostrarán como "N/A" en el interfaz de usuario.

Nota: la elección de "cero" es una decisión de diseño, pero es posible elegir otro valor. El código del paso 2 es el siguiente:

```javascript
// Listing 09
function prepareEmptyDataset(fromDate, monthDays){

  /* Init an empty associative array */
  var xyDataSet = {};
  for(var i=0;i < monthDays+1; i++) {
      /* initialize to zero the temperature value for the date */
      xyDataSet[DateUtils.addDaysAndFormat(fromDate, i)] = 0;
  }
  return xyDataSet;
}
```

### Paso 3

En el tercer paso se extraen, de la base de datos SQLite, los valores de temperatura del mes elegido. Después se actualizan los valores clave - valor del array que se ha preparado en el apartado anterior. Para conocer la forma de acceder a una base de datos de SQLite usando QML te invitamos a leer el capítulo anterior.

Esta acción la realiza el método de JavaScript `function updateXYdataset(fromDate, toDate, xyDataSet)`. Se omite el código completo para simplificar la explicación.

Cuando ha finalizado la actualización del array, el dataset de la gráfica contendrá valores diferentes de cero en los días que ha actualizado el usuario. En los otros valores se mostrará el valor por defecto que se asignó en la inicialización. Ahora ya está preparado el dataset XY para la gráfica.

### Paso 4

En el cuarto paso hay que separar los valores X e Y en dos conjuntos distintos. Para hacerlo se utilizan los métodos de JavaScript:

* `function getXaxisValue(xyDataSet)`:  extrae las claves del array, corresponde a los valores de las X.
* `function getYaxisValue(xyDataSet)`: extrae los valores del array, corresponden a los valores de las Y.

Esta división del dataset es necesaria porque el objeto JavaScript que utiliza la librería de gráficas trabaja de esta forma \(ver el siguiente paso\).

### Paso 5

En el último paso se crea el objeto JavaScript que se asigna al componente QChart \(se ha declarado en el listado 7\).

```javascript
// Listing 10
var ChartBarData = {
  labels: < our X axis values extracted at step 4 above>,
  datasets: [{
    fillColor: "rgba(220,220,220,0.5)",
    strokeColor: "rgba(220,220,220,1)",
    data: < our Y axis values extracted at step 4 above>
    }
  ]
}
```

La asignación al componente QChart se hace con la sentencia `temperatureChart.chartData = ChartUtils.getChartData(firstDayMonth,lastDayMonth);` donde "temperatureChart" es el identificador de nuestro componente QChart \(listado 7\). "charData" es un campo que tiene los datos del dataset.

Nota: en algunas ocasiones, cuando el dataset de la gráfica se conoce previamente o no depende de elecciones del usuario \(el mes elegido en nuestro caso\), se puede inicializar el campo "chartData" cuando se declara el componente QChart \(en el listado 7\) en lugar de hacerlo después.

## Leyenda de la gráfica

En la parte derecha de la gráfica se muestra una leyenda con los valores XY que se han usado para dibujar la gráfica. No es obligatorio usarla, se ha añadido para completar la gráfica y dar al lector nuevas ideas para personalizar la gráfica.

La leyenda se crea en **ChartPage.qml** usando un componente ListModel de QML. Este componente actúa como un contenedor para los valores que hay que mostrar. El modelo se rellena en el siguiente método \(ver ChartUtils.js\).

```javascript
// Listing 11

/* fill the Listmodel used to create the Chart legend */
function getChartLegendData(xyDataSet){
  customRangeChartListModel.clear();
  for(var key in xyDataSet) {
    customRangeChartListModel.append({"date": key,"temp":xyDataSet[key]});
  }
}
```

Se itera sobre todos los valores XY del dataset y se extraen los valores \(como un objeto clave - valor de JavaScript\) para usarlos en el ListModel. Os invitamos a consultar la documentación oficial para tener más información acerca de ListModel. Se ha elegido no profundizar en el funcionamiento porque se queda fuera del objetivo de este capítulo.

Para mostrar los valores de un ListModel se utiliza el componente [UbuntuListView Component](https://docs.ubuntu.com/phone/en/apps/api-qml-current/Ubuntu.Components.UbuntuListView). La implementación y la configuración del componente UbuntuListView se puede encontrar en el archivo ChartPage.qml. Aquí se pueden ver las partes más importantes, se ha omitido el resto por brevedad.

```javascript
// Listing 12
/* ListView that display the values in the legend */
UbuntuListView {
  id:chartLegendListView
  anchors.fill: parent

  /* disable the dragging of the model list elements */
  boundsBehavior: Flickable.StopAtBounds
  model: /* id of the ListModel containing the data */

  delegate:
    /* ‘delegate’ is the component that define the layout
    used to display the value from the ListModel */
    Component{
      id: customReportChartLegend
      Rectangle {
        id: wrapper
        height: legendEntry.height
        border.color: UbuntuColors.graphite
        border.width:units.gu(0.5)

        Label {
          id: legendEntry
          /* ‘key’ and ‘temp’ are the key used to
          store date and temperature values in the
          ListModel */
          text: date+" :  "+temp
          fontSize: Label.XSmall
        }
      }
    }
}
```

Sólo un par de detalles sobre el funcionamiento de UbuntuListView: recibe una entrada \(con un campo llamado "model\)"\) que contiene los valores a mostrar y un componente QML con el layout que se utilizará para mostrarlos en la pantalla \(campo "delegate"\). El componente "delegate" accede a los valores del ListModel usando las claves que se han usado al guardarlos \("date" y "temp", en el listado 11\).

## Conclusiones

Se ha mostrado como dibujar gráficas utilizando una aplicación con QML + JavaScript que usa una base de datos SQLite. Para conseguirlo se han tomado algunas decisiones de diseño, como usar una gráfica de barras o el valor "cero" en los valores de temperatura que estaban vacíos. Puede ser que algunas decisiones sean discutibles pero el objetivo es proporcionar al lector un conocimiento acerca de la forma de dibujar gráficas con QML y sugerir nuevas ideas para nuevas aplicaciones.

Como ejercicio, el lector puede probar otro tipo de gráficas o añadir otra gráfica para mostrar los valores de presión de la ciudad...

Invitamos al lector a consultar el código fuente \(y los comentarios\) de la aplicación para tener más detalles del funcionamiento

## Referencias

Algunos enlaces relacionados con los conceptos explicados en este capítulo:

* [Ubuntu touch QML API](https://docs.ubuntu.com/phone/en/apps/api-qml-current)
* [The repository of Qchart.js library](https://github.com/jwintz/qchart.js)

## Personas que han colaborado

* Fulvio Russo: autor.
* Miguel Menéndez: traducción al español.

