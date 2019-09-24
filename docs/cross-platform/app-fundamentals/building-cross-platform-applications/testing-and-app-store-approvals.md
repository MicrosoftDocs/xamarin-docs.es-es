---
title: 'Parte 6: Pruebas y aprobaciones de App Store'
description: En este documento se describe cómo probar una aplicación multiplataforma en el dispositivo, administrar casos de prueba, automatizar pruebas, ejecutar pruebas unitarias y trabajar a través del proceso de envío de la aplicación.
ms.prod: xamarin
ms.assetid: 46E0578A-7EB9-C105-ABB0-A043E501F36B
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: bf6ea18c73226a620ce002797d2d2b04b48c7cc8
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198423"
---
# <a name="part-6---testing-and-app-store-approvals"></a>Parte 6: Pruebas y aprobaciones de App Store

## <a name="testing"></a>Pruebas

Muchas aplicaciones (incluso las aplicaciones Android, en algunas tiendas) tendrán que superar un proceso de aprobación antes de que se publiquen; por lo tanto, la prueba es fundamental para garantizar que la aplicación alcanza el mercado (por lo que solo se realiza correctamente con los clientes). Las pruebas pueden adoptar muchas formas, desde pruebas unitarias de nivel de desarrollador hasta la administración de pruebas beta a través de una amplia variedad de hardware.

### <a name="test-on-all-platforms"></a>Prueba en todas las plataformas

Hay ligeras diferencias entre lo que .NET admite en dispositivos Windows Phone, tabletas y equipos de escritorio, así como las limitaciones de iOS que impiden que se genere código dinámico sobre la marcha. Piense en probar el código en varias plataformas a medida que lo desarrolla, o bien Programe el tiempo para refactorizar y actualizar la parte del modelo de la aplicación al final del proyecto.

Siempre es recomendable usar el simulador/emulador para probar varias versiones del sistema operativo y también diferentes configuraciones y capacidades de dispositivo.

También debe probar en tantos dispositivos de hardware físicos diferentes como pueda.

#### <a name="devices-in-cloud"></a>Dispositivos en la nube

El teléfono móvil y el ecosistema de Tablet PC están creciendo todo el tiempo, lo que impide que se realicen pruebas en el número cada vez mayor de dispositivos disponibles. Para solucionar este problema, una serie de servicios ofrece la capacidad de controlar de forma remota muchos dispositivos diferentes para que las aplicaciones se puedan instalar y probar sin necesidad de invertir directamente en una gran cantidad de hardware.

[App Center test](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) ofrece una manera sencilla de probar aplicaciones iOS y Android en cientos de dispositivos diferentes.

### <a name="test-management"></a>Administración de pruebas

Al probar las aplicaciones dentro de su organización o administrar un programa beta con usuarios externos, existen dos desafíos:

- **Distribución** : administrar el proceso de aprovisionamiento (especialmente para dispositivos iOS) y obtener versiones actualizadas de software para los evaluadores.
- **Comentarios** : recopilación de información sobre el uso de la aplicación e información detallada sobre los errores que puedan producirse.

Hay una serie de servicios que ayudan a resolver estos problemas, ya que proporcionan una infraestructura integrada en la aplicación para recopilar e informar sobre el uso y los errores, así como para optimizar el proceso de aprovisionamiento para ayudar a registrar y administrar los evaluadores y sus dispositivos. .

[Visual Studio App Center](/appcenter/) ofrece una solución a estos problemas, lo que proporciona distribución de la versión de prueba, informes de bloqueo e información de uso de la aplicación sofisticada.

### <a name="test-automation"></a>Automatización de pruebas

Xamarin [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) se puede usar para crear scripts de prueba de interfaz de usuario automatizados que se pueden ejecutar localmente o cargar en [App Center prueba](https://docs.microsoft.com/appcenter/test-cloud/).

## <a name="unit-testing"></a>Pruebas unitarias

### <a name="touchunit"></a>Touch. Unit

Xamarin. iOS incluye un marco de pruebas unitarias denominado Touch. Unit, que sigue las pruebas de escritura de estilo JUnit/NUnit.

Consulte la documentación sobre las [pruebas unitarias con Xamarin. iOS](~/ios/deploy-test/touch.unit.md) para más información sobre cómo escribir pruebas y ejecutar Touch. Unit.

### <a name="andrunit"></a>Andr. unidad

Hay un equivalente de código abierto de Touch. Unit para Android llamado Andr. Unit. Puede descargarlo de [GitHub](https://github.com/spouliot/Andr.Unit) y obtener información sobre la herramienta en [ @spouliotel blog](http://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/)de.

## <a name="app-store-approvals"></a>Aprobaciones de App Store

Apple y Microsoft funcionan el único almacén en sus plataformas: App Store y Marketplace, respectivamente. Ambos bloquean sus dispositivos e implementan un proceso riguroso de revisión de la aplicación para controlar la calidad de las aplicaciones disponibles para su descarga. La naturaleza abierta de Android significa que hay una serie de opciones de almacenamiento que van desde el juego de Google, que está ampliamente disponible y no tiene ningún proceso de revisión, hasta el AppStore de Amazon para Android y esfuerzos específicos de hardware como aplicaciones de Samsung que tienen una distribución más limitada. e implementan un proceso de aprobación.

Esperar a que una aplicación sea revisada puede ser muy intensa. a menudo, las aplicaciones se envían para su aprobación con muy poco margen de error antes de la fecha de inicio "de destino". El propio proceso puede tardar hasta dos semanas y no es necesariamente transparente: hay un comentario limitado sobre el progreso de la aplicación hasta que finalmente se rechaza o aprueba. El rechazo puede significar que falta una ventana de promoción, especialmente si se produce más de una vez, y las semanas pasan entre la fecha de inicio original y el momento en que la aplicación se aprueba finalmente.

### <a name="be-prepared"></a>Estar preparado

La primera parte de los consejos: Planee el lanzamiento de la aplicación de antemano y realice provisiones para un posible rechazo y reenvío. Cada tienda requiere la creación de una cuenta antes de enviar la aplicación: Hágalo lo antes posible.
Aunque la suscripción de Google Play solo tarda unos minutos si las aplicaciones son gratuitas, el proceso se vuelve mucho más complicado si los vende y necesita proporcionar información bancaria y fiscal. Los procesos de Apple y Microsoft son mucho más complicados que los de Google, por lo que puede tardar una semana o más en aprobar su cuenta, por lo que debe factorizar este tiempo en sus planes de lanzamiento.

Una vez aprobada su cuenta, está listo para enviar una aplicación. El proceso real para enviar aplicaciones se trata en la siguiente documentación:

- [Publicación en la App Store de iOS de Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Preparación de una aplicación para Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
- Los desarrolladores de Windows deben visitar el [centro de desarrollo de Windows](https://developer.microsoft.com/windows/windows-apps) para leer sobre el envío de sus aplicaciones.

En el resto de esta sección se tratan los aspectos que debe tener en cuenta para asegurarse de que la aplicación se aprueba sin ningún interrupciones.

### <a name="quality"></a>Calidad

Suena obvio, pero las aplicaciones a menudo se rechazarán porque no cumplen un cierto nivel de calidad: después de todo, este es el motivo por el que los almacenes de seleccionada tienen un proceso de aprobación en primer lugar.

Los bloqueos son una razón común para el rechazo. Si es demasiado fácil de hacer que la aplicación se bloquee, se garantiza que se rechaza. La mayoría de los desarrolladores no envían sus aplicaciones con la expectativa de que se bloqueen, pero con frecuencia lo hacen. Pruebe minuciosamente la aplicación antes de enviarla, centrándose no solo en asegurarse de que todo funciona, sino también de que controla escenarios comunes de error móvil, como problemas de red y restricciones de recursos, como la memoria o el espacio de almacenamiento. Usar el simulador y los dispositivos físicos para realizar pruebas, independientemente de cómo se ejecute el código en un simulador, solo un dispositivo puede mostrar el rendimiento real de una aplicación. Use tantos dispositivos diferentes como pueda encontrar y dé de alta a un equipo de evaluadores de versiones beta si puede: los servicios de terceros pueden ayudar a administrar la distribución y los comentarios de la versión beta.

Todos los sistemas operativos móviles eliminarán una aplicación que no se inicia con la rapidez suficiente. El período de tiempo permitido varía, pero en las aplicaciones generales debe responder en unos segundos y usar tareas en segundo plano para realizar cualquier trabajo que tarde más tiempo. Las aplicaciones que tardan demasiado tiempo en cargarse o que no responden lo suficiente en el uso normal se rechazarán. Proporcione siempre comentarios de los usuarios cuando se produzca algo en segundo plano, o cuando la aplicación parezca que se ha bloqueado y, de nuevo, se ha rechazado.

### <a name="check-your-edge-cases"></a>Comprobar los casos de los límites

Una captura común para los desarrolladores no puede tratar los casos de los extremos, especialmente aquellos que requieren volver a configurar el simulador o el dispositivo para probar correctamente. Puede ser fácil olvidar que no todos los clientes van a "permitir" que la aplicación acceda a su ubicación porque, una vez que el desarrollador ha aceptado la solicitud una vez, nunca se le volverá a preguntar. Los permisos y el uso de la red se centran específicamente en el proceso de aprobación, lo que significa que una pequeña supervisión en estas áreas puede dar lugar a un rechazo.

La lista siguiente es un buen punto de partida para comprobar los casos de los extremos que podrían haberse perdido:

- **Los clientes pueden "denegar el acceso a los servicios** , especialmente en iOS, el acceso a los datos, como la información de ubicación geográfica, solo se proporciona si el usuario concede permiso a la aplicación. Los evaluadores de aplicaciones deben volver a instalar la aplicación con frecuencia en su estado inicial y no permitir ninguna solicitud de permiso para asegurarse de que la aplicación se comporta correctamente. Active y desactive los permisos para comprobar el comportamiento correcto a medida que los clientes cambian de opinión.
- **Los clientes se encuentran en todas partes** : no se da por sentado que una aplicación solo se usará en la ciudad o el país en el que se desarrolló. El código que funciona con coordenadas GPS, valores de fecha y hora y monedas puede verse afectado por la ubicación del cliente y la configuración regional. Todas las plataformas ofrecen un simulador que le permite especificar distintas ubicaciones y configuraciones regionales: Úsela para probar ubicaciones en otros esferas y con referencias culturales que dan formato a fechas y monedas de manera diferente. Los valores de latitud y longitud pueden ser positivos o negativos, el separador decimal puede ser un punto o una coma, y se puede dar formato a las fechas a una gran cantidad de formas.
- **Conexiones de red lentas** : los desarrolladores de aplicaciones suelen trabajar en un "mundo ideal" de conectividad de red rápida y en funcionamiento, que obviamente no es el caso del mundo real. La realización de pruebas con una conectividad de red lenta (por ejemplo, una conexión 3G deficiente), así como sin acceso a la red, es fundamental para garantizar que no se envía una aplicación con errores. El proceso de aprobación siempre incluirá una prueba con el dispositivo en modo de avión, por lo que debe asegurarse de que se ha probado para sí mismo.
- El **hardware varía** : Recuerde probar el hardware más antiguo y más lento que tiene previsto admitir. Hay dos aspectos que pueden afectar a la aplicación: rendimiento, que podría no ser utilizable en un dispositivo anterior, y compatibilidad con características de hardware como una cámara, un micrófono, un GPS, giroscopio u otro componente opcional. Las aplicaciones deben degradarse correctamente (y no bloquearse) cuando un componente no está disponible.

### <a name="guidelines-are-more-than-just-a-guide"></a>Las instrucciones son algo más que una "Guía"

Apple es famosa por ser estricto en cuanto al cumplimiento de las directrices de la interfaz de usuario, ya que uno de los puntos fuertes clave de su plataforma es la coherencia (y el aumento percibido en la facilidad de uso). Microsoft ha adoptado un enfoque similar con las aplicaciones Windows que implementan el [sistema de diseño fluida](https://microsoft.com/design/fluent). El proceso de aprobación de ambas plataformas implicará que la aplicación se evalúe para su adherencia a la filosofía de diseño pertinente.

Esto no significa que la innovación de la interfaz de usuario no se admite o se le anima, pero hay ciertas cosas que "simplemente no debe hacer" o que se rechazará la aplicación.

En iOS, esto incluye el uso indebido de iconos integrados o el uso de otras metáforas bien establecidas de una manera no coherente; por ejemplo, el uso del icono ' Compose ' para cualquier elemento que no sea la creación de contenido nuevo.

Los desarrolladores de Windows deben tener un cuidado similar; un error común es no admitir correctamente el botón atrás de hardware de acuerdo con las directrices de Microsoft.

Anime a los diseñadores a leer y seguir las instrucciones de diseño de cada plataforma.

### <a name="implementing-platform-specific-features"></a>Implementación de características específicas de la plataforma

Las cosas son un poco más estrictas en lo que respecta a la implementación de servicios específicos de la plataforma, especialmente en iOS. Para evitar el rechazo automático de Apple, hay algunas reglas que deben seguirse con las siguientes características de iOS:

- **Compras desde la aplicación** : las aplicaciones no deben implementar mecanismos de pago externos para productos digitales, como la moneda del juego, las características de las aplicaciones, las suscripciones de la revista y mucho más. las aplicaciones de iOS deben usar el servicio basado en iTunes de Apple para este tipo de funcionalidad. Hay un escape: aplicaciones como el lector Kindle y algunas aplicaciones basadas en suscripciones permiten adquirir contenido en otro lugar que se adjunta a una "cuenta" a la que se puede acceder a través de la aplicación; sin embargo, en este caso, la aplicación no debe contener vínculos ni referencias al proceso de compra fuera de la aplicación (o, una vez más, se rechazará).
- **copia de seguridad de icloud** : con la llegada de icloud, los revisores de Apple son mucho más estrictos con respecto al modo en que las aplicaciones usan Storage (para asegurarse de que la experiencia de copia de seguridad remota del cliente es agradable). Es posible que se rechacen las aplicaciones que desperdician espacio de almacenamiento capaz de copia de seguridad, por lo que debe usar la carpeta de caché correctamente y seguir las otras directrices relacionadas con el almacenamiento de Apple.
- **NewsStand** : las aplicaciones de periódicos y revistas son una buena opción para NewsStand de Apple. sin embargo, las aplicaciones deben implementar al menos una suscripción de renovación automática y admitir la descarga en segundo plano para su aprobación.
- **Maps** : cada vez es más común agregar superposiciones y otras características a las asignaciones móviles. sin embargo, tenga cuidado de no oscurecer la información de los créditos del mapa (como el logotipo de Google en IOS5), ya que esto provocará un rechazo.

### <a name="manage-your-metadata"></a>Administrar los metadatos

Además de los problemas técnicos obvios que pueden dar lugar a la rechazo de una aplicación, hay algunos aspectos más sutiles del envío que podrían dar lugar a un rechazo, especialmente en torno a los metadatos (descripción, palabras clave y imágenes de marketing) que Envíe su aplicación para que se muestre en la tienda de aplicaciones o en Marketplace.

- **Imágenes** : siga las instrucciones de la plataforma para los iconos de la aplicación y las imágenes de la tienda. No usar imágenes marcadas, hemos visto las aplicaciones que se rechazan porque sus iconos incluyen un dibujo de un iPhone.
- **Marcas comerciales** : Evite usar marcas que no sean las suyas propias. Se han denegado las aplicaciones para mencionar las marcas comerciales en la descripción de la aplicación o incluso en las palabras clave de la tienda de aplicaciones de Apple.
- **Descripción** : no use la palabra ' beta ' ni para indicar que la aplicación no está lista para la hora principal. No mencione otras plataformas móviles (aunque la aplicación sea multiplataforma). Y lo que es más importante, asegúrese de que la aplicación hace exactamente lo que hace. Si enumera una serie de características en la descripción, habrá sido más obvio cómo usar cada una de esas características, o bien obtendrá una "característica mencionada en el rechazo la descripción de la aplicación no está implementada".

Ponga todo el esfuerzo en los metadatos de la aplicación en el desarrollo y las pruebas. Las aplicaciones se rechazan por infracciones menores en los metadatos, por lo que merece la pena dedicar tiempo a conseguirlo.

### <a name="app-stores-not-for-everyone"></a>Tiendas de aplicaciones: No para todos

El objetivo principal de los almacenes en cada plataforma es la distribución del consumidor: la capacidad de llegar a tantos clientes como sea posible. Sin embargo, no todas las aplicaciones están destinadas a los consumidores, existe una base de aplicaciones de redes internas y de tipo extranet que requiere una distribución limitada a los empleados, proveedores o clientes. Estas aplicaciones no son "para venta" y no necesitan aprobación, ya que el desarrollador controla la distribución a un grupo cerrado de usuarios.
La compatibilidad con este tipo de implementación varía según la plataforma.

Android ofrece la máxima flexibilidad en este sentido: las aplicaciones se pueden instalar directamente desde una dirección URL o un archivo adjunto de correo electrónico (siempre y cuando la configuración del dispositivo lo permita). Esto significa que es trivial crear y distribuir aplicaciones corporativas internas o publicar aplicaciones para clientes o proveedores concretos.

Apple proporciona una opción de implementación interna para los desarrolladores inscritos en el programa para desarrolladores empresariales de iOS, que omite el proceso de aprobación del App Store y permite a las empresas distribuir aplicaciones internas a sus empleados.
Desafortunadamente, esta licencia no aborda la necesidad de la distribución de aplicaciones de tipo extranet a otros grupos cerrados de clientes o proveedores. [Implementación empresarial (y ad hoc)](~/ios/deploy-test/app-distribution/ipa-support.md)

### <a name="app-store-summary"></a>Resumen de la tienda de aplicaciones

El proceso de revisión puede ser desalentadora, pero, al igual que el resto del ciclo de vida de desarrollo, puede ayudar a garantizar el éxito con cierta planificación y atención al detalle. Todo se refiere a unos sencillos pasos: leer y comprender las directrices de la interfaz de usuario que debe cumplir, siga las reglas si va a implementar características específicas de la plataforma, realice pruebas exhaustivas (después pruebe algo más) y, por último, asegúrese de que los metadatos de la aplicación es correcto antes de enviar.

Una última palabra de consejos para los desarrolladores que publican en Google Play: la falta de proceso de aprobación puede parecer que facilita el trabajo, pero los clientes serán incluso más exigentes que un equipo de revisión. Siga estas instrucciones como si su aplicación pudiera rechazarse; de lo contrario, los clientes realizarán el rechazo.
