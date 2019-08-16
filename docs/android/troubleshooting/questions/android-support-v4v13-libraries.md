---
title: Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 64ceacc37a303e69b0dd7073ec6547ed344af51d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523428"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13

## <a name="about-the-android-support-libraries"></a>Acerca de las bibliotecas de compatibilidad con Android

Google ha creado bibliotecas de compatibilidad para poner nuevas características a disposición de versiones anteriores de Android. En general, las bibliotecas de compatibilidad reciben un número de versión en su nombre, que es el nivel de API de Android más bajo, son compatibles con (por ejemplo: Support-V4 solo se puede usar en el nivel de API 4 y versiones posteriores. Más información en este [Stack Overflow debate](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Dos de las bibliotecas de soporte `Support-v4` : `Support-v13` y no se pueden usar juntas en la misma aplicación, es decir, se excluyen mutuamente. Esto se debe `Support-v13` a que realmente contiene todos los tipos y la `Support-v4`implementación de. Si intenta hacer referencia a ambos en el mismo proyecto, se producirán errores de tipo duplicado.

## <a name="problems-with-referencing"></a>Problemas con la referencia

Dado `Support-v4` que ha llegado tan popular, muchas bibliotecas de terceros dependen ahora de ella. Podrían haber elegido depender de support-v13 en su lugar, pero es más común depender de _V4_ , ya que proporciona a todas las aplicaciones que usan estas bibliotecas de terceros la opción de admitir niveles de API hasta 4.

Si una biblioteca de terceros de Xamarin hace `Xamarin.Android.Support.v4.dll` referencia al `Support-v4`enlace a, cualquier aplicación que use esta biblioteca también `Xamarin.Android.Support.v4.dll`debe hacer referencia a. Esto se convierte en un problema cuando la misma aplicación también quiere usar parte de la funcionalidad del `Xamarin.Android.Support.v13.dll` enlace a `Support-v13`. Si hace referencia a ambos enlaces, se producirán errores de tipo duplicado.

## <a name="type-forwarded-v4-binding-assembly"></a>Ensamblado de enlace V4 con reenvío de tipos

Para solucionar este problema, se ha creado un ensamblado `Xamarin.Android.Support.v4.dll` especial que no tiene ninguna implementación, sino `[assembly: TypeForwardedTo (..)]` simplemente atributos que reenvían `Support-v4` todos los tipos a la implementación `Xamarin.Android.Support.v13.dll` dentro del ensamblado.

Esto significa que un desarrollador puede hacer referencia a este ensamblado de _tipo reenviado_ en su aplicación, lo `Xamarin.Android.Support.v4.dll` que satisfará la referencia a las bibliotecas de `Xamarin.Android.Support.v13.dll` terceros y, al mismo tiempo, permitirá que se use en la aplicación.

## <a name="nuget-assistance"></a>Asistencia de NuGet

Aunque un desarrollador podría agregar manualmente las referencias correctas necesarias, podemos usar NuGet para ayudar a elegir el ensamblado correcto (el enlace _V4_ normal o el ensamblado _V4_ con reenvío de tipos) cuando se instala el paquete NuGet.

Por lo tanto `Xamarin.Android.Support.v4` , el paquete NuGet ahora contiene la lógica siguiente:

Si su aplicación tiene como destino el nivel de API 13 (Gingerbread 3,2) o una versión posterior:

* `Xamarin.Android.Support.v13`NuGet se agregará automáticamente como una dependencia
* Se `Xamarin.Android.Support.v4.dll` hará referencia al tipo reenviado en el proyecto.

Si su aplicación tiene como destino algo más bajo que el nivel de API 13, obtendrá el `Xamarin.Android.Support.v4.dll` enlace normal al que se hace referencia en el proyecto.

## <a name="do-i-have-to-use-support-v13"></a>¿Tengo que usar support-v13?

Si su aplicación tiene como destino el nivel de API 13 o superior y decide usar el `Xamarin Android Support-v4` paquete Nuget, el `Xamarin Android Support v13` paquete Nuget es una dependencia necesaria.

Creemos que el aumento muy poco importante en el tamaño de la aplicación (los dos archivos. jar difieren en 17kb) merece la pena la compatibilidad y se producen menos quebraderos de cabeza.

Si Adamant sobre el `Support-v4` `.nupkg`uso de en una aplicación que tiene como destino el nivel de API 13 o superior, siempre puede descargar manualmente, extraerlo y hacer referencia al ensamblado.
