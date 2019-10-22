---
title: Controles de tabla watchos en Xamarin
description: En este documento se describe cómo usar los controles de tabla de watchos en Xamarin. Describe cómo agregar una tabla, cómo agregar un controlador de filas, cómo crear y rellenar filas, cómo responder a los grifos, etc.
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 2bed40c3ac2853a5f99c2b487e909164e12e676d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70766952"
---
# <a name="watchos-table-controls-in-xamarin"></a>Controles de tabla watchos en Xamarin

El control de `WKInterfaceTable` watchos es mucho más sencillo que el homólogo de iOS, pero realiza un rol similar. Crea una lista de desplazamiento de filas que puede tener diseños personalizados y que responden a los eventos de toque.

![](table-images/table-list-sml.png "Lista de la tabla de inspección") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Agregar una tabla

Arrastre el control **tabla** hasta una escena. De forma predeterminada, tendrá el siguiente aspecto (mostrando un solo diseño de fila no especificado):

[![](table-images/add-table-sml.png "Adding a table")](table-images/add-table.png#lightbox)

Asigne un nombre a la tabla en el cuadro **nombre** del panel de **propiedades** , de modo que se pueda hacer referencia a ella en el código.

## <a name="add-a-row-controller"></a>Agregar un controlador de filas

La tabla incluye automáticamente una sola fila, representada por un controlador de filas que contiene un control de **Grupo** de forma predeterminada.

Para establecer la **clase** para el controlador de filas, seleccione la fila en el **esquema del documento** y escriba un nombre de clase en el panel de **propiedades** :

[![](table-images/add-row-controller-sml.png "Entering a class name in the Properties pad")](table-images/add-row-controller.png#lightbox)

Una vez establecida la clase para el controlador de la fila, el IDE creará un C# archivo correspondiente en el proyecto. Arrastre los controles (como etiquetas) a la fila y asígneles nombres para que se pueda hacer referencia a ellos en el código.

## <a name="create-and-populate-rows"></a>Crear y rellenar filas

`SetNumberOfRows` crea las clases de controlador de filas para cada fila, utilizando el `Identifier` para seleccionar el correcto. Si asignó un `Identifier` personalizado al controlador de filas, cambie el **valor predeterminado** en el fragmento de código siguiente al identificador que ha usado. El `RowController` *para cada fila* se crea cuando se llama a `SetNumberOfRows` y se muestra la tabla.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
    // loads row controller by identifier
```

> [!IMPORTANT]
> Las filas de la tabla no se virtualizan de la misma forma que en iOS. Intente limitar el número de filas (Apple recomienda menos de 20).

Una vez creadas las filas, debe rellenar cada celda (como `GetCell` haría en iOS). En este fragmento de código del [ejemplo WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables) se actualiza la etiqueta de cada fila.

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> El uso de `SetNumberOfRows` y, a continuación, recorrer en bucle el uso de `GetRowController` hace que toda la tabla se envíe al reloj. En las vistas posteriores de la tabla, si necesita agregar o quitar filas específicas, use `InsertRowsAt` y `RemoveRowsAt` para obtener un mejor rendimiento.

## <a name="respond-to-taps"></a>Responder a los grifos

Puede responder a la selección de filas de dos maneras diferentes:

- Implemente el método `DidSelectRow` en el controlador de la interfaz, o bien
- Cree un segue en el guion gráfico e implemente `GetContextForSegue` si desea que la selección de fila abra otra escena.

### <a name="didselectrow"></a>DidSelectRow

Para controlar mediante programación la selección de filas, implemente el método `DidSelectRow`. Para abrir una nueva escena, use `PushController` y pase el identificador de la escena y el contexto de datos que se va a usar:

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetContextForSegue

Arrastre un segue del guion gráfico desde la fila de la tabla a otra escena (mantenga presionada la tecla **control** mientras arrastra).
Asegúrese de seleccionar segue y asígnele un identificador en el panel de **propiedades** (por ejemplo, `secondLevel` en el ejemplo siguiente).

En el controlador de interfaz, implemente el método `GetContextForSegue` y devuelva el contexto de datos que se debe proporcionar a la escena presentada por segue.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Estos datos se pasan a la escena del guión gráfico de destino en su método `Awake`.

## <a name="multiple-row-types"></a>Varios tipos de filas

De forma predeterminada, el control de tabla tiene un solo tipo de fila que se puede diseñar. Para agregar más filas ' plantillas ', use el cuadro **filas** del panel de **propiedades** para crear más controladores de filas:

![](table-images/prototype-rows1.png "Setting the number of Prototype rows")

Si establece la propiedad **Rows** en **3** , se crearán marcadores de posición de fila adicionales para arrastrar controles. Para cada fila, establezca el nombre de **clase** en el panel de **propiedades** para asegurarse de que se crea la clase de controlador de filas.

![](table-images/prototype-rows2.png "The prototype rows in the designer")

Para rellenar una tabla con distintos tipos de fila, use el método `SetRowTypes` para especificar el tipo de controlador de filas que se va a utilizar para cada fila de la tabla. Use los identificadores de la fila para especificar qué controlador de filas se debe utilizar para cada fila.

El número de elementos de esta matriz debe coincidir con el número de filas que espera que estén en la tabla:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Al rellenar una tabla con varios controladores de filas, deberá realizar un seguimiento del tipo que espera a medida que rellena la interfaz de usuario:

```csharp
for (var i = 0; i < rows.Count; i++) {
    if (i == 0) {
        var elementRow = (Type1RowController)myTable.GetRowController (i);
        // populate UI controls
    } else if (i == 3) {
        var elementRow = (Type2RowController)myTable.GetRowController (i);
        // populate UI controls
    } else {
        var elementRow = (DefaultRowController)myTable.GetRowController (i);
        // populate UI controls
    }
}
```

## <a name="vertical-detail-paging"></a>Paginación de detalles vertical

watchos 3 presentó una nueva característica para las tablas: la capacidad de desplazarse por las páginas de detalles relacionadas con cada fila, sin tener que volver a la tabla y elegir otra fila. Las pantallas de detalles se pueden desplazar hacia arriba y hacia abajo, o mediante el Digital Crown.

![](table-images/table-scroll-sml.png "Ejemplo de paginación de detalles vertical") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Esta característica solo está disponible actualmente editando el guión gráfico en Xcode Interface Builder.

Para habilitar esta característica, seleccione la `WKInterfaceTable` en la superficie de diseño y marque la opción de **paginación detalle vertical** :

![](table-images/vertical-detail-paging-sml.png "Selecting the Vertical Detail Paging option")

Como se [explica en Apple,](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) la navegación por la tabla debe usar objetos segue para que funcione la característica de paginación. Vuelva a escribir el código existente que usa `PushController` para usar objetos segue en su lugar.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Apéndice: ejemplo de código del controlador de filas

El IDE creará automáticamente dos archivos de código cuando se cree un controlador de filas en el diseñador. El código de estos archivos generados se muestra a continuación como referencia.

El primero se denominará para la clase, por ejemplo **RowController.CS**, como se indica a continuación:

```csharp
using System;
using Foundation;

namespace WatchTablesExtension
{
    public partial class RowController : NSObject
    {
        public RowController ()
        {
        }
    }
}
```

El otro archivo **. Designer.CS** es una definición de clase parcial que contiene las salidas y las acciones que se crean en la superficie del diseñador, como este ejemplo con un control `WKInterfaceLabel`:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using UIKit;

namespace WatchTables.OnWatchExtension
{
    [Register ("RowController")]
    partial class RowController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        public WatchKit.WKInterfaceLabel MyLabel { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (MyLabel != null) {
                MyLabel.Dispose ();
                MyLabel = null;
            }
        }
    }
}
```

A continuación, se puede hacer referencia a las salidas y acciones declaradas en el código; sin embargo, el archivo **. Designer.CS** no debe editarse directamente.

## <a name="related-links"></a>Vínculos relacionados

- [WatchTables (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
- [WatchKitCatalog (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documento de la tabla de Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
