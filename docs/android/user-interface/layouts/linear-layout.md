---
title: LinearLayout de Xamarin. Android
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: 14e9b352a309de94a374b52141e3fd61715d8f75
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764376"
---
# <a name="xamarinandroid-linearlayout"></a>LinearLayout de Xamarin. Android

[`LinearLayout`](xref:Android.Widget.LinearLayout)es un[`ViewGroup`](xref:Android.Views.ViewGroup)
que muestra secundario[`View`](xref:Android.Views.View)
elementos en una dirección lineal, ya sea vertical u horizontalmente.

Tenga cuidado con el uso excesivo de [`LinearLayout`](xref:Android.Widget.LinearLayout).
Si comienza a anidar varios [`LinearLayout`](xref:Android.Widget.LinearLayout), puede que desee considerar la posibilidad de usar un[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
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

Inspeccione atentamente este XML. Hay una raíz[`LinearLayout`](xref:Android.Widget.LinearLayout)
que define su orientación para que sea &ndash; vertical todos [`View`](xref:Android.Views.View)los elementos secundarios (de los que tiene dos) se apilarán verticalmente. El primer elemento secundario es otro.[`LinearLayout`](xref:Android.Widget.LinearLayout)
que usa una orientación horizontal y el segundo elemento secundario es un[`LinearLayout`](xref:Android.Widget.LinearLayout)
que usa una orientación vertical. Cada una de estas [`LinearLayout`](xref:Android.Widget.LinearLayout)s anidadas contiene varios[`TextView`](xref:Android.Widget.TextView)
elementos, que están orientados entre sí de la manera definida por su elemento [`LinearLayout`](xref:Android.Widget.LinearLayout)primario.

Ahora Abra **HelloLinearLayout.CS** y asegúrese de que carga el diseño **Resources/layout/main. axml** en el[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)método) carga el archivo de diseño para [`Activity`](xref:Android.App.Activity)el, especificado por el identificador &ndash; `Resources.Layout.Main` de recurso hace referencia al archivo de diseño **Resources/layout/main. axml** .

Ejecute la aplicación. Debería ver lo siguiente:

[![Captura de pantalla de primera LinearLayout organizada horizontalmente, segundo verticalmente](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Observe cómo los atributos XML definen el comportamiento de cada vista. Pruebe a experimentar con valores diferentes para `android:layout_weight` para ver cómo se distribuye el espacio real de la pantalla en función del peso de cada elemento. Vea el documento sobre [objetos de diseño comunes](https://developer.android.com/guide/topics/ui/declaring-layout.html) para obtener más información sobre cómo[`LinearLayout`](xref:Android.Widget.LinearLayout)
controla el `android:layout_weight` atributo.

## <a name="references"></a>Referencias

- [`LinearLayout`](xref:Android.Widget.LinearLayout)
- [`TextView`](xref:Android.Widget.TextView)

_Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la [licencia de atribución de Creative Commons 2,5](http://creativecommons.org/licenses/by/2.5/)._
