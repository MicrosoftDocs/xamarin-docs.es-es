---
title: Patrones y expresiones comunes en Xamarin. Mac
description: En este documento se describen los patrones de diseño comunes que se usan al compilar aplicaciones de Xamarin. Mac. Se describe el patrón de controlador de vista de modelos, los patrones de origen de datos y delegado, y los protocolos.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 06/17/2016
ms.openlocfilehash: b508cc12f468e5b9dfef91718585f61bfd633816
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030056"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Patrones y expresiones comunes en Xamarin. Mac

En las API de Apple que C#se exponen a través de, ciertas expresiones y patrones se vuelven a poner en marcha una y otra vez. Si tiene experiencia con la programación con Xamarin. iOS, puede que le resulte familiar. La documentación a menudo hará referencia a estos patrones y expresiones varias veces, por lo que tener una comprensión sólida de ellos le ayudará a tener sentido la documentación que encuentre.

## <a name="mvc---model-view-controller"></a>MVC: controlador de vistas de modelo

El controlador de vista de modelo, o MVC para abreviar, es un patrón muy común que se encuentra en el chocolate. Una explicación detallada queda fuera del ámbito de este documento, pero en breve es una forma de estructurar la aplicación en componentes:

- Los objetos de **modelo** representan los datos subyacentes que se están viendo y manipulando (como las direcciones de una libreta de direcciones)
- Los objetos de **vista** controlan el dibujo de un objeto determinado en pantalla y controlan la interacción del usuario (un campo de texto que muestra la dirección en la pantalla)
- Los objetos de **controlador** controlan la interacción entre el modelo y la vista. Los cambios del modelo de pulsación "subir" para actualizar la vista y enviar los cambios "inactivos" desde la vista cuando los usuarios realizan cambios en la interfaz de usuario.

Si está familiarizado con MVVM (Model View ViewModel) de otras bibliotecas, como WPF, el controlador actúa de forma similar a ViewModel, pero suele estar enlazado más estrechamente a los elementos de la interfaz de usuario específicos.

Puede encontrar más detalles aquí:

- [Learning MVC en el sitio web de Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Controlador de vista de modelo en Objective-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Trabajar con Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Origen de datos/delegado/subclase

Otro patrón muy común en cacao trata de proporcionar datos a los elementos de la interfaz de usuario y reaccionar a las interacciones de los usuarios. Mediante `NSTableView` como ejemplo, debe proporcionar de algún modo los datos de cada fila, algún conjunto de elementos de la interfaz de usuario que representen esa fila, algún conjunto de comportamientos para reaccionar a las interacciones de los usuarios y, posiblemente, alguna cantidad de personalización. Los patrones de origen de datos y de delegado permiten controlar la mayoría de los casos sin tener que recurrir a la subclase `NSTableView` usted mismo.

- A la propiedad `DataSource` se le asigna una instancia de una subclase personalizada de `NSTableViewDataSource` a la que se llama para rellenar la tabla con datos (a través de `GetRowCount` y `GetObjectValue`).

- A la propiedad `Delegate` se le asigna una instancia de una subclase personalizada de `NSTableViewDelegate` que proporciona la vista para un objeto de modelo determinado (a través de `GetViewForItem`) y controla las interacciones de la interfaz de usuario (a través de `DidClickTableColumn`, `MouseDownInHeaderOfTableColumn`, etc.).

En algunos casos, querrá personalizar un control de una forma más allá de los enlaces proporcionados en el origen de datos o delegado, y puede crear subclases de la vista directamente. Sin embargo, tenga cuidado, en muchos casos, si se invalida el comportamiento predeterminado, se le pedirá que controle todo ese comportamiento personalmente (la personalización del comportamiento de la selección puede requerir que implemente todos los comportamientos de selección usted mismo).

En Xamarin. iOS, algunas API, como `UITableView` se han ampliado con una propiedad que implementa tanto el delegado como el origen de datos (`UITableViewSource`). Esto soluciona la limitación común de que una sola C# clase solo puede tener una clase base, y nuestra exposición de protocolos se realiza a través de clases base.

Para más información sobre cómo trabajar con vistas de tabla en una aplicación de Xamarin. Mac, consulte nuestra documentación de la [vista de tabla](~/mac/user-interface/table-view.md) .

## <a name="protocols"></a>Protocolos

Los protocolos de Objective-C se pueden comparar con las C#interfaces de y, en muchos casos, se usan en situaciones similares. Por ejemplo, en el `NSTableView` ejemplo anterior, tanto el delegado como el origen de datos son protocolos. Xamarin. Mac los expone como clases base con métodos virtuales que se pueden invalidar. La principal diferencia entre C# las interfaces y los protocolos de Objective-C es que algunos métodos de un protocolo pueden ser opcionales para implementar. Tendrá que consultar la documentación o la definición de una API para determinar qué es opcional.

Para más información, consulte la documentación sobre [delegados, protocolos y eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [Delegados, protocolos y eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Controlador de vista de modelo](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
