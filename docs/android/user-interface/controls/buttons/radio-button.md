---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: c1dabfcd481dccf50075c02c54019ee27499769f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758825"
---
# <a name="radiobutton"></a>RadioButton

En esta sección, creará dos botones de radio mutuamente excluyentes (habilitando uno, se deshabilitará el otro) mediante el[`RadioGroup`](xref:Android.Widget.RadioGroup)
etc[`RadioButton`](xref:Android.Widget.RadioButton)
widgets. Cuando se presiona cualquiera de los botones de radio, se mostrará un mensaje de notificación.

Abra el archivo **Resources/layout/main. axml** y [`RadioButton`](xref:Android.Widget.RadioButton)agregue dos objetos anidados en [`RadioGroup`](xref:Android.Widget.RadioGroup) un (dentro [`LinearLayout`](xref:Android.Widget.LinearLayout)de):

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

Es importante que [`RadioButton`](xref:Android.Widget.RadioButton)los elementos estén agrupados por el [`RadioGroup`](xref:Android.Widget.RadioGroup) elemento para que no se pueda seleccionar más de uno a la vez. Esta lógica la controla automáticamente el sistema Android. Cuando uno[`RadioButton`](xref:Android.Widget.RadioButton)
dentro de un grupo seleccionado, todos los demás se anulan automáticamente.

Para hacer algo cuando se [`RadioButton`](xref:Android.Widget.RadioButton) selecciona cada uno, es necesario escribir un controlador de eventos:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

En primer lugar, el remitente que se pasa se convierte en un RadioButton.
Después, un[`Toast`](xref:Android.Widget.Toast)
mensaje muestra el texto del botón de radio seleccionado.

Ahora, en la parte inferior del[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Agregue lo siguiente:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Esto captura cada [`RadioButton`](xref:Android.Widget.RadioButton)s del diseño y agrega el controlador de eventos recién creado a cada uno de ellos.

Ejecute la aplicación.

> [!TIP]
> Si necesita cambiar el estado (por ejemplo, al cargar un guardado [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)), use el[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> establecedor de propiedad o[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> .

*Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la*
[*licencia de atribución de Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/). 
