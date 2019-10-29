---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: fe444d255beb9c08b4b5bcf5de36a8740e503b55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029297"
---
# <a name="togglebutton"></a>ToggleButton

En esta sección, creará un botón que se usa específicamente para alternar entre dos Estados, mediante el widget [`ToggleButton`](xref:Android.Widget.ToggleButton) . Este widget es una alternativa excelente a los botones de radio si tiene dos Estados simples que se excluyen mutuamente ("ON" y "OFF", por ejemplo). Android 4,0 (nivel de API 14) presentó una alternativa al botón de alternancia conocido como [`Switch`](xref:Android.Widget.Switch).

Un ejemplo de un control **ToggleButton** puede verse en el par izquierdo de imágenes, mientras que el par derecho de imágenes presenta un ejemplo de un **modificador**:

![Ejemplos de conmutadores y ToggleButtons en los Estados activado y desactivado](toggle-button-images/togglebutton-switch.png)  

El control que usa una aplicación es una cuestión de estilo. Ambos widgets son funcionalmente equivalentes.

Abra el archivo **Resources/layout/main. axml** y agregue el elemento [`ToggleButton`](xref:Android.Widget.ToggleButton) (dentro del [`LinearLayout`](xref:Android.Widget.LinearLayout)):

Para hacer algo cuando se cambia el estado, agregue el código siguiente al final de la [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
ToggleButton togglebutton = FindViewById<ToggleButton>(Resource.Id.togglebutton);

togglebutton.Click += (o, e) => {
    // Perform action on clicks
    if (togglebutton.Checked)
        Toast.MakeText(this, "Checked", ToastLength.Short).Show ();
    else
        Toast.MakeText(this, "Not checked", ToastLength.Short).Show ();
};
```

Esto captura el elemento [`ToggleButton`](xref:Android.Widget.ToggleButton) del diseño y controla el evento click, que define la acción que se realizará cuando se haga clic en el botón. En este ejemplo, el método comprueba el nuevo estado del botón y, a continuación, muestra un mensaje de [`Toast`](xref:Android.Widget.Toast) que indica el estado actual.

Tenga en cuenta que el [`ToggleButton`](xref:Android.Widget.ToggleButton) controla su propio cambio de estado entre Checked y unchecked, por lo que solo tiene que preguntar qué es.

Ejecute la aplicación.

> [!TIP]
> Si necesita cambiar el estado (por ejemplo, al cargar un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)guardado), use el [`Checked`](xref:Android.Widget.CompoundButton.Checked)
> [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) o establecedor de propiedad
> .

## <a name="related-links"></a>Vínculos relacionados

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](https://developer.android.com/reference/android/widget/Switch.html)
