---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 56372bb643cab545529d6a4a89c804471f3344bc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762453"
---
# <a name="togglebutton"></a>ToggleButton

En esta sección, creará un botón que se usa específicamente para alternar entre dos Estados, mediante [`ToggleButton`](xref:Android.Widget.ToggleButton) el widget. Este widget es una alternativa excelente a los botones de radio si tiene dos Estados simples que se excluyen mutuamente ("ON" y "OFF", por ejemplo). Android 4,0 (nivel de [`Switch`](xref:Android.Widget.Switch)API 14) presentó una alternativa al botón de alternancia conocido como.

Un ejemplo de un control **ToggleButton** puede verse en el par izquierdo de imágenes, mientras que el par derecho de imágenes presenta un ejemplo de un **modificador**:

![Ejemplos de conmutadores y ToggleButtons en los Estados activado y desactivado](toggle-button-images/togglebutton-switch.png)  

El control que usa una aplicación es una cuestión de estilo. Ambos widgets son funcionalmente equivalentes.

Abra el archivo **Resources/layout/main. axml** y [`ToggleButton`](xref:Android.Widget.ToggleButton) agregue el elemento ( [`LinearLayout`](xref:Android.Widget.LinearLayout)dentro del):

Para hacer algo cuando se cambia el estado, agregue el código siguiente al final de la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
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

Esto captura el [`ToggleButton`](xref:Android.Widget.ToggleButton) elemento del diseño y controla el evento de clic, que define la acción que se realizará cuando se haga clic en el botón. En este ejemplo, el método comprueba el nuevo estado del botón y, a continuación, [`Toast`](xref:Android.Widget.Toast) muestra un mensaje que indica el estado actual.

Tenga en cuenta [`ToggleButton`](xref:Android.Widget.ToggleButton) que el controla su propio cambio de estado entre Checked y unchecked, por lo que solo tiene que preguntar qué es.

Ejecute la aplicación.

> [!TIP]
> Si necesita cambiar el estado (por ejemplo, al cargar un guardado [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)), use el[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> establecedor de propiedad o[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> .

## <a name="related-links"></a>Vínculos relacionados

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](https://developer.android.com/reference/android/widget/Switch.html)
