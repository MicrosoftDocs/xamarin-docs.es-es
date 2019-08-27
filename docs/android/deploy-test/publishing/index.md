---
title: Publicar una aplicación
ms.prod: xamarin
ms.assetid: 51E19000-040A-2B74-C462-EC57C617085C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: b5f09413379de13aaf12e8113e8deb0f3c2bf0c3
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525458"
---
# <a name="publishing-an-application"></a>Publicar una aplicación

Después de crear una gran aplicación, la gente querrá usarla. En este artículo se describen los pasos implicados en la distribución pública de una aplicación creada con Xamarin.Android a través de canales como el correo electrónico, un servidor web privado, Google Play o la Tienda Apps de Amazon para Android.


## <a name="overview"></a>Información general

El paso final en el desarrollo de una aplicación de Xamarin.Android es publicar la aplicación. La publicación es el proceso de compilación de una aplicación de Xamarin.Android para que esté lista para que los usuarios la instalen en sus dispositivos, e implica dos tareas esenciales:

- **Preparar la publicación**: se crea una versión de lanzamiento de la aplicación que se puede implementar en dispositivos Android (consulte [Preparar una aplicación para su lanzamiento](~/android/deploy-test/release-prep/index.md) para obtener más información sobre la preparación de lanzamiento).

- **Distribución**: la versión de lanzamiento de una aplicación estará disponible a través de uno o varios canales de distribución.

El siguiente diagrama ilustra los pasos de publicación de una aplicación de Xamarin.Android:

[![Diagrama de flujo de compilación e implementación](images/build-and-deploy-steps.png)](images/build-and-deploy-steps.png#lightbox)

Como puede observarse en el diagrama anterior, la preparación es la misma independientemente del método de distribución que se utilice. Hay varias formas de poner una aplicación de Android a disposición de los usuarios:

- **A través de un sitio web**: una aplicación Xamarin.Android puede estar disponible para su descarga en un sitio web, desde el que los usuarios pueden instalarla haciendo clic en un vínculo.
- **Por correo electrónico**: los usuarios pueden instalar una aplicación Xamarin.Android desde su correo electrónico. La aplicación se instalará cuando se abra el archivo adjunto con un dispositivo Android.
- **A través de una tienda**: existen varias tiendas de aplicaciones para la distribución, como [Google Play](http://play.google.com/) o la [Tienda Apps de Amazon para Android](http://www.amazon.com/mobile-apps/b?ie=UTF8&node=2350149011).


Utilizar una tienda establecida es la manera más común para publicar una aplicación, ya que proporciona la mayor cobertura de mercado y el máximo control sobre la distribución. Sin embargo, publicar una aplicación a través de una tienda requiere un esfuerzo adicional.

Varios canales pueden distribuir una aplicación de Xamarin.Android simultáneamente. Por ejemplo, una aplicación puede publicarse en Google Play, la Tienda Apps de Amazon para Android y también se puede descargar desde un servidor web.

Los otros dos métodos de distribución (descarga o correo electrónico) son más útiles para un subconjunto de usuarios, como un entorno empresarial o una aplicación que solo está destinada a un conjunto de usuarios pequeño o controlado.
La distribución a través de un servidor y de correo electrónico también son modelos de publicación más sencillos, que requieren menos preparación para publicar una aplicación.

El programa de distribución de aplicaciones de Amazon Mobile permite a los desarrolladores de aplicaciones móviles distribuir y vender sus aplicaciones en Amazon. Los usuarios pueden descubrir y comprar aplicaciones en sus dispositivos Android mediante la aplicación Tienda Apps de Amazon. A continuación aparece una captura de pantalla de la Tienda Apps de Amazon en un dispositivo Android:

Google Play es, posiblemente, la tienda más completa y popular para aplicaciones Android. Google Play permite a los usuarios detectar, descargar, evaluar y pagar por aplicaciones haciendo clic en un solo icono en su dispositivo o su equipo. Google Play también proporciona herramientas para ayudar en el análisis de ventas y las tendencias del mercado y controlar qué dispositivos y usuarios pueden descargar una aplicación. A continuación aparece una captura de pantalla de Google Play en un dispositivo Android:

[![Captura de pantalla de Google Play](images/google-play-app.png)](images/google-play-app.png#lightbox)

En esta sección se muestra cómo cargar la aplicación en una tienda, como Google Play, junto con el material promocional adecuado. Se describen los archivos de expansión del APK y se ofrece información general conceptual sobre qué son y cómo funcionan. También se describen los servicios de Licencias de Google. Por último, se presentan medios alternativos de distribución, incluido el uso de un servidor web HTTP, la distribución por correo electrónico simple y Amazon Appstore para Android.


## <a name="related-links"></a>Vínculos relacionados

- [HelloWorldPublishing (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/helloworldpublishing)
- [Creación de proceso](~/android/deploy-test/building-apps/build-process.md)
- [Vinculación](~/android/deploy-test/linker.md)
- [Obtener una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Firma de aplicaciones](https://source.android.com/security/apksigning/)
- [Publicación en Google Play](https://developer.android.com/distribute/googleplay/publish/index.html)
- [Licencias de aplicaciones de Google](https://developer.android.com/guide/google/play/licensing/index.html)
- [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary)
- [Portal de distribución de aplicaciones móviles](https://developer.amazon.com/welcome.html)
- [Preguntas más frecuentes sobre la distribución de aplicaciones móviles de Amazon](https://developer.amazon.com/help/faq.html)
