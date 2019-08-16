---
title: Solucionar problemas relacionados con System. Diagnostics. Tracing y el flujo de entrada de TPL
description: 'Caso práctico de PCL: ¿Cómo se pueden resolver problemas relacionados con System.Diagnostics.Tracing para el paquete NuGet de flujo de datos TPL de Microsoft?'
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: asb3993
ms.author: amburns
ms.openlocfilehash: 09aef14efdce93e28326deb78292da98f1969ea1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521563"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Caso práctico de PCL: ¿Cómo se pueden resolver problemas relacionados con System.Diagnostics.Tracing para el paquete NuGet de flujo de datos TPL de Microsoft?

> [!IMPORTANT]
> Este ejemplo determinado de `System.Diagnostic.Tracing` ya no genera ningún error de forma predeterminada en las versiones más recientes de Xamarin. Aunque la solución sugerida seguirá funcionando, tenga en cuenta que se han corregido algunos de los errores mencionados en la sección "niveles de error".
> Además, debe tener en cuenta que .NET Standard es ahora la mejor manera de implementar las API de .NET multiplataforma.

## <a name="summary"></a>Resumen

Xamarin. iOS y Xamarin. Android no implementan el 100% de todos los perfiles de PCL que permiten como referencias. Por comodidad práctica en Visual Studio para Mac, Visual Studio y el administrador de paquetes NuGet, los proyectos de Xamarin permiten el uso de varios perfiles que solo tienen implementaciones incompletas. Por ejemplo, ni Xamarin. iOS ni Xamarin. Android incluyen actualmente una implementación completa de los tipos en el espacio de nombres PCL "System. Diagnostics. Tracing". Esta limitación conduce a tres niveles de errores al intentar usar la versión predeterminada `portable-net45+win8+wpa81` del paquete NuGet de flujo de entrada TPL de Microsoft.

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Solución: Cambiar el proyecto de la aplicación para `portable-net45+win8+wp8+wpa81` hacer referencia a la versión de la biblioteca de flujos de entrada TPL

(Esto evita los tres niveles de errores y funciona en todas las versiones recientes de Xamarin).

1. Abra el archivo **. csproj** del proyecto de aplicación en un editor de texto.

2. Busque la línea que tiene un aspecto similar al siguiente:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Cambiar `portable-net45+win8+wpa81` a `portable-net45+win8+wp8+wpa81` (`+wp8` se agrega):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Explicación

La `portable-net45+win8+wp8+wpa81` versión de la biblioteca no hace referencia _a_ **System. Diagnostics. Tracing. dll** , por lo que evita los tres niveles de problemas.

### <a name="limitations"></a>Limitaciones

- Es `portable-net45+win8+wp8+wpa81` posible que la versión de la biblioteca no incluya el 100% de la `portable-net45+win8+wpa81` funcionalidad de la versión.

- De forma predeterminada, el administrador de `portable-net45+win8+wpa81` paquetes Nuget instala la versión del paquete Nuget de PCL, por lo que debe ajustar la referencia manualmente.

## <a name="details-about-the-three-layers-of-errors"></a>Detalles sobre los tres niveles de error

1. El ensamblado de fachada **System. Diagnostics. Tracing. dll** no se encuentra actualmente en todas las versiones Mac de Xamarin. Android (error no público 34888) y no está presente en todas las versiones de Xamarin. iOS inferiores a 9,0 (o inferior a XamarinVS 3.11.1443 en Windows) (corregido en [Error 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Este problema producirá uno de los siguientes errores en función de la configuración del enlazador y del destino de implementación:

    - Xamarin.Android.Common.targets: Error: Excepción al cargar los ensamblados: System.IO.FileNotFoundException: No se pudo cargar el ensamblado ' System. Diagnostics. Tracing, version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a '. Quizás no exista en el perfil mono para Android?

    - No se pudo cargar el archivo o ensamblado ' System. Diagnostics. Tracing ' o una de sus dependencias. El sistema no puede encontrar el archivo especificado. (System.IO.FileNotFoundException)

    - MTOUCH: error MT3001: No se pudo el AOT en el ensamblado '/Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - MTOUCH: error MT2002: No se pudo resolver el ensamblado: 'System.Diagnostics.Tracing, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'

2. En la [implementación de mono actual de los tipos de "System. Diagnostics. Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) faltan algunas sobrecargas de método ([error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Este problema producirá uno de los siguientes errores del enlazador al compilar una aplicación de Xamarin:

    - /Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: error: Error al ejecutar la tarea LinkAssemblies: error XA2006: Referencia al elemento de metadatos ' System. void System. Diagnostics. Tracing. EventSource:: WriteEvent (System. Int32, System. Object []) ' (definido en ' System. Threading. Tasks. DataFlow, version = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a ') de ' System. Threading. Tasks. DataFlow, version = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a ' no se pudo resolver.

    - MTOUCH: error MT2002: No se pudo resolver la referencia "System. void System. Diagnostics. Tracing. EventSource:: WriteEvent (System. Int32, System. Object [])" de "System. Diagnostics. Tracing, version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a"

3. La [implementación de mono actual de los tipos en "System. Diagnostics. Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) también es actualmente una implementación "ficticia" _vacía_ ([error 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Cualquier intento de utilizar estos métodos en tiempo de ejecución podría producir resultados inesperados. Para el caso concreto de la biblioteca de flujos de _datos_ TPL de Microsoft, parece que `WriteEvent(System.Int32,System.Object[])` las llamadas a no son esenciales para la mayor parte del comportamiento de la biblioteca, por lo que la corrección para "nivel 2" ([error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), agregar implementaciones vacías) probablemente será suficiente. para la mayoría de los casos de uso de flujo de DataFlow de TPL de Microsoft.

## <a name="questions--answers"></a>Preguntas & respuestas

### <a name="i-was-able-to-leave-linking-enabled-with-the-portable-net45win8wpa81-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>He podido dejar la vinculación habilitada con `portable-net45+win8+wpa81` la versión de la biblioteca en versiones anteriores de Xamarin. iOS o Xamarin. Android. ¿Cómo funcionaba esto?

#### <a name="answer"></a>Respuesta

Es _posible_ que la compilación se complete correctamente (con la vinculación habilitada) en las versiones anteriores de Xamarin. iOS o en Xamarin. Android en Mac si se incluye una referencia al `System.Diagnostics.Tracing.dll` _ensamblado_ \[de referencia 1\]en lugar del _ensamblado_ \[de fachada 2], pero desafortunadamente esto no es una solución "correcta". Los ensamblados de referencia solo están diseñados para usarse al compilar _bibliotecas portables_, no código específico de la plataforma como aplicaciones. Es probable que el intento de _Ejecutar_ el código contenido en ensamblados de referencia (en lugar de simplemente compilar en él) genere resultados inesperados. La corrección correcta será que el equipo de mono agregue la sobrecarga que `WriteEvent(System.Int32,System.Object[])` falta [`EventSource`](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) al tipo ([error 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Por ahora, la mejor opción es cambiar a la `portable-net45+win8+wp8+wpa81` versión de la biblioteca de flujos de entrada de TPL de Microsoft, como se describe en la sección solución anterior.

(Para cualquier persona que pueda estar leyendo este artículo después de ver una respuesta más antigua y más antigua de<https://stackoverflow.com/a/23591322/2561894>stackoverflow (), tenga en cuenta que la distinción entre ensamblados de referencia y ensamblados de fachada _no_ se mencionó allí.)

**\[1\] "ensamblado de referencia"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] "ubicaciones del ensamblado de fachada"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>¿Le ayudará si agrego manualmente una referencia al ensamblado de fachada "System. Diagnostics. Tracing"?

_En concreto, ¿puedo resolver el problema con estos 2 pasos?_

1. _Copie el `System.Diagnostics.Tracing.dll` ensamblado de fachada en la carpeta del proyecto de aplicación desde una de las siguientes ubicaciones:_

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Agregue una referencia al ensamblado de fachada en el proyecto de aplicación de Xamarin. iOS o Xamarin. Android._

#### <a name="answer"></a>Respuesta

No, esto no servirá de ayuda.

- Para Xamarin. iOS 9,0 o cualquier versión reciente de Xamarin. Android en Windows, esta solución es estrictamente redundante y puede provocar errores de compilación similares a "se ha importado un ensamblado System. Diagnostics. Tracing ' con la misma identidad".

- En el caso de Xamarin. iOS 8,10 o inferior o para Xamarin. Android en Mac, esta solución alternativa le ayudará, pero _solo_ para el problema del ensamblado "capa 1" que falta. _No_ se resolverán los errores del enlazador "capa 2", por lo que no es una solución completa.

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>¿Puedo usar el [paquete NuGet System. Diagnostics. Tracing](https://www.nuget.org/packages/System.Diagnostics.Tracing/) para solucionar el problema?

#### <a name="answer"></a>Respuesta

No, el paquete NuGet 3,0 "System. Diagnostics. Tracing" solo incluye implementaciones específicas de la plataforma para "DNXCore50" y "netcore50". _Omite_ explícitamente las implementaciones de Xamarin. Android ("monoandroid") y Xamarin. iOS ("MonoTouch" y "xamarinios"). Esto significa que la instalación del paquete _no tendrá ningún efecto_ en los proyectos de Xamarin. Android y Xamarin. iOS. El paquete NuGet supone que ambas plataformas proporcionan su _propia_ implementación de los tipos. Esta suposición es "correcta" en el sentido de que mono tiene _una_ implementación del espacio de nombres, pero como se \#describe en \#los puntos 2 y 3 de "detalles sobre los tres niveles de error", la implementación es actualmente completamente. Por lo tanto, la solución adecuada será que el equipo de mono resuelva los [errores 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) y [34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, para ponerse en contacto con nosotros o si este problema permanece incluso después de usar la información anterior, consulte [¿Qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias y cómo archivar un nuevo error si es necesario .
