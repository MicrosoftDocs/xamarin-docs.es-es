---
title: RatingBar de Xamarin. Android
description: Cómo agregar un widget de RatingBar a una actividad de Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 729daef1a7a003613bebc4f82067bc267dab8e00
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522848"
---
# <a name="xamarinandroid-ratingbar"></a>RatingBar de Xamarin. Android

Un RatingBar es un widget de interfaz de usuario que muestra una clasificación de una a cinco estrellas. El usuario puede seleccionar una clasificación por punteando en una estrella en esta sección, creará un widget que permite al usuario proporcionar una clasificación con el [`RatingBar`](xref:Android.Widget.RatingBar) widget.

![Ejemplo de RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>Creación de un RatingBar

1. Abra el archivo **Resource/layout/main. axml** y agregue el[`RatingBar`](xref:Android.Widget.RatingBar)
   elemento (dentro del [`LinearLayout`](xref:Android.Widget.LinearLayout)):

    ```xml
    <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
    ```
   El `android:numStars` atributo define el número de estrellas que se van a mostrar para la barra de clasificación. El `android:stepSize` atributo define la granularidad de cada estrella (por ejemplo, un valor de `0.5` permitiría clasificaciones de mitad de estrella).

2. Para hacer algo cuando se ha establecido una nueva clasificación, agregue el código siguiente al final de la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
   forma

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Esto captura el [`RatingBar`](xref:Android.Widget.RatingBar) widget del diseño con [`FindViewById`](xref:Android.App.Activity.FindViewById*) y, a continuación, establece un método de evento y, a continuación, define la acción que se realizará cuando el usuario establezca una clasificación. En este caso, un mensaje [`Toast`](xref:Android.Widget.Toast) simple muestra la nueva clasificación.

3. Ejecute la aplicación.

