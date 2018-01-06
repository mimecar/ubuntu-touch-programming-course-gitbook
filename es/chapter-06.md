# Introducción
Tenía ganas de empezar un nuevo bloque del curso centrado en el desarrollo de una **aplicación nativa con C / C++ en QML**. Este bloque no es simplemente un cambio en el lenguaje de programación sino que tiene más implicaciones detrás. 

[UBports](https://ubports.com/) está haciendo un trabajo increible manteniendo el proyecto de Ubuntu Touch. No sólo continúan con las actualizaciones sino que están trabajando en migrar el sistema base a Ubuntu 16.04 (LTS). Este trabajo, junto a la posibilidad futura de usar aplicaciones de Android en UBports pueden atraer a muchos usuarios. Si queréis ayudar económicamente al proyecto podéis hacer una pequeña donación en su [cuenta de Patreon](https://www.patreon.com/ubports).

**Programar una aplicación exige mucho tiempo y trabajo**. Lógicamente el programador quiere llegar al máximo número de usuarios y eso, en UBports es complicado de conseguir en estos momentos. Hasta ahora se programaba una aplicación nativa para un dispositivo y luego se probaba en el ordenador. ¿Por qué no cambiamos esta forma de trabajar? 

[Qt es una framework de desarrollo multiplataforma](https://www.qt.io/), que puede funcionar en dispositivos móviles (UBports, Android) y escritorios (GNU/Linux, Windows). Tiene buena documentación y recibe actualizaciones con cierta frecuencia. El mismo código que se usa en el escritorio puede funcionar con relativamente pocas modificaciones en otros dispositivos. De esta forma se podría trabajar en el escritorio con las herramientas nativas y adaptar más adelante la aplicación a UBports. Tendríamos una aplicación que podría funcionar en modo escritorio y en modo dispositivo. Con una base común y un número de usuarios mucho mayor.

Lógicamente las cosas no son tan simples y exigiría algo más de trabajo portar el código a un dispositivo móvil teniendo en cuenta el estado del SDK. Junto con los cambios que está introduciendo UBports no hay que olvidar las herramientas de desarrollo. En estos momentos el SDK es "complicado" de usar. Tiene fallos que no serán corregidos y al final acabas dedicando más tiempo al SDK que a programar la aplicación.

En el curso no me voy a olvidar de programar para UBports. Simplemente cambiaré un poco la filosofía: se programará la aplicación en el escritorio y después se adaptará al dispositivo. De esta forma pueden seguir el curso los usuarios de UBports y los que quieran aprender a programar con Qt y tengan únicamente el ordenador.

¿Os animáis a participar en esta locura?