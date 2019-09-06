---
title: Creación manual de paquetes NuGet para Xamarin
description: Este documento contiene sugerencias para ayudar a compilar paquetes NuGet que tienen como destino la plataforma Xamarin. Describe los perfiles de Xamarin de paquetes de NuGet, PCL paquetes Nuget con dependencias de plataforma y vínculos a varios ejemplos de código abierto.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 88feea4c0a638da99a0316d802f04f08f0fea689
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289200"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Creación manual de paquetes NuGet para Xamarin

_Esta página contiene algunas sugerencias para ayudar a compilar paquetes NuGet que tienen como destino la plataforma Xamarin._

> [!NOTE]
> Xamarin Studio 6,2 (y Visual Studio para Mac) incluye la capacidad de generar paquetes NuGet _automáticamente_ desde PCL, .net Standard o proyectos compartidos. Para obtener más información, consulte la guía [bibliotecas multiplataforma para el uso compartido de código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) .

## <a name="nuget-package-xamarin-profiles"></a>Perfil de Xamarin de paquetes de NuGet

El sitio web de NuGet que [admite varias versiones y perfiles de .NET Framework](https://docs.nuget.org/create/enforced-package-conventions) explica cómo admitir diferentes marcos y perfiles de Microsoft, pero no incluye los nombres de los marcos de trabajo de destino usados por Xamarin.

Las principales plataformas de destino de Xamarin que se usan hoy en día son:

- **MonoAndroid** - Xamarin.Android
- **Xamarin. iOS** : [Unified API](~/cross-platform/macios/unified/index.md) de Xamarin. iOS (admite 64 bits)
- **Xamarin. Mac** : perfil móvil de Xamarin. Mac, que es equivalente a la superficie de la API de Xamarin. iOS y Xamarin. Android.

También hay un destino para el [Classic API](~/cross-platform/macios/unified/index.md)de iOS anterior:

- Classic API **MonoTouch** -iOS

Un archivo **. nuspec** destinado a todos estos elementos tendría un aspecto similar al siguiente:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

Lo anterior omite cualquier biblioteca de clases portable.

La mayoría de los archivos **. nuspec** especifican el número de versión de la plataforma de destino, pero es opcional si el ensamblado funciona con todas las versiones de esa plataforma de destino. Por lo tanto, si su destino se **lib\MonoAndroid** , esto significa que funciona con cualquier versión de Xamarin. Android.

Puede especificar la versión con un conjunto de números sin un separador decimal o puede especificarlo mediante separadores decimales. Sin el separador decimal, NuGet solo tomará cada número y lo convertirá en una versión insertando un '. ' entre cada dígito.

En la sección anterior, "MonoAndroid10" hace referencia a "Android 1,0". Esto significa simplemente que la [plataforma de destino](~/android/app-fundamentals/android-api-levels.md) del proyecto debe ser la versión 1,0 o superior de monoandroid. La versión se especifica en el `<TargetFrameworkVersion>` elemento del archivo de proyecto.

Para aclarar:

- **MonoAndroid403** coincide con Android 4.0.3 y versiones más recientes (nivel de API de IE 15)
- **Xamarin. iOS10** coincide con Xamarin. iOS 1,0 y versiones más recientes
- **Xamarin. iOS 1.0** también coincide con Xamarin. iOS 1,0 y versiones más recientes

## <a name="pcl-nugets-with-platform-dependencies"></a>PCL paquetes Nuget con dependencias de plataforma

Los perfiles de PCL están limitados en lo que a las API de .NET Framework pueden acceder y, por supuesto, no pueden tener acceso a código específico de la plataforma. Estos vínculos de terceros describen los distintos enfoques para crear paquetes NuGet que usan las API nativas y PCL para proporcionar compatibilidad con Xamarin y otras plataformas:

- [Cómo hacer que las bibliotecas de clases portables funcionen para usted](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [El anzuelo y el interruptor de PCL](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Creación de una PCL de NuGet que funcione con Xamarin. iOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Esta [lista externa de perfiles de PCL con su nombre de destino de NuGet](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) también es una referencia útil.

## <a name="examples"></a>Ejemplos

Algunos ejemplos de código abierto a los que puede hacer referencia:

- [**ModernHttpClient**](https://www.nuget.org/packages/modernhttpclient/) : escriba la aplicación con System .net. http, pero Quite esta biblioteca en y tendrá un uso más rápido (ver el [origen](https://github.com/paulcbetts/ModernHttpClient)).
- [**Con asterisco**](https://www.nuget.org/packages/Splat/) : una biblioteca para que las cosas sean multiplataforma que deben ser (ver [origen](https://github.com/paulcbetts/Splat)).
- [**NGraphics**](https://www.nuget.org/packages/NGraphics/) : una biblioteca multiplataforma para representar gráficos vectoriales en .net (ver [origen](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Vínculos relacionados

- [Nugetizer-3000 creación automatizada de Nuget](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [Inclusión de un NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
