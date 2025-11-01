::: entry-content
# Instalando un *broker* MQTT doméstico (y IV)

Disculpas nuevamente por el gran lapso de tiempo desde la tercera
entrega de esta serie. Estoy desbordado como hacía tiempo que no estaba.
Bueno, el caso es que ya está aquí la cuarta y última entrega que
prometí para nuestro *broker* MQTT doméstico.

En los tres artículos anteriores abordamos temas interesantes:

-   La actualización del hardware de un Thin Client Fujitsu Futro para
    usarlo como *broker* MQTT doméstico.
-   La instalación del sistema operativo Ubuntu Server y del *broker*
    MQTT Mosquitto.
-   La configuración de todo lo necesario para que el Mosquitto sirviera
    conexiones MQTT sobre TLS.

Quedaba pendiente para este último post la configuración de ACLs y la
autenticación de usuarios utilizando los mecanismos básicos que
proporciona Mosquitto. ¡Adelante con ello!

## Autenticación de usuarios y ACL (Access Control List)

No es la primera vez que me encuentro a personas confundiendo una cosa
con la otra, así que, por aclarar, vamos a ver qué son ambos conceptos y
para que debemos utilizarlos.

### Autenticación de usuarios

Por defecto, tal y como dejamos configurado el *broker* MQTT en la
tercera entrega de esta serie de artículos, cualquier usuario puede
acceder al *broker* y usarlo. Acceder a todos los *topics*, suscribirse,
publicar, etcétera. No es lo que habitualmente queremos. En su lugar, lo
normal es tener muy controlados cuáles son los usuarios con permisos
para conectar al *broker* y hacer uso de sus recursos. Con autenticación
de usuarios, nos referimos precisamente a eso.

En mi caso concreto, en el proyecto para el que necesito este *broker*,
requiero que nadie pueda acceder, por defecto. Y habrá tres o cuatro
usuarios a los que se dará permiso bajo ciertas condiciones (que nada
tienen que ver con aspectos técnicos, sino de negocio). Bien, pues
Mosquitto proporciona mecanismos básicos, de serie, para mantener un
listado de pares usuario/password. Aprenderemos a configurar este
mecanismo par no dejar que nuestro *broker* sea un coladero.

### ACLs

La autenticación de usuarios permite un control de grano grueso sobre
quién accede a los recursos del *broker*. Lo habitual es que primero
permitamos a ciertos usuarios poder conectarse, pero luego necesitaremos
que no todos los usuarios que conecten Mosquitto puedan acceder a
cualquier *topic*. Necesitaremos un control de grano fino. Aunque puede
ser un sistema abierto, lo razonable, recomendable y habitual es que
cada usuario pueda acceder sólo a ciertos *topics* del *broker* MQTT. Y,
además, que se pueda especificar el modo de acceso (lectura o escritura
o, mejor expresado, suscripción y publicación).

Con el mecanismo de ACLs se pueden especificar cosas como *"Que el
usuario 'luis' pueda suscribirse al topic
notificaciones/servidores/servidor1 y sólo a ese y que no pueda publicar
en ningún topic"* y *"que el usuario 'juan' pueda publicar en el topic
notificaciones/servidores/servidor1 y sólo en ese, pero no pueda
suscribirse a ningún topic"*. De esta forma Juan sería un publicador de
contenidos y luis sería un consumidor, ambos trabajando de forma
distinta sobre el mismo *topic*.

Veremos de qué forma el *broker* Mosquitto nos permite hacer esto y qué
opciones adicionales tenemos, como el uso de comodines o parámetros.

## Activando y configurando la autenticación de usuarios en Mosquitto

La forma de activar la autenticación de usuarios en Mosquitto es, como
siempre, tocando algunos parámetros en el fichero de configuración
*mosquitto.conf*. Concretamente el parámetro:

    password_file filepath

Donde se especifica el *path* y nombre de fichero del fichero que
contendrá el listado de pares usuario/password que podrán conectar con
autenticación. Como es algo que hay que hacer sobre el propio *broker*,
nos conectaremos vía SSH y haremos *login* en el Ubuntu Server donde
está instalado el Mosquitto. Editamos el fichero
*/etc/mosquitto/mosquitto.conf* (con permisos de administrador) y
añadiremos estas dos líneas al fichero:

    allow_anonymous false
    password_file /etc/mosquitto/users/passwd

La primera de las líneas indica que no permitiremos la conexión a
usuarios anónimos. Mosquitto permite la conexión de clientes con par
usuario/password simultáneamente a la conexión de clientes no
autenticados. Es posible dar un trato diferencial a unos y otros y en
ciertas ocasiones podría ser útil. En este caso, vamos a eliminar esta
posibilidad y sólo podrán acceder al *broker* los usuarios que estén en
nuestra lista de usuarios, que se encuentra, donde dice la siguiente
línea que hemos escrito, en */etc/mosquitto/users/passwd*.

Este fichero no existe en realidad, lo tenemos que crear. Y lo haremos
en dos pasos. En primer lugar, crearemos el directorio que lo contendrá,
que hasta la fecha no existe. Puede ser cualquiera, pero el que hemos
especificado es el directorio "*users*" dentro */etc/mosquitto*. Lo
creamos.

    sudo mkdir /etc/mosquitto/users

Y ahora, para crear el fichero con los usuarios, estando aún conectados
al Ubuntu del *broker*, haremos uso de la herramienta
*mosquitto_passwd*, que permite crear el fichero, añadir usuarios al
fichero y borrar usuarios del fichero.

    sudo mosquitto_passwd -c /etc/mosquitto/users/passwd usuario1

Esto creará el fichero (debido al parámetro "*-c*") y el primer usuario
que será el usuario "*usuario1*". Una vez ejecutado el comando, nos
solicitará dos veces la *password* para que no cometamos errores al
introducirla. Y... ya tenemos nuestro primer usuario con posibilidad de
autenticarse.

Ahora repetimos el proceso para crear dos usuarios más. Con cuidado de
no poner "*-c*" en el comando, que borraría el fichero y todos los
usuarios. Ese sólo hay que especificarlo la primera vez, para la
creación del fichero. Luego, ejecutamos lo mismo, pero sin ese
parámetro.

    sudo mosquitto_passwd /etc/mosquitto/users/passwd usuario2
    sudo mosquitto_passwd /etc/mosquitto/users/passwd usuario3

Y cada vez nos pedirá que confirmemos la clave de los usuarios dos
veces. Al final, tendremos un fichero que contiene los usuarios y sus
claves (no están en texto claro) y será el mismo fichero que hemos
indicado a Mosquitto que utilice para la autenticación de usuarios.
Ahora, para que Mosquitto recoja estos cambios, forzamos que relea el
fichero de configuración

    sudo service mosquitto reload

Y... *et voilà*, ya tenemos nuestro *broker*, funcionando, sobre TLS,
con autenticación de usuarios, sin permitir conexiones anónimas y además
tenemos creados los usuarios usuario1, usuario2 y usuario3.

¿Cómo podemos comprobar si lo hemos hecho bien?

Sencillo, si intentamos conectarnos como se comentaba en el artículo
anterior (en remoto, no desde el propio *broker*), no debería dejarnos:

    mosquitto_sub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "UnTopicCualquiera" -v
    Connection Refused: not authorised.
    Connection Refused: not authorised.
    Connection Refused: not authorised.

Nos indica que se rechaza la conexión porque no estamos autorizados a
acceder al *broker*. Ahora, para que nos deje, debemos especificar un
usuario. De momento cualquiera de los usuarios que hemos creado nos
valdría, por ejemplo, usuario1. Especificamos el usuario y la password
en el comando de conexión con los parámetros *-u* y *-P* (en mayúsculas
la P), respectivamente, de la siguiente forma:

    mosquitto_sub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "UnTopicCualquiera" -v -u usuario1 -P passwordusuario1

Donde "*passwordusuario1*" hay que sustituirlo por la *password* que
especificamos a la hora de crear el usuario con el comando
*mosquitto_passwd*.

Ahora, comprobamos que el usuario conecta al *broker*, se suscribe a
"*UnTopicCualquiera*" y permanece ahí, en silencio, esperando que algo
sea publicado para hacerse eco de ello. Lo podemos comprobar, desde otro
terminal, con el comando para publicar en ese mismo *topic* (esta vez
publicaremos como *usuario2*).

    mosquitto_pub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "UnTopicCualquiera" -m Hola -u usuario2 -P passwordusuario2

De momento todo sigue funcionan igual, salvo que ahora hay que
especificar un usuario que exista y proporcionar su correspondiente
clave de usuario. Y con esto, tendríamos configurado el sistema de
autenticación de usuarios básico de Mosquitto. Podemos añadir, quitar o
modificar usuarios con el comando *mosquitto_passwd* según nuestras
necesidades.

## Activando el sistema de ACLs en Mosquitto

Para configurar las ACLs en Mosquitto, hay que añadir una línea al
fichero maestro de configuración, *mosquitto.conf*. Concretamente:

    acl_file filepath

Donde se especifica el *path* y nombre de fichero del fichero que
contendrá la definición de las ACLs. Como, de nuevo, es algo que hay que
hacer sobre el propio *broker*, nos conectaremos vía SSH y haremos login
en el Ubuntu Server donde está instalado el Mosquitto. Editamos el
fichero */etc/mosquitto/mosquitto.conf* (con permisos de administrador)
y añadiremos estas líneas al fichero:

    acl_file /etc/mosquitto/users/acl

Ese fichero no existe. Existe la ruta */etc/mosquitto/users*, porque la
creamos en el paso en el que configuramos la autenticación de usuarios.
Así que tendremos que crear (como root) dicho fichero y su contenido.
Pero veamos antes algo importante.

## Breves nociones sobre el sistema básico de de ACLs de Mosquitto

Antes de ponernos a configurar ACLs para el caso concreto que nos ocupa,
haré una breve explicación de qué cosas están permitidas y cómo se
estructuran en el fichero de configuración de ACLs.

Existen tres "niveles" sobre los cuales se pueden definir ACLs en
Mosquitto: a nivel de usuarios anónimos (que nosotros hemos prohibido en
nuestro *broker*), de usuarios autenticados y de todos los usuarios.
Para todos ellos se pueden definir ACLs.

Usuarios anónimos: Son los que no están en una lista de usuarios
autenticados y solo son posible si hemos activado la opción de permitir
usuarios anónimos. En nuestro caso no lo hicimos. Pero si lo hubiésemos
hecho, podemos definir a qué *topics* pueden acceder. Se hace de la
siguiente forma, al principio del fichero de ACLs:

    topic [read|write|readwrite] <topic>

Por ejemplo:

    topic readwrite UnTopicPublico

Esto permite que cualquier usuario anónimo pueda suscribirse o publicar
en el *topic* *UnTopicPublico*. Se pueden poner varias líneas como la
descrita, una debajo de otra, definiendo distintos *topics* y distintos
modos de acceso, en todos los casos para los usuarios anónimos.

Debajo de la definición de *topics* y accesos para los usuarios
anónimos, podemos definir lo mismo pero por cada usuario autenticado. En
este caso es exactamente igual pero justo antes hay que poner la línea:

    user nombreDeUsuario

Y después, debajo, especificamos los *topics* y niveles de acceso para
dicho usuario. Por ejemplo:

    user pedro
    topic readwrite topicQueSea
    topic read topicDeLectura

Este ejemplo define para el usuario "*pedro*" (que tendrá que
autenticarse al conectar), que puede suscribirse y publicar en el *topic
topicQueSea* y que podrá suscribirse, solamente, al *topic*
*topicDeLectura*. Este bloque podemos repetirlo por todos los usuarios
autenticados que tengamos.

Y, finalmente, se pueden definir accesos a nivel global para todos los
usuarios, tanto autenticados como no, basado en patrones. Esto significa
que podemos utilizar dos variables que serán sustituidas en tiempo real
por el valor que contienen. Dichas variables son:

-   **%u** es el nombre de usuario (el que definimos en el fichero de
    usuarios autenticados)
-   **%c** es el nombre del cliente. Esto es lo siguiente: en MQTT un
    mismo usuario puede acceder desde varios clientes. Digamos que el
    cliente podría representar el dispositivo desde el que se conecta el
    usuario (perdónenme los puristas; ya se que esto no es exacto). En
    MQTT se habla de cliente, no de usuario. Se reservan recursos del
    *broker* por cliente, no por usuario. Y se almacenan mensajes que no
    hayan sido entregado por clientes, no por usuarios. Pero la
    autenticación se hace por usuarios. Así que si un usuario accede
    desde un cliente y el mismo usuario accede desde otro cliente, para
    el *broker* son dos conexiones que deben ser tratadas de forma
    independiente en cuanto a asignación de recursos, prioridad... y
    cada una de estas conexiones puede tener unas características
    determinadas (de calidad de servicio, de retención de mensajes...).
    Soltado el rollo, el caso es que cualquiera que accede al *broker*,
    obligatoriamente proporciona un nombre o identificador de cliente y
    esta variable lo contiene.

El caso es que podemos definir reglas genéricas que se ajusten a cada
cliente o usuario en tiempo de ejecución, con el uso de patrones
utilizando estos parámetros. Se hace de la siguiente forma:

    pattern [read|write|readwrite] <topic>

Vamos, igual que en el caso de los usuarios individuales pero cambiando
la palabra clave "*topic*" al inicio, por "*pattern*". Pero, en este
caso podemos (no estamos obligados) aplicar los patrones. Por ejemplo:

    pattern readwrite usuarios/%u

Esto hará que cualquier usuario que se conecte, pueda publicar y
suscribirse al *topic* "*usuarios/%u*". Si se conecta el usuario
«*pedro*«, el *topic* será *"usuarios/pedro"*; si se conecta *«maria»*,
será *"usuarios/maria"*. Mosquitto sustituye de forma automática la
variable por el valor que corresponda.

## Comodines en los topics

Otra cosa importante a conocer es que en la definición de *topics* se
permite el uso de dos comodines:

-   **+**, equivale a "cualquier nombre en este nivel de la jerarquía"
-   **\#**, equivale a "cualquier nivel de la jerarquía a partir de
    aquí"

Por ejemplo, acceso de lectura a:

    usuario/#

Permitirá suscribirse a:

    usuario/pepe
    usuario/maria
    usuario/maria/casa/salon

etcétera. Y, por ejemplo, acceso de lectura a :

    usuario/+/casa

permitiría suscribirse a:

    usuario/maria/casa
    usuario/pepe/casa

Pero no a:

    usuario/maria/piscina
    usuario/pepe

porque no hay coincidencia con el patrón.

## Configurando ACLs en Mosquitto para nuestro caso

Ahora sí, una vez explicados los conceptos básicos de los ACLs en
Mosquitto, y de la estructura de un *topic* con comodines, editamos el
fichero */etc/mosquitto/users/acl*, con permisos de administrador, y
configuramos los ALCs que nos interesan (este fichero no existe aún, lo
creamos en este momento con el editor que más te guste).

Y ponemos este contenido en él:

    user usuario1
    topic write usuarios/sinprivilegios/publicaciones/usuario1
    topic read usuarios/administradores/publicaciones

    user usuario2
    topic write usuarios/sinprivilegios/publicaciones/usuario2
    topic read usuarios/administradores/publicaciones

    user usuario3
    topic write usuarios/administradores/publicaciones
    topic read usuarios/sinprivilegios/publicaciones/#

Y guardamos. Aquí hemos definido que haya dos usuarios (usuario1 y
usuario2) que puedan publicar en su correspondiente *topic* dentro de
*usuarios/sinprivilegios/publicaciones*. Y puedan leer de
*usuarios/administradores/publicaciones*. Y hemos dicho que el tercero
de los usuarios, que nos imaginaremos como un administrador, puede leer
las publicaciones de esos dos usuarios y publicar en
*usuarios/administradores/publicaciones*, de tal forma que la respuesta
pueda ser leída por los usuarios *usuario1* y *usuario2*.

Para que Mosquitto cargue los ACLs que hemos definido, tenemos que
recargar el fichero de configuración primero:

    sudo service mosquitto reload

Y... ya está. Los usuarios *usuario1* y *usuario2* podrán publicar y
suscribirse únicamente en los *topics* exactos que cada uno tiene
definidos. En ninguno más. Para el usuario *usuario3*, ocurre
exactamente lo mismo; sólo podrá escribir en
*usuarios/administradores/publicaciones*, pero podrá leer lo que
publiquen los usuarios *usuario1* y *usuario2* simultáneamente (si se
suscribe a *usuarios/sinprivilegios/publicaciones/#*) o bien lo de uno
de ellos exclusivamente (por ejemplo, de *usuario1* si se suscribe a
*usuarios/sinprivilegios/publicaciones/usuario1*).

Ya podemos desconectarnos del Fujitsu Futro donde tenemos el *broker*
instalado y hacer pruebas en remoto, como hemos realizado siempre.

Si realizamos de nuevo las pruebas que hicimos al final del apartado
*"Activando y configurando la autenticación de usuarios en Mosquitto"*
de este mismo artículo, veremos que no nos aparece ningún error, pero no
llega ningún mensaje al usuario que está suscrito. Porque
"*UnTopicCualquiera*" no es un *topic* permitido ni para suscribirse ni
para publicar, para ninguno de los usuarios, según las ACLs que hemos
definido. **Ojo ¡no aparece ningún error!**

Sin embargo, podemos hacer nuevas pruebas, acorde a la lista de ACLs que
hemos definido y ver cómo funciona. Desde un terminal introducimos el
siguiente comando:

    mosquitto_sub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "usuarios/sinprivilegios/publicaciones/#" -v -u usuario3 -P passwordUsuario3

Y queda suscrito, esperando. Y desde otro terminal, publicamos:

    mosquitto_pub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "usuarios/sinprivilegios/publicaciones/usuario2" -m Hola -u usuario2 -P passwordUsuario2

Y vemos que el usuario *usuario3* que estaba suscrito, recibe el
mensaje:

    usuarios/sinprivilegios/publicaciones/usuario2 Hola

Porque cada uno está publicando o suscrito al *topic* al que le está
permitido.

## Consejo

La autenticación de usuarios no es algo problemático. Sin embargo, el
control de acceso a los *topics* mediante las ACLs suele ser un
quebradero de cabeza. No tanto con los *topics* que hemos descrito en
este caso, que son sencillos. Pero cuando defines una gran estructura de
*topics* con permisos a distintos niveles y ofreciendo distinto modo de
acceso a cada uno y cientos o miles de usuarios, la cosa se complica.
Definirlo no es lo más difícil; determinar qué estructura de *topics* es
más conveniente, es lo realmente complicado. Dependerá mucho del
contexto de la aplicación que vayas a desarrollar, porque no vas a
acceder con los clientes del Mosquitto (que están bien para pruebas).
Accederás desde tu aplicación, que será Java, Nodejs, PHP, erlang,
etcétera. Y dado que en el caso de los ACLs la acción por defecto no es
responder con un "no se puede" sino no responder, en muchas ocasiones es
complejo determinar el origen del error.

Por ello, mi consejo es que desactives los ACLs mientras afinas la
aplicación que estés desarrollando y los vayas activando poco a poco una
vez estés seguro de que tu aplicación, tu *backend* o tu app móvil,
funciona correctamente. Así siempre sabrás que si algo deja de
funcionar, has metido la pata en el establecimiento de las ACLs.

## Conclusión

Con esto ya he terminado la serie de artículos que empecé meses atrás;
tenemos finalizado nuestro *broker* doméstico, en un *thin client*
Fujitsu Futro S450 basado en Ubuntu Server 16.04 y Mosquitto, con
conexiones cifradas vía TLS, autenticación de usuarios con *password*,
sin permitir usuarios anónimos y con control estricto de acceso a los
*topics*. A un coste de risa. Espero que, pese a lo denso del artículo,
haya sido interesante.

¿Alguna duda?``{.western}
:::
