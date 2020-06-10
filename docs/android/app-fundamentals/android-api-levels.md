---
title: Descripción de los niveles de API de Android
description: Xamarin. Android tiene varias configuraciones de nivel de API de Android que determinan la compatibilidad de la aplicación con varias versiones de Android. En esta guía se explica lo que significan estas opciones de configuración, cómo configurarlas y el efecto que tienen en la aplicación en tiempo de ejecución.
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 188cc5b2ffba4540766edf0403d3a55228a21d6d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84568989"
---
# <a name="understanding-android-api-levels"></a>Descripción de los niveles de API de Android

_Xamarin. Android tiene varias configuraciones de nivel de API de Android que determinan la compatibilidad de la aplicación con varias versiones de Android. En esta guía se explica lo que significan estas opciones de configuración, cómo configurarlas y el efecto que tienen en la aplicación en tiempo de ejecución._

## <a name="quick-start"></a>Inicio rápido

Xamarin. Android expone tres configuraciones de proyecto de nivel de API de Android:

- [Plataforma](#framework) &ndash; de destino Especifica el marco que se va a usar para compilar la aplicación. Xamarin. Android usa este nivel de API en tiempo de *compilación* .

- Versión mínima de [Android](#minimum) &ndash; Especifica la versión de Android más antigua que quiere que admita su aplicación. Android usa este nivel de API en tiempo de *ejecución* .

- Versión de Android de [destino](#target) &ndash; Especifica la versión de Android en la que se va a ejecutar la aplicación. Android usa este nivel de API en tiempo de *ejecución* .

Para poder configurar un nivel de API para el proyecto, debe instalar los componentes de la plataforma SDK para ese nivel de API. Para obtener más información acerca de cómo descargar e instalar los componentes de Android SDK, consulte [Android SDK Setup](~/android/get-started/installation/android-sdk.md).

> [!NOTE]
> A partir de agosto de 2018, la consola de Google Play requerirá que las nuevas aplicaciones tengan como destino el nivel de API 26 (Android 8,0) o posterior.
Las aplicaciones existentes deberán tener como destino el nivel de API 26 o superior a partir de noviembre de 2018. Para obtener más información, consulte [mejorar la seguridad y el rendimiento de las aplicaciones en Google Play durante años](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html).

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Normalmente, los tres niveles de la API de Xamarin. Android se establecen en el mismo valor. En la página de la **aplicación** , establezca **compilar con la versión de Android (plataforma de destino)** en la versión de API estable más reciente (o, como mínimo, en la versión de Android que tenga todas las características que necesita).
En la siguiente captura de pantalla, la versión de .NET Framework de destino está establecida en **Android 7,1 (nivel de API 25-nougat)**:

[![Versión predeterminada de la plataforma de destino para compilar con la versión de Android](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

En la página **manifiesto de Android** , establezca la versión mínima de Android para **usar compilar con la versión del SDK** y establezca la versión de Android de destino en el mismo valor que la versión de .NET Framework de destino (en la siguiente captura de pantalla, el marco de trabajo de Android de destino se establece en **Android 7,1 (nougat)**):

[![Versiones de Android mínima y de destino establecidas en la versión de .NET Framework de destino](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Si quiere mantener la compatibilidad con versiones anteriores de Android, establezca la **versión mínima de Android en destino** a la versión más antigua de Android que quiera que admita la aplicación. (Tenga en cuenta que el nivel de API 14 es el nivel de API mínimo necesario para los [servicios de Google Play y la compatibilidad con Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)). La configuración de ejemplo siguiente admite versiones de Android desde el nivel de API 14 a través del nivel de API 25:

[![Compilación con el nivel de API 25 nougat, versión mínima de Android establecida en el nivel de API 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Normalmente, los tres niveles de la API de Xamarin. Android se establecen en el mismo valor. Establezca la **plataforma de destino** en la versión de API estable más reciente (o, como mínimo, en la versión de Android que tenga todas las características que necesita). Para establecer la versión de **.NET Framework de destino**, vaya a **compilar > general** en las **Opciones del proyecto**. En la siguiente captura de pantalla, la versión de .NET Framework de destino se establece para **usar la plataforma instalada más reciente (8,0)**:

[![Versión de .NET Framework de destino predeterminada para usar la plataforma instalada más reciente](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

La configuración de versión de Android mínima y de destino se puede encontrar en **Compilar > aplicación Android** en **Opciones de proyecto**. Establezca la versión mínima de Android en **versión de .NET Framework de destino automática** y establezca la versión de Android de destino en el mismo valor que la versión de .NET Framework de destino. En la siguiente captura de pantalla, el marco de trabajo de Android de destino se establece en **android 8,0 (nivel de API 26)** para que coincida con la configuración de plataforma de destino anterior:

[![Establecer los niveles de destino y de marco en las opciones del proyecto](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Si quiere mantener la compatibilidad con versiones anteriores de Android, cambie **versión mínima de Android** a la versión más antigua de Android que quiera que admita la aplicación. Tenga en cuenta que el nivel de API 14 es el nivel de API mínimo necesario para los [servicios de Google Play y la compatibilidad con Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
Por ejemplo, la configuración siguiente admite versiones de Android tan pronto como el nivel de API 14:

[![Versiones mínima y de destino establecidas en Automatic: usar la versión de .NET Framework de destino](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----

Si la aplicación admite varias versiones de Android, el código debe incluir comprobaciones en tiempo de ejecución para asegurarse de que la aplicación funciona con la configuración de versión mínima de Android (consulte [comprobaciones en tiempo de ejecución para las versiones de Android](#runtimechecks) a continuación para más información). Si está consumiendo o creando una biblioteca, consulte [niveles de API y bibliotecas](#libraries) a continuación para conocer los procedimientos recomendados para configurar las opciones de nivel de API de las bibliotecas.

## <a name="android-versions-and-api-levels"></a>Versiones de Android y niveles de API

A medida que la plataforma Android evoluciona y se publican nuevas versiones de Android, a cada versión de Android se le asigna un identificador entero único, denominado *nivel de API*. Por lo tanto, cada versión de Android corresponde a un solo nivel de API de Android. Dado que los usuarios instalan aplicaciones en versiones anteriores, así como en las versiones más recientes de Android, las aplicaciones Android del mundo real deben estar diseñadas para funcionar con varios niveles de API de Android.

### <a name="android-versions"></a>Versiones de Android

Cada versión de Android va acompañada de varios nombres:

- La versión de Android, como **android 9,0**
- Un nombre de código (o postre), como un _círculo_
- Un nivel de API correspondiente, como el **nivel de API 28**

Un nombre de código de Android puede corresponder a varias versiones y niveles de API (como se muestra en la tabla siguiente), pero cada versión de Android corresponde a un nivel de API exactamente.

Además, Xamarin. Android define *códigos de versión de compilación* que se asignan a los niveles de API de Android conocidos actualmente. La tabla siguiente puede ayudarle a traducir entre el nivel de API, la versión de Android, el nombre de código y el código de la versión de compilación de Xamarin. Android (los códigos de versión de compilación se definen en el `Android.OS` espacio de nombres):

[!include[](~/android/includes/api-levels.md)]

Como se indica en esta tabla, las nuevas versiones de Android se publican frecuentemente &ndash; a veces en más de una versión al año. Como resultado, el universo de dispositivos Android que pueden ejecutar la aplicación incluye una amplia variedad de versiones de Android antiguas y más recientes. ¿Cómo se puede garantizar que la aplicación se ejecute de forma coherente y confiable en tantas versiones diferentes de Android? Los niveles de API de Android pueden ayudarle a administrar este problema.

### <a name="android-api-levels"></a>Niveles de API de Android

Cada dispositivo Android se ejecuta en *un* nivel de API exactamente &ndash; . se garantiza que este nivel de API es único por cada versión de la plataforma Android. El nivel de API identifica con precisión la versión del conjunto de API al que puede llamar la aplicación; identifica la combinación de elementos de manifiesto, permisos, etc., que se codifican como desarrollador. El sistema de Android de niveles de API ayuda a Android a determinar si una aplicación es compatible con una imagen de sistema Android antes de instalar la aplicación en un dispositivo.

Cuando se compila una aplicación, contiene la siguiente información de nivel de API:

- El nivel de API de *destino* de Android en el que se crea la aplicación.

- El nivel *mínimo* de API de Android que debe tener un dispositivo Android para ejecutar la aplicación. 

Esta configuración se usa para garantizar que la funcionalidad necesaria para ejecutar la aplicación correctamente esté disponible en el dispositivo Android en el momento de la instalación. Si no es así, se bloquea la ejecución de la aplicación en ese dispositivo. Por ejemplo, si el nivel de API de un dispositivo Android es inferior al nivel de API mínimo que se especifica para la aplicación, el dispositivo Android impedirá que el usuario instale la aplicación.

## <a name="project-api-level-settings"></a>Configuración de nivel de API del proyecto

En las secciones siguientes se explica cómo usar SDK Manager para preparar el entorno de desarrollo para los niveles de API a los que desea dirigirse, seguido de una explicación detallada de cómo configurar el *marco de trabajo de destino*, la *versión mínima de Android*y la configuración de versión de Android de *destino* en Xamarin. Android.

### <a name="android-sdk-platforms"></a>Plataformas Android SDK

Antes de poder seleccionar un nivel de API de destino o mínimo en Xamarin. Android, debe instalar la versión de la plataforma Android SDK que se corresponda con ese nivel de API. El intervalo de opciones disponibles para el marco de trabajo de destino, la versión mínima de Android y la versión de Android de destino se limita al intervalo de versiones Android SDK que ha instalado. Puede usar el administrador de SDK para comprobar que están instaladas las versiones de Android SDK necesarias y puede usarla para agregar los nuevos niveles de API que necesite para la aplicación. Si no está familiarizado con la instalación de los niveles de API, consulte [Android SDK Setup](~/android/get-started/installation/android-sdk.md).

<a name="framework"></a>

### <a name="target-framework"></a>Versión de .NET Framework de destino

La *plataforma de destino* (también conocida como `compileSdkVersion` ) es la versión específica de Android Framework (nivel de API) para la que se compila la aplicación en tiempo de compilación. Esta configuración especifica qué API *espera* usar la aplicación cuando se ejecuta, pero no tiene ningún efecto en qué API están realmente disponibles para la aplicación cuando se instala. Como resultado, el cambio de la configuración de la plataforma de destino no cambia el comportamiento en tiempo de ejecución.

La versión de .NET Framework de destino identifica las versiones de la biblioteca a las que está vinculada la aplicación en &ndash; esta configuración y determina qué API se pueden usar en la aplicación. Por ejemplo, si quiere usar el método [NotificationBuilder. SetCategory](xref:Android.App.Notification.Builder.SetCategory*) que se presentó en Android 5,0 Lollipop, debe establecer la versión de .NET Framework de destino en el **nivel de API 21 (Lollipop)** o posterior. Si establece la plataforma de destino del proyecto en un nivel de API como el **nivel de API 19 (KitKat)** e intenta llamar al `SetCategory` método en el código, obtendrá un error de compilación.

Se recomienda compilar siempre con la *última* versión de .NET Framework de destino disponible. Al hacerlo, se proporcionan mensajes de advertencia útiles para las API en desuso a las que puede llamar el código. El uso de la versión más reciente de la plataforma de destino es especialmente importante cuando se usa la versión más reciente de la biblioteca de soporte técnico &ndash; cada biblioteca espera que la aplicación se compile en el nivel mínimo de API de la biblioteca de compatibilidad o superior.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para acceder a la configuración de la plataforma de destino en Visual Studio, abra las propiedades del proyecto en **Explorador de soluciones** y seleccione la página de la **aplicación** :

[![Página de la aplicación de propiedades del proyecto](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Establezca la versión de .NET Framework de destino seleccionando un nivel de API en el menú desplegable de **compilar con la versión de Android** como se mostró anteriormente.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para obtener acceso a la configuración de la plataforma de destino en Visual Studio para Mac, haga clic con el botón secundario en el nombre del proyecto y seleccione **Opciones**. se abrirá el cuadro de diálogo **Opciones del proyecto** . En este cuadro de diálogo, vaya a **Compilar > general** como se muestra aquí:

[![Sección generar general de la página Opciones del proyecto](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Establezca la versión de .NET Framework de destino seleccionando un nivel de API en el menú desplegable que se encuentra a la derecha de la **plataforma de destino** , como se mostró anteriormente.

-----

<a name="minimum"></a>

### <a name="minimum-android-version"></a>Versión mínima de Android

La *versión mínima de Android* (también conocida como `minSdkVersion` ) es la versión más antigua del sistema operativo Android (es decir, el nivel de API más bajo) que puede instalar y ejecutar la aplicación. De forma predeterminada, una aplicación solo se puede instalar en dispositivos que coincidan con la configuración de la plataforma de destino o superior; Si la configuración de versión *mínima de Android es anterior* a la configuración de la plataforma de destino, la aplicación también puede ejecutarse en versiones anteriores de Android. Por ejemplo, si establece la plataforma de destino en **android 7,1 (nougat)** y establece la versión mínima de Android en **Android 4.0.3 (sandwich**de helado), la aplicación se puede instalar en cualquier plataforma desde el nivel de API 15 hasta el nivel de API 25, ambos inclusive.

Aunque la aplicación puede compilarse e instalarse correctamente en este intervalo de plataformas, esto no garantiza que se *ejecute* correctamente en todas estas plataformas. Por ejemplo, si la aplicación se instala en **android 5,0 (Lollipop)** y el código llama a una API que solo está disponible en **Android 7,1 (nougat)** y versiones más recientes, la aplicación obtendrá un error en tiempo de ejecución y, posiblemente, se bloqueará. Por lo tanto, el código debe asegurarse &ndash; en tiempo de ejecución de &ndash; que solo llama a las API admitidas por el dispositivo Android en el que se está ejecutando. En otras palabras, el código debe incluir comprobaciones explícitas en tiempo de ejecución para asegurarse de que la aplicación usa las API más recientes solo en los dispositivos que son lo suficientemente recientes como para admitirlos.
[Las comprobaciones en tiempo de ejecución de las versiones de Android](#runtimechecks), más adelante en esta guía, explica cómo agregar estas comprobaciones en tiempo de ejecución al código.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para acceder a la configuración de versión mínima de Android en Visual Studio, abra las propiedades del proyecto en **Explorador de soluciones** y seleccione la página **manifiesto de Android** . En el menú desplegable de la **versión mínima de Android** , puede seleccionar la versión mínima de Android para la aplicación:

[![Opción de Android to target mínima establecida para compilar con la versión del SDK](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Si selecciona **usar compilar con la versión del SDK**, la versión mínima de Android será la misma que la configuración de la plataforma de destino.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para tener acceso a la versión mínima de Android en Visual Studio para Mac, haga clic con el botón secundario en el nombre del proyecto y seleccione **Opciones**. se abrirá el cuadro de diálogo **Opciones del proyecto** . Vaya a **Compilar > aplicación Android**.
Con el menú desplegable a la derecha de la **versión mínima de Android**, puede establecer la versión mínima de Android para la aplicación:

[![Versión mínima de Android establecida en Automatic: usar versión de plataforma de destino](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Si selecciona **usar la &ndash; versión de .NET Framework de destino automática**, la versión mínima de Android será la misma que la configuración de plataforma de destino.

-----

<a name="target"></a>

### <a name="target-android-version"></a>Versión de Android de destino

La *versión de Android de destino* (también conocida como `targetSdkVersion` ) es el nivel de API del dispositivo Android en el que la aplicación espera ejecutarse. Android usa esta opción para determinar si se debe habilitar cualquier comportamiento de compatibilidad &ndash; . Esto garantiza que la aplicación siga funcionando de la manera esperada. Android usa la configuración de versión de Android de destino de la aplicación para averiguar qué cambios de comportamiento se pueden aplicar a la aplicación sin interrumpirla (este es el modo en que Android proporciona compatibilidad con versiones posteriores).

La plataforma de destino y la versión de Android de destino, aunque tienen nombres muy similares, no son lo mismo. La configuración de plataforma de destino comunica información de nivel de API de destino a Xamarin. Android para su uso en *tiempo de compilación*, mientras que la versión de Android de destino comunica información de nivel de API de destino a Android para su uso en *tiempo de ejecución* (cuando la aplicación está instalada y en ejecución en un dispositivo).

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para acceder a esta configuración en Visual Studio, abra las propiedades del proyecto en **Explorador de soluciones** y seleccione la página **manifiesto de Android** . En el menú desplegable de **versión de Android de destino** puede seleccionar la versión de Android de destino de la aplicación:

[![Versión de Android de destino establecida para compilar con la versión del SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

Se recomienda establecer explícitamente la versión de Android de destino en la versión más reciente de Android que se usa para probar la aplicación. Idealmente, debe establecerse en la versión de Android SDK más reciente, lo que &ndash; le permite usar nuevas API antes de trabajar con los cambios de comportamiento. Para la mayoría de los desarrolladores, *no* se recomienda establecer la versión de Android de destino para **usar compilar con la versión del SDK**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para obtener acceso a esta configuración en Visual Studio para Mac, haga clic con el botón secundario en el nombre del proyecto y seleccione **Opciones**. se abrirá el cuadro de diálogo **Opciones del proyecto** . Vaya a **Compilar > aplicación Android**. Con el menú desplegable a la derecha de la **versión de Android de destino**, puede establecer la versión de Android de destino para la aplicación:

[![Versión de Android de destino establecida en automático: usar versión de plataforma de destino](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

Se recomienda establecer explícitamente la versión de Android de destino en la versión más reciente de Android que se usa para probar la aplicación. Idealmente, debe establecerse en la última versión Android SDK disponible, lo que &ndash; le permite usar nuevas API antes de trabajar con los cambios de comportamiento. Para la mayoría de los desarrolladores, no se recomienda establecer la versión de Android de destino en la **versión de .NET Framework de destino de uso automático**.

-----

En general, la versión de Android de destino debe estar limitada por la versión mínima de Android y la plataforma de destino. Es decir:

**Versión mínima de Android <= versión de Android de destino <= plataforma de destino**

Para obtener más información sobre los niveles de SDK, consulte la documentación sobre el [SDK](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) para desarrolladores de Android.

<a name="runtimechecks"></a>

## <a name="runtime-checks-for-android-versions"></a>Comprobaciones en tiempo de ejecución para las versiones de Android

A medida que se publica cada nueva versión de Android, la API de Framework se actualiza para proporcionar una funcionalidad nueva o de reemplazo. Con pocas excepciones, la funcionalidad de API de versiones anteriores de Android se lleva a cabo en nuevas versiones de Android sin modificaciones. Como resultado, si la aplicación se ejecuta en un nivel de API de Android determinado, normalmente podrá ejecutarse en un nivel de API de Android posterior sin modificaciones. Pero ¿qué ocurre si también quiere ejecutar la aplicación en versiones anteriores de Android?

Si selecciona una versión mínima de Android que sea *anterior* a la configuración de la plataforma de destino, es posible que algunas API no estén disponibles para la aplicación en tiempo de ejecución. Sin embargo, la aplicación todavía puede ejecutarse en un dispositivo anterior, pero con funcionalidad reducida. Para cada API que no esté disponible en plataformas Android correspondientes a la configuración de versión mínima de Android, el código debe comprobar explícitamente el valor de la `Android.OS.Build.VERSION.SdkInt` propiedad para determinar el nivel de API de la plataforma en la que se ejecuta la aplicación. Si el nivel de API es *inferior* a la versión mínima de Android que admite la API a la que desea llamar, el código tiene que encontrar una manera de funcionar correctamente sin hacer esta llamada API.

Por ejemplo, supongamos que deseamos usar el método [NotificationBuilder. SetCategory](xref:Android.App.Notification.Builder.SetCategory*) para clasificar una notificación cuando se ejecuta en **Android 5,0 Lollipop** (y versiones posteriores), pero todavía queremos que nuestra aplicación se ejecute en versiones anteriores de Android, como **4,1 Android** (donde `SetCategory` no está disponible). Al hacer referencia a la tabla de versiones de Android al principio de esta guía, vemos que el código de versión de compilación para el **círculo de android 5,0** es `Android.OS.BuildVersionCodes.Lollipop` . Para admitir versiones anteriores de Android en las que `SetCategory` no está disponible, nuestro código puede detectar el nivel de API en tiempo de ejecución y llamar condicionalmente `SetCategory` solo cuando el nivel de API sea mayor o igual que el código de la versión de compilación Lollipop:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

En este ejemplo, el marco de trabajo de destino de la aplicación está establecido en **android 5,0 (nivel de API 21)** y su versión mínima de Android está establecida en **Android 4,1 (nivel de API 16)**. Dado `SetCategory` que está disponible en el nivel de API `Android.OS.BuildVersionCodes.Lollipop` y versiones posteriores, este código de ejemplo solo llamará `SetCategory` cuando esté realmente disponible, &ndash; *no* intentará llamar a `SetCategory` cuando el nivel de API sea 16, 17, 18, 19 o 20. La funcionalidad se reduce en estas versiones anteriores de Android solo en la medida en que las notificaciones no están ordenadas correctamente (porque no se clasifican por tipo), pero las notificaciones siguen publicadas para alertar al usuario. Nuestra aplicación sigue funcionando, pero su funcionalidad se reduce ligeramente.

En general, la comprobación de la versión de compilación ayuda a su código a decidir en tiempo de ejecución entre hacer algo de la forma nueva en lugar de la antigua. Por ejemplo:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    // Do things the Lollipop way
}
else
{
    // Do things the pre-Lollipop way
}
```

No hay ninguna regla rápida y sencilla que explique cómo reducir o modificar la funcionalidad de la aplicación cuando se ejecuta en versiones de Android anteriores que carecen de una o más API. En algunos casos (como en el `SetCategory` ejemplo anterior), es suficiente para omitir la llamada API cuando no está disponible. Sin embargo, en otros casos, puede que necesite implementar una funcionalidad alternativa para cuando `Android.OS.Build.VERSION.SdkInt` se detecte que es menor que el nivel de API que la aplicación necesita para presentar su experiencia óptima.

<a name="libraries"></a>

## <a name="api-levels-and-libraries"></a>Niveles y bibliotecas de API

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Al crear un proyecto de biblioteca Xamarin. Android (como una biblioteca de clases o una biblioteca de enlaces), solo puede configurar la plataforma de destino estableciendo &ndash; la versión mínima de Android y la configuración de versión de Android de destino no está disponible. Esto se debe a que no hay ninguna página de **manifiesto de Android** :

[![Solo está disponible la opción compilar con la versión de Android](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Cuando se crea un proyecto de biblioteca de Xamarin. Android, no hay ninguna página de **aplicación de Android** en la que se pueda configurar la versión mínima de Android y la versión de Android de destino &ndash; . la versión mínima de Android y la configuración de versión de Android de destino no están disponibles.
Esto se debe a que no hay ninguna página de **compilación > aplicación Android** :

[![Página de compilación general sin las opciones de versión mínima y de destino](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

La versión mínima de Android y la configuración de versión de Android de destino no están disponibles porque la biblioteca resultante no es una aplicación independiente &ndash; . la biblioteca puede ejecutarse en cualquier versión de Android, en función de la aplicación con la que está empaquetada. Puede especificar cómo se va a *compilar*la biblioteca, pero no puede predecir en qué nivel de API de plataforma se ejecutará la biblioteca. Teniendo esto en cuenta, deben tenerse en cuenta los siguientes procedimientos recomendados al consumir o crear bibliotecas:

- **Al utilizar una biblioteca** &ndash; de Android Si está consumiendo una biblioteca de Android en la aplicación, asegúrese de establecer la configuración de la plataforma de destino de la aplicación en un nivel de API que sea *al menos tan alto como* el de la versión de .NET Framework de destino de la biblioteca.

- **Al crear una biblioteca** &ndash; de Android Si va a crear una biblioteca de Android para su uso por parte de otras aplicaciones, asegúrese de establecer la configuración de la plataforma de destino en el nivel de API mínimo que necesita para compilar.

Se recomiendan estas prácticas recomendadas para evitar la situación en la que una biblioteca intenta llamar a una API que no está disponible en tiempo de ejecución (lo que puede hacer que la aplicación se bloquee). Si es un desarrollador de bibliotecas, debe esforzarse por restringir el uso de las llamadas API a un subconjunto pequeño y bien establecido del área expuesta de API total. Esto ayuda a garantizar que la biblioteca se puede usar de forma segura en una amplia gama de versiones de Android.

## <a name="summary"></a>Resumen

En esta guía se explica cómo se usan los niveles de API de Android para administrar la compatibilidad de aplicaciones en diferentes versiones de Android. Proporcionó pasos detallados para configurar la *plataforma de destino*de Xamarin. Android, la *versión mínima de Android*y la configuración de proyecto de versión de Android de *destino* . Se proporcionan instrucciones para usar el administrador de Android SDK para instalar paquetes de SDK, se incluyen ejemplos de cómo escribir código para tratar con diferentes niveles de API en tiempo de ejecución y se explica cómo administrar los niveles de API al crear o usar bibliotecas de Android. También proporciona una lista completa que relaciona los niveles de API con los números de versión de Android (como Android 4,4), los nombres de versión de Android (como KitKat) y los códigos de versión de compilación de Xamarin. Android.

## <a name="related-links"></a>Vínculos relacionados

- [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)
- [Cambios en las herramientas de la CLI de SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Selección de compileSdkVersion, minSdkVersion y targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [¿Qué es el nivel de API?](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Nombres, etiquetas y números de compilación](https://source.android.com/source/build-numbers)
