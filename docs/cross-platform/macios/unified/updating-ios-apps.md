---
title: Actualización de aplicaciones iOS existentes
description: En este documento se describen los pasos que deben seguirse para actualizar una aplicación de Xamarin. iOS desde el Classic API al Unified API.
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 1cfc2a955ffbe3f1416c291b7d644392d51e0b8f
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930486"
---
# <a name="updating-existing-ios-apps"></a>Actualización de aplicaciones iOS existentes

_Siga estos pasos para actualizar una aplicación de Xamarin. iOS existente para usar el Unified API._

La actualización de una aplicación existente para usar el Unified API requiere cambios en el propio archivo de proyecto, así como en los espacios de nombres y las API que se usan en el código de la aplicación.

## <a name="the-road-to-64-bits"></a>El camino a 64 bits

Las nuevas API unificadas son necesarias para admitir arquitecturas de dispositivos de 64 bits desde una aplicación móvil de Xamarin. iOS. A partir del 1 de febrero, 2015 Apple requiere que todos los envíos de aplicaciones nuevas a iTunes App Store admitan arquitecturas de 64 bits.

Xamarin proporciona herramientas para Visual Studio para Mac y Visual Studio para automatizar el proceso de migración desde el Classic API al Unified API o puede convertir los archivos de proyecto manualmente. Aunque el uso de las herramientas automáticas es muy sugerido, en este artículo se tratarán ambos métodos.

### <a name="before-you-start"></a>Antes de empezar...

Antes de actualizar el código existente al Unified API, se recomienda encarecidamente que elimine todas las *advertencias de compilación*. Muchas *advertencias* en el Classic API se convertirán en errores una vez que migre a Unified. Corregirlos antes de empezar es más fácil, ya que los mensajes del compilador del Classic API suelen proporcionar sugerencias sobre lo que se debe actualizar.

## <a name="automated-updating"></a>Actualización automatizada

Una vez que se hayan corregido las advertencias, seleccione un proyecto de iOS existente en Visual Studio para Mac o Visual Studio y elija **migrar a Xamarin. iOS Unified API** en el menú **proyecto** . Por ejemplo:

![Elija migrar a Xamarin. iOS Unified API en el menú proyecto.](updating-ios-apps-images/beta-tool1.png)

Deberá aceptar esta advertencia antes de que se ejecute la migración automática (obviamente, debe asegurarse de que tiene copias de seguridad o control de código fuente antes de embarcarse en esta aventura):

![Aceptar esta advertencia antes de que se ejecute la migración automatizada](updating-ios-apps-images/beta-tool2.png)

Básicamente, la herramienta automatiza todos los pasos descritos en la sección **actualización manual** que se muestra a continuación y es el método sugerido para convertir un proyecto de Xamarin. iOS existente en el Unified API.

## <a name="steps-to-update-manually"></a>Pasos para actualizar manualmente

Una vez que se han corregido las advertencias, siga estos pasos para actualizar manualmente las aplicaciones de Xamarin. iOS para usar el nuevo Unified API:

### <a name="1-update-project-type--build-target"></a>1. actualizar el tipo de proyecto & destino de compilación

Cambie el tipo de proyecto en los archivos **csproj** de `6BC8ED88-2882-458C-8E55-DFD12B67127B` a `FEACFBD2-3405-455C-9665-78FE426C6842` . Edite el archivo **csproj** en un editor de texto, reemplazando el primer elemento del `<ProjectTypeGuids>` elemento como se muestra a continuación:

![Edite el archivo csproj en un editor de texto, reemplazando el primer elemento del elemento ProjectTypeGuids como se muestra](updating-ios-apps-images/csproj.png)

Cambie el elemento **Import** que contiene `Xamarin.MonoTouch.CSharp.targets` a `Xamarin.iOS.CSharp.targets` , como se muestra a continuación:

![Cambie el elemento Import que contiene Xamarin. MonoTouch. CSharp. targets a Xamarin. iOS. CSharp. targets tal como se muestra](updating-ios-apps-images/csproj2.png)

### <a name="2-update-project-references"></a>2. actualizar las referencias del proyecto

Expanda el nodo **referencias** del proyecto de aplicación de iOS. Inicialmente, se muestra una referencia * rota- **MonoTouch** similar a esta captura de pantalla (porque simplemente hemos cambiado el tipo de proyecto):

![Inicialmente, se mostrará una referencia rota-MonoTouch similar a esta captura de pantalla porque el tipo de proyecto ha cambiado.](updating-ios-apps-images/references.png)

Haga clic con el botón derecho en el proyecto de aplicación de iOS para **editar las referencias**y, después, haga clic en la referencia **MonoTouch** y elimínela con el botón rojo "X".

![Haga clic con el botón derecho en el proyecto de aplicación de iOS para editar las referencias y, después, haga clic en la referencia MonoTouch y elimínela con el botón X de color rojo.](updating-ios-apps-images/references-delete-monotouch-sml.png)

Ahora Desplácese hasta el final de la lista de referencias y marque el ensamblado de **Xamarin. iOS** .

![Ahora Desplácese hasta el final de la lista de referencias y marque el ensamblado de Xamarin. iOS.](updating-ios-apps-images/references-add-xamarinios-sml.png)

Presione **Aceptar** para guardar los cambios de las referencias del proyecto.

### <a name="3-remove-monotouch-from-namespaces"></a>3. quitar MonoTouch de los espacios de nombres

Quite el prefijo **MonoTouch** de los espacios de nombres de las `using` instrucciones o cualquier lugar en el que se haya calificado un nombre de clase completo (por ejemplo, `MonoTouch.UIKit`solo se convierte en `UIKit` ).

### <a name="4-remap-types"></a>4. reasignar tipos

Se han introducido [tipos nativos](~/cross-platform/macios/nativetypes.md) que reemplazan a algunos tipos que se usaron previamente, como instancias de `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (por ejemplo,). La lista completa de tipos se puede encontrar en la página [tipos nativos](~/cross-platform/macios/nativetypes.md) .

### <a name="5-fix-method-overrides"></a>5. corregir invalidaciones de método

Algunos `UIKit` métodos han cambiado su firma para usar los nuevos [tipos nativos](~/cross-platform/macios/nativetypes.md) (como `nint` ). Si las subclases personalizadas invalidan estos métodos, las firmas dejarán de coincidir y producirán errores. Corrija estas invalidaciones de método cambiando la subclase para que coincida con la nueva firma mediante tipos nativos.

Entre los ejemplos `public override int NumberOfSections (UITableView tableView)` se incluye cambiar para devolver `nint` y cambiar el tipo de valor devuelto y los tipos de parámetro de `public override int RowsInSection (UITableView tableView, int section)` a `nint` .

## <a name="considerations"></a>Consideraciones

Se deben tener en cuenta las siguientes consideraciones al convertir un proyecto de Xamarin. iOS existente del Classic API al nuevo Unified API si esa aplicación se basa en uno o varios paquetes de componentes o NuGet.

### <a name="components"></a>Componentes

Cualquier componente que haya incluido en la aplicación también tendrá que actualizarse al Unified API o se producirá un conflicto al intentar compilar. Para cualquier componente incluido, reemplace la versión actual por una nueva versión del almacén de componentes de Xamarin que admita el Unified API y realice una compilación limpia. Cualquier componente que el autor no haya convertido todavía, mostrará una advertencia solo de 32 bits en el almacén de componentes.

### <a name="nuget-support"></a>Compatibilidad con NuGet

Aunque hemos contribuido a los cambios a NuGet para que funcionen con el soporte de Unified API, no se ha producido una nueva versión de NuGet, por lo que estamos evaluando cómo obtener NuGet para reconocer las nuevas API.

Hasta ese momento, al igual que los componentes, deberá cambiar cualquier paquete de NuGet que haya incluido en el proyecto a una versión que admita las API unificadas y, posteriormente, realizar una compilación limpia.

> [!IMPORTANT]
> Si tiene un error con el formato _"error 3 no puede incluir ' monotouch.dll ' y ' Xamarin.iOS.dll ' en el mismo Xamarin. se hace referencia de forma explícita al proyecto de iOS: ' Xamarin.iOS.dll ', mientras que ' XXX, version = 0.0.000, Culture = neutral, PublicKeyToken = null '" hace referencia a ' monotouch.dll '_ después de convertir la aplicación en las API unificadas, normalmente se debe a que hay un componente o un paquete NuGet en el proyecto que no se ha actualizado a la Unified API. Deberá quitar el componente o NuGet existente, actualizar a una versión que admita las API unificadas y realizar una compilación limpia.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitar compilaciones de 64 bits de aplicaciones de Xamarin. iOS

En el caso de una aplicación móvil de Xamarin. iOS que se ha convertido en el Unified API, el desarrollador todavía necesita habilitar la compilación de la aplicación para equipos de 64 bits a partir de las opciones de la aplicación. Consulte las **compilaciones de 64 bits de las aplicaciones de Xamarin. iOS** del documento de consideraciones de la [plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) para obtener instrucciones detalladas sobre cómo habilitar las compilaciones de 64 bits.

## <a name="finishing-up"></a>Finalizando

Tanto si decide usar el método automático como el manual para convertir la aplicación de Xamarin. iOS del modelo clásico al de las API unificadas, hay varias instancias que requerirán una intervención manual adicional. Consulte [las sugerencias para actualizar el código al Unified API documento para ver los](~/cross-platform/macios/unified/updating-tips.md) problemas conocidos y solucionar el problema.

## <a name="related-links"></a>Vínculos relacionados

- [Sugerencias para actualizar el código a Unified API](~/cross-platform/macios/unified/updating-tips.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de Unified API clásicas](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
