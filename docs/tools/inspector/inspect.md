---
title: Inspección de aplicaciones activas
description: En este documento se describe cómo usar el Xamarin Inspector para inspeccionar las aplicaciones. También se describen las limitaciones de la herramienta de Xamarin Inspector.
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: conceptdev
ms.author: crdun
ms.date: 06/19/2018
ms.openlocfilehash: 2ccf6966e85eddaa10b5651e1b0b48dec9203b28
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772201"
---
# <a name="inspecting-live-applications"></a>Inspección de aplicaciones activas

La inspección de aplicaciones activas está disponible para los clientes empresariales.

1. Abra cualquier [proyecto de aplicación compatible](~/tools/inspector/install.md#supported-platforms) en Visual Studio para Mac o Visual Studio.
1. Ejecute la aplicación en modo de depuración.
1. Haga clic en el botón **inspeccionar** en la barra de herramientas del IDE (en Visual Studio, el elemento de menú **inspeccionar aplicación actual...** también está disponible en el menú **herramientas** o **depurar** ).

[![](inspect-images/mac-heres-the-button.png "Haga clic en el botón inspeccionar en la barra de herramientas del IDE")](inspect-images/mac-heres-the-button.png#lightbox)

Se abrirá una nueva ventana del cliente de Xamarin Inspector, con un mensaje nuevo de REPL.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "Se abrirá una nueva ventana del cliente de Xamarin Inspector, con un mensaje de REPL actualizado")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Una vez que aparezca esta ventana, tendrá un C# símbolo del sistema interactivo que puede usar para ejecutar C# y evaluar instrucciones y expresiones. Lo que lo convierte en único es que el código se evalúa en el contexto del proceso de destino. En este caso, se muestra el código que se ejecuta en la aplicación iOS mostrada.

Cualquier cambio que se realice en el estado de la aplicación se produce realmente en el proceso de destino, por lo que C# puede usar para cambiar la aplicación en directo o puede inspeccionar el estado de la aplicación en directo.

Por ejemplo, en iOS, es posible que quiera encontrar nuestra clase de delegado UIApplication, que es nuestro principal controlador (donde se almacena una gran parte del estado de la aplicación):

```csharp
var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
del.Database.GetAllCustomers ()
...
del.Database.AddCustomer (...)
```

(Tenga en cuenta que cada envío se produce en un editor multilínea. `Shift + Enter`creará una nueva línea y `Cmd + Enter` (`Ctrl + Enter` en Windows) enviará el código para su evaluación. `Enter`envía automáticamente cuando es seguro).

Una manera más cómoda de llegar a los elementos visuales de la aplicación es mediante el botón "inspeccionar". Una vez que lo presione, puede seleccionar un elemento de la interfaz de usuario haciendo clic en la aplicación. La variable `selectedView` se asignará para que apunte al elemento real de la pantalla. En la captura de pantalla anterior, puede ver cómo se obtuvo acceso a y `selectedView.BarTintColor` después se `UISearchBar` editó en el que hemos seleccionado.

El árbol visual dinámico también es muy útil. Representa la instantánea actual de la jerarquía de vistas. Puede seleccionar las filas que se `selectedView` van a establecer en REPL y ver los valores de propiedad de la vista. En Mac, puede interactuar con una visualización en 3D seccionada de las vistas por capas. En Windows, puede editar visualmente los valores de propiedad de una vista.

## <a name="known-limitations"></a>Limitaciones conocidas

- La selección de vista solo se admite en la pantalla principal.
- La edición de la cuadrícula de propiedades no está disponible para Mac y, en Windows, está limitado a algunos tipos de datos. Use REPL para una edición más eficaz.
- Siempre y cuando la extensión AddIn/Extension esté instalada y habilitada en el IDE, se inserta código en la aplicación cada vez que se inicia en modo de depuración. Si observa un comportamiento extraño en la aplicación, intente deshabilitar o desinstalar el complemento o la extensión del inspector, reiniciar el IDE y volver a realizar la comprobación. Y registre los [errores](~/tools/inspector/install.md#reporting-bugs) para que podamos saberlo.
- Si la inspección de un elemento de la interfaz de usuario hace que cambie en cualquier caso, [háganoslo saber](~/tools/inspector/install.md#reporting-bugs), ya que esto puede indicar un error.
