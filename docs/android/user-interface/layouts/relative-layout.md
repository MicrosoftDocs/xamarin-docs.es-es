---
title: Uso de RelativeLayout en Xamarin. Android
description: Cómo usar RelativeLayout en una aplicación de Xamarin. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 6cac771f46242cc0475be0a7ec0d475950f4b4e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028792"
---
# <a name="xamarinandroid-relativelayout"></a>RelativeLayout de Xamarin. Android

[`RelativeLayout`](xref:Android.Widget.RelativeLayout) es una [`ViewGroup`](xref:Android.Views.ViewGroup) que muestra los elementos secundarios [`View`](xref:Android.Views.View)
elementos en posiciones relativas. La posición de un [`View`](xref:Android.Views.View) se puede especificar en relación con los elementos del mismo nivel (por ejemplo, a la izquierda o por debajo de un elemento determinado) o en posiciones relativas al [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
área (como alineada en la parte inferior izquierda del centro).

Una [`RelativeLayout`](xref:Android.Widget.RelativeLayout) es una utilidad muy eficaz para diseñar una interfaz de usuario, ya que puede eliminar [`ViewGroup`](xref:Android.Views.ViewGroup)s anidadas. Si se encuentra con varias [`LinearLayout`](xref:Android.Widget.LinearLayout) anidadas
los grupos, es posible que pueda reemplazarlos por un único [`RelativeLayout`](xref:Android.Widget.RelativeLayout).

Inicie un nuevo proyecto denominado **HelloRelativeLayout**.

Abra el archivo **Resources/layout/main. axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_background"
        android:layout_below="@id/label"/>
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/entry"
        android:layout_alignParentRight="true"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toLeftOf="@id/ok"
        android:layout_alignTop="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```

Observe cada uno de los atributos de `android:layout_*`, como `layout_below`, `layout_alignParentRight`y `layout_toLeftOf`.
Al utilizar un [`RelativeLayout`](xref:Android.Widget.RelativeLayout), puede utilizar estos atributos para describir cómo desea colocar cada [`View`](xref:Android.Views.View). Cada uno de estos atributos define un tipo diferente de posición relativa. Algunos atributos usan el identificador de recurso de un [`View`](xref:Android.Views.View) relacionado para definir su propia posición relativa. Por ejemplo, la última [`Button`](xref:Android.Widget.Button) está definida para que se encuentre a la izquierda y a la que está alineada con la parte superior de la [`View`](xref:Android.Views.View) identificada por el identificador `ok` (que es el [`Button`](xref:Android.Widget.Button)anterior).

Todos los atributos de diseño disponibles se definen en [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams).

Asegúrese de cargar este diseño en el [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El método [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*) carga el archivo de diseño para el [`Activity`](xref:Android.App.Activity), especificado por el identificador de recurso &mdash; `Resource.Layout.Main` hace referencia al archivo de diseño **Resources/layout/main. axml** .

Ejecute la aplicación. Debería ver el siguiente diseño:

[![captura de pantalla de un diseño relativo con los botones TextView, EditText y dos](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>Recursos

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la [licencia de atribución de Creative Commons 2,5](https://creativecommons.org/licenses/by/2.5/)._
