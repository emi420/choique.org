---
title: Configuración de la primera instancia de Panoramax para Argentina
description: "Hemos configurado la primera instancia (pública) de Panoramax en América. Permite la recopilación de imágenes a nivel de calle en territorio argentino"
extra:
  author: Bastian Greshake Tzovaras
date: 2026-05-18
taxonomies:
  tags: ["OSM", "openstreetmap", "panoramax", "street-level imagery"]
---

**Una primera versión de este articulo [fue publicado en OSM](https://www.openstreetmap.org/user/Bastian%20Greshake%20Tzovaras/diary/408687)**

Panoramax es un sistema de código abierto para crear un repositorio común de imágenes a nivel de calle, ofreciendo así una alternativa con licencia abierta a Google Street View, etc.
A diferencia de otras alternativas que licencian las imágenes de forma abierta, todo el software es libre y de código abierto.
Además, se basa en la idea de crear una federación de servidores o instancias de Panoramax, similar al Fediverso de Mastodon, PeerTube, Lemmy, etc.

Desde [la primera vez que publiqué sobre Panoramax](https://tzovar.as/open-source-streetview/), la federación ha crecido considerablemente, superando las 10 instancias.
Pero, cal igual que (lamentablemente) en la mayoría de los sistemas federados, todavía existe una fuerte centralización en cuanto al uso.
Las instancias del capítulo francés de OpenStreetMap (OSM FR) y del Instituto Geográfico Nacional de Francia [reúnen alrededor del 97 % de todas las imágenes hasta la fecha](https://panoramax.fr/stats).
En el caso de la instancia de OSM FR, esto se debe en parte a que, hasta ahora, era la única instancia grande que permitía subir imágenes de cualquier lugar del mundo, en lugar de restringir geográficamente las subidas a una región o país.

Para Panoramax, esta centralización no solo es problemática en lo que respecta a la capacidad de trabajar en contextos locales o la resiliencia de la federación, sino también para la sostenibilidad de esas instancias individuales por motivos de almacenamiento:
Almacenar millones de imágenes a nivel de calle de alta resolución consume muchísimo espacio.
En el caso de la instancia de OSM FR, los aproximadamente 51 millones de imágenes ocupan 120 TB para las imágenes originales y otros 66 TB para las imágenes derivadas necesarias para su visualización y distribución eficientes.

Por ello, hace un par de semanas, Christian Quest publicó en el foro de la comunidad de OSM que [la instancia de OSM FR se estaba quedando sin espacio](https://community.openstreetmap.org/t/osm-fr-panoramax-server-only-for-testing-if-outside-of-france/143428).
También comentó que aproximadamente la mitad de esas imágenes provienen de fuera de Francia, lo que contribuye a que la situación actual no sea sostenible a largo plazo.
Por esta razón, es muy probable que no sea posible subir imágenes desde el extranjero en el futuro.
Como usuario que subió bastantes imágenes desde Argentina, sin duda contribuí a este problema, lo que implicó acelerar la puesta en marcha de una instancia local.

## Panoramax en el contexto argentino

No contar con una instancia local no se debió necesariamente a falta de intentos, sino a varios factores que representan barreras adicionales:
El primero, y quizás el más importante, es el elevado costo del hardware.
La electrónica en general es extremadamente cara en Argentina.
No solo en términos relativos, comparado con los salarios locales, sino también en términos absolutos, incluso sin tener en cuenta el aumento de precios derivado de la IA, debido a los aranceles y tasas de importación.
Esto significa que el hardware suele utilizarse hasta que se avería, lo que implica que el mercado de hardware usado no es muy amplio y que, además, los precios del hardware usado aquí suelen ser más altos que los que se pagarían por uno nuevo en lugares como la UE o Estados Unidos.
Por lo tanto, las posibilidades de obtener donaciones de hardware, [con las que otros lugares sí tienen suerte](https://forum.geocommuns.fr/t/deploying-a-panoramax-instance-the-pre-flight-check-list/1892) para un proyecto de este tipo, son bastante escasas, especialmente si se parte de cero.

Al mismo tiempo, las instituciones públicas como universidades o municipios, que en otros lugares podrían ser socios naturales para una iniciativa así, aunque potencialmente interesadas, carecen de los recursos necesarios para ayudar:
Todas están sufriendo pérdidas económicas debido al recorte intencional de fondos del gobierno nacional a todos los servicios públicos.
Si lee algo de alemán (o está dispuesto a usar la traducción automática), [puede leer mi reportaje sobre este tema en Amerika21](https://amerika21.de/autor/bastian-greshake-tzovaras); de lo contrario, cualquier medio de comunicación con cobertura internacional debería ser útil.

La otra alternativa para poner en marcha una instancia de Panoramax, que parece ser utilizada por otras instancias más pequeñas, es el autoalojamiento en casa.
Pero, más allá de la disponibilidad de hardware, incluso eso puede resultar complicado debido a las limitaciones de infraestructura.
Sobre todo en entornos rurales como el mío:
Además de los cortes de luz habituales, que un SAI (sistema de alimentación ininterrumpida) podría mitigar, los proveedores locales de internet suelen ofrecer velocidades muy bajas.
Por ejemplo, en el pueblo donde vivo la cooperativa local —que gestiona los servicios públicos, incluidas las conexiones a internet— ofrece velocidades aDSL lentas, y se prevé que la fibra óptica llegue a *algunos barrios* en algún momento del próximo año.
Eso significaría que usar una instancia de Panoramax sería como intentar ver imágenes HD a través de una pajita.

Todos estos factores llevaron a que —a pesar de intentarlo intermitentemente desde finales de 2024, con distinta intensidad—, hasta ahora no se había logrado crear ninguna instancia local.
Pero con el cambio a la instancia de OSM FR, que generó la urgencia necesaria, era hora de dejar de buscar soluciones "perfectas" y, en cambio, enfocarnos en crear *algo*.
Y aunque Argentina sea un país enorme, ¿quizás no se necesiten cientos de terabytes para empezar?
Por un lado, la red de carreteras actual parece ser sólo [aproximadamente una cuarta parte de la de Francia](https://en.wikipedia.org/wiki/List_of_countries_by_road_network_size).
Y además, el número de colaboradores locales que suben imágenes, al menos según las estimaciones de la instancia de OSM FR, es bastante manejable.

## Elección de configuración y creación de una instancia

En base a esto, me pregunté si no sería posible alquilar un servidor dedicado *pequeño* en algún lugar, simplemente para empezar.
Dados los costos de hardware (ver arriba), debía ser un proveedor de alojamiento fuera del país, ya que las opciones locales también son bastante caras.
Lo cual no es ideal en cuanto a latencia, pero por el momento no se puede ser exigente.
Así que comencé a intercambiar ideas de forma más concreta con Daniel y Marcos sobre las opciones y cómo podríamos configurar una instancia.

Para aprovechar su amplia experiencia de primera mano con el alojamiento de Panoramax, contacté con Christian Quest, quien me indicó que el proveedor francés *OVH* ofrece un servidor dedicado a un precio bastante asequible de 26,60 USD al mes, que incluye cuatro discos duros de 4 TB configurables en RAID por software y un SSD de 500 GB para almacenar la base de datos, etc.
En una configuración RAID 5, esto proporcionaría 12 TB de almacenamiento útil para las imágenes (o 16 TB, pagando un poco más por la opción de cuatro discos de 6 TB), y el resto de las especificaciones serían suficientes para ejecutar una instancia.
Al menos, siempre que se utilice el servicio de *blurring* que OSM FR ofrece como servicio, ya que el tipo de máquina sugerido no cuenta con una GPU dedicada.
En la configuración de 16 TB, esto debería ser suficiente para almacenar alrededor de 4 millones de imágenes, suponiendo la misma combinación de fuentes y calidades de imagen que almacena OSM FR.

Según nuestros cálculos eso duraría al menos un año, ya que hasta ahora se han subido alrededor de 830.000 imágenes tomadas en Argentina a la instancia de OSM FR en los últimos dos años.
Así que decidimos probarlo para empezar y, con suerte, crear una *demo* convincente.
Esto, a su vez, esperamos que nos ayude a entablar conversaciones más específicas para obtener soporte en el futuro.

Una vez decidido el tipo de alojamiento, la configuración inicial no fue demasiado complicada:
El equipo de Panoramax creó una [excelente documentación sobre el despliegue con `docker compose`](https://docs.panoramax.fr/backend/install/tutorials/running_docker_osm_auth/) y el uso de OSM a través de OAuth como proveedor de inicio de sesión.
Y nos beneficiamos aún más del [detallado informe de Matija Nalis](https://www.openstreetmap.org/user/Matija%20Nalis/diary/408636), quien recientemente había desplegado la instancia Panoramax de OSM Croacia, lo que nos permitió completar el 99% del despliegue (para la única excepción, consulte los detalles a continuación).

Así pues, desplegamos [nuestra instancia Panoramax en](https://panoramax.libre.net.ar).
Hasta donde sabemos, esta no solo es la primera instancia en Latinoamérica, ¡sino en todo el continente americano! [^1]
Actualmente está configurada para aceptar imágenes tomadas en Argentina y Uruguay, pero si alguien en países vecinos necesita espacio, podemos ver qué podemos hacer; ¡contáctenos!

En general, fue mucho más fácil e incluso más económico de lo que esperaba.
Espero que esto motive a otros a intentarlo también. Panoramax sólo puede funcionar a mayor escala si más personas implementan instancias locales.
Quizás no a nivel nacional, pero sí a nivel regional o municipal, lo que reduciría las necesidades de almacenamiento y, por lo tanto, los costos.

En cuanto al esfuerzo administrativo y la experiencia necesaria:
Cada vez hay más personas con experiencia en la administración de servidores Panoramax, y por lo que he visto, todos están encantados de ayudar.
Dicho esto, contar con un pequeño equipo dispuesto a colaborar es genial, tanto para obtener segundas opiniones y facilitar la gestión del tráfico, como para convertirlo en un esfuerzo colectivo.
En nuestra pequeña instancia, esto incluye no solo a Marcos y Dani, sino también a [Lucas](https://www.openstreetmap.org/user/lbellomo), quien se ofreció a compartir su experiencia en administración de servidores al enterarse de nuestra idea.

## Apéndice: Configuración del RAID por software

Lo único que faltaba en la descripción detallada de la instancia OSM-HR era cómo configurar el RAID por software.
La configuración del servidor *OVH* viene sin ningún disco duro configurado, ya que solo formatea la unidad SSD.
Pero, por suerte, no fue muy difícil [investigar](https://hetmanrecovery.com/recovery_news/how-to-create-software-raid-5-with-lvm.htm).
Para quienes deseen reproducir este proceso, los pasos se encuentran a continuación.

Para configurar los 4 discos duros como un RAID 5, también utilizamos LVM, de forma similar a la descripción para OSM-HR.
Para preparar los discos duros, primero creamos los volúmenes físicos:

```
pvcreate /dev/sda
pvcreate /dev/sdb
pvcreate /dev/sdc
pvcreate /dev/sdd
```

Luego creamos el grupo de volúmenes `vg1` ejecutando `vgcreate vg1 /dev/sda /dev/sdb /dev/sdc /dev/sdd`

Finalmente, creamos el volumen lógico (LV): `lvcreate -n panoramax-photos --type raid5 -i 3 -l 100%VG vg1`

Los parámetros son:

```

-n: nombre del volumen
--type raid5: tipo de RAID
-i: usar 3 dispositivos de los 4 (para redundancia de un solo disco)
-l 100%VG: usar el 100% del grupo de volúmenes
```

Como resultado, Obtenido:

```
root@panoramax-ar:~# lvscan
  ACTIVE            '/dev/vg1/panoramax-photos' [16.37 TiB] inherit
```

A partir de ahí, [el resto de las instrucciones de OSM HR](https://www.openstreetmap.org/user/Matija%20Nalis/diary/408636) te llevarán allí.

[^1]: Al menos la primera instancia pública visible y accesible que busca federarse; por supuesto, podría haber instancias alojadas de forma privada.
