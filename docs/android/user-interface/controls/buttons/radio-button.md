---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 9f51adcbd1accb4f780318cc0853e612ed8e5bed
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029301"
---
# <a name="radiobutton"></a>RadioButton

En esta sección, creará dos botones de radio mutuamente exclusivos (habilitando uno, se deshabilitará el otro) mediante el [`RadioGroup`](xref:Android.Widget.RadioGroup)
y [`RadioButton`](xref:Android.Widget.RadioButton)
widgets. Cuando se presiona cualquiera de los botones de radio, se mostrará un mensaje de notificación.

Abra el archivo **Resources/layout/main. axml** y agregue dos [`RadioButton`](xref:Android.Widget.RadioButton)s, anidados en un [`RadioGroup`](xref:Android.Widget.RadioGroup) (dentro del [`LinearLayout`](xref:Android.Widget.LinearLayout)):

```xml
<RadioGroup
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">
  <RadioButton android:id="@+id/radio_red"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Red" />
  <RadioButton android:id="@+id/radio_blue"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Blue" />
</RadioGroup>
```

Es importante que el elemento [`RadioGroup`](xref:Android.Widget.RadioGroup) agrupe los [`RadioButton`](xref:Android.Widget.RadioButton)s para que no se pueda seleccionar más de uno a la vez. Esta lógica la controla automáticamente el sistema Android. Cuando una [`RadioButton`](xref:Android.Widget.RadioButton)
dentro de un grupo seleccionado, todos los demás se anulan automáticamente.

Para hacer algo cuando se selecciona cada [`RadioButton`](xref:Android.Widget.RadioButton) , es necesario escribir un controlador de eventos:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

En primer lugar, el remitente que se pasa se convierte en un RadioButton.
Después, un [`Toast`](xref:Android.Widget.Toast)
mensaje muestra el texto del botón de radio seleccionado.

Ahora, en la parte inferior del [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Agregue lo siguiente:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Esto captura cada uno de los [`RadioButton`](xref:Android.Widget.RadioButton)s del diseño y agrega el controlador de eventos recién creado a cada uno de ellos.

Ejecute la aplicación.

> [!TIP]
> Si necesita cambiar el estado (por ejemplo, al cargar un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)guardado), use el [`Checked`](xref:Android.Widget.CompoundButton.Checked)
> [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) o establecedor de propiedad
> .

*Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la licencia de* [*atribución
Creative Commons 2,5*](https://creativecommons.org/licenses/by/2.5/). 
