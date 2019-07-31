---
title: Trabajar con tablas y celdas en Xamarin. iOS
description: Este documento contiene vínculos a varias guías que describen cómo Mostrar datos con el control UITableView en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/06/2016
ms.openlocfilehash: d01814bc241dcfb7b62f40bef226ee720a96ff23
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657007"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>Trabajar con tablas y celdas en Xamarin. iOS

En esta sección se presentan las clases que se usan para crear y mostrar tablas y, a continuación, se proporcionan ejemplos de cómo usarlas en Xamarin. iOS. Tratará el uso de la apariencia predeterminada para las tablas, la personalización del diseño, la implementación de la edición y el uso del diseñador de Xamarin iOS para diseñar una tabla visualmente. A veces, la presentación es obviamente una lista de filas (por ejemplo, la aplicación de música) y otras veces es difícil reconocer el control de tabla (por ejemplo, la edición en la aplicación de contactos o una conversación en la aplicación de mensajes).

Para aquellos que trabajan en aplicaciones multiplataforma con Xamarin. Android, el control UITableView es similar a la clase ListView en Android (y la clase UITableViewSource es similar a las clases de adaptador de Android).

En estos artículos se proporciona una visión completa del trabajo con tablas, entre las que se incluyen:

-   **Partes** de la tabla: introducción y explicación de los elementos `UITableView` visuales del control. 
-   **Mostrar datos en tablas** : demostrar cómo crear y rellenar una tabla, usar distintos estilos de tabla y celda y evitar problemas de memoria mediante el reciclaje de objetos de celda. 
-   **Uso avanzado** : compilar celdas personalizadas y usar las características de edición de la clase UITableView. 
-   **Crear una tabla visualmente** : mediante el Xamarin Designer para iOS para crear una interfaz controlada por tabla con un guion gráfico. 

## <a name="contents"></a>Contenido

 [Funcionalidad de &amp; partes de tabla](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Relleno de una tabla con datos](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Personalización de la apariencia de una tabla](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Edición](~/ios/user-interface/controls/tables/editing.md)
 
 [Acciones de fila](~/ios/user-interface/controls/tables/row-action.md)

 [Crear tablas en un guión gráfico](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [Ajuste del tamaño automático de la altura de la fila](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
- [Tablas en guiones gráficos (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable)
- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [Guion gráfico de una receta de TableView](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Introducción a MonoTouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Ejemplo de TableEditing en github](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Ejemplo de TableParts en github](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Ejemplo de TableAndCellStyles en github](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Referencia de la clase UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Referencia de la clase UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
