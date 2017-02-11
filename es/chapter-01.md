# Introducción del curso
Este curso es un proyecto nuevo que empiezo para aprender a programar aplicaciones que funcionen en Ubuntu Touch. En el proceso iré generando documentación con todas las fases de creación de una aplicación: toma de requisitos, implementación y distribución de la aplicación en la Tienda de Ubuntu. Uno de los problemas que encontramos en Ubuntu Touch es la falta de aplicaciones tanto en número como en funciones de las mismas. No espero cambiar esa situación a corto plazo, pero una forma de cambiarla es programando aplicaciones y ayudando a otros usuarios a que hagan lo mismo. Sólo de esta forma podrá cambiar la situación en un futuro.

Este curso no es una clase magistral en la que explico las cosas y los demás las hacen de forma automática. La idea es publicar capítulos y con la realimentación de los usuarios completar la documentación del curso. Si en un bloque concreto hay más interés se puede profundizar más. No hay ningún problema en preguntar las dudas en los medios asociados al curso.

Por último quiero agradecer a los usuarios que me han animado a empezar esta locura, entre ellos están kain_X_X o LarreaMikel. Un curso de este tipo no lo puede hacer un único usuario. Sólo cuando participan muchas personas se puede evolucionar y conseguir metas más grandes.

# Conocimientos previos
Por la temática del propio curso es necesario tener una base mínima con los conceptos básicos de programación. En el curso se va a emplear principalmente QML para el interfaz del usuario y JavaScript o C/C++ para la lógica. Ayuda conocer cualquiera de los dos lenguajes aunque no será algo crítico. En cada capítulo se explicarán los elementos básicos y se incluirá una bibliografía para que el usuario la consulte si tiene dudas.

Las herramientas de programación de Ubuntu Touch (SDK) están preparadas para la distribución Ubuntu. Será por tanto necesario utilizar Ubuntu o cualquiera de las distribuciones que la toman como base. Si no se cumple este requisito tampoco es un problema grave ya que se puede hacer lo mismo con una máquina virtual o usando un Live USB.

Para hacer más cómodo de seguir el curso, sólo pondré las partes más importantes del código fuente. El resto de los archivos estarán en un repositorio de código fuente en Launchpad. Vendría bien conocer el funcionamiento básico de Bazaar.

# Objetivos del cruso
El primer objetivo del curso es aprender a programar aplicaciones para Ubuntu Touch mientras te diviertes. La programación es una tarea que absorbe mucho tiempo y tiene que gustarte lo que estás haciendo. Las aplicaciones pueden ser sencillas o complejas, lo importante es que resuelvan una necesitad que tenemos. Por ejemplo, una aplicación que tenga un listado de las plantas del jardín y nos avise cuando tengamos que regarlas.

Un buen diseño en la lógica de la aplicación puede reducir el tiempo de desarrollo de forma importante. De la misma forma un mal diseño puede hacer que acabemos tirando el código a la papelera de reciclaje y empezando de nuevo porque es más sencillo.

# Tipos de aplicaciones
Ubuntu Touch tiene tres tipos de aplicaciones. Se pueden encontrar aplicaciones Web (WebApps), Scopes y Aplicaciones nativas. Una aplicación web es básicamente una pestaña del navegador Web que se ejecuta de forma independiente. Tiene un icono propio en Unity (el lanzador de aplicaciones) y puede contener información remota de cualquier tipo. Por cuestiones de seguridad una aplicación Web no tiene acceso al contenido local del terminal.

![Ejemplo de WebApp](/assets/chapter-01/01_webapp.png)

El scope es el segundo tipo de aplicación que encontramos en Ubuntu Touch. En cierta medida se comporta como una pantalla que muestra información al usuario. La información puede ser externa, por ejemplo la previsión del tiempo, o interna en forma de agregador de información. Un ejemplo de este caso sería el scope "Hoy" porque muestra la información de diferentes aplicaciones.

![Ejemplo de Scope](/assets/chapter-01/02_scope.png)

Por último tenemos las aplicaciones nativas. En este caso las aplicaciones pueden acceder a todos los recursos del teléfono y son en principio más complejas que las aplicaciones Web y los scopes. Las aplicaciones están confinadas en Ubuntu Touch y sólo pueden acceder a su información propia. Si queremos acceder a la información de otras aplicaciones es necesario acceder al content-hub. Como ejemplo de aplicación nativa tenemos el calendario.

![Ejemplo de Aplicación nativa](/assets/chapter-01/03-native_app.png)

# Evolución del curso
Un detalle que quiero comentar (y que os cansaréis de que lo repita a lo largo del curso) es que este curso no es una clase magistral. Es importante que participéis ya sea con dudas, sugerencias o errores. El orden de los capítulos puede variar y los capítulos que ya estaban cerrados se pueden abrir para añadir nuevo contenido. Es algo vivo y que sólo puede mejorar si todos intervenimos en él. No importa que las dudas sean muy básicas ni el que dirán el resto de usuarios. Si sigues el curso es para aprender. Recuerda: la única pregunta estúpida es la que no se hace.

En la lista de correo el acceso es público y cualquiera que tenga una cuenta de Launchpad puede entrar. No hay ningún tipo de filtro excepto varios casos de sentido común:
* Las consultas tienen que estar relacionadas con el curso.
* No se admite spam de ninguna clase.
* No se admiten ataques a los usuarios de ningún tipo.

Cuando se dé alguno de esos casos primero se avisará y si el usuario continúa con su actitud tendrá que salir de la lista de correo. Espero no tener que llegar en ningún momento a ese extremo.

El curso no está preparado en el sentido que lo escribiré semana a semana. Por esta razón es posible que se cuele algún error a medida que lo hago. Si se diera el caso no dudéis en indicarlo para modificarlo y hacer poco a poco un curso consistente y que sirva para muchos usuarios. Es una oportunidad de crear contenido que no existe en la red y darle un empujón a Ubuntu Touch.

# Recursos
* Lista de correo: https://launchpad.net/~curso-ubuntu-phone-touch
* Tablerto de Trello: https://trello.com/b/s55CcaLp/curso-de-programacion-espanol

# Colaboradores
* Larrea Mikel: revisión de los capítulos.
