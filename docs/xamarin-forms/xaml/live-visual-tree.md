---
title: Xamarin.Forms Árbol visual dinámico
description: Vea la jerarquía de la interfaz de usuario en tiempo de ejecución de la aplicación durante la depuración.
ms.prod: xamarin
ms.assetid: 29c45b85-21b1-40ab-941f-4d9e57770c46
ms.technology: xamarin-forms
author: BretJohnson
ms.author: bretjohn
ms.date: 03/25/2021
no-loc:
- Xamarin.Forms
ms.openlocfilehash: 66ee5c28d3c6f35bec8e30e409a38430d41d8c68
ms.sourcegitcommit: 3aa9bdcaaedca74ab5175cb2338a1df122300243
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751630"
---
# <a name="xamarinforms-live-visual-tree"></a>Xamarin.Forms árbol visual dinámico

Puede recibir una vista en tiempo real del código XAML en ejecución con el **árbol visual dinámico**. Muestra una vista de árbol de los elementos de la interfaz de usuario de la aplicación en ejecución Xamarin.Forms .

## <a name="requirements"></a>Requisitos

* Use Xamarin.Forms 5,0 o una versión más reciente.
* *Los cambios solo* se habilitan para la recarga activa (está habilitada de forma predeterminada).

## <a name="usage"></a>Uso

Con los requisitos cumplidos, depurar la aplicación y la ventana del árbol visual dinámico mostrarán la jerarquía de la interfaz de usuario en tiempo de ejecución de la aplicación.

  * **Windows**: de forma predeterminada, aparece en la parte izquierda del IDE. Si no lo ve, use **Depurar > Windows > árbol visual activo** para mostrarlo.
  * **Mac**: de forma predeterminada, aparece en el derecho del IDE. Si no lo ve, use **ver > depurar Windows > árbol visual activo** para mostrarlo.

Use la vista de árbol para inspeccionar la jerarquía de la interfaz de usuario en tiempo de ejecución de la aplicación, expandir o contraer los nodos para centrarse en determinadas partes de la interfaz de usuario.

## <a name="live-visual-tree-toolbar"></a>Barra de herramientas del árbol visual dinámico 

La vista de los elementos XAML se simplifica de forma predeterminada mediante la característica **solo mi XAML** . Alterne el botón **Mostrar solo mi XAML** , situado más a la derecha en la barra de herramientas del árbol visual activo, para mostrar todos los elementos de la interfaz de usuario. Si quiere, puede [deshabilitar esta opción](/visualstudio/debugger/general-debugging-options-dialog-box.md) en opciones para mostrar siempre todos los elementos XAML.

> [!NOTE]
> Visual Studio para Mac no admite actualmente la característica **solo mi XAML** .

La estructura de XAML tiene muchos elementos que probablemente no le interesen directamente y, si no conoce bien el código, es posible que tenga mucho tiempo navegando por el árbol para encontrar lo que está buscando. Por lo tanto, el **árbol visual dinámico** tiene varios enfoques que permiten usar la interfaz de usuario de la aplicación para ayudarle a encontrar el elemento que desea examinar.

**Seleccione el elemento en la aplicación en ejecución** (actualmente solo se admite para aplicaciones para UWP). Puede habilitar este modo al seleccionar el botón situado más a la izquierda de la barra de herramientas de **Live Visual Tree**. Con este modo en, puede seleccionar un elemento de la interfaz de usuario en la aplicación y el **árbol visual dinámico** se actualiza automáticamente para mostrar el nodo en el árbol correspondiente a ese elemento y sus propiedades. 

**Mostrar adornos de diseño en la aplicación en ejecución** (actualmente solo se admite para aplicaciones para UWP). Puede habilitar este modo al seleccionar el botón que está justo a la derecha del botón Habilitar selección. Cuando la opción **Mostrar adornos de diseño** está activada, la ventana de la aplicación muestra líneas horizontales y verticales a lo largo de los límites del objeto seleccionado para que pueda ver lo que alinea, así como rectángulos que muestran los márgenes.

**Obtener una vista previa de la selección**. Puede habilitar este modo seleccionando el tercer botón de la izquierda en la barra de herramientas de Live Visual Tree. Este modo muestra el XAML donde se ha declarado el elemento, siempre y cuando tenga acceso al código fuente de la aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Escribir y depurar código XAML en ejecución con recarga activa de XAML](hot-reload.md)
