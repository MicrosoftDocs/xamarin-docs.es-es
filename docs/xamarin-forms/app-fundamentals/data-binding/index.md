---
title: Enlace de datos de Xamarin.Forms
description: El enlace de datos es la técnica que consiste en vincular las propiedades de dos objetos para que los cambios en una propiedad se reflejen automáticamente en la otra propiedad. El enlace de datos es una parte integral de la arquitectura de aplicación Model-View-ViewModel (MVVM).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
ms.openlocfilehash: 9e3e602eda0d2fa78dd25905a2b6ccf3ce5a744d
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247605"
---
# <a name="xamarinforms-data-binding"></a>Enlace de datos de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_El enlace de datos es la técnica que consiste en vincular las propiedades de dos objetos para que los cambios en una propiedad se reflejen automáticamente en la otra propiedad. El enlace de datos es una parte integral de la arquitectura de aplicación Model-View-ViewModel (MVVM)._

## <a name="the-data-linking-problem"></a>El problema de vinculación de datos

Una aplicación de Xamarin.Forms consta de una o varias páginas, cada una de los cuales generalmente contiene varios objetos de interfaz de usuario denominados *vistas*. Una de las tareas principales del programa consiste en mantener estas vistas sincronizadas y realizar un seguimiento de los distintos valores o selecciones que representan. A menudo las vistas representan valores de un origen de datos subyacente y el usuario manipula estas vistas para cambiar esos datos. Cuando la vista cambia, los datos subyacentes deben reflejar ese cambio y, de forma similar, cuando los datos subyacentes cambian, ese cambio debe reflejarse en la vista.

Para controlar este trabajo correctamente, el programa debe recibir una notificación de cambios en estas vistas o en los datos subyacentes. La solución habitual consiste en definir eventos que indican cuándo se produce un cambio. Después, se puede instalar un controlador de eventos que recibe la notificación de estos cambios, y responde transfiriendo datos de un objeto a otro. Pero cuando hay muchas vistas, también es necesario que haya muchos controladores de eventos y se involucra una gran cantidad de código.

## <a name="the-data-binding-solution"></a>La solución de enlace de datos

El enlace de datos automatiza este trabajo y vuelve innecesarios los controladores de eventos. Los enlaces de datos se pueden implementar en el código o en XAML, pero son mucho más comunes en XAML, ya que así es más fácil reducir el tamaño del archivo de código subyacente. Al reemplazar el código de procedimientos en los controladores de eventos con código declarativo o marcado, se simplifica y se aclara la aplicación.

Uno de los dos objetos implicados en un enlace de datos es casi siempre un elemento que se deriva de `View` y forma parte de la interfaz visual de una página. El otro objeto puede ser:

- Otro derivado de `View`, normalmente en la misma página.
- Un objeto en un archivo de código.

En los programas de demo, como los del ejemplo [**DataBindingDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos), los enlaces de datos entre dos derivados de `View` a menudo se muestran con fines de claridad y simplicidad. Pero se pueden aplicar los mismos principios a los enlaces de datos entre un `View` y otros objetos. Cuando se compila una aplicación con la arquitectura Model-View-ViewModel (MVVM), la clase con los datos subyacentes a menudo se denomina ViewModel.

En los siguientes artículos se abordan los enlaces de datos:

## <a name="basic-bindings"></a>[Enlaces básicos](basic-bindings.md)

Conozca la diferencia entre el origen y el destino del enlace de datos y vea enlaces de datos sencillos en código y en XAML.

## <a name="binding-mode"></a>[Modo de enlace](binding-mode.md)

Descubra cómo el modo de enlace puede controlar el flujo de datos entre los dos objetos.

## <a name="string-formatting"></a>[Formato de cadena](string-formatting.md)

Use un enlace de datos para dar formato y mostrar objetos como cadenas.

## <a name="binding-path"></a>[Enlace de ruta de acceso](binding-path.md)

Profundice en la propiedad `Path` del enlace de datos para acceder a las subpropiedades y los miembros de la colección.

## <a name="binding-value-converters"></a>[Enlace de convertidores de valores](converters.md)

Use convertidores de valor de enlace para modificar valores en el enlace de datos.

## <a name="relative-bindings"></a>[Enlaces relativos](relative-bindings.md)

Use enlaces relativos para establecer el origen de enlace en relación con la posición del destino de enlace.

## <a name="binding-fallbacks"></a>[Conmutación por recuperación de enlaces](binding-fallbacks.md)

Fortalezca los enlaces de datos mediante la definición de valores de reserva para usarlos si se produce un error en el proceso de enlace.

## <a name="the-command-interface"></a>[Interfaz de comandos](commanding.md)

Implemente la propiedad `Command` con los enlaces de datos.

## <a name="compiled-bindings"></a>[Enlaces compilados](compiled-bindings.md)

Use enlaces compilados para mejorar el rendimiento del enlace de datos.

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
- [Capítulo sobre enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
