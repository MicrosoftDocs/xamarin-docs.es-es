---
title: TableLayout de Xamarin. Android
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 4175b1fa62b2bc0e4209d13934c2bdbdd1e2a085
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028745"
---
# <a name="xamarinandroid-tablelayout"></a>TableLayout de Xamarin. Android

[`TableLayout`](xref:Android.Widget.TableLayout) es una [`ViewGroup`](xref:Android.Views.ViewGroup)
que muestra los [`View`](xref:Android.Views.View) secundarios
elementos de filas y columnas.

Inicie un nuevo proyecto denominado **HelloTableLayout**.

Abra el archivo **Resources/layout/main. axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

Observe cómo es similar a la estructura de una tabla HTML. El [`TableLayout`](xref:Android.Widget.TableLayout)
el elemento es como el elemento de `<table>` HTML; [`TableRow`](xref:Android.Widget.TableRow)
es como un elemento `<tr>`; pero para las celdas, puede usar cualquier tipo de [`View`](xref:Android.Views.View) elemento. En este ejemplo, un [`TextView`](xref:Android.Widget.TextView)
se utiliza para cada celda. Entre algunas de las filas, también hay una [`View`](xref:Android.Views.View)básica, que se utiliza para dibujar una línea horizontal.

Asegúrese de que la actividad **HelloTableLayout** carga este diseño en el [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El método [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)) carga el archivo de diseño para el [`Activity`](xref:Android.App.Activity), especificado por el identificador de recurso &mdash; `Resource.Layout.Main` hace referencia al archivo de diseño **Resources/layout/main. axml** .

Ejecute la aplicación. Debería ver lo siguiente:

[![captura de pantalla de ejemplo de la aplicación TableLayout que muestra varias filas de la tabla](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)

## <a name="references"></a>Referencias

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la [licencia de atribución de Creative Commons 2,5](https://creativecommons.org/licenses/by/2.5/)._
