---
title: Actualización de aplicaciones Mac existentes
description: En este documento se describen los pasos que deben seguirse para actualizar una aplicación de Xamarin. Mac desde el Classic API al Unified API.
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: a6d245e60a0d163c054fd74e0fb64780225da031
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197807"
---
# <a name="updating-existing-mac-apps"></a>Actualización de aplicaciones Mac existentes

La actualización de una aplicación existente para usar el Unified API requiere cambios en el propio archivo de proyecto, así como en los espacios de nombres y las API que se usan en el código de la aplicación.

## <a name="the-road-to-64-bits"></a>El camino a 64 bits

Las nuevas API unificadas son necesarias para admitir arquitecturas de dispositivos de 64 bits desde una aplicación de Xamarin. Mac. A partir del 1 de febrero, 2015 Apple requiere que todos los envíos de aplicaciones nuevas a Mac App Store admitan las arquitecturas de 64 bits.

Xamarin proporciona herramientas para Visual Studio para Mac y Visual Studio para automatizar el proceso de migración desde el Classic API al Unified API o puede convertir los archivos de proyecto manualmente. Aunque el uso de las herramientas automáticas es muy sugerido, en este artículo se tratarán ambos métodos.

### <a name="before-you-start"></a>Antes de empezar...

Antes de actualizar el código existente al Unified API, se recomienda encarecidamente que elimine todas las *advertencias de compilación*. Muchas *advertencias* en el Classic API se convertirán en errores una vez que migre a Unified. Corregirlos antes de empezar es más fácil, ya que los mensajes del compilador del Classic API suelen proporcionar sugerencias sobre lo que se debe actualizar.

## <a name="automated-updating"></a>Actualización automatizada

Una vez que se hayan corregido las advertencias, seleccione un proyecto de Mac existente en Visual Studio para Mac o Visual Studio y elija **migrar a Xamarin. Mac Unified API** en el menú **proyecto** . Por ejemplo:

![](updating-mac-apps-images/beta-tool1.png "Elija migrar a Xamarin. Mac Unified API en el menú proyecto.")

Deberá aceptar esta advertencia antes de que se ejecute la migración automática (obviamente, debe asegurarse de que tiene copias de seguridad o control de código fuente antes de embarcarse en esta aventura):

![](updating-mac-apps-images/migrate01.png "Aceptar esta advertencia antes de que se ejecute la migración automatizada")

Hay dos tipos de plataforma de destino admitidos que se pueden seleccionar al usar el Unified API en una aplicación de Xamarin. Mac:

- **Entorno móvil de Xamarin. Mac** : es el mismo .NET Framework optimizado que usa Xamarin. iOS y Xamarin. Android, lo que admite un subconjunto del marco de trabajo de **escritorio** completo. Este es el marco de trabajo recomendado porque proporciona archivos binarios más pequeños debido a un comportamiento de vinculación superior.
- **SDK de Xamarin. Mac .net 4,5** : este marco de trabajo vuelve a ser un subconjunto del marco de trabajo de **escritorio** . Sin embargo, recorta mucho menos el marco de trabajo de **escritorio** completo que el marco **móvil** y debe _"simplemente trabajar"_ con la mayoría de los paquetes de NuGet o bibliotecas de terceros. Esto permite al desarrollador consumir ensamblados de **escritorio** estándar mientras sigue usando un marco compatible, pero esta opción genera agrupaciones de aplicaciones más grandes. Este es el marco recomendado en el que se usan ensamblados .NET de terceros que no son compatibles con el **marco móvil de Xamarin. Mac**. Para obtener una lista de los ensamblados admitidos , consulte la documentación de los [ensamblados](~/cross-platform/internals/available-assemblies.md).

Para obtener información detallada sobre las plataformas de destino y las implicaciones de seleccionar un destino específico para su aplicación de Xamarin. Mac, consulte nuestra documentación sobre las [plataformas de destino](~/mac/platform/target-framework.md) . 

Básicamente, la herramienta automatiza todos los pasos descritos en la sección **actualización manual** que se muestra a continuación y es el método sugerido para convertir un proyecto de Xamarin. Mac existente en el Unified API.

## <a name="steps-to-update-manually"></a>Pasos para actualizar manualmente

Una vez que se han corregido las advertencias, siga estos pasos para actualizar manualmente las aplicaciones de Xamarin. Mac para usar el nuevo Unified API:

### <a name="1-update-project-type--build-target"></a>1. Actualizar el tipo de proyecto & destino de compilación

Cambie el tipo de proyecto en los archivos csproj `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` de `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`a. Edite el archivo **csproj** en un editor de texto, reemplazando el primer `<ProjectTypeGuids>` elemento del elemento como se muestra a continuación:

![](updating-mac-apps-images/csproj.png "Edite el archivo csproj en un editor de texto, reemplazando el primer elemento del elemento ProjectTypeGuids como se muestra")

Cambie el elemento **Import** que contiene `Xamarin.Mac.targets` a `Xamarin.Mac.CSharp.targets` , como se muestra a continuación:

![](updating-mac-apps-images/csproj2.png "Cambie el elemento Import que contiene Xamarin. Mac. targets a Xamarin. Mac. CSharp. targets tal como se muestra")

Agregue las siguientes líneas de código después del `<AssemblyName>` elemento:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Ejemplo:

![Agregue estas líneas de código después del \<elemento AssemblyName >](updating-mac-apps-images/csproj3.png)

### <a name="2-update-project-references"></a>2. Actualizar referencias de proyecto

Expanda el nodo **referencias** del proyecto de aplicación Mac. Inicialmente, mostrará una referencia * broked- **XamMac** similar a esta captura de pantalla (porque simplemente hemos cambiado el tipo de proyecto):

![](updating-mac-apps-images/references.png "Inicialmente mostrará una referencia rota-XamMac similar a esta captura de pantalla")

Haga clic en el **icono de engranaje** situado junto a la entrada **XamMac** y seleccione **eliminar** para quitar la referencia rota.

A continuación, haga clic con el botón derecho en la carpeta **referencias** en el **Explorador de soluciones** y seleccione **Editar referencias**. Desplácese hasta la parte inferior de la lista de referencias y coloque una comprobación además de **Xamarin. Mac**.

![](updating-mac-apps-images/references2.png "Desplácese hasta la parte inferior de la lista de referencias y coloque una comprobación además de Xamarin. Mac.")

Presione **Aceptar** para guardar los cambios de las referencias del proyecto.

### <a name="3-remove-monomac-from-namespaces"></a>3. Quitar MonoMac de los espacios de nombres

Quite el prefijo **MonoMac** de los espacios `using` de nombres de las instrucciones o cualquier lugar en el que se haya calificado un nombre de clase completo (por ejemplo, `MonoMac.AppKit`solo `AppKit`se convierte en).

### <a name="4-remap-types"></a>4. Tipos de reasignación

Se han introducido [tipos nativos](~/cross-platform/macios/nativetypes.md) que reemplazan a algunos tipos que se usaron previamente, como `System.Drawing.RectangleF` instancias `CoreGraphics.CGRect` de con (por ejemplo,). La lista completa de tipos se puede encontrar en la página [tipos nativos](~/cross-platform/macios/nativetypes.md) .

### <a name="5-fix-method-overrides"></a>5. Corregir invalidaciones de método

Algunos `AppKit` métodos han cambiado su firma para usar los nuevos [tipos nativos](~/cross-platform/macios/nativetypes.md) ( `nint`como). Si las subclases personalizadas invalidan estos métodos, las firmas dejarán de coincidir y producirán errores. Corrija estas invalidaciones de método cambiando la subclase para que coincida con la nueva firma mediante tipos nativos. 

## <a name="considerations"></a>Consideraciones

Se deben tener en cuenta las siguientes consideraciones al convertir un proyecto de Xamarin. Mac existente del Classic API al nuevo Unified API si esa aplicación se basa en uno o varios paquetes de componentes o NuGet. 

### <a name="components"></a>Componentes

Cualquier componente que haya incluido en la aplicación también tendrá que actualizarse al Unified API o se producirá un conflicto al intentar compilar. Para cualquier componente incluido, reemplace la versión actual por una nueva versión del almacén de componentes de Xamarin que admita el Unified API y realice una compilación limpia. Cualquier componente que el autor no haya convertido todavía, mostrará una advertencia solo de 32 bits en el almacén de componentes.

### <a name="nuget-support"></a>Compatibilidad con NuGet

Aunque hemos contribuido a los cambios a NuGet para que funcionen con el soporte de Unified API, no se ha producido una nueva versión de NuGet, por lo que estamos evaluando cómo obtener NuGet para reconocer las nuevas API. 

Hasta ese momento, al igual que los componentes, deberá cambiar cualquier paquete de NuGet que haya incluido en el proyecto a una versión que admita las API unificadas y, posteriormente, realizar una compilación limpia.

> [!IMPORTANT]
> Si tiene un error con el formato _"el error 3 no puede incluir ' monomac. dll ' y ' Xamarin. Mac. dll ' en el mismo proyecto de Xamarin. Mac: se hace referencia a ' Xamarin. Mac. dll ' explícitamente, mientras que ' XXX, version = 0.0.000, Culture = neutral, PublicKeyToken = null ' "_ después de convertir la aplicación en las API unificadas, normalmente se debe a que hay un componente o un paquete NuGet en el proyecto que no se ha actualizado al Unified API. Deberá quitar el componente o NuGet existente, actualizar a una versión que admita las API unificadas y realizar una compilación limpia.

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitar compilaciones de 64 bits de aplicaciones de Xamarin. Mac

En el caso de una aplicación móvil de Xamarin. Mac que se ha convertido en el Unified API, el desarrollador todavía necesita habilitar la compilación de la aplicación para equipos de 64 bits a partir de las opciones de la aplicación. Consulte la información sobre la habilitación de las compilaciones **de 64 bits de las aplicaciones de Xamarin. Mac** del documento consideraciones de la [plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md) para obtener instrucciones detalladas sobre cómo habilitar compilaciones de 64 bits

## <a name="finishing-up"></a>Finalizando

Tanto si decide usar el método automático como el manual para convertir la aplicación de Xamarin. Mac del modelo clásico al de las API unificadas, hay varias instancias que requerirán más intervención manual. Consulte [las sugerencias para actualizar el código al Unified API documento para ver los](~/cross-platform/macios/unified/updating-tips.md) problemas conocidos y solucionar el problema.

## <a name="related-links"></a>Vínculos relacionados

- [Sugerencias para actualizar el código a Unified API](~/cross-platform/macios/unified/updating-tips.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de Unified API clásicas](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
