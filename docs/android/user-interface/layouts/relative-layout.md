---
title: Uso de RelativeLayout en Xamarin. Android
description: Cómo usar RelativeLayout en una aplicación de Xamarin. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af74ae3c7c87f501bff519bcfa361264205ca3f1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522380"
---
# <a name="xamarinandroid-relativelayout"></a>RelativeLayout de Xamarin. Android

[`RelativeLayout`](xref:Android.Widget.RelativeLayout)es un [`ViewGroup`](xref:Android.Views.ViewGroup) que muestra secundario[`View`](xref:Android.Views.View)
elementos en posiciones relativas. La posición de se [`View`](xref:Android.Views.View) puede especificar en relación con los elementos del mismo nivel (por ejemplo, a la izquierda o por debajo de un elemento determinado) o en posiciones relativas al[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
área (como alineada en la parte inferior izquierda del centro).

Una [`RelativeLayout`](xref:Android.Widget.RelativeLayout) es una utilidad muy eficaz para diseñar una interfaz de usuario, ya que puede eliminar [`ViewGroup`](xref:Android.Views.ViewGroup)anidadas. Si se encuentra con varios[`LinearLayout`](xref:Android.Widget.LinearLayout)
los grupos, es posible que pueda reemplazarlos por un único [`RelativeLayout`](xref:Android.Widget.RelativeLayout).

Inicie un nuevo proyecto denominado **HelloRelativeLayout**.

Abra el archivo Resources **/layout/main. axml** e inserte lo siguiente:

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

Observe cada uno de `android:layout_*` los atributos, `layout_below`como, `layout_alignParentRight`y `layout_toLeftOf`.
Al usar [`RelativeLayout`](xref:Android.Widget.RelativeLayout), puede utilizar estos atributos para describir cómo desea colocar cada uno [`View`](xref:Android.Views.View)de ellos. Cada uno de estos atributos define un tipo diferente de posición relativa. Algunos atributos usan el identificador de recurso de un [`View`](xref:Android.Views.View) elemento del mismo nivel para definir su propia posición relativa. Por ejemplo, la última [`Button`](xref:Android.Widget.Button) se define para que se encuentre a la izquierda y alineada con la parte superior de la [`View`](xref:Android.Views.View) identificada por el identificador `ok` (que es el anterior [`Button`](xref:Android.Widget.Button)).

Todos los atributos de diseño disponibles se definen en [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams).

Asegúrese de cargar este diseño en el[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*) método carga el archivo de diseño para [`Activity`](xref:Android.App.Activity)el, especificado por el identificador &mdash; `Resource.Layout.Main` de recurso hace referencia al archivo de diseño Resources **/layout/main. axml** .

Ejecute la aplicación. Debería ver el siguiente diseño:

[![Captura de pantalla de un diseño relativo con los botones TextView, EditText y dos](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>Recursos

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la licencia de atribución de [Creative Commons 2,5](http://creativecommons.org/licenses/by/2.5/)._
