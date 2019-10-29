---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 06908ad8993eb6d47476006b23865fa1c7fe694f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029344"
---
# <a name="checkbox"></a>CheckBox

En esta sección, creará una casilla para seleccionar elementos, mediante el widget [`CheckBox`](xref:Android.Widget.CheckBox) . Cuando se presiona la casilla, un mensaje del sistema indicará el estado actual de la casilla.

Abra el archivo **Resources/layout/main. axml** y agregue el elemento [`CheckBox`](xref:Android.Widget.CheckBox) (dentro del [`LinearLayout`](xref:Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Para hacer algo cuando se cambia el estado, agregue el código siguiente al final del método [`OnCreate()`](xref:Android.App.Activity.OnCreate*) :

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Esto captura el elemento [`CheckBox`](xref:Android.Widget.CheckBox) del diseño y, a continuación, controla el evento click, que define la acción que se realizará cuando se haga clic en la casilla. Al hacer clic en él, se llama a la propiedad [`Checked`](xref:Android.Widget.CompoundButton.Checked) para comprobar el nuevo estado de la casilla. Si se ha activado, un [`Toast`](xref:Android.Widget.Toast) muestra el mensaje "seleccionado"; en caso contrario, muestra "no seleccionado". El [`CheckBox`](xref:Android.Widget.CheckBox) controla sus propios cambios de estado, por lo que solo necesita consultar el estado actual.

Ejecútelo.

> [!TIP]
> Si necesita cambiar el estado por su cuenta (por ejemplo, al cargar una [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)guardada, utilice el método setter de la propiedad [`Checked`](xref:Android.Widget.CompoundButton.Checked) o [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) .

*Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la licencia de* [*atribución de Creative Commons 2,5*](https://creativecommons.org/licenses/by/2.5/).
