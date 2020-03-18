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
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73019537"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13

## <a name="about-the-android-support-libraries"></a>Acerca de las bibliotecas de compatibilidad de Android

Google ha creado bibliotecas de compatibilidad para que las nuevas características estén disponibles para las versiones anteriores de Android. En general, las bibliotecas de compatibilidad contienen un número de versión en su nombre, que es el nivel de API de Android más bajo con las que son compatibles (por ejemplo: Support-v4 solo se puede usar en el nivel de API 4 y superiores. Más información en esta [explicación de Stack Overflow](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Dos de las bibliotecas de compatibilidad: `Support-v4` y `Support-v13` no se pueden usar juntas en la misma aplicación, es decir, se excluyen mutuamente. Esto se debe a que, en realidad, `Support-v13` contiene todos los tipos e implementaciones de `Support-v4`. Si se prueban y se hace referencia a ambas en el mismo proyecto, se producirán errores de tipo duplicado.

## <a name="problems-with-referencing"></a>Problemas con la referencia

Dado que `Support-v4` se ha vuelto tan popular, muchas bibliotecas de terceros dependen de ella. Podrían haber elegido depender de Support-v13 en su lugar, pero es más común depender de _v4_ ya que proporciona a las aplicaciones que usan estas bibliotecas de terceros la opción de compatibilidad hasta el nivel de API 4.

Si una biblioteca de terceros de Xamarin hace referencia al enlace `Xamarin.Android.Support.v4.dll` a `Support-v4`, cualquier aplicación que use esta biblioteca también debe hacer referencia a `Xamarin.Android.Support.v4.dll`. Esto se convierte en un problema cuando la misma aplicación también quiere usar parte de la funcionalidad del enlace de `Xamarin.Android.Support.v13.dll` a `Support-v13`. Si hace referencia a ambos enlaces, se producirán errores de tipo duplicado.

## <a name="type-forwarded-v4-binding-assembly"></a>Ensamblado de enlace v4 con reenvío de tipos

Para obtener una alternativa para solucionar este problema, se ha creado un ensamblado `Xamarin.Android.Support.v4.dll` especial que no tiene ninguna implementación, sino simplemente los atributos `[assembly: TypeForwardedTo (..)]` que reenvían todos los tipos de `Support-v4` a la implementación dentro del ensamblado `Xamarin.Android.Support.v13.dll`.

Esto significa que un desarrollador puede hacer referencia a este ensamblado con _reenvío de tipos_ en su aplicación, lo que satisfará la referencia a `Xamarin.Android.Support.v4.dll` de cualquier biblioteca de terceros, a la vez que sigue permitiendo que se use `Xamarin.Android.Support.v13.dll` en la aplicación.

## <a name="nuget-assistance"></a>Asistencia para NuGet

Aunque un desarrollador puede agregar manualmente las referencias correctas necesarias, podemos usar NuGet para ayudar a elegir el ensamblado correcto (el enlace normal _v4_ o el ensamblado _v4_ con reenvío de tipos) al instalar el paquete NuGet.

Por lo tanto, el paquete NuGet `Xamarin.Android.Support.v4` contiene ahora la siguiente lógica:

Si su aplicación tiene como destino el nivel de API 13 (Gingerbread 3.2) o superior:

* `Xamarin.Android.Support.v13` de NuGet se agregará automáticamente como una dependencia.
* Se hará referencia a `Xamarin.Android.Support.v4.dll` con _reenvío de tipos_ en el proyecto.

Si su aplicación tiene como destino un nivel más bajo que el nivel de API 13, obtendrá el enlace `Xamarin.Android.Support.v4.dll` normal al que se hace referencia en el proyecto.

## <a name="do-i-have-to-use-support-v13"></a>¿Tengo que usar Support-v13?

Si su aplicación tiene como destino el nivel de API 13 o superior y decide usar el paquete NuGet `Xamarin Android Support-v4`, el paquete NuGet de `Xamarin Android Support v13` es una dependencia necesaria.

Creemos que para la compatibilidad merece la pena un aumento tan pequeño del tamaño de la aplicación (los dos archivos .jar difieren en 17 Kb) y se producen menos quebraderos de cabeza.

Si es inflexible sobre el uso de `Support-v4` en una aplicación que tiene como destino el nivel de API 13 o superior, siempre puede descargar `.nupkg` manualmente, extraerlo y hacer referencia al ensamblado.
