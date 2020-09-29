---
title: Control de menú de watchos (Force Touch) en Xamarin
description: En este documento se describe cómo usar el gesto de toque forzar fuerza táctil en Xamarin. En él se describe cómo responder a una interacción forzada, cómo agregar un menú y cómo cambiar los elementos de menú.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 5ad45e1cc17a58875037f20996463f55f2b68685
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431558"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Control de menú de watchos (Force Touch) en Xamarin

El kit de inspección proporciona un gesto Force Touch que desencadena un menú cuando se implementa en una pantalla de la aplicación de inspección.

![Apple Watch mostrar un menú](menu-images/menu.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Responder a Force Touch

Si se ha `Menu` implementado un para un controlador de interfaz, cuando un usuario realiza una Force Touch se mostrará el menú. Si no se ha implementado ningún menú, la pantalla se anima brevemente y no se produce ninguna otra acción.

Los toques de fuerza no se asocian a ningún elemento determinado de la pantalla. solo se puede adjuntar un menú a un controlador de interfaz y aparecerá independientemente del lugar en que se produzca el Force Touch Presione en la pantalla.

Se pueden presentar entre una y cuatro opciones de menú.

## <a name="adding-a-menu"></a>Agregar un menú

Un `Menu` se debe agregar a un `InterfaceController` en el guión gráfico en tiempo de diseño. Cuando se arrastra un control de menú a un controlador de interfaz, no hay ninguna indicación visual en la vista previa del guion gráfico, pero el **menú** aparece en el panel del **esquema del documento** :

![Editar un menú en tiempo de diseño](menu-images/menu-action.png)

Se pueden agregar hasta cuatro elementos de menú al control de menú. Se pueden configurar en el panel de **propiedades** . Se pueden establecer los siguientes atributos:

- Título y
- Imagen personalizada, o
- Una imagen del sistema: aceptar, agregar, bloquear, rechazar, información, quizás, más, silenciar, pausar, reproducir, repetir, reanudar, compartir, ordenar aleatoriamente, orador, papelera.

Cree un `Action` control seleccionando la sección de **eventos** del panel de **propiedades** y escribiendo el nombre del método de acción. Se creará un método parcial en el código, que se puede implementar en la clase de controlador de interfaz, de la siguiente manera:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Custom Images

De forma similar a las imágenes de pestañas en iOS, las imágenes de elementos de menú requieren un patrón opaco con un canal alfa que permita que el fondo se muestre a través de.

Debe agregar las imágenes que se usan para el menú al proyecto de aplicación de inspección (no al proyecto de extensión de aplicación de inspección) para obtener el mejor rendimiento.

## <a name="changing-the-menu-items"></a>Cambiar los elementos de menú

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Agregar en tiempo de ejecución

No se puede hacer `Menu` que se agregue un a un controlador de interfaz en tiempo de ejecución, aunque la colección de `MenuItem` s se *puede* modificar mediante programación.
Use el `AddMenuItem` método como se muestra a continuación:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

La API del kit de inspección de Xamarin. iOS actualmente requiere `selector` para el `AdMenuItem` método, que se debe declarar de la siguiente manera:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Quitar en tiempo de ejecución

`ClearAllMenuItems`Se puede llamar al método para quitar todos los elementos de menú *agregados mediante programación* .

Los elementos de menú configurados en el guión gráfico no se pueden borrar.

## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documento de menú de Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)