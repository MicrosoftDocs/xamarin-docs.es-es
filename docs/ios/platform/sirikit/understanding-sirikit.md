---
title: Descripción de los conceptos de SiriKit
description: En este documento se describen los conceptos clave necesarios para trabajar con SiriKit en una aplicación de Xamarin. iOS. Por ejemplo, se describen las extensiones de la interfaz de usuario de intents y los inconvenientes, los permisos de SiriKit, el diseño de una gran experiencia y mucho más.
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 2ba5d6b99a32684fd53572e0db00b19a6841ed64
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996466"
---
# <a name="understanding-sirikit-concepts"></a>Descripción de los conceptos de SiriKit

_En este artículo se tratan los conceptos clave necesarios para trabajar con SiriKit en una aplicación de Xamarin. iOS._

Novedad de iOS 10, SiriKit permite que una aplicación de Xamarin. iOS proporcione servicios que son accesibles para el usuario mediante Siri y la aplicación Maps en un dispositivo iOS. Esta funcionalidad se proporciona en una o más extensiones de aplicación mediante los **nuevos marcos** de interfaz de usuario de intents y **intents** .

SiriKit permite que una aplicación de iOS proporcione servicios que son accesibles para el usuario mediante Siri y la aplicación Maps en un dispositivo iOS mediante extensiones de aplicación y **los nuevos marcos** de interfaz de usuario de intents y **intents** .

Siri funciona con el concepto de **dominios**, grupos de acciones conocidas para tareas relacionadas. Cada interacción que tiene la aplicación con Siri debe encontrarse en uno de sus dominios de servicio conocidos de la siguiente manera:

- Llamada de audio o vídeo.
- Reservar un viaje.
- Administrar entrenamientos.
- Correo.
- Buscar fotografías.
- Enviar o recibir pagos.

Cuando el usuario realiza una solicitud de Siri que implica a uno de los servicios de la extensión de la aplicación, SiriKit envía a la extensión un objeto de **intención** que describe la solicitud del usuario junto con los datos auxiliares. A continuación, la extensión de la aplicación genera el objeto de **respuesta** adecuado para el **objetivo**dado, lo que detalla cómo la extensión puede controlar la solicitud.

## <a name="the-intents-and-intents-ui-extensions"></a>Extensiones de la interfaz de usuario de intents y intents

Tanto Siri como la aplicación Maps interactúan con los servicios de la aplicación a través de dos tipos diferentes de extensiones de aplicación:

- **Extensión de intents** : proporciona Siri y se asigna con el contenido de la aplicación y realiza las tareas necesarias para cumplir con las intenciones admitidas.
- **Extensión de la interfaz de usuario de intents** : proporciona una interfaz de usuario personalizada que se mostrará para el contenido de la aplicación dentro de Siri o Maps.

La aplicación debe proporcionar una extensión de intents para admitir SiriKit y es responsable de proporcionar información que Siri y Maps pueden presentar al usuario y para controlar las intenciones.

La creación de una extensión de interfaz de usuario de intents es opcional, ya que Siri normalmente controla toda la interacción del usuario y tiene una interfaz de usuario estándar integrada para presentar información en cada uno de los dominios admitidos. Al proporcionar una extensión de interfaz de usuario de intents, la aplicación puede usar el marco de interfaz de usuario de **intención** para presentar una interfaz de usuario enriquecida y personalizada que incluye la personalización de marca y la información adicional de la aplicación.

## <a name="siri-and-the-maps-app-role"></a>Siri y el rol de aplicación Maps

Las solicitudes habladas del usuario se procesan y se analizan semánticamente mediante Siri, lo que convierte esas solicitudes en intentes que pueden procesarse por las extensiones de intención.

Maps usa las extensiones de intención de la aplicación para mostrar información en la interfaz de asignación en respuesta a las acciones del usuario. Por ejemplo, solicitar restaurantes cercanos u obtener las revisiones de restaurante de la aplicación.

Tanto Siri como Maps administran todas las interacciones del usuario y muestran los resultados mediante la interfaz estándar del sistema. El rol extensiones de aplicación es principalmente para proporcionar los datos que se muestran. Opcionalmente, la aplicación puede proporcionar una extensión de interfaz de usuario de intents y presentar una interfaz de usuario personalizada para mejorar la interfaz predeterminada del sistema.

## <a name="interacting-with-siri-via-sirikit"></a>Interacción con Siri mediante SiriKit

En esta sección se presentará una visión general de cómo SiriKit permite al usuario interactuar con la aplicación mediante Siri. En este ejemplo, vamos a usar la aplicación MonkeyChat falsa:

[![El icono de MonkeyChat](understanding-sirikit-images/monkeychat01.png)](understanding-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene su propio libro de contactos de los amigos del usuario, cada uno asociado a un nombre de pantalla (por ejemplo, Bobo) y permite al usuario enviar chats de texto a cada amigo por su nombre de pantalla.

Hay muchas maneras en las que el usuario puede iniciar una interacción con la aplicación, ya que es posible que distintas personas realicen la misma solicitud en muchas formas diferentes.

Por ejemplo, si el usuario desea enviar un mensaje a su Bobo de confianza, es posible que tenga la conversación siguiente con Siri:

_Usuario: Hola Siri, enviar un mensaje MonkeyChat._<br />
_Siri: ¿a quién?_<br />
_Usuario: Bobo._<br />
_Siri: ¿Qué quiere decir Bobo?_<br />
_Usuario: envíe más plátanos._<br />

Otra persona podría hacer la misma solicitud con una conversación diferente:

_Usuario: envía un mensaje a Bobo en MonkeyChat._<br />
_Siri: ¿Qué quiere decir Bobo?_<br />
_Usuario: envíe más plátanos._<br />

Y otro usuario podría hacer una solicitud incluso más corta:

_Usuario: MonkeyChat Bobo envíe más plátanos._<br />
_Siri: OK, enviando mensaje envíe más plátanos a Bobo en Monkeychat._<br />

O incluso hacer la misma solicitud en un idioma diferente:

_Usuario: MonkeyChat Bobo s'il vous plaît._<br />
_Siri: oui, envoi Message s'il vous plaît de envío más de bananes à Bobo sur Monkeychat._<br />

Otro usuario podría ser muy detallado en su conversación:

_Usuario: Hola Siri, ¿puedo hacer un favor e iniciar la aplicación MonkeyChat para enviar un texto con el mensaje, envíe más plátanos?_<br />
_Siri: ¿a quién?_<br />
_Usuario: mi mejor Bobo PAL._<br />

Además, hay muchas maneras en las que Siri puede responder a una solicitud, algunas en función de cómo se realizó la solicitud:

- **Al mantener el botón Inicio** , Siri proporcionará más respuestas visuales con comentarios verbales limitados.
- **"Hola Siri"** : Siri será más verbal y proporcionará menos respuestas visuales.

Siri también se ajusta para satisfacer las necesidades de accesibilidad del usuario y interactuará y responderá en función de esas necesidades.

Independientemente de cómo se realice una solicitud o de cómo responda Siri a la solicitud, Siri controla la conversación con el usuario y la aplicación (a través de sus extensiones) proporciona la funcionalidad.

Cuando el usuario realiza una solicitud verbal de Siri, estos son los pasos que Siri seguirá:

[![Los pasos que seguirá Siri](understanding-sirikit-images/monkeychat02.png)](understanding-sirikit-images/monkeychat02.png#lightbox)

1. En primer lugar, Siri toma el audio de la **voz** del usuario y lo convierte en texto.
2. Después, el texto se convierte en una **intención**, una representación estructurada de la solicitud del usuario.
3. En función de la intención, Siri tomará **medidas** para realizar la solicitud del usuario.
4. Por último, Siri presentará las **respuestas** (visual y verbal) al usuario en función de la acción realizada.

Hay tres maneras principales en las que la aplicación puede participar en la conversación del usuario con Siri:

[![Las tres formas principales en que la aplicación puede participar en la conversación de los usuarios con Siri](understanding-sirikit-images/monkeychat03.png)](understanding-sirikit-images/monkeychat03.png#lightbox)

1. **Vocabulario** : este es el modo en que la aplicación indica a Siri las palabras que necesita saber para interactuar con ella.
2. **Lógica de aplicación** : son las acciones y las respuestas que la aplicación tomará en función de los intentos determinados.
3. **Interfaz de usuario** : esta es la interfaz de usuario personalizada opcional a la que la aplicación puede proporcionar sus respuestas.

### <a name="example"></a>Ejemplo

Dada la información anterior, examine el modo en que la conversación siguiente interactuaría con la aplicación MonkeyChat:

_Usuario: Hola Siri, enviar un mensaje a Bobo en MonkeyChat._<br />
_Siri: ¿Qué quiere decir Bobo?_<br />
_Usuario: envíe más plátanos._<br />

El primer rol que toma la aplicación en la conversación es ayudar a Siri a comprender la voz del usuario:

[![Ayudar a Siri a comprender la voz de los usuarios](understanding-sirikit-images/monkeychat04.png)](understanding-sirikit-images/monkeychat04.png#lightbox)

Siri no tiene el nombre "Bobo" en su base de datos, pero la aplicación lo hace y ha compartido esta información con Siri a través de su vocabulario. La aplicación también ayuda a Siri a reconocer que Bobo es un destinatario, ya que lo especificó en Siri como *contacto*.

Siri sabe que se requiere más para enviar un mensaje que solo un destinatario, de modo que se comprobará rápidamente con la extensión de la aplicación para ver si un mensaje requiere contenido. Como MonkeyChat, Siri responderá al usuario con la pregunta: *"¿Qué quiere decir a Bobo?"*

En el ejemplo anterior, el usuario ha respondido, *"envíe más plátanos"*, que Siri agrupará en un **intento**estructurado:

[![Siri agrupará la respuesta del usuario en un intento estructurado](understanding-sirikit-images/monkeychat05.png)](understanding-sirikit-images/monkeychat05.png#lightbox)

La intención estructurada contendrá la siguiente información:

- **Dominio:** Mensajes
- **Intención:** SendMessage
- **Destinatario:** Bobo
- **Contenido:** Envíe más plátanos

Cada dominio tiene un conjunto de *acciones* conocidas que se pueden realizar dentro de ellas y que se basan en el dominio y la acción, es posible que se incluyan de cero a muchos parámetros en el intento enviado a la aplicación.

A continuación, el intento se envía a la extensión de la aplicación para su procesamiento. Como resultado del procesamiento de la intención, la aplicación generará un **IntentResponse** que se incluirá con la intención e incluirá parámetros que describen lo que la aplicación hizo con el intento.

Cada IntentResponse también incluirá un **código de respuesta** que indica a Siri si la aplicación pudo completar la solicitud o no. Algunos dominios tienen códigos de respuesta de error muy específicos que también se pueden enviar.

Por último, el IntentResponse incluirá un `NSUserActivity` (como los que se usan para admitir la entrega). Se `NSUserActivity` usará para iniciar la aplicación si la respuesta requiere que abandone el entorno de Siri y escriba la aplicación para completarla.

Siri creará automáticamente una adecuada `NSUserActivity` para iniciar la aplicación y la recogida en la que el usuario se quedó en el entorno de Siri. Sin embargo, la aplicación puede proporcionar su propia `NSUserActivity` información personalizada, si es necesario.

Una vez que la aplicación ha procesado la intención y ha devuelto una respuesta a Siri, presenta los resultados al usuario (tanto verbalmente como visualmente):

[![Los resultados que se presentan al usuario tanto verbalmente como visualmente](understanding-sirikit-images/monkeychat06.png)](understanding-sirikit-images/monkeychat06.png#lightbox)

Siri tiene varias interfaces de usuario de respuesta integradas para cada uno de los dominios disponibles para la aplicación. Sin embargo, como MonkeyChat ha proporcionado una extensión de IU de intención opcional, se usa para presentar los resultados de la conversación al usuario en el ejemplo anterior.

## <a name="the-intent-lifecycle"></a>Ciclo de vida de la intención

Hay tres tareas principales que la extensión de la aplicación tendrá que realizar al tratar con los intentos:

[![Ciclo de vida de la intención](understanding-sirikit-images/monkeychat07.png)](understanding-sirikit-images/monkeychat07.png#lightbox)

1. La aplicación debe **resolver** todos los parámetros de un evento. Como resultado, la aplicación llamará a Resolve varias veces (una vez por cada parámetro) y, a veces, varias veces en el mismo parámetro hasta que la aplicación y el usuario acuerden lo que se solicita.
2. La aplicación debe **confirmar** que puede controlar el intento solicitado e indicar a Siri sobre el resultado esperado.
3. Por último, la aplicación debe **controlar** el intento y realizar los pasos necesarios para lograr el resultado solicitado.

### <a name="the-resolve-stage"></a>La fase de resolución

La fase de resolución ayuda a Siri a comprender los valores que el usuario ha proporcionado y garantiza que lo que realmente quiere decir es lo que ocurrirá cuando la aplicación procese la intención.

Esta fase también proporciona una oportunidad para que la aplicación influya en el comportamiento de Siri durante la conversación con el usuario. Para ello, la aplicación proporcionará una **respuesta de resolución**. Hay una serie de respuestas predefinidas a los distintos tipos de datos que entiende Siri.

La respuesta de resolución más común de la aplicación será **correcta**, lo que significa que la aplicación coincidía con la parte de datos específica de un parámetro (como el nombre de la pantalla de usuario) con una parte de la información que conoce.

Puede haber ocasiones en las que la aplicación deba confirmar que una solicitud determinada coincide con la parte correcta de la información que conoce. En estos casos, enviará una respuesta de **ConfirmationRequired** para formular una pregunta sí o no al usuario, como *"Enviar mensaje a Bobo la excelente?"* .

Puede haber otros casos en los que la aplicación necesitará que el usuario elija una lista de opciones breve. En este caso, la aplicación proporcionará una respuesta de anulación de **ambigüedades** con una lista de entre dos y diez opciones para que el usuario elija:

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

Siri controlará al usuario que realiza la selección, ya sea verbalmente o interactuando con la interfaz de usuario de Siri, y el resultado se devolverá a la aplicación.

En otros casos, puede que no haya suficiente información para que la aplicación resuelva el parámetro o que haya demasiadas coincidencias para resolver el uso de la desambiguación (por ejemplo, 80 usuarios con Bobo en su nombre). En este caso, la aplicación enviará una respuesta **NeedsMoreDetails** y Siri le pedirá al usuario que sea más específico.

Si el usuario no proporcionó un valor necesario para procesar el intento, puede enviar una respuesta **NeedsValue** para que Siri solicite el valor al usuario.

Si la aplicación no admite un valor que el usuario haya dado para un parámetro concreto, puede enviar la respuesta **UnsupportedWithReason** para proporcionar un motivo por el que no se admitía el valor. A continuación, Siri solicitará al usuario un valor completamente nuevo y les dará el motivo de que sea necesario.

Por último, use la respuesta **NotRequired** para indicar a Siri que la aplicación no requiere un valor para un parámetro determinado. Si el usuario proporciona uno de todos modos, simplemente lo omitirá Siri.

### <a name="the-confirm-stage"></a>Fase de confirmación

La fase de confirmación tiene dos propósitos:

- Para indicar a Siri el resultado esperado de controlar una intención para que Siri pueda indicar al usuario lo que va a ocurrir.
- Proporciona una comprobación de la oportunidad de los Estados necesarios que la aplicación podría necesitar para completar la solicitud presentada por el usuario, por ejemplo, con un importe de dinero suficiente en el Banco para realizar el pago solicitado.

La aplicación proporcionará una **respuesta de intención** desde el paso de confirmación, que se debe rellenar con toda la información que la aplicación tiene disponible, por lo que Siri puede comunicarse eficazmente con el usuario.

En función del dominio y el tipo de acción, Siri puede solicitar confirmación al usuario, por ejemplo, antes de enviar un pago o de reservar un viaje.

### <a name="the-handle-stage"></a>La fase de identificador

La fase de control es la parte más importante del trabajo con una intención porque es el punto en el que la aplicación cumple la solicitud del usuario realizando la tarea que se le ha solicitado.

Al igual que en la fase de confirmación, la aplicación debe proporcionar toda la información sobre el resultado como sea posible para que Siri pueda relacionarla con el usuario. A veces, esta información se presentará visualmente u otras veces Siri simplemente la devolverá al usuario.

Puede haber ocasiones en las que la aplicación necesite un tiempo adicional para procesar una solicitud determinada, como retrasos de la llamada de red o si una persona activa necesita completar la solicitud (por ejemplo, completar y enviar un pedido o dirigir un automóvil a la ubicación del usuario). Cuando Siri está esperando una respuesta de la aplicación, muestra una interfaz de usuario en espera para el usuario que le indica que la aplicación está procesando la solicitud.

Idealmente, la aplicación debe proporcionar una respuesta a Siri dentro de dos o tres segundos como máximo. Si la aplicación sabe que una respuesta determinada va a tardar más tiempo en procesarse, debe enviar un código de respuesta en **curso** a Siri. A continuación, Siri informará al usuario de que la aplicación está procesando la solicitud en segundo plano y continuará haciendo esto incluso si deja el entorno de Siri.

## <a name="adding-sirikit-to-the-app"></a>Agregar SiriKit a la aplicación

Con SiriKit en iOS 10, Apple ha creado dos nuevos puntos de extensión:

- **Extensión de intents** : proporciona Siri con el contenido de la aplicación y realiza las tareas necesarias para cumplir los intentos admitidos.
- **Extensión de la interfaz de usuario de intents** : proporciona una interfaz de usuario personalizada que se mostrará para el contenido de las aplicaciones dentro de Siri.

También hay una API para proporcionar palabras y frases a Siri para ayudar en el reconocimiento en la forma de:

- **Vocabulario** de la aplicación: palabras y frases que son comunes a todos los usuarios de la aplicación.
- **Vocabulario de usuario** : palabras y frases que son únicas para un usuario de aplicación determinado.

## <a name="the-intents-extension"></a>La extensión intents

La extensión intents es responsable de controlar las interacciones principales entre la aplicación y Siri como se indica a continuación:

[![La extensión intents](understanding-sirikit-images/intents01.png)](understanding-sirikit-images/intents01.png#lightbox)

La extensión de intención puede admitir una o más intenciones, por lo que el desarrollador decide cómo desean implementar SiriKit en la aplicación. El desarrollador también podría agregar una extensión de intención independiente para cada intención que deba controlarse.  Dicho esto, Apple solicita que el desarrollador limite el número de extensiones de intención para que Siri no tenga varios procesos abiertos en la aplicación, lo que requiere más memoria y tiempo para controlar.

El desarrollador también debe tener en cuenta que la extensión de intención se ejecutará en segundo plano mientras Siri está activo. Esto permite que Siri realice activamente una conversación con el usuario al mismo tiempo que se comunica con la extensión para procesar la información sobre la solicitud.

## <a name="privacy-and-security-considerations"></a>Consideraciones sobre privacidad y seguridad

Apple ha tomado grandes medidas para garantizar que la información privada de un usuario es segura al trabajar con Siri y, como tal, hay varias interacciones que requieren que el usuario inicie sesión en el dispositivo iOS. Por ejemplo, al solicitar una conducción o efectuar un pago.

Además, hay comportamientos específicos que la aplicación podría querer limitar al usuario que ha iniciado sesión en el dispositivo. En estas situaciones, la aplicación puede solicitar el comportamiento **restringir mientras está bloqueado** . Esto se hace a través de una configuración en el `Info.plist` archivo.

El marco de autenticación local está disponible para la extensión de intención, por lo que la aplicación puede pedir al usuario información adicional de autenticación, incluso si el dispositivo ya está desbloqueado.

Por último, Apple Pay está disponible para la extensión de intención, por lo que la aplicación puede completar una transacción mediante Apple Pay y la hoja de Apple Pay integrada aparecerá encima de la interfaz Siri.

Además, Apple desea asegurarse de que los usuarios saben cuándo envían información a una aplicación de terceros y como tal, el usuario **debe** indicar el nombre específico de la aplicación (como se especifica en el nombre para mostrar del lote de la aplicación) al hacer una solicitud.

Apple ha diseñado Siri para llevar a cabo conversaciones naturales y fluidas con el usuario y, por este motivo, el nombre de la agrupación de la aplicación se puede usar en muchas partes de la voz, dondequiera que se ajuste a la solicitud del usuario de forma natural.

Uno de los aspectos comunes que los usuarios realizarán es "verbify" el nombre de la aplicación, es decir, tomar el nombre de la aplicación y usarlo como verbo en una solicitud. Por ejemplo, *"MonkeyChat Bobo eran excelentes plátanos".*

## <a name="the-intents-ui-extension"></a>La extensión de la interfaz de usuario de intents

La extensión de la interfaz de usuario de intents presenta la oportunidad de llevar la interfaz de usuario y la personalización de marca de la aplicación a la experiencia de Siri y hacer que los usuarios se sientan conectados a la aplicación. Con esta extensión, la aplicación puede traer la marca, así como el visual y otra información, a la transcripción.

[![Salida de la extensión de interfaz de usuario de intents de ejemplo](understanding-sirikit-images/intents02.png)](understanding-sirikit-images/intents02.png#lightbox)

La extensión de la interfaz de usuario de intents siempre devolverá un `UIViewController` y la aplicación puede agregar cualquier cosa que le guste dentro del controlador de vistas, como Mostrar información adicional que va más allá de la respuesta inicial. La interfaz de usuario de intents también puede actualizar el usuario con el estado de un evento de ejecución prolongada, por ejemplo, cuánto tiempo tardará un automóvil de uso compartido en llegar a su ubicación.

La extensión de la interfaz de usuario de intents siempre se mostrará junto con otro contenido de Siri, como el icono y el nombre de la aplicación en la parte superior de la interfaz de usuario o, en función de la intención, los botones (como enviar o cancelar) se pueden mostrar en la parte inferior.

Hay algunos casos en los que la aplicación puede reemplazar la información que Siri muestra al usuario de forma predeterminada, como la mensajería o las asignaciones en las que la aplicación puede reemplazar la experiencia predeterminada por una adaptada a la aplicación.

> [!IMPORTANT]
> Aunque es posible agregar elementos interactivos como `UIButtons` o `UITextFields` a la extensión de la interfaz de usuario de intención `UIViewController` , están estrictamente prohibidos como la interfaz de usuario de intención en el modo no interactivo y el usuario no podrá interactuar con ellos.

Es totalmente opcional que la aplicación proporcione una extensión de interfaz de usuario con intención, ya que Siri contiene un conjunto predeterminado de interfaz de usuario para cada tipo de intento. Además, las interfaces de la interfaz de usuario de intents solo están disponibles para ciertos intentos que Apple ha considerado como útiles para el usuario.

## <a name="adding-sirikit-vocabulary"></a>Agregando vocabulario de SiriKit

La parte final de la implementación de SiriKit se encuentra dentro de la aplicación proporcionando el vocabulario requerido. Muchas aplicaciones tienen formas únicas de describir la información al usuario y las formas únicas en que el usuario proporcionará información a la aplicación.

Debido a esto, Siri requiere la ayuda de la aplicación para comprender las palabras y frases únicas de la aplicación. Algunas de estas frases formarán parte de la aplicación para que todos los usuarios la sepan y las entienda. Pero otros serán únicos para un usuario determinado de la aplicación.

### <a name="app-specific-vocabulary"></a>Vocabulario específico de la aplicación

El vocabulario específico de la aplicación define las palabras y frases específicas que conocerán todos los usuarios de la aplicación, como los tipos de vehículos o los nombres de entrenamiento. Dado que forman parte de la aplicación, se definen en un `AppIntentVocabulary.plist` archivo como parte del grupo de aplicaciones principal. Además, se deben localizar estas palabras y frases.

Hay varias partes en un archivo de vocabulario `AppIntentVocabulary.plist` :

- **Ejemplo de usos** de la aplicación: proporcionan un conjunto de casos de uso comunes para las solicitudes que el usuario puede hacer de la aplicación. Por ejemplo: *"iniciar un entrenamiento con MonkeyFit".*
- **Parámetros** : proporcionan un conjunto de tipos de parámetros no estándar específicos de la aplicación. Por ejemplo, los nombres de entrenamiento de la aplicación MonkeyFit. Constan de:
  - **Frase** : permite que la aplicación defina términos únicos para la aplicación. Por ejemplo: el tipo de entrenamiento "Bananarific" para la aplicación MonkeyFit.
  - **Pronunciación** : proporciona sugerencias de Pronunciación a Siri como una escritura fonética simple para una frase determinada. Por ejemplo, "BA Nana RI fica".
  - **Ejemplo** : proporciona un ejemplo del uso de la frase determinada en la aplicación. Por ejemplo, *"Start a Bananarific in MonkeyFit"*.

Para obtener más información, consulte referencia del [formato de archivo de vocabulario de aplicación](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)de Apple.

### <a name="user-specific-vocabulary"></a>Vocabulario específico del usuario

El vocabulario específico del usuario va a proporcionar palabras o frases que son únicas para los usuarios individuales de la aplicación. Estos se proporcionarán en tiempo de ejecución desde la aplicación principal (no las extensiones de aplicación) como un conjunto ordenado de términos ordenados en una prioridad de uso más importante para los usuarios, con los términos más importantes al principio de la lista.

Eche un vistazo al ejemplo de la aplicación MonkeyChat mostrada anteriormente. MonkeyChat mantiene una lista de todos los contactos del usuario, que se enviará a Siri a través del vocabulario específico del usuario. También mantiene una lista de los 10 contactos más recientes en los que el usuario ha dado un mensaje y tiene un conjunto de contactos favoritos para cada usuario. En este ejemplo, los contactos favoritos deben estar al principio de nuestro vocabulario específico del usuario, seguidos de los contactos recientes y, después, el resto de los contactos del usuario.

El vocabulario específico del usuario admite los siguientes tipos de información:

- Nombres de contacto.
- Nombres de entrenamiento.
- Nombres de álbumes de fotografías.
- Palabras clave de foto.

Si la aplicación se basa en la libreta de direcciones de iOS, la aplicación no tendrá que realizar ninguna acción, ya que esta información ya está disponible para Siri. La aplicación solo necesita proporcionar nombres de contacto si la aplicación tiene su propia base de datos única de contactos.

Al diseñar el vocabulario, solo debe proporcionar los valores necesarios que los usuarios conocen y interesan. Evite proporcionar información como números de teléfono o direcciones de correo electrónico.

La aplicación también necesita actualizar Siri inmediatamente cuando cambia el vocabulario específico del usuario. Los usuarios están acostumbrados a solicitar información de Siri en el momento en que se ha agregado a su dispositivo iOS. Por ejemplo, si el usuario agrega un nuevo contacto en la aplicación, envíe la información a Siri tan pronto como el usuario la guarde.

Lo que es más importante, la aplicación _debe_ eliminar la información del vocabulario Siri rápidamente, ya que un usuario podría ser perdedoble si eliminaba un fragmento de información, pero Siri todavía lo reconocía horas o días.

> [!IMPORTANT]
> La aplicación debe quitar todo el vocabulario específico del usuario de Siri si el usuario decide restablecer la aplicación o si se inicia sesión.

## <a name="sirikit-permissions"></a>Permisos de SiriKit

La parte final de SiriKit se centra en los permisos. Al igual que con otras características de iOS (como fotos, cámara o contactos), los usuarios tienen que conceder permiso explícito para que la aplicación se comunique con Siri.

La aplicación puede proporcionar una cadena que defina la información que va a proporcionar a Siri y proporcione un motivo de por qué el usuario debe conceder este acceso.

Apple sugiere que la aplicación debe solicitar permiso del usuario para usar Siri la primera vez que el usuario abre la aplicación después de haber actualizado a iOS 10. Esto es para que los usuarios sepan la integración de Siri y puedan usar el uso previamente aprobado antes de que realicen su primera solicitud.

## <a name="sirikit-and-maps"></a>SiriKit y Maps

SiriKit es una parte integral de iOS y hace uso del marco de trabajo de mayor tamaño agregado a iOS 10. El marco de trabajo de intents se diseñó para compartir acciones comunes y compartidas con otras partes del sistema.

El marco intents va más allá de la integración de Siri y proporciona otras características, como la integración de contactos, donde la aplicación puede convertirse en la aplicación de telefonía o mensajería predeterminada para contactos específicos. Las intenciones también proporcionan una integración profunda con CallKit para proporcionar a los usuarios la mejor experiencia de VOIP posible.

La aplicación Maps en iOS 10 ha agregado características como el uso compartido de la ubicación en la que el usuario puede reservar un viaje directamente dentro de la interfaz de usuario de maps. SiriKit proporciona un punto de extensión común con mapas, por lo que se pueden compartir los intentos de uso compartido (y otros) entre Siri y Maps.

Esto significa que si la aplicación ha adoptado las extensiones SiriKit, también recibirá la integración de Maps de forma gratuita.

## <a name="designing-a-great-siri-experience"></a>Diseño de una experiencia de Siri fantástica

Diseñar una excelente experiencia del usuario al integrar una aplicación en Siri es diferente de diseñar una excelente interfaz de usuario de la aplicación. A diferencia de las situaciones normales en las que el usuario está interactuando con la aplicación directamente en la pantalla, cuando se usa Siri, hay muchas ocasiones en las que no hay ninguna interfaz visual visible. Por ejemplo, cuando el usuario ha iniciado la conversación con *"Hola Siri"*.

### <a name="how-siri-helps-the-developer"></a>Cómo ayuda Siri el desarrollador

Al diseñar interacciones de una aplicación con Siri, la aplicación creará una *interfaz de conversación*, lo que significa que el contexto se deriva de la conversación que Siri tiene con el usuario en nombre de la aplicación.

En ausencia de una referencia visual, el usuario debe realizar un seguimiento de la información que se presenta en su encabezado. Por este motivo, Siri presenta la información mínima necesaria para lograr la tarea que el usuario desea realizar.

La interfaz de conversación está formada por las preguntas y respuestas del usuario y de Siri durante la conversación. Por lo tanto, es importante pensar en cómo Siri formula preguntas y responde al diseñar esta interfaz.

Tome el ejemplo siguiente del usuario que crea un mensaje, Siri puede responder con la pregunta *"¿está listo para enviarlo?"*. El usuario podría responder de muchas maneras diferentes, como *"enviarlo"*, *"Cancelar"* o incluso algo que no esté relacionado con esta pregunta. Independientemente de cómo se reproduzca la conversación, Siri la controlará para la aplicación y solo la enviará la información relevante a medida que esté disponible.

Hay varias maneras diferentes de que un usuario pueda iniciar una conversación con Siri:

- Al seleccionar el dispositivo, presione el botón Inicio. En esta situación, Siri presentará más interfaces visuales y respuestas menos verbales.
- Al decir *"Hola Siri"* e iniciar una conversación gratuita. En esta situación, Siri será menos visual y más verbal.
- Usar características de accesibilidad como ayudas auditivas habilitadas para Bluetooth, donde la interfaz de usuario se adaptará a un usuario con necesidades especiales.
- Usar la reproducción de automóviles, donde el usuario necesita mantener su atención centrada en la conducción, manteniendo el mínimo de distracciones.

### <a name="how-the-developer-helps-siri"></a>Cómo ayuda el desarrollador a Siri

Al integrar una aplicación con Siri, el desarrollador debe probar esta integración con frecuencia y asegurarse de que se realizan muchas solicitudes diferentes solicitando el mismo fragmento de información o tarea en tantas formas como sea posible.

Dado que dos personas piensan que no son iguales, es fundamental que el desarrollador obtenga tantos evaluadores beta como sea posible para ayudar a ajustar la integración de Siri. Es posible que los usuarios soliciten información o realicen solicitudes de forma que el desarrollador no lo haga nunca y este ajuste puede ayudar a garantizar que el grupo más amplio de usuarios tenga una gran experiencia con la aplicación con Siri.

Pruebe en situaciones y entornos diferentes. Inicie las conversaciones con Siri en todas las formas posibles para asegurarse de que estas conversaciones permanecen fluidas y naturales. Pruebe en ubicaciones donde el usuario más de lo probable sería usar la aplicación, como en un gimnasio abarrotado.

Asegúrese de que la aplicación proporciona toda la información que Siri necesita para representar correctamente la solicitud y el resultado al usuario. Esto es especialmente cierto cuando se usa Siri en una situación de manos libres.

### <a name="siri-design-guidelines"></a>Directrices de diseño de Siri

Recuerde siempre que Siri tiene una conversación con el usuario en nombre de la aplicación. El desarrollador desea asegurarse de que esta conversación se mantiene tan fluida y natural como sea posible.

Como lo harían con cualquier conversación importante, el desarrollador debe asegurarse de lo siguiente:

- Que la aplicación está preparada para la conversación.
- Que la aplicación escucha exactamente lo que el usuario está intentando realizar.
- Que la aplicación formule las preguntas adecuadas en el momento adecuado.
- Que la aplicación responde a la solicitud con la información que el usuario está buscando.

#### <a name="preparing-for-the-conversation"></a>Preparación para la conversación

Lo primero que hay que recordar es que los usuarios de la aplicación no van a ser exactamente como el desarrollador. Pueden provienen de diferentes procedencias, hablan de distintos idiomas o tienen necesidades especiales al trabajar con la aplicación.

Además, dado que el desarrollador diseñó y compiló la aplicación, tiene un conocimiento profundo y profundo de la aplicación y de sus funciones y trabajos internos que no tendrá un usuario típico. Por lo tanto, el desarrollador podría solicitar una solicitud de Siri de forma distinta a un usuario normal.

Esta es la razón por la que es fundamental tener tantas personas diferentes como sea posible interactuar con la aplicación a través de Siri. Los usuarios pueden realizar solicitudes de la aplicación a través de Siri que el desarrollador nunca ha considerado o de maneras en las que el desarrollador no tiene en cuenta.

#### <a name="ensure-the-app-is-a-good-listener"></a>Asegúrese de que la aplicación es un buen agente de escucha

El desarrollador debe asegurarse de que la aplicación es un buen agente de escucha y obtiene los detalles de la conversación que cumplen las expectativas del usuario. Pero también es posible que no hayan proporcionado toda la información que la aplicación necesita para lograr la tarea solicitada.

Hay varias maneras en las que la aplicación podría controlar esta situación:

- **Elija un valor predeterminado para el valor que falta** ; por ejemplo, una aplicación de uso compartido de acceso puede tener como valor predeterminado la ubicación actual del usuario si no especificó dónde quería recogerse.
- **Realice una estimación instruida** : mediante la información específica que la aplicación ha recopilado en el usuario, es posible que la aplicación pueda realizar y instruir la información que falta, como rellenar el número de teléfono móvil que falta de la información de contacto del usuario. Sin embargo, debe tener cuidado para evitar sorpresas incorrectas, como seleccionar la opción más costosa, etc.
- **Pedir más información** : la aplicación puede tener Siri pedir al usuario el valor que falta. Sin embargo, la clave aquí es mantener las conversaciones simples y hasta el punto. Los usuarios se verán frustrados rápidamente si tienen que responder a varias preguntas para lograr su solicitud.
- **Controlar la información incorrecta correctamente** : el usuario podría proporcionar un valor que la aplicación no esperaba o que no se puede controlar en el contexto especificado. Asegúrese de que la aplicación relaciona esta situación con el usuario de forma que resulte clara y fácil de corregir.

Cuando la aplicación se presenta con un valor único en cuestión, la mejor manera de controlarlo es hacer que Siri pida confirmación al usuario. Por ejemplo, *"¿quiso decir que Bobo la excelente?"*, a la que puede responder con una respuesta simple sí o no.

Cuando hay una situación en la que varias opciones posibles pueden ser correctas para un único valor, la desambiguación es el método de control preferido. En esta situación, Siri puede preguntar al usuario con hasta diez opciones posibles entre las que elegir. Por ejemplo:

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

Si sigue en duda, pida al usuario que proporcione una respuesta completamente nueva y más específica para un valor determinado.

#### <a name="request-final-confirmation"></a>Solicitar confirmación final

Antes de que la aplicación realice realmente la tarea para completar la solicitud del usuario, Siri comprobará con la extensión de la aplicación para asegurarse de que todo está en su lugar. Por ejemplo, ¿tiene el usuario suficiente dinero en su cuenta para realizar el pago solicitado?

Además, la aplicación debe asegurarse de que proporciona toda la información posible a Siri para que pueda presentarla al usuario y confirmar que la tarea que se va a realizar satisface sus expectativas.

Una vez que el usuario ha confirmado la solicitud y la aplicación la ha realizado, la aplicación debe asegurarse de que se han proporcionado todos los resultados a Siri para que pueda relacionarlas con el usuario.

#### <a name="responding-to-the-request"></a>Responder a la solicitud

Siri tiene varias interfaces de usuario integradas para cada uno de los dominios y las acciones que conoce. Sin embargo, si es necesario, la aplicación puede proporcionar una extensión de interfaz de usuario de intención personalizada para enriquecer la experiencia del usuario presentando la marca y la interfaz de usuario de la aplicación o más información de la que estaba presente en la solicitud.

Dicho esto, se debe usar la restricción al diseñar interfaces personalizadas para Siri. Normalmente, el usuario desea obtener una tarea específica lo más rápido posible y no desea sobrecargarla con información innecesaria.

También debe tener cuidado para asegurarse de que la interfaz de usuario personalizada parezca y responda correctamente en todos los dispositivos iOS y orientaciones diferentes que el usuario pueda tener o que use el dispositivo.

Cuando sea necesario, use la API SiriKit para ocultar cualquier información redundante que ya esté presente en la interfaz de usuario predeterminada de Siri. Además, asegúrese de que la aplicación sigue proporcionando la información a Siri para que pueda presentarla de forma verbal en situaciones gratuitas.

Puede haber situaciones en las que Siri iniciará la aplicación para satisfacer la solicitud del usuario, como presentar las fotos solicitadas por el usuario. En estas situaciones, no sorprende al usuario. Mostrar la información esperada sin tener que realizar pasos intermedios o una interacción adicional. Nunca Mostrar información o realizar una tarea que el usuario no espera.

### <a name="polishing-the-design"></a>Pulido del diseño

Hay varios pasos que se sugieren para perfeccionar el diseño de las interfaces de conversación. En primer lugar, proporciona ejemplos claros, concisos y de casos de uso a Siri.

Una de las formas en que un usuario detecta la aplicación es iniciar una conversación con Siri y preguntar *"¿Qué puede hacer?"* . Siri mostrará varias cosas que puede hacer, incluida la aplicación del desarrollador y los casos de uso de Heroes de ejemplo que ha proporcionado a través de su `plist` archivo.

Cómo escribir buenos casos de uso de ejemplo:

- Asegúrese de que los ejemplos incluyen el nombre de la aplicación.
- Conserve el ejemplo en corto y en el punto.
- Proporcione varios ejemplos para cada una de las intenciones que admite la aplicación.
- Dé prioridad a las intenciones y los ejemplos que contiene en función de los casos de uso más comunes de la aplicación.
- Asegúrese de que la aplicación proporciona ejemplos localizados.
- Asegúrese de que cada ejemplo dado funciona según lo previsto en la aplicación.
- Evite direccionar Siri en los ejemplos, por lo que no incluya texto como *"Hola Siri.* .."
- Evite los pleasantries innecesarios, como *"* " o *"gracias*".

Tómese el tiempo adecuado para explorar y experimentar cómo la aplicación puede dar forma a la conversación que Siri tiene con el usuario en su nombre. Asegúrese de hablar con los usuarios típicos a lo largo del proceso, ya que sus interacciones con y las expectativas de la aplicación pueden cambiar con el tiempo.

Recuerde siempre probar la aplicación en distintas situaciones y en todos los distintos métodos para invocar una conversación con Siri. Pruebe en las ubicaciones reales. es posible que el usuario esté usando la aplicación, fuera de la oficina y el escritorio.

Esforzarse por tener las conversaciones con Siri (en nombre de la aplicación) ser fluida, natural y "sentir justo".

## <a name="summary"></a>Resumen

En este artículo se han tratado los conceptos clave necesarios para usar SiriKit y se muestra cómo puede interactuar con las aplicaciones de Xamarin. iOS para proporcionar servicios a los que puede acceder el usuario mediante Siri y la aplicación Maps en un dispositivo iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ElizaChat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referencia de marco de trabajo](https://developer.apple.com/reference/intents)
- [Referencia del marco de interfaz de usuario de Intent](https://developer.apple.com/reference/intentsui)
