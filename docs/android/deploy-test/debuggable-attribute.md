---
title: Atributo Debuggable
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487976"
---
# <a name="debuggable-attribute"></a>Atributo Debuggable

Para que la depuración sea posible, Android admite el protocolo JDWP (Java Debug Wire Protocol). Se trata de una tecnología que permite que herramientas como ADB se comuniquen con una JVM. Aunque JDWP es importante durante el desarrollo, se debe deshabilitar antes de la publicación.

JDWP puede ser el valor del atributo `android:debuggable` en una aplicación Android. Elija _una_ de las tres maneras siguientes para establecer este atributo en Xamarin.Android:

## <a name="androidmanifestxml"></a>AndroidManifest.xml

Se crea o se abre el archivo `AndroidManifext.xml` y se establece ahí el atributo `android:debuggable`. Tenga cuidado y no envíe su versión de lanzamiento con la depuración habilitada.

## <a name="add-an-application-class-attribute"></a>Adición de un atributo de clase de aplicación

Si la aplicación de Xamarin.Android tiene una clase con un atributo `[Application]`, actualice el atributo a `[Application(Debuggable = true)]`. Establézcalo en `false` para deshabilitarlo.

## <a name="add-an-assembly-attribute"></a>Adición de un atributo de ensamblado

Si la aplicación de Xamarin.Android aún no tiene un atributo de la clase `[Application]`, agregue un atributo de nivel de ensamblado `[assembly: Application(Debuggable=true)]` en un archivo de c#. Establézcalo en `false` para deshabilitarlo.

## <a name="summary"></a>Resumen

Si `AndroidManifest.xml` y `ApplicationAttribute` existen, el contenido de `AndroidManifest.xml` tiene prioridad sobre lo que se especifica mediante `ApplicationAttribute`.

Si agrega un atributo de clase _y_ un atributo de ensamblado, se producirá un error del compilador:

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

De forma predeterminada, si no existen `AndroidManifest.xml` ni `ApplicationAttribute`, el valor predeterminado del atributo `android:debuggable` depende de si se generan o no símbolos de depuración. Si existen símbolos de depuración, Xamarin.Android establece el atributo `android:debuggable` en `true`.

> [!WARNING]
> El valor del atributo `android:debuggable` no depende necesariamente de la configuración de compilación. Puede que las compilaciones de lanzamiento tengan el atributo `android:debuggable` establecido en true. Si usa un atributo para establecer este valor, puede elegir ajustar el atributo en una directiva de compilador:
> 
> ```csharp
> #if DEBUG
> [Application(Debuggable = true)]
> #else
> [Application(Debuggable = false)]
> #endif
> ```

## <a name="related-links"></a>Vínculos relacionados

- [Debuggable apps in the Android market](https://labs.f-secure.com/archive/debuggable-apps-in-android-market/) (Aplicaciones que se pueden depurar en Android Market)
