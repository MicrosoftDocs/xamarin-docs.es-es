---
title: TableLayout de Xamarin. Android
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 0e09bf2364df9b672a9612829eaa7a8ba343b0e9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758397"
---
# <a name="xamarinandroid-tablelayout"></a>TableLayout de Xamarin. Android

[`TableLayout`](xref:Android.Widget.TableLayout)es un[`ViewGroup`](xref:Android.Views.ViewGroup)
que muestra secundario[`View`](xref:Android.Views.View)
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

Observe cómo es similar a la estructura de una tabla HTML. El[`TableLayout`](xref:Android.Widget.TableLayout)
el elemento es como el `<table>` elemento HTML;[`TableRow`](xref:Android.Widget.TableRow)
es como un `<tr>` elemento; pero para las celdas, puede usar cualquier tipo de [`View`](xref:Android.Views.View) elemento. En este ejemplo, un[`TextView`](xref:Android.Widget.TextView)
se utiliza para cada celda. Entre algunas de las filas, también hay un básico [`View`](xref:Android.Views.View), que se usa para dibujar una línea horizontal.

Asegúrese de que la actividad **HelloTableLayout** carga este diseño en el[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)método) carga el archivo de diseño para [`Activity`](xref:Android.App.Activity)el, especificado por el identificador &mdash; `Resource.Layout.Main` de recurso hace referencia al archivo de diseño **Resources/layout/main. axml** .

Ejecute la aplicación. Debería ver lo siguiente:

[![Captura de pantalla de ejemplo de la aplicación TableLayout que muestra varias filas de la tabla](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)

## <a name="references"></a>Referencias

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la [licencia de atribución de Creative Commons 2,5](http://creativecommons.org/licenses/by/2.5/)._
