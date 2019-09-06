---
title: Trabajo con tipos nativos en aplicaciones multiplataforma
description: En este artículo se explica el uso de los nuevos tipos nativos de Unified API de iOS (NINT, nuint, nfloat) en una aplicación multiplataforma donde el código se comparte con dispositivos que no son iOS como Android o Windows Phone os.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: conceptdev
ms.author: crdun
ms.date: 04/07/2016
ms.openlocfilehash: dde5b2429622c967fa4419700ce8fe9860afbb10
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290850"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Trabajo con tipos nativos en aplicaciones multiplataforma

_En este artículo se explica el uso de los nuevos tipos nativos de Unified API de iOS (NINT, nuint, nfloat) en una aplicación multiplataforma donde el código se comparte con dispositivos que no son iOS como Android o Windows Phone os._


Los tipos nativos de tipo 64 funcionan con las API de iOS y Mac. Si está escribiendo código compartido que se ejecuta también en Android o Windows, deberá administrar la conversión de tipos unificados en tipos de .NET normales que puede compartir.

En este documento se describen diferentes formas de interoperar con el Unified API del código compartido o común.

## <a name="when-to-use-the-native-types"></a>Cuándo usar los tipos nativos

Las API unificadas de Xamarin. iOS y Xamarin. Mac `int`todavía `uint` incluyen `float` los tipos de datos, y, `RectangleF`así como `PointF` los tipos, `SizeF` y. Estos tipos de datos existentes deben seguir utilizándose en cualquier código compartido multiplataforma. Los nuevos tipos de datos nativos solo deben usarse al realizar una llamada a una API de Mac o iOS, donde se requiere compatibilidad con tipos compatibles con la arquitectura.

Dependiendo de la naturaleza del código que se comparte, puede haber ocasiones en las que el código multiplataforma pueda necesitar tratar con los `nint`tipos de datos, `nuint` y `nfloat` . Por ejemplo: una biblioteca que controla las transformaciones de datos rectangulares que anteriormente `System.Drawing.RectangleF` utilizaba para compartir la funcionalidad entre las versiones de Xamarin. iOS y Xamarin. Android de una aplicación, tendría que actualizarse para controlar tipos nativos en iOS.

La forma en que se controlan estos cambios depende del tamaño y la complejidad de la aplicación y de la forma de uso compartido de código que se ha usado, como veremos en las secciones siguientes.

## <a name="code-sharing-considerations"></a>Consideraciones sobre el uso compartido de código

Como se indica en el documento [Opciones de código para compartir](~/cross-platform/app-fundamentals/code-sharing.md) , hay dos formas principales de compartir código entre proyectos multiplataforma: Proyectos compartidos y bibliotecas de clases portables. Cuál de los dos tipos se ha usado, limitará las opciones que tenemos al controlar los tipos de datos nativos en código multiplataforma.

### <a name="portable-class-library-projects"></a>Proyectos de biblioteca de clases portable

Una biblioteca de clases portable (PCL) permite tener como destino las plataformas que desea admitir y utilizar interfaces para proporcionar funcionalidad específica de la plataforma.

Dado que el tipo de proyecto PCL se compila en `.DLL` un y no tiene sentido el Unified API, se le obligará a seguir usando los tipos de datos existentes (`int`, `uint`, `float`) en el código fuente de PCL y a convertir las llamadas a las de la PCL clases y métodos en las aplicaciones de front-end. Por ejemplo:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Proyectos compartidos

El tipo de proyecto de recurso compartido le permite organizar el código fuente en un proyecto independiente que, a continuación, se incluye y se compila en las aplicaciones front-end específicas `#if` de cada plataforma y usa directivas de compilador según sea necesario para administrar requisitos específicos de la plataforma.

Es necesario tener en cuenta el tamaño y la complejidad de las aplicaciones móviles front-end que consumen código compartido, junto con el tamaño y la complejidad del código que se comparte, al elegir el método de compatibilidad con tipos de datos nativos en una plataforma multiplataforma. Proyecto de recurso compartido.

En función de estos factores, los siguientes tipos de soluciones podrían implementarse mediante `if __UNIFIED__ ... #endif` las directivas de compilador para controlar el Unified API cambios concretos en el código.

#### <a name="using-duplicate-methods"></a>Usar métodos duplicados

Tome el ejemplo de una biblioteca que realiza transformaciones en datos rectangulares especificados anteriormente. Si la biblioteca solo contiene uno o dos métodos muy sencillos, puede optar por crear versiones duplicadas de esos métodos para Xamarin. iOS y Xamarin. Android. Por ejemplo:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

En el código anterior, dado que `CalculateArea` la rutina es muy simple, se ha usado la compilación condicional y se ha creado una versión independiente Unified API del método. Por otro lado, si la biblioteca contiene muchas rutinas o varias rutinas complejas, esta solución no sería factible, ya que suponería un problema para mantener todos los métodos sincronizados para modificaciones o correcciones de errores.

#### <a name="using-method-overloads"></a>Usar sobrecargas de método

En ese caso, la solución podría ser crear una versión de sobrecarga de los métodos usando tipos de datos de 32 bits para que ahora tomen `CGRect` como parámetro y/o un valor devuelto, convertir ese valor en un `RectangleF` (sabiendo que la conversión `nfloat` de a `float` es una conversión de pérdida) y llama a la versión original de la rutina para realizar el trabajo real. Por ejemplo:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Call original routine to calculate area
                return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

Una vez más, se trata de una buena solución, siempre y cuando la pérdida de precisión no afecte a los resultados de las necesidades específicas de la aplicación.

#### <a name="using-alias-directives"></a>Usar directivas de alias

En el caso de las áreas en las que la pérdida de precisión es un problema, `using` otra posible solución es usar directivas para crear un alias para los tipos de datos nativos y CoreGraphics incluyendo el código siguiente en la parte superior de los archivos de código fuente compartidos y convirtiendo cualquier necesario `int`, `uint` o valores`float` para `nint` o:`nfloat` `nuint`

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

Por lo tanto, el código de ejemplo se convierte en:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Map Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Map Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

Tenga en cuenta que aquí hemos cambiado `CalculateArea` el método para devolver `nfloat` un en lugar del estándar `float`. Esto se hizo para que no se obtenga un error de compilación al intentar convertir _implícitamente_ el `nfloat` resultado de nuestro cálculo (dado que los dos valores que se van `nfloat`a multiplicar `float` son del tipo) a un valor devuelto.

Si el código se compila y se ejecuta en un dispositivo que no es `using nfloat = global::System.Single;` Unified API, `nfloat` asigna a `Single` un que se convertirá implícitamente `float` en un que `CalculateArea` permita a la aplicación de front-end que lo consume llamar al método sin produzca.


#### <a name="using-type-conversions-in-the-front-end-app"></a>Usar conversiones de tipos en la aplicación front-end

En caso de que las aplicaciones front-end solo realicen una serie de llamadas a la biblioteca de código compartido, otra solución podría ser dejar la biblioteca sin cambios y realizar la conversión de tipos en la aplicación Xamarin. iOS o Xamarin. Mac al llamar a la rutina existente. Por ejemplo:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Si la aplicación consumidora realiza cientos de llamadas a la biblioteca de código compartido, es posible que no sea una buena solución.

En función de la arquitectura de la aplicación, podríamos acabar usando una o varias de las soluciones anteriores para admitir tipos de datos nativos (cuando sea necesario) en nuestro código multiplataforma.


## <a name="xamarinforms-applications"></a>Aplicaciones de Xamarin. Forms

Se requiere lo siguiente para usar Xamarin. Forms para ius multiplataforma que también se compartirán con una aplicación Unified API:

- Toda la solución debe usar la versión 1.3.1 (o superior) del paquete NuGet de Xamarin. Forms.
- En el caso de las representaciones personalizadas de Xamarin. iOS, use los mismos tipos de soluciones que se han presentado anteriormente en función de cómo se haya compartido el código de la interfaz de usuario (proyecto compartido o PCL).

Como en una aplicación multiplataforma estándar, los tipos de datos existentes de 32 bits deben usarse en cualquier código compartido multiplataforma para la mayoría de las situaciones. Los nuevos tipos de datos nativos solo deben usarse al realizar una llamada a una API de Mac o iOS, donde se requiere compatibilidad con tipos compatibles con la arquitectura.

Para obtener más información, consulte nuestra documentación sobre la [actualización de aplicaciones de Xamarin. Forms existentes](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) .

## <a name="summary"></a>Resumen

En este artículo hemos descubierto Cuándo usar los tipos de datos nativos en una aplicación Unified API y sus implicaciones entre plataformas. Hemos presentado varias soluciones que se pueden usar en situaciones en las que los nuevos tipos de datos nativos deben usarse en bibliotecas multiplataforma. Además, hemos visto una guía rápida para la compatibilidad de API unificadas en aplicaciones multiplataforma de Xamarin. Forms.



## <a name="related-links"></a>Vínculos relacionados

- [Unified API](~/cross-platform/macios/unified/index.md)
- [Tipos nativos](~/cross-platform/macios/nativetypes.md)
- [Opciones de código compartido](~/cross-platform/app-fundamentals/code-sharing.md)
- [Ejemplo de uso compartido de código](https://docs.microsoft.com/samples/xamarin/mobile-samples/sharingcode/)
