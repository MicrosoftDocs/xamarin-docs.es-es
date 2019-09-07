---
title: Nuevo sistema de recuento de referencias en Xamarin. iOS
description: En este documento se describe el sistema de recuento de referencias mejorado de Xamarin, que está habilitado de forma predeterminada en todas las aplicaciones de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 56e35662230a3c529eb48a0ae742c2b063c1ac10
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753344"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>Nuevo sistema de recuento de referencias en Xamarin. iOS

Xamarin. iOS 9.2.1 presentó el sistema de recuento de referencias mejorado a todas las aplicaciones de forma predeterminada. Se puede usar para eliminar muchos problemas de memoria que resultaban difíciles de seguir y corregir en versiones anteriores de Xamarin. iOS.

## <a name="enabling-the-new-reference-counting-system"></a>Habilitar el nuevo sistema de recuento de referencias

A partir de Xamarin 9.2.1, el nuevo sistema de recuento de referencias está habilitado para **todas** las aplicaciones de forma predeterminada.

Si está desarrollando una aplicación existente, puede comprobar el archivo. csproj para asegurarse de que todas las apariciones de `MtouchUseRefCounting` se establecen en `true`, como se indica a continuación:

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

Si se establece en `false` , la aplicación no utilizará las nuevas herramientas.

### <a name="using-older-versions-of-xamarin"></a>Usar versiones anteriores de Xamarin

Xamarin. iOS 7.2.1 y versiones posteriores ofrece una vista previa mejorada de nuestro nuevo sistema de recuento de referencias.

**Classic API:**

Para habilitar este nuevo sistema de recuento de referencias, active la casilla **usar la extensión de recuento de referencias** que se encuentra en la pestaña **Opciones avanzadas** de las **Opciones de compilación de iOS**del proyecto, como se muestra a continuación: 

[![](newrefcount-images/image1.png "Habilitar el nuevo sistema de recuento de referencias")](newrefcount-images/image1.png#lightbox)

Tenga en cuenta que estas opciones se han quitado en las versiones más recientes de Visual Studio para Mac.

 **[Unified API:](~/cross-platform/macios/unified/index.md)**

 La nueva extensión de recuento de referencias es necesaria para el Unified API y debe estar habilitada de forma predeterminada. Es posible que las versiones anteriores del IDE no tengan este valor comprobado automáticamente y que tenga que realizar una comprobación por él mismo.

> [!IMPORTANT]
> Una versión anterior de esta característica se ha solucionado desde MonoTouch 5,2, pero solo estaba disponible para **SGen** como vista previa experimental. Esta nueva versión mejorada también está disponible para el recolector de elementos no utilizados de **Boehm** .

Históricamente, había dos tipos de objetos administrados por Xamarin. iOS: aquellos que simplemente eran un contenedor alrededor de un objeto nativo (objetos del mismo nivel) y los que extenderon o incorporaban nuevas funcionalidades (objetos derivados), normalmente manteniendo un estado adicional en memoria. Anteriormente era posible que se pudiera aumentar un objeto del mismo nivel con el estado (por ejemplo, C# agregando un controlador de eventos), pero se permite que el objeto pase sin referencia y se recopile. Esto podría provocar un bloqueo más adelante (por ejemplo, si el tiempo de ejecución de Objective-C vuelve a llamar al objeto administrado).

El nuevo sistema actualiza automáticamente los objetos del mismo nivel en objetos administrados por el tiempo de ejecución cuando almacenan información adicional.

Esto soluciona varios bloqueos que se produjeron en situaciones como esta:

```csharp
class MyTableSource : UITableViewSource {
   public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath) {
        var cell = tableView.DequeueReusableCell ("myId");
        if (cell != null)
                return cell;

        cell = new UITableViewCell (UITableViewCellStyle.Default, "myId");
        var txt = new UITextField ();
        txt.TouchDown += delegate { Console.WriteLine ("...."); };
        cell.ContentView.AddSubview (txt);
        return cell;
   }
}
```

Sin la extensión de recuento de referencias, este `cell` código se bloqueó porque se `TouchDown` vuelve a recopilar y, por tanto, su delegado, que se traducirá en un puntero pendiente.

La extensión de recuento de referencias garantiza que el objeto administrado permanece activo y evita su colección, siempre que el código nativo retenga el objeto nativo.

El nuevo sistema también elimina la necesidad de la mayoría de los campos de respaldo privados usados en los enlaces, que es el método predeterminado para mantener *la* instancia activa. El vinculador administrado es lo suficientemente inteligente como para quitar todos los campos *innecesarios* de las aplicaciones que usan la nueva extensión de recuento de referencias.

Esto significa que cada instancia de objeto administrado utiliza menos memoria que antes. También resuelve un problema relacionado en el que algunos campos de respaldo contendrían referencias que ya no eran necesarias en el tiempo de ejecución de Objective-C, lo que dificultaba la recuperación de memoria.
