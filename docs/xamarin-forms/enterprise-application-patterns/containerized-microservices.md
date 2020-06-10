---
Título: "microservicios en contenedores" Descripción: "en este capítulo se explica cómo usar microservicios y contenedores para crear aplicaciones en la nube modernas, escalables y confiables".
MS. Prod: Xamarin ms. AssetID: 5872ad92-04e0-4f1a-9691-79d5602f5683 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 08/07/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="containerized-microservices"></a>Microservicios en contenedores

El desarrollo de aplicaciones cliente-servidor ha provocado un enfoque en la creación de aplicaciones en capas que usan tecnologías específicas en cada nivel. Estas aplicaciones a menudo se denominan aplicaciones *monolíticas* y se empaquetan en el hardware que se ha ajustado previamente para cargas máximas. Los principales inconvenientes de este enfoque de desarrollo son el acoplamiento estrecho entre los componentes de cada nivel, los componentes individuales no se pueden escalar fácilmente y el costo de las pruebas. Una actualización simple puede tener efectos imprevistos en el resto del nivel, por lo que un cambio en un componente de la aplicación requiere que se vuelva a probar y volver a implementar todo el nivel.

Especialmente en lo que respecta a la edad de la nube, es que los componentes individuales no se pueden escalar fácilmente. Una aplicación monolítica contiene funcionalidad específica del dominio y se divide normalmente por capas funcionales como front-end, lógica de negocios y almacenamiento de datos. Una aplicación monolítica se escala mediante la clonación de toda la aplicación en varios equipos, tal como se muestra en la figura 8-1.

![](containerized-microservices-images/monolithicapp.png "Monolithic application scaling approach")

**Figura 8-1**: enfoque de escalado de aplicaciones monolíticas

## <a name="microservices"></a>Microservicios

Los microservicios ofrecen un enfoque diferente para el desarrollo y la implementación de aplicaciones, un enfoque que se adapta a los requisitos de agilidad, escala y confiabilidad de las aplicaciones en la nube modernas. Una aplicación de microservicios se descompone en componentes independientes que funcionan conjuntamente para ofrecer la funcionalidad general de la aplicación. El término microservicio destaca que las aplicaciones deben estar compuestas de servicios lo suficientemente pequeños como para reflejar problemas independientes, de modo que cada microservicio implementa una sola función. Además, cada microservicio tiene contratos bien definidos para que otros microservicios puedan comunicarse y compartir datos con él. Entre los ejemplos típicos de microservicios se incluyen los carros de la compra, el procesamiento de inventario, los subsistemas de compra y el procesamiento de pagos.

Los microservicios se pueden escalar horizontalmente de forma independiente, en comparación con las aplicaciones monolíticas de Giant que se escalan juntos. Esto significa que un área funcional específica, que requiere más capacidad de procesamiento o ancho de banda de red para admitir la demanda, se puede escalar en lugar de escalar horizontalmente otras áreas de la aplicación. En la figura 8-2 se muestra este enfoque, donde se implementan y escalan microservicios de forma independiente, y se crean instancias de servicios entre equipos.

![](containerized-microservices-images/microservicesapp.png "Microservices application scaling approach")

**Figura 8-2**: enfoque de escalado de aplicaciones de microservicios

La escalabilidad horizontal de microservicios puede ser casi instantánea, lo que permite a una aplicación adaptarse a las cargas cambiantes. Por ejemplo, un único microservicio en la funcionalidad orientada a la Web de una aplicación podría ser el único microservicio de la aplicación que necesita escalar horizontalmente para controlar el tráfico entrante adicional.

El modelo clásico para la escalabilidad de la aplicación es tener un nivel sin estado y con equilibrio de carga con un almacén de datos externo compartido para almacenar datos persistentes. Los microservicios con estado administran sus propios datos persistentes, normalmente almacenados localmente en los servidores en los que se colocan, para evitar la sobrecarga que supone el acceso a la red y la complejidad de las operaciones entre servicios. Esto permite el procesamiento de datos más rápido posible y puede eliminar la necesidad de almacenar en memoria caché los sistemas. Además, los microservicios con estado escalables suelen particionar los datos entre sus instancias, para administrar el tamaño de los datos y transferir el rendimiento más allá del que un solo servidor puede admitir.

Los microservicios también admiten actualizaciones independientes. Este acoplamiento flexible entre microservicios proporciona una evolución de la aplicación rápida y confiable. Su naturaleza independiente distribuida es compatible con las actualizaciones graduales, donde solo se actualizará un subconjunto de instancias de un único microservicio en un momento dado. Por lo tanto, si se detecta un problema, se puede revertir una actualización con errores antes de que todas las instancias se actualicen con el código o la configuración defectuosos. Del mismo modo, los microservicios suelen usar el control de versiones de esquema, de modo que los clientes ven una versión coherente cuando se aplican las actualizaciones, independientemente de la instancia de microservicio con la que se esté comunicando.

Por lo tanto, las aplicaciones de microservicios tienen muchas ventajas con respecto a las aplicaciones monolíticas:

- Cada microservicio es relativamente pequeño, fácil de administrar y evolucionar.
- Cada microservicio se puede desarrollar e implementar independientemente de otros servicios.
- Cada microservicio se puede escalar horizontalmente de forma independiente. Por ejemplo, es posible que sea necesario escalar horizontalmente un servicio de catálogo o un servicio de cesta de la compra más que un servicio de pedidos. Por lo tanto, la infraestructura resultante consumirá recursos de forma más eficaz al escalar horizontalmente.
- Cada microservicio aísla cualquier problema. Por ejemplo, si hay un problema en un servicio, solo afecta a ese servicio. Los otros servicios pueden seguir controlando las solicitudes.
- Cada microservicio puede usar las tecnologías más recientes. Dado que los microservicios son autónomos y se ejecutan en paralelo, se pueden usar las tecnologías y los marcos más recientes, en lugar de forzar el uso de un marco de trabajo anterior que podría ser utilizado por una aplicación monolítica.

Sin embargo, una solución basada en microservicios también tiene posibles desventajas:

- La elección de cómo particionar una aplicación en microservicios puede ser desafiante, ya que cada microservicio tiene que ser completamente autónomo, de un extremo a otro, lo que incluye la responsabilidad de sus orígenes de datos.
- Los desarrolladores deben implementar la comunicación entre servicios, lo que aumenta la complejidad y la latencia de la aplicación.
- Normalmente no es posible realizar transacciones atómicas entre varios microservicios. Por lo tanto, los requisitos empresariales deben adoptar la coherencia final entre los microservicios.
- En producción, existe una complejidad operativa en la implementación y administración de un sistema comprometido con muchos servicios independientes.
- La comunicación directa de cliente a microservicio puede dificultar la refactorización de los contratos de microservicios. Por ejemplo, con el tiempo, es posible que deba cambiar el sistema en el que se crean particiones en los servicios. Un único servicio puede dividirse en dos o más servicios, y dos servicios pueden combinarse. Cuando los clientes se comunican directamente con los microservicios, este trabajo de refactorización puede interrumpir la compatibilidad con las aplicaciones cliente.

## <a name="containerization"></a>Inclusión en contenedores

La inclusión en contenedores es un enfoque para el desarrollo de software en el que una aplicación y su conjunto de dependencias con control de versiones, además de su configuración de entorno abstraeda como archivos de manifiesto de implementación, se empaquetan como una imagen de contenedor, se prueban como una unidad y se implementan en un sistema operativo host.

Un contenedor es un entorno operativo aislado, controlado por recursos y portátil, donde una aplicación puede ejecutarse sin tocar los recursos de otros contenedores o del host. Por lo tanto, un contenedor se parece a un equipo físico recién instalado o a una máquina virtual.

Hay muchas similitudes entre contenedores y máquinas virtuales, como se muestra en la figura 8-3.

![](containerized-microservices-images/containersvsvirtualmachines.png "Microservices application scaling approach")

**Figura 8-3**: comparación de máquinas virtuales y contenedores

Un contenedor ejecuta un sistema operativo, tiene un sistema de archivos y se puede acceder a él a través de una red como si fuera una máquina física o virtual. Sin embargo, la tecnología y los conceptos que usan los contenedores son muy diferentes de las máquinas virtuales. Las máquinas virtuales incluyen las aplicaciones, las dependencias necesarias y un sistema operativo invitado completo. Los contenedores incluyen la aplicación y sus dependencias, pero comparten el sistema operativo con otros contenedores, que se ejecutan como procesos aislados en el sistema operativo host (aparte de los contenedores de Hyper-V que se ejecutan dentro de una máquina virtual especial por contenedor). Por lo tanto, los contenedores comparten recursos y normalmente requieren menos recursos que las máquinas virtuales.

La ventaja de un enfoque de implementación y desarrollo orientado a contenedores es que elimina la mayoría de los problemas que surgen de las configuraciones de entorno incoherentes y los problemas que les acompañan. Además, los contenedores permiten una funcionalidad rápida de escalado vertical de aplicaciones mediante la creación de instancias de nuevos contenedores según sea necesario.

Los conceptos clave al crear y trabajar con contenedores son:

- Host de contenedor: la máquina física o virtual configurada para hospedar contenedores. El host de contenedor ejecutará uno o varios contenedores.
- Imagen de contenedor: una imagen se compone de una Unión de sistemas de archivos superpuestos apilados entre sí y es la base de un contenedor. Una imagen no tiene el estado y nunca cambia a medida que se implementa en entornos diferentes.
- Contenedor: un contenedor es una instancia en tiempo de ejecución de una imagen.
- Imagen del sistema operativo de contenedor: los contenedores se implementan a partir de imágenes. La imagen del sistema operativo de contenedor es la primera capa de potencialmente muchas capas de imagen que componen un contenedor. Un sistema operativo de contenedor es inmutable y no se puede modificar.
- Repositorio de contenedor: cada vez que se crea una imagen de contenedor, la imagen y sus dependencias se almacenan en un repositorio local. Estas imágenes se pueden reutilizar muchas veces en el host de contenedor. Las imágenes de contenedor también pueden almacenarse en un registro público o privado, como [Docker Hub](https://hub.docker.com/), de modo que se puedan usar en distintos hosts de contenedor.

Las empresas están adoptando cada vez más contenedores al implementar aplicaciones basadas en microservicios y Docker se ha convertido en la implementación de contenedor estándar que ha adoptado la mayoría de las plataformas de software y los proveedores de nube.

La aplicación de referencia de eShopOnContainers usa Docker para hospedar cuatro microservicios de back-end en contenedor, como se muestra en la figura 8-4.

![](containerized-microservices-images/microservicesarchitecture.png "eShopOnContainers reference application back-end microservices")

**Figura 8-4**: microservicios de back-end de la aplicación de referencia eShopOnContainers

La arquitectura de los servicios back-end de la aplicación de referencia se descompone en varios subsistemas autónomos en forma de los microservicios y contenedores de colaboración. Cada microservicio proporciona un área única de funcionalidad: un servicio de identidad, un servicio de catálogo, un servicio de pedidos y un servicio de cesta.

Cada microservicio tiene su propia base de datos, lo que le permite desacoplarlo por completo de los otros microservicios. Cuando sea necesario, la coherencia entre las bases de datos de diferentes microservicios se logra mediante eventos de nivel de aplicación. Para obtener más información, consulte [comunicación entre microservicios](#communication-between-microservices).

Para obtener más información acerca de la aplicación de referencia, consulte [microservicios de .net: arquitectura para aplicaciones .net en contenedor](https://aka.ms/microservicesebook).

## <a name="communication-between-client-and-microservices"></a>Comunicación entre cliente y microservicios

La aplicación móvil eShopOnContainers se comunica con los microservicios de back-end en contenedor mediante la comunicación *directa de cliente a microservicio* , que se muestra en la figura 8-5.

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Microservices application scaling approach")

**Figura 8-5**: comunicación directa de cliente a microservicio

Con la comunicación directa de cliente a microservicio, la aplicación móvil realiza solicitudes a cada microservicio directamente a través de su punto de conexión público, con un puerto TCP diferente por microservicio. En producción, el punto de conexión normalmente se asigna al equilibrador de carga del microservicio, que distribuye las solicitudes entre las instancias disponibles.

> [!TIP]
> Considere el uso de la comunicación de puerta de enlace de API. La comunicación directa entre el cliente y el microservicio puede tener inconvenientes al compilar una aplicación grande y compleja basada en microservicios, pero es más que adecuada para una aplicación pequeña. Al diseñar una aplicación grande basada en microservicios con decenas de microservicios, considere la posibilidad de usar la comunicación de puerta de enlace de API. Para obtener más información, vea [microservicios de .net: arquitectura para aplicaciones .net en contenedor](https://aka.ms/microservicesebook).

## <a name="communication-between-microservices"></a>Comunicación entre microservicios

Una aplicación basada en microservicios es un sistema distribuido, que puede ejecutarse en varios equipos. Lo habitual es que cada instancia de servicio sea un proceso. Por lo tanto, los servicios deben interactuar mediante un protocolo de comunicación entre procesos, como HTTP, TCP, Advanced Message Queuing Protocol (AMQP) o los protocolos binarios, dependiendo de la naturaleza de cada servicio.

Los dos enfoques comunes para la comunicación de microservicios a microservicios son la comunicación de REST basada en HTTP al consultar los datos y la mensajería asincrónica ligera al comunicar actualizaciones entre varios microservicios.

La comunicación asincrónica basada en eventos de mensajería es fundamental al propagar los cambios entre varios microservicios. Con este enfoque, un microservicio publica un evento cuando se produce algo importante, por ejemplo, cuando actualiza una entidad de negocio. Otros microservicios se suscriben a estos eventos. A continuación, cuando un microservicio recibe un evento, actualiza sus propias entidades comerciales, lo que a su vez puede dar lugar a que se publiquen más eventos. Esta funcionalidad de publicación y suscripción normalmente se logra con un bus de eventos.

Un bus de eventos permite la comunicación de publicación y suscripción entre microservicios, sin necesidad de que los componentes se reconozcan entre sí de forma explícita, como se muestra en la figura 8-6.

![](containerized-microservices-images/eventbus.png "Publish-subscribe with an event bus")

**Figura 8-6:** Publicación: suscripción con un bus de eventos

Desde la perspectiva de la aplicación, el bus de eventos es simplemente un canal de publicación-suscripción expuesto a través de una interfaz. Sin embargo, el modo en que se implementa el bus de eventos puede variar. Por ejemplo, una implementación de bus de eventos podría usar RabbitMQ, Azure Service Bus u otros buses de servicio como NServiceBus y MassTransit. En la figura 8-7 se muestra cómo se usa un bus de eventos en la aplicación de referencia eShopOnContainers.

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "Asynchronous event-driven communication in the reference application")

**Figura 8-7:** Comunicación asincrónica controlada por eventos en la aplicación de referencia

El bus de eventos eShopOnContainers, implementado mediante RabbitMQ, proporciona funcionalidad de publicación-suscripción asincrónica de uno a muchos. Esto significa que después de publicar un evento, puede haber varios suscriptores que escuchen el mismo evento. En la figura 8-9 se muestra esta relación.

![](containerized-microservices-images/eventdrivencommunication.png "One-to-many communication")

**Figura 8-9**: comunicación de uno a varios

Este enfoque de comunicación uno a varios usa eventos para implementar transacciones empresariales que abarcan varios servicios, lo que garantiza la coherencia final entre los servicios. Una transacción de coherencia final se compone de una serie de pasos distribuidos. Por lo tanto, cuando el microservicio de Perfil de usuario recibe el comando UpdateUser, actualiza los detalles del usuario en su base de datos y publica el evento UserUpdated en el bus de eventos. Tanto el microservicio de cesta como el microservicio de pedidos se han suscrito para recibir este evento y, en respuesta, actualizar la información de comprador en sus bases de datos respectivas.

> [!NOTE]
> El bus de eventos eShopOnContainers, implementado mediante RabbitMQ, está pensado para usarse únicamente como prueba de concepto. En el caso de los sistemas de producción, se deben tener en cuenta implementaciones de bus de eventos alternativas.

Para obtener información sobre la implementación del bus de eventos, vea [microservicios de .net: arquitectura para aplicaciones .net en contenedor](https://aka.ms/microservicesebook).

## <a name="summary"></a>Resumen

Los microservicios ofrecen un enfoque para el desarrollo y la implementación de aplicaciones que se adapta a los requisitos de agilidad, escala y confiabilidad de las aplicaciones en la nube modernas. Una de las principales ventajas de los microservicios es que se pueden escalar horizontalmente de forma independiente, lo que significa que se puede escalar un área funcional específica que requiere más capacidad de procesamiento o ancho de banda de red para admitir la demanda, sin necesidad de escalar de forma innecesaria las áreas de la aplicación que no experimentan una mayor demanda.

Un contenedor es un entorno operativo aislado, controlado por recursos y portátil, donde una aplicación puede ejecutarse sin tocar los recursos de otros contenedores o del host. Las empresas están adoptando cada vez más contenedores al implementar aplicaciones basadas en microservicios y Docker se ha convertido en la implementación de contenedor estándar que ha adoptado la mayoría de las plataformas de software y los proveedores de nube.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
