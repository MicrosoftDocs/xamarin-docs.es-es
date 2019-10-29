---
title: LinearLayout de Xamarin. Android
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/07/2018
ms.openlocfilehash: b1cff01c66ae2581a68286e62bd8c8c5fb7f9d72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028959"
---
# <a name="xamarinandroid-linearlayout"></a>LinearLayout de Xamarin. Android

[`LinearLayout`](xref:Android.Widget.LinearLayout) es una [`ViewGroup`](xref:Android.Views.ViewGroup)
que muestra los [`View`](xref:Android.Views.View) secundarios
elementos en una dirección lineal, ya sea vertical u horizontalmente.

Debe tener cuidado al usar el [`LinearLayout`](xref:Android.Widget.LinearLayout).
Si comienza a anidar varios [`LinearLayout`](xref:Android.Widget.LinearLayout)s, puede que desee considerar la posibilidad de usar una [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
en lugar.

Inicie un nuevo proyecto denominado **HelloLinearLayout**.

Abra **Resources/layout/main. axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "match_parent"
      android:layout_height=    "match_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

Inspeccione atentamente este XML. Hay una [`LinearLayout`](xref:Android.Widget.LinearLayout) raíz
que define su orientación para que sea vertical &ndash; todos los [`View`](xref:Android.Views.View)secundarios (de los que tiene dos) se apilarán verticalmente. El primer elemento secundario es otro [`LinearLayout`](xref:Android.Widget.LinearLayout)
que usa una orientación horizontal y el segundo elemento secundario es un [`LinearLayout`](xref:Android.Widget.LinearLayout)
que usa una orientación vertical. Cada una de estas [`LinearLayout`](xref:Android.Widget.LinearLayout)anidadas contiene varios [`TextView`](xref:Android.Widget.TextView)
elementos, que están orientados entre sí de la forma definida por su [`LinearLayout`](xref:Android.Widget.LinearLayout)primaria.

Ahora Abra **HelloLinearLayout.CS** y asegúrese de que carga el diseño **Resources/layout/main. axml** en el [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El método [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)) carga el archivo de diseño para el [`Activity`](xref:Android.App.Activity), especificado por el identificador de recurso &ndash; `Resources.Layout.Main` hace referencia al archivo de diseño **Resources/layout/main. axml** .

Ejecute la aplicación. Debería ver lo siguiente:

[![captura de pantalla de la aplicación First LinearLayout organizada horizontalmente, segundo verticalmente](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Observe cómo los atributos XML definen el comportamiento de cada vista. Pruebe a experimentar con valores diferentes para `android:layout_weight` para ver cómo se distribuye el espacio real de la pantalla en función del peso de cada elemento. Vea el documento [objetos de diseño comunes](https://developer.android.com/guide/topics/ui/declaring-layout.html) para obtener más información sobre cómo [`LinearLayout`](xref:Android.Widget.LinearLayout)
controla el atributo de `android:layout_weight`.

## <a name="references"></a>Referencias

- [`LinearLayout`](xref:Android.Widget.LinearLayout)
- [`TextView`](xref:Android.Widget.TextView)

_Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la [licencia de atribución de Creative Commons 2,5](https://creativecommons.org/licenses/by/2.5/)._
