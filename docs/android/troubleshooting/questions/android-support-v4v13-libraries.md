---
title: Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: c74cac6a6d669385855999a565711a3fdc85f3b7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019537"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13

## <a name="about-the-android-support-libraries"></a>Acerca de las bibliotecas de compatibilidad con Android

Google ha creado bibliotecas de compatibilidad para poner nuevas características a disposición de versiones anteriores de Android. En general, las bibliotecas de compatibilidad reciben un número de versión en su nombre, que es el nivel de API de Android más bajo que son compatibles con (por ejemplo, solo se puede usar support-V4 en el nivel de API 4 y versiones posteriores. Más información en este [Stack Overflow debate](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Dos de las bibliotecas de compatibilidad: `Support-v4` y `Support-v13` no se pueden usar juntas en la misma aplicación, es decir, se excluyen mutuamente. Esto se debe a que `Support-v13` realmente contiene todos los tipos e implementaciones de `Support-v4`. Si intenta hacer referencia a ambos en el mismo proyecto, se producirán errores de tipo duplicado.

## <a name="problems-with-referencing"></a>Problemas con la referencia

Dado que `Support-v4` ha vuelto tan popular, muchas bibliotecas de terceros dependen de ella. Podrían haber elegido depender de support-v13 en su lugar, pero es más común depender de _V4_ , ya que proporciona a todas las aplicaciones que usan estas bibliotecas de terceros la opción de admitir niveles de API hasta 4.

Si una biblioteca de terceros de Xamarin hace referencia al enlace `Xamarin.Android.Support.v4.dll` a `Support-v4`, cualquier aplicación que use esta biblioteca también debe hacer referencia a `Xamarin.Android.Support.v4.dll`. Esto se convierte en un problema cuando la misma aplicación también quiere usar parte de la funcionalidad del enlace de `Xamarin.Android.Support.v13.dll` a `Support-v13`. Si hace referencia a ambos enlaces, se producirán errores de tipo duplicado.

## <a name="type-forwarded-v4-binding-assembly"></a>Ensamblado de enlace V4 con reenvío de tipos

Para solucionar este problema, se ha creado un ensamblado de `Xamarin.Android.Support.v4.dll` especial que no tiene ninguna implementación, sino simplemente `[assembly: TypeForwardedTo (..)]` atributos que reenvían todos los tipos de `Support-v4` a la implementación dentro del ensamblado de `Xamarin.Android.Support.v13.dll`.

Esto significa que un desarrollador puede hacer referencia a este ensamblado de _tipo reenviado_ en su aplicación que satisfará la referencia a `Xamarin.Android.Support.v4.dll` de cualquier biblioteca de terceros, al tiempo que permite que `Xamarin.Android.Support.v13.dll` se use en la aplicación.

## <a name="nuget-assistance"></a>Asistencia de NuGet

Aunque un desarrollador podría agregar manualmente las referencias correctas necesarias, podemos usar NuGet para ayudar a elegir el ensamblado correcto (el enlace _V4_ normal o el ensamblado _V4_ con reenvío de tipos) cuando se instala el paquete NuGet.

Por lo tanto, el paquete NuGet de `Xamarin.Android.Support.v4` ahora contiene la siguiente lógica:

Si su aplicación tiene como destino el nivel de API 13 (Gingerbread 3,2) o una versión posterior:

* `Xamarin.Android.Support.v13` NuGet se agregará automáticamente como una dependencia
* Se hará referencia al `Xamarin.Android.Support.v4.dll` con _reenvío de tipos_ en el proyecto.

Si su aplicación tiene como destino algo más bajo que el nivel de API 13, obtendrá el enlace de `Xamarin.Android.Support.v4.dll` normal al que se hace referencia en el proyecto.

## <a name="do-i-have-to-use-support-v13"></a>¿Tengo que usar support-v13?

Si su aplicación tiene como destino el nivel de API 13 o superior y decide usar el paquete de `Xamarin Android Support-v4` NuGet, el paquete NuGet de `Xamarin Android Support v13` es una dependencia necesaria.

Creemos que el aumento muy poco importante en el tamaño de la aplicación (los dos archivos. jar difieren en 17kb) merece la pena la compatibilidad y se producen menos quebraderos de cabeza.

Si Adamant sobre el uso de `Support-v4` en una aplicación que tiene como destino el nivel de API 13 o superior, siempre puede descargar manualmente el `.nupkg`, extraerlo y hacer referencia al ensamblado.
