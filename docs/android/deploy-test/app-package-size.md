---
title: Tamaños de paquete de aplicación
description: En este artículo se examinan los elementos que componen un paquete de aplicación Xamarin.Android y las estrategias asociadas que se pueden utilizar para la implementación eficaz del paquete durante las etapas de depuración y lanzamiento de la versión.
ms.prod: xamarin
ms.assetid: 8D70CDDD-3D3C-9949-8045-AB8F93D18E74
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: ff1bc56ab1cf02e9e5354da94bebd0661da34bc5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028159"
---
# <a name="application-package-size"></a>Tamaños de paquete de aplicación

_En este artículo se examinan los elementos que componen un paquete de aplicación Xamarin.Android y las estrategias asociadas que se pueden utilizar para la implementación eficaz del paquete durante las etapas de depuración y lanzamiento de la versión._

## <a name="overview"></a>Información general

Xamarin.Android usa diversos mecanismos para minimizar el tamaño de paquete y mantener un proceso eficiente de depuración e implementación de versiones. En este artículo, se examina el flujo de trabajo de implementación de depuración y lanzamiento de Xamarin.Android y cómo la plataforma Xamarin.Android garantiza que se compilan y lanzan pequeños paquetes de aplicación.

## <a name="release-packages"></a>Paquetes de versión

Para distribuir una aplicación totalmente contenida, el paquete debe incluir la aplicación, las bibliotecas asociadas, el contenido, el entorno de ejecución Mono y los ensamblados de biblioteca de clases base (BCL) necesarios. Por ejemplo, si tomamos la plantilla predeterminada "Hola, mundo", el contenido de una compilación de paquete completa sería similar al siguiente:

[![Tamaño de paquete antes del enlazador](app-package-size-images/hello-world-package-size-before-linker.png)](app-package-size-images/hello-world-package-size-before-linker.png#lightbox)

15,8 MB es un tamaño de descarga que nos gustaría. El problema son las bibliotecas BCL, dado que incluyen mscorlib, System y Mono.Android, que proporcionan muchos de los componentes necesarios para ejecutar la aplicación. Sin embargo, también proporcionan una funcionalidad que puede que no use en la aplicación, por lo que puede ser preferible excluir estos componentes.

Cuando se compila una aplicación para su distribución, se ejecuta un proceso, conocido como vinculación, que examina la aplicación y quita cualquier código que no se use directamente. Este proceso es similar a la funcionalidad que proporciona la [recolección de elementos no utilizados](~/android/internals/garbage-collection.md) para la memoria asignada por el montón. Pero, en lugar de trabajar sobre objetos, la vinculación trabaja sobre el código. Por ejemplo, hay un espacio de nombres completo en System.dll para enviar y recibir correo electrónico, pero si la aplicación no hace uso de esta funcionalidad, lo que hace simplemente se código es desperdiciar espacio. Después de ejecutar el enlazador en la aplicación Hola, mundo, nuestro paquete se parece ahora a este:

[![Tamaño de paquete después del enlazador](app-package-size-images/hello-world-package-size-after-linker.png)](app-package-size-images/hello-world-package-size-after-linker.png#lightbox)

Como se puede observar, se quita una cantidad considerable de la BCL que no se usaba. Tenga en cuenta que el tamaño final de la BCL depende de lo que la aplicación use realmente. Por ejemplo, si echamos un vistazo a una aplicación de ejemplo más significativa llamada ApiDemo, podemos ver que el componente BCL ha aumentado de tamaño porque ApiDemo usa más cantidad de la BCL que Hola, mundo:

![Tamaño de paquete de ApiDemo después de la vinculación](app-package-size-images/api-demo-package-size-after-linker.png)

Como se ilustra aquí, el tamaño del paquete de aplicación suele ser 2,9 MB más grande que la aplicación y sus dependencias.

## <a name="debug-packages"></a>Depuración de paquetes

En las compilaciones de depuración, las cosas se tratan de forma ligeramente diferente. Cuando se realizan las mismas implementaciones una y otra vez en un dispositivo, una aplicación tiene que ser lo más rápida posible, de modo que se optimizan los paquetes de depuración en favor de la velocidad de implementación en lugar del tamaño.

Android es relativamente lento en copiar e instalar un paquete, así que queremos que el tamaño del paquete sea lo más pequeño posible. Como se explicó anteriormente, una de las formas posibles de minimizar el tamaño de paquete es mediante el enlazador. Sin embargo, la vinculación es lenta y lo habitual es que queramos implementar solo partes de la aplicación que hayan cambiado desde la última implementación. Para lograr esto, separamos nuestra aplicación de los componentes principales del Xamarin.Android.

La primera vez que realizamos la depuración en el dispositivo, copiamos dos paquetes grandes llamados *Tiempo de ejecución compartido* y *Plataforma compartida*. El tiempo de ejecución compartido contiene el entorno de ejecución de Mono y BCL, mientras que la plataforma compartida contiene ensamblados específicos de nivel de API de Android:

[![Tamaño de paquete de tiempo de ejecución compartido](app-package-size-images/shared-runtime-package-size.png)](app-package-size-images/shared-runtime-package-size.png#lightbox)

La copia de estos componentes principales solo se hace una vez, ya que tarda bastante tiempo, pero permite que las sucesivas aplicaciones que se ejecutan en modo de depuración los utilicen. Por último, copiamos la aplicación real, que es pequeña y rápida:

![La aplicación real es pequeña](app-package-size-images/hello-world-debug-application-no-link.png)

### <a name="fast-assembly-deployment"></a>Implementación rápida de ensamblados

La opción de compilación *Implementación rápida de ensamblados* se puede usar para reducir aún más el tamaño del paquete de instalación de depuración al no incluir los ensamblados en el paquete de la aplicación, instalarlos directamente en el dispositivo una sola vez y copiarlos únicamente sobre los archivos que se han modificado desde la última implementación.

Para habilitar *Implementación rápida de ensamblados*, siga estos pasos:

1. Haga clic con el botón derecho en el proyecto de Android en el Explorador de soluciones y seleccione **Opciones**.

2. En el cuadro de diálogo Opciones del proyecto, seleccione **Compilación de Android**:  

    ![Opciones del proyecto: Compilación de Android](app-package-size-images/fastdev0.png)

3. Active las casillas **Usar el entorno de ejecución Mono compartido** e **Implementación rápida de ensamblados**:  

    ![Casillas de verificación seleccionadas en la pestaña Empaquetado](app-package-size-images/fastdev.png)

4. Haga clic en el botón **Aceptar** para guardar los cambios y cerrar el cuadro de diálogo Opciones del proyecto.

La próxima vez que la aplicación se compile para la depuración, los ensamblados se instalarán directamente en el dispositivo (si aún no lo están) y un paquete de aplicación más pequeño (que no incluye los ensamblados) también se instalará en el dispositivo. Con ello se acorta el tiempo necesario para conseguir que los cambios en la aplicación se pongan en funcionamiento para la realización de pruebas.

Al soportar la primera implementación larga del entorno de tiempo de ejecución compartido y de la plataforma compartida, cada vez que realizamos cambios en la aplicación, podemos implementar la nueva versión rápidamente y sin problemas, de modo que conseguimos un ciclo de cambio, implementación y ejecución rápido.

## <a name="summary"></a>Resumen

En este artículo, examinamos las facetas del empaquetado de perfiles de depuración y lanzamiento de Xamarin.Android. Además, analizamos las estrategias que usa la plataforma Mono para Android para facilitar la implementación eficiente de paquetes durante las fases de desarrollo de depuración y lanzamiento.
