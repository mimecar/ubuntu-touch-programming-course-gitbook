# chapter-05-s01

## Acceso a base de datos con QML

En este capítulo se mostrará cómo acceder a una base de datos _SQLite_ desde una aplicación que use _QML_ y JavaScript. _SQLite_ es la base de datos que se usa en _Ubuntu Touch \(UBports\)_ \(y otros dispositivos móviles\). Para más información sobre SQLite se puede consultar su [Web](https://www.sqlite.org/) y los enlaces que hay al final del capítulo.

Los prerrequisitos necesarios para entender este tutorial son un conocimiento de QML, JavaScript, el SDK de Ubuntu Touch con su IDE y un conocimiento del lenguaje SQL básico. Si no cumples estos prerrequisitos, te invitamos a leer las secciones dedicadas del curso QML y el tutorial sugerido al final del capítulo.

**Premisa**: para el acceso a la base de datos se usará el objeto _QtQuick.LocalStorage_ \(proporcionado por la librería QT\) y no _U1Db-QT_ \(el módulo QML creado por [Canonical](https://docs.ubuntu.com/phone/en/apps/api-qml-development/U1db.tutorial)\).Esta elección se debe al hecho que _U1Db-QT_ parece que está incompleto y no es tan completo como la solución que usa JavaScript.

## Introducción

En este capítulo se mostrará una aplicación QML como ejemplo para las operaciones típicas de una base de datos: crear, buscar, actualizar y borrar \(es una aplicación C.R.U.D. por sus iniciales en inglés\). Esta aplicación de ejemplo tendrá un interfaz de usuario básico \(nos concentraremos en la parte del acceso a la base de datos\) y no tendrá una utilidad real; se ha elegido y creado sólo por su relevancia didáctica.

**Nota:** este capítulo no cubre los pasos necesarios para empaquetar, probar e instalar la aplicación \(se verá en el siguiente capítulo\).

## La aplicación de ejemplo: presentación funcional

La aplicación que se estudiará es un "registrador de temperatura del tiempo". Usándola, el usuario puede insertar el valor de la temperatura diaria de su ciudad favorita y gestionar las temperaturas guardadas: actualizarlas, borrarlas o buscarlas. En la primera ejecución, la aplicación muestra valores por defecto para la ciudad elegida y para la unidad que usa la temperatura \(por ejemplo, grados Celsius o Fahrenheit\). El usuario puede aceptar los valores sugeridos o añadir unos propios \(la configuración guardada puede ser actualizada más tarde si el usuario lo desea\).

Cada día el usuario puede introducir _sólo_ un valor de temperatura. Los valores almacenados se pueden buscar por fecha. Si se encuentra un valor, es posible modificarlo o eliminarlo. También es posible ejecutar el borrado de todos los valores de temperatura almacenados. La aplicación debe proporcionar un sistema de validación para evitar inserciones no válidas, como valores vacíos o no numéricos.

Empecemos: 1. Configura el SDK de Ubuntu y el IDE como se describe en los primeros capítulos de este curso creando un kit "Desktop". 2. Descarga e importa el proyecto de ejemplo \(llamado \("WeatherRecorder"\)\) en el IDE de Ubuntu \(File → Open File or Project... → elige la carpeta que contiene el archivo ‘WeatherRecorder.pro’\).

En la importación, el SDK de Ubuntu pregunta el "kit de destino" para el proyecto \(es decir, la plataforma en la que se ejecutará la aplicación\). Se pretende ejecutar el proyecto en el IDE, por lo que hay que seleccionar el kit "Desktop" o crearlo si no existe previamente. Puedes encontrar más información sobre los "kits" en los primeros capítulos del curso.

Al finalizar el proceso de importación, expande los nodos de la vista de "Project". La estructura tiene que ser similar a la siguiente:

![&#xC1;rbol de archivos del proyecto](../.gitbook/assets/01_application_source%20%283%29.png)

Siéntete libre de abrir y explorar cualquier archivo y de estudiar la estructura del proyecto antes de continuar. Antes de centrar nuestra atención en el acceso a la base de datos, se comentarán algunos conceptos que pueden ser interesantes en el futuro.

El punto de entrada de una aplicación QML es el archivo _Main.qml_, contiene el código que crea e inicia la aplicación completa. Al ejecutar por primera vez la aplicación \(pulsa el botón "Play" que hay en la esquina inferior izquierda\) se mostrará un diálogo de configuración que tendrá valores por defecto. Si los valores son correctos, pulsa el botón "Save", en caso contrario modifica los valores antes de guardarlos.

La siguiente imagen muestra la página de configuración en el primer inicio: ![Asistente de configuraci&#xF3;n](../.gitbook/assets/02_application_configuration_wizard.png)

Veamos con más detalle lo que ocurre cuando se inicia la aplicación. Si abres el archivo _Main.qml_ encontrarás este código \(también se puede usar CTRL + F para realizar una búsqueda\):

```javascript
Component.onCompleted: {
    //some code... omitted for shortness
}
```

Todo el código de este bloque se ejecutará cuando se dispare el evento _onCompleted_. En este caso, cada vez que la aplicación se ejecute, porque el código mencionado se encuentra dentro del componente _QML_ Page. El evento _onCompleted_ se dispara cuando se inicializa cualquier componente QML. De esta forma se puede usar para inicializar sus valores en el propio componente en lugar de hacerlo en la aplicación.

Por ejemplo:

```javascript
Label{
    id:myLabel
    width: units.gu(10)
    Component.onCompleted: {
        myLabel.text = /* get his value from the database */
    }
}
```

Cuando el evento _onComplete_ de la etiqueta se dispara \(es decir, cuando se dibuja\), se ejecuta el código que inicializa la etiqueta \(por ejemplo, cargando el texto a mostrar de la base de datos o de otra fuente\). Por lo general, el evento _onComplete_ se usa para realizar ciertas tareas de inicialización, tanto a nivel de componente como de aplicación.

En nuestro caso, el evento _onComplete_ de la aplicación realiza dos operaciones: 1. Creación de la base de datos y de las tablas \(se verá en la siguiente sección\). 2. Insertar los valores por defectos \(es decir, los que se muestran en el diálogo de configuración cuando se muestra en el primer inicio\).

En este punto, alguien podría decir: "... no queremos realizar la misma operación cada vez que se ejecuta la aplicación". Esto es cierto, para prevenirlo, se ha usado el componente _Settings_. Este componente proporciona persistencia en la configuración, es decir, almacena las preferencias del usuario u otras personalizaciones en un archivo de configuración.

En el archivo _Main.qml_ tenemos:

```javascript
Settings {
    id:settings
    /* flag to show or not the App configuration pop-up */
    property bool isFirstUse : true;
}
```

Se declara una propiedad booleana llamada _isFirsUse_ con el valor _true_. Después de realizar las operaciones de inicialización, dentro del bloque _onComplete_ se cambia su valor a _false_. Con esta solución, la próxima vez que se ejecute la aplicación no se mostrará el diálogo de configuración ni se inicializará la base de datos.

Para diferenciar la primera ejecución de las siguientes, se usa un _if_ para hacer la comprobación, por lo que la lógica de decisión es la siguiente:

```javascript
Component.onCompleted: {
    if(settings.isFirstUse)
    {
        //create database, insert default data
    }else{
        //if necessary do something else
    }
}
```

¿Dónde se están guardando los valores de la configuración \(es decir, el flag "isFirstUse\)? Los valores se guardan en un archivo .conf que se encuentra en la carpeta:

```bash
~userHome/.config/<applicationName>/<applicationName>.conf
```

En nuestro caso,  es _weatherrecorder_ \(éste es el valor asignado en el parámetro "applicationName" del archivo _Main.qml_\). Siéntete libre de abrir el archivo, es un archivo de texto. Al eliminarlo o poner a _true_ el valor del flag, se ejecutarán de nuevo las operaciones de inicialización \(pruébalo si quieres\).

Fíjate también que en el código del proyecto hay también un archivo llamado "Settings.qml". Debe contener todos los flags / variables que se quieran almacenar en el archivo _weatherrecorder.conf_. El componente _Settings_ se puede usar para implementar cualquier otra lógica, no sólo la que se ha descrito aquí.

Después de guardar la configuración, la página de inicio de la aplicación es la siguiente: ![P&#xE1;gina de inicio de la aplicaci&#xF3;n](../.gitbook/assets/03_application_home_page%20%281%29.png)

El interfaz de usuario es bastante simple por lo que se omite su descripción. Antes de continuar, es recomendable probar y conocer con más confianza el comportamiento de la aplicación. Se comentarán algunos conceptos sobre la barra de encabezado de la aplicación que podrían ser útiles para otras aplicaciones. Se pueden ver dos barras de "acción": una en la esquina superior derecha y otra en la superior izquierda.

La barra de la izquierda se llama _leadingActionBar_, mientras que la de la derecha se llama _trailingActionBar_. Busca en el archivo Main.qml su código para conocer como están implementadas \(realiza una búsqueda en el archivo con CTRL + F\).

En las siguientes secciones veremos el foco del capítulo: el acceso a la base de datos desde una aplicación QML + JavaScript.

## Introducción al acceso de la base de datos

Como se ha comentado antes, no se usa el API de _U1Db-QT_ sino el de QtQuick.LocalStorage para acceder a la base de datos. La base de datos utilizada por _Ubuntu Touch \(Ubports\)_ es [SQLite](https://www.sqlite.org), un archivo que contiene la base de datos y soporta sentencias del lenguaje SQL. Para usarlo esde una aplicación _QML_ no es necesario instalar aplicaciones de terceros: todo lo necesario está en _QML_ y el _SDK de Ubuntu_.

La lógica de acceso a la base de datos está en el archivo Storage.js \(no hay una convención en el nombre, se puede usar otro o usar un patrón de DAO\). El siguiente ejemplo está tomado de ese archivo.

Se tiene que importar el módulo _QtQuick.LocalStorage_ en todos los archivos _QML_ que lo necesitan, la sentencia es:

```javascript
import QtQuick.LocalStorage 2.0
```

También es necesario importar el archivo JavaScript que contiene los métodos de acceso a la base de datos \(Storage.js en nuestro caso\):

```javascript
import "Storage.js" as Storage
```

Ésta es la sintaxis para importar cualquier archivo JavaScript desde QML, no es exclusivo del ejemplo. Para llamar a los métodos que están en el archivo _Storage.js_ se usa el alias _Storage_ \(se sugiere que el nombre sea similar al que tiene el archivo JavaScript\). Recuerda que los nombres de los alias deben tener la primera letra mayúscula.

## Interacción con la base de datos

A continuación, se describirán las operaciones de la base de datos que realiza la aplicación \(consulta el archivo Storage.js para ver todo el código\).

## Creación de la base de datos y de las tablas

En cualquier aplicación _QML_ que utilice una base de datos, la primera operación a realizar es crear la base de datos y las tablas. Esta operación \(como muchas otras\) requiere de una conexión con la base de datos. La conexión se crea con el objeto _QTquick.LocalStorage_ y es una buena práctica crear un método en JavaScript que devuelva una referencia a la conexión, como el que se muestra a continuación:

```javascript
function getDatabase() {
    return LocalStorage.openDatabaseSync("weatherRecorder_db", "1.0", "StorageDatabase", 1000000);
}
```

Para conocer más detalles de los parámetros del API _openDatabaseSync_ se puede consultar: [http://doc.qt.io/archives/qt-5.5/qtquick-localstorage-qmlmodule.html](http://doc.qt.io/archives/qt-5.5/qtquick-localstorage-qmlmodule.html) \(se omite por brevedad\). Antes de realizar cualquier operación es necesario obtener una conexión con la base de datos. Para este propósito, la función anterior puede ser reutilizada en cualquier otra función JavaScript que necesite la base de datos \(ver archivo _Storage. js_\).

Cuando se obtiene una conexión, es posible ejecutar consultas _SQL_. Nota: la conexión con la base de datos se cierra de forma automática cuando se activa el recolector de basura de JavaScript \(es un proceso de limpieza que ejecuta el motor de JavaScript para liberar memoria\).

Veamos un ejemplo de creación de la base de datos \(se omite el bloque de código completo para tener una visión más compacta\). Consulta el archivo de código fuente para más detalles:

```javascript
/* create the necessary tables */
function createTables() {
    var db = getDatabase();
    db.transaction(
        function(tx) {
            tx.executeSql(' < SQL Create statement 1 > ');
            tx.executeSql(' < SQL Create statement 2 > ');
        });
}
```

Nota: la primera sentencia es la llamada al método _getDatabase\(\)_, que devuelve una conexión a la base de datos. De manera similar al código anterior, cada vez que se quiera realizar una consulta a la base de datos \(cualquier operación C.R.U.D\), es necesario abrir una transacción que contiene la sentencia _SQL_ a ejecutar.

Si el método callback lanza una excepción \(por ejemplo, al encontrar un error\), la transacción se revierte \(una "transacción" es un conjunto de operaciones que se tienen que ejecutar con éxito, si una de ellas falla, se deshacen las operaciones para recuperar el estado original\).

En nuestro ejemplo, en el método _createTables\(\)_ se crean dos tablas: una para guardar los valores de temperatura y otra para los parámetros de configuración.

La sentencia SQL que crea la table de _configuration_ es:

```sql
CREATE TABLE IF NOT EXISTS configuration(id INTEGER PRIMARY KEY AUTOINCREMENT,
    param_name TEXT, param_value TEXT)
```

Para la tabla de _temperature_ la sentencia es:

```sql
CREATE TABLE IF NOT EXISTS temperature(id INTEGER PRIMARY KEY AUTOINCREMENT, date TEXT, temperature_value REAL)
```

\(El código anterior se introduce entre &lt; SQL Create y &gt;.

Obviamente, el tipo de datos de las columnas tienen que ser los que soporte la base de datos _SQLite_ \(el tipo "date" no está soportado por lo que se define como TEXTO\). Se usa un campo llamado "id" que está marcado como clave primaria \(PK\) en las dos tablas.

Este campo "id" está definido como _AUTOINCREMENT_ de forma que cada vez que se añada un registro en la tabla se incremente de forma automática. No es obligatorio usar un campo con autoincremento como clave primaria \(PK\) si hay cualquier otro campo que pueda identificar de forma únivoca un registro en la tabla \(se ha elegido usarlo para proporcionar otra función útil de SQLite\).

Después de ejecutar la sentencia del método _createTables\(\)_ se creará el archivo de la base de datos y se introducirán dos tablas.

Se ha comentado antes que _SQLite_ es una base de datos basada en archivos, pero ¿dónde se guardan los archivos de la base de datos? La localización depende del nombre de la aplicación. De forma general, la ruta completa es:

```bash
/<home-folder>/.local/share/<applicationName>/Databases/
```

donde  es el valor del parámetro _applicationName_ que está definido en el archivo _Main.qml_.

```javascript
applicationName: "weatherrecorder"
```

De esta forma, la base de datos se encuentra en:

```bash
/<home-folder>/.local/share/weatherrecorder/Databases/
```

En los dispositivos que usan _Ubuntu Touch \(UBports\),  es_ /home/phablet/_, en el escritorio hay que remplazar "phablet" por el nombre del usuario que ha iniciado sesión \(escribe el comando_ whoami\* en una consola para conocer el nombre si no estás seguro\).

Ese directorio contiene dos archivos: un archivo _.sqlite_ y un archivo _.ini_. La base de datos se encuentra en el archivo con extensión _.sqlite_ \(el nombre es un identificador único definido por _QTQuick.LocalStorage_\). El archivo _.ini_ es un archivo descriptor asociado al archivo _.sqlite_. No te preocupes por el nombre que tienen, no es necesario recordar el nombre para trabajar con ellos.

Para ver las dos tablas y ejecutar consultas _SQL_, abre el archivo _.sqlite_ con un interfaz gráfico como [Sqliteman](https://sourceforge.net/projects/sqliteman/), que se puede encontrar en el "Centro de software de Ubuntu".

Si abres el archivo de la base de datos con _Sqliteman_ se verá una pantalla similar a la siguiente: ![Interfaz de usuario de Sqliteman](../.gitbook/assets/04_sqliteman_user_interface.png)

En la parte izquierda se pueden ver las tablas que tiene la base de datos \("configuration" y "temperature"\) con sus columnas. En la parte superior derecha hay un área de texto en la que se pueden ejecutar sentencias _SQL_. Por ejemplo, escribe la siguiente sentencia y pulsa el botón "Play" de la barrra de menú:

```sql
select * from configuration;
```

El resultado es el contenido de la tabla _configuration_ \(es decir, los parámetros de configuración que se han introducido en el primer inicio de la aplicación\). Si pulsas con el botón derecho sobre el nombre de la tabla es posible realizar algunas tareas de administración \(añadir o editar columnas, borrar la tabla y otras tareas similares\).

Obviamente, cualquier modificación que se haga con _Sqliteman_ afecta a la aplicación que usa la base de datos \(por ejemplo, si se elimina una tabla la aplicación fallará al inicio porque es necesario adaptar antes la aplicación\).

En el desarrollo con _QML_ y _JavaScript_, _Sqliteman_ \(o cualquier herramienta similar\) se usa sólo para comprobaciones, como ejecutar sentencias SQL que extraigan información o añadan / editen información. Por ejemplo, para insertar datos de ejemplo o corregir los que sean incorrectos. Se estudiarán las sentencias _SQL_ en las siguientes secciones:

### Operaciones de búsqueda

Con una operación de búsqueda se buscan en la tabla\(s\) de una base de datos un registro\(s\) concreto que cumpla ciertos criterios. Esa operación se realiza con la sentencia _select_ de _SQL_, que devuelve un conjunto de ceros o N filas de tabla que cumplen el criterio \(para nosotros _registros_ y _filas de la tabla_ son conceptos equivalentes\). El _set_ que devuelve está generalmente identificado con el nombre _result-set_.

Se puede encontrar un ejemplo de búsqueda en el método _getTemperatureValueByDate\(\)_ que se encuentra en el archivo _Storage.js_. Ese método se ejecuta cuando el usuario realiza una operación de búsqueda en el interfaz de usuario pasando como entrada la fecha del día que contiene el valor de temperatura a conocer.

Aquí está el método JavaScript que realiza la búsqueda:

```javascript
function getTemperatureValueByDate(date){

    var db = getDatabase();
    var targetDate = new Date (date);

    /* return a formatted date like: 2017-04-30 (yyyy-mm-dd) */
    var fullTargetDate = DateUtils.formatDateToString(targetDate);
    var rs = "";

    db.transaction(function(tx) {
       rs = tx.executeSql("SELECT temperature_value FROM temperature t where date(t.date) = date('"+fullTargetDate+"')");
       } );

    /* check if value is missing or not */
    if (rs.rows.length > 0) {
        return rs.rows.item(0).temperature_value;
    } else {
        return "N/A";
    }
}
```

Al principio del método _getTemperatureValueByDate\(\)_ se utiliza _getDataBase\(\)_ \(se ha descrito en el apartado anterior\). Después se llaman a métodos que están en otros archivos JavaScript \(como es el caso de _DateUtils.formatDateToString\(targetDate\)_. La técnica que se usa para ejecutar una sentencia SQL es la misma que la que se ha empleado en la creación de la tabla: crear una transacción y dentro de una función callback introducir la sentencia SQL.

El ejemplo anterior se muestra cómo gestionar los valores que devuelve la consulta _SQL_. Se usa la variable de JavaScript _rs_ que actúa como un handle para acceder a la información devuelta. El acceso a los registros / filas se realiza con el campo _rows_ \(es una palabra clave en JavaScript\). Es un puntero al conjunto de registros que permite acceder a un registro específico o iterar sobre todos ellos.

Por ejemplo:

```javascript
rs.rows.item(0).temperature_value;
```

obtiene el aceso sólo al _primer_ registro que devuelve el result-set \(nota: el primer elemento es el 0\) y desde él se obtiene el valor de la columna de la tabla _temperature\_value_. Se puede hacer esto porque se ha ejecutado una consulta de selección \(_select_\); si se usan otra sentencia SQL \(consulta las siguientes secciones\), la información que devolverá no será un conjunto de registros de la tabla sino un número.

Para iterar sobre los registros, por ejemplo, para imprimir el _temperature\_value_, se debe usar este código JavaScript:

```javascript
for(var i = 0; i < rs.rows.length; i++) {
    console.log("Temperature found:"+ rs.rows.item(i).temperature_value);
}
```

donde _console_ es un objeto que se usa para mostrar mensajes en la consola \(es similar al System.out.println\("a message"\) de Java\).

### Operación de actualización

La operación de actualización se realiza con una consulta _update_ de SQL. Se puede encontrar un ejemplo en el método de JavaScript _updateTemperature\(\)_ que se usa para actualizar el valor de una temperatura ya almacenada.

```javascript
function updateTemperature(date,tempValue){
    var db = getDatabase();
    var fullDate = new Date (date);

    /* return a formatted date like: 2017-09-30 (yyyy-mm-dd) */
    var dateFormatted = DateUtils.formatDateToString(fullDate);
    var res = "";
    db.transaction(function(tx) {

        var rs = tx.executeSql('UPDATE temperature SET temperature_value=?WHERE date=?;', [tempValue,dateFormatted]);

        if (rs.rowsAffected > 0) {
            res = "OK";
        } else {
            res = "KO";
        }
    }
);
return res;
}
```

En el ejemplo anterior se muestra otro uso del resultado de la consulta. El valor devuelto es un número, no un conjunto de registros, debido a que se ha ejecutado una sentencia _SQL_ de actualización. Esta sentencia sólo devuelve el número de fila\(s\) de la tabla que se han actualizado. El valor está en el campo _rowsAffected_ \(el nombre es fijo y no se puede modificar\). En el método se comprueba el valor de filas afectadas y se devuelve una cadena de texto \("OK" / "KO"\). También se puede devolver directamente el número de filas sin añadir lógica extra.

### Operación de inserción

Para introducir un registro / fila en una tabla se utiliza la sentencia SQL _insert_. En nuestro caso se realiza esa operación cuando se introduce un nuevo valor de temperatura:

```javascript
/* Insert a new temperature value in the give date */
function insertTemperature(date,tempValue){
    var db = getDatabase();
    var fullDate = new Date (date);
    var res = "";
    var dateFormatted = DateUtils.formatDateToString(fullDate);

    db.transaction(function(tx) {
        var rs = tx.executeSql('INSERT INTO temperature (date, temperature_value) VALUES (?,?);', [dateFormatted, tempValue]);

        if (rs.rowsAffected > 0) {
            res = "OK";
        } else {
            res = "Error";
        }
    });

    return res;
}
```

La lógica para procesar el valor que se devuelve es similar a la seguida en la operación update, sólo varía la sentencia _SQL_ que se ha ejecutado.

### Operación borrar

La última operación que se puede realizar es la de _borrar_. Por ejemplo, esta operación se realiza cuando el usuario quiere eliminar todos los valores almacenados de la temperatura al pulsar el botón _trash_ de la barra de menú. La función de JavaScript que se usa es:

```javascript
/* Remove ALL the saved Temperature values NOT the tables. Return the number of rows deleted */
function deleteAllTemperatureValues(){
    var db = getDatabase();
    var rs;
    db.transaction(function(tx) {
        rs = tx.executeSql('DELETE FROM temperature;');
    });

    return rs.rowsAffected;
}
```

En este caso hay que recordar que _rowsAffected_ representa el número de filas de la tabla que se han borrado. En este caso se devuelve directamente el número de filas afectadas, no se ha añadido lógica extra. Por ejemplo, se puede asociar el valor que devuelve al valor de una etiqueta _QML_ que lo muestre directamente o se puede usar para otros propósitos.

## Conclusiones

Se ha mostrado la forma de crear desde cero una base de datos _SQLite_ y como introducir y recibir la información guardada usando _QML_ + _JavaScript_. También se ha demostrado como funcione el método _onComplete_ y la forma de guardar las preferencias de usuario o valores personalizados usando el objeto _Settings_. Tanto el proyecto como los bloques de código que se han mostrado se pueden tomar como base para una aplicación personalizada o para mejorar la aplicación actual.

Os invitamos a modificar el código fuente para clarificar las posibles dudas \(esta es la mejor forma de comprender y mejorar los conocimientos\). También se pueden consultar los comentarios que hay en el código fuente.

En el próximo capítulo se estudiarán las gráficas. Se verán los pasos para crear una gráfica que muestre los valores de temperatura almacenados usando la librería _Qchats.js_.

## Referencias

Aquí se encuentran algunos enlaces que permiten profundizar en los conceptos introducidos en el capítulo:

* Lenguaje _SQL_: [https://www.w3schools.com/sql/sql\_intro.asp](https://www.w3schools.com/sql/sql_intro.asp) incluye la sintaxis básica: insertar, actualizar, borrar, seleccionar, crear.
* _SQLite_: [http://www.sqlitetutorial.net/](http://www.sqlitetutorial.net/)
* API de _QML_: [https://docs.ubuntu.com/phone/en/apps/qml/index](https://docs.ubuntu.com/phone/en/apps/qml/index) la referencia oficial para la documentación de Ubuntu Touch.

## Personas que han colaborado

* Fulvio Russo: autor.
* Miguel Menéndez: traducción al español.

