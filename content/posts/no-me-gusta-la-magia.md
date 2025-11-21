+++
date = '2025-11-20T23:30:26+01:00'
draft = false
title = 'No me gusta la magia'
summary = 'Sobre documentación, Spring Boot y abstracción'
tags = ["java", "abstracción", "docs", "spring"]
+++

## Prefacio

A lo mejor todo esto es una skill issue como un templo de grande y en realidad es bastante más obvio de lo que pueda parecer y no lo sé.

Aún partiendo de la base de que esto suele ser así (y ya sabiendo que cojeo de la pata de los despistes y de leer documentación en diagonal) creo que la documentación inicial debería estar entre un 50 y un 125% más idiotizada y a prueba de tontos. Pero podría ser que no soy el más espabilado, eso siempre por delante (aunque me guste creer lo contrario).

Y todo esto dentro del contexto de ser alguien relativamente novato en la industria que está aprendiendo por su cuenta según sus intereses, lo que opino (al momento de escribir esto) está sujeto a cambios constantes y debería tener (y tiene) el peso justito y necesario.

## Contexto

Estoy desarrollando [realista](https://scod.is-a.dev/projects.html), una aplicación de coordinación entre inquilinos para conseguir información _real_ sobre las viviendas a las que intentamos acceder con todos sus defectos (y virtudes) para, al menos, saberlos de antemano, llevarnos menos sorpresas e intentar saber si la gestora va a tardar 8 meses en cambiar un calentador, para saber si va a tocar comprar una olla industrial para calentar agua con antelación.

Para esto empecé con el stack con el que tengo experiencia profesional, que consiste en Spring con Hibernate y PostgreSQL en el backend y Next.js/React para el frontend.

## De camino al hola-mundo

Me podía esperar problemas al empezar con Spring ya que, a pesar de haberlo utilizado en mi entorno laboral, nunca había preparado un backend entero desde cero con este _framework_. Pero para mi sorpresa no me funcionaba ni un `hola-mundo` de Java vainilla pero me consoló saber que a otro puñado de personas les pasaba lo mismo y gestionar el versionado de Java en Linunx no es lo más intuitivo del mundo.

Partía de una instalación de Java en mi Fedora que no recordaba bien cómo la había hecho, así que haciendo inventario, resulta que mi `java` (sólo el JRE) tenía la versión 17 de la distribución Amazon Coretto y no tenía forma de compilarlo. Así que a instalar un JDK completo para poder compilar mis cosas. Y obviamente tener distribuciones distintas no es buena idea, así que después de media hora sin poder desintalar el JRE como tocaba, lo hice a mano y conseguí compilar un hola mundo en Java. Acto seguido encontré por ~~Google~~ Kagi adelante un programita majísimo que me facilitó la vida inmensamente, me permitió limpiar las instalaciones previas e instalar OpenJDK.

Gracias, [SDKMAN](https://sdkman.io/). Y yo os maldigo JREs, JDKs, `java --version`, `javac -version` y `$JAVA_HOME`.

El _quick start_ de Spring es muy bueno. Da contexto, especifica los caminos distintos del desarrollo con Maven y Gradle y, quitando la odisea previa, todo fue como la seda.

Maven no dio mucho problema aunque lo de tener que copiar el mvnw.sh de otro proyecto de ejemplo por no-recuerdo-qué-error no fue demasiado bonito, pero se apañó bien.

Con Java ordenado, Spring Boot funcionando y teniendo un hola mundo funcional, en este momento tocaba ponerse con la persistencia.

## Persistencia

No quería tener que instalar nada más, así que me instalé (jaja) Docker para tener una instancia de PostgreSQL y todo iba correcto. Con el `docker-compose.yaml` listo y quitando que se borraba todo con cada encendido por no haber configurado el volumen correctamente, todo fue correcto.

Conozco Hibernate y el patrón de `Entidad - Repositorio - Servicio` a grandes rasgos, así que conseguir un `hola-mundo` a estas alturas no debería ser complicado. Después de revisar la documentación de inicialización de bases de datos de Spring sin ver cómo montarlo, me fui a leer la documentación de Hibernate.

Esto lo digo con todo el cariño del mundo, se nota que la documentación de Hibernate está hecha por gente veterana y muy disfrutona. Se explayan a gusto, ponen _snippets_ de ejemplo constantemente, son muy específicos con la terminología que utilizan y hasta hay ejercios para el lector, tremendo.

Pero no compilaba ni desde la primera línea de código. Spring configura al inicio lo relacionado con la base de datos, así que esta tenía que estar funcionando. Lo revisé, entré a la consola y después de aprender un poquito de Docker, resultó que lo que tenía que estar fallando era otra cosa. Y de hecho, ni siquiera compilaba desde que añadí la dependencia de Hibernate al `pom`. Revisé el repositorio de Maven para ver que la versión de la documentación es la más reciente (lo era), copio-pego el error en ~~Google~~ Kagi para rebuscar en Stack Overflow y reviso la configuración del dialecto, la URL de la base de datos, los puertos (internos y externos de Docker), la configuración del DDL (por si acaso) y absolutamente nada funciona. El error ya no era descriptivo así que fui a preguntarle a Claude.

Resulta que Spring viene con una versión integrada de Hibernate que genera problemas de compatibilidad y sólo por asegurarme de que el [prefacio](#prefacio) no tenía razón, me repaso toda la documentación de Spring para ver si encuentro algo sobre esto (para futuras ocasiones) y... efectivamente lo menciona y explica, vaya por dios. El [prefacio](#prefacio) se lleva este punto.

Ahora sí que tengo el `hola-mundo` preparado, pero el cristo que tengo montado intentando entender dónde encajan exactamente conceptos como `DataSource`, `JDBC`, `SpringData JPA`, `EntityManager`, `JPA`, `Jakarta` y demás conceptos relacionados-pero-distintos no está en los escritos.

## _Rendez-vous_

Ahora sé que `DataSource` gestiona conexiones y está construido sobre la `JDBC`, que es la API de conectividad de Java (que queda más abajo del stack de abstracciones). Que `SpringData JPA` es una implementación de la especificación `JPA` y `EntityManager` está también a este nivel y permite gestionar más fácilmente el acceso (menos boilerplate y más claridad). `Jakarta` es el nuevo nombre que recibió al cambiar Java EE de dueño (cambio que se produjo por copyright, bastante gracioso si me preguntan. Si me regalan algo, ¿por qué tendría que cambiarle el nombre?)

Todo esto hace que al principio sea increíblemente fácil perderse en cualquiera de los niveles de la pila de abstracciones incluso a pesar de haber hecho un intento honesto de seguir la documentación. Que conozca las definiciones a grandes rasgos de ese puñado de conceptos no significa que los conozca en profundidad pero sí tengo bastante claro que es un problema manejable, interesante y es **fascinante** descubrir estas simas poco a poco, error a error.

## La magia

Volviendo a hacer inventario, ya podía rescatar información de mi base de datos y escupirla en el frontend, maravilloso. Pero no encontraba la query que estaba utilizando. ¿Cómo va a ser eso?

Tengo mi entidad con su anotación @Entity, mi servicio con nada más que _boilerplate_, anotaciones esta vez más raras y una referencia al repositorio: `entityRepository.findById(id);` que también está anotado y también completamente vacío que, de alguna forma, interactúa con mi `entityRequest` (que de nuevo es una clase _boiler_ sin ninguna referencia externa pero sin la cual no funciona).

{{< figure src="https://i.kym-cdn.com/entries/icons/original/000/054/773/doakescover.jpg" title="El tío que sospecha de Dexter" >}}

Aquí obviamente hay truco, y es que gente más lista que yo se encargó de que dinámimcamente estos métodos básicos se generen para mi comodidad. Y `entityRequest` no es más que un DTO que utiliza Jackson para parsear JSON entrante, y las anotaciones le sirven a Hibernate para, precisamente, localizar las dependencias necesarias para crear estas funcionalidades.

## Concluyendo

¿La magia es mala? No lo creo. Si se utiliza en una [parte importante de las empresas en la _Fortune 500_](https://nareshit.com/blogs/how-full-stack-java-powers-fortune-500-companies-in-2025), tan malo no será. Pero no quita que me guste que las cosas sean más explícitas, tanto por dentro como por fuera. Particularmente porque de vez en cuando las cosas se rompen (ver los últimos tropiezos de [Amazon](https://aws.amazon.com/premiumsupport/technology/pes/) y [Cloudflare](https://blog.cloudflare.com/tag/post-mortem/)) y probablemente deberían ser más obvias tanto en diseño como en implementación.

En cualquier caso, infinitamente agradecido estoy de que esto sea público, se me permita estudiarlo porque quiero y puedo sin más explicaciones y a pesar de cualquier opinión que pueda tener sólo estoy aprendiendo.

Y si yo me estoy tropezando con esto seguramente sea porque otros antes hicieron lo mismo en baches que ya están más que reparados gracias al trabajo de mucha gente, muy capaz y muchos sin nombre, así que un besazo para todos ellos.
