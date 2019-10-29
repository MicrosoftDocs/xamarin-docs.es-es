---
title: Editar texto
description: Cómo usar el widget EditText para aceptar los datos proporcionados por el usuario.
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/09/2018
ms.openlocfilehash: 62859e19c533d7b200833996b44a65cb275805c6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029250"
---
# <a name="xamarinandroid-edit-text"></a>Editar texto de Xamarin. Android

En esta sección, usará el widget [EditText](xref:Android.Widget.EditText) para crear un campo de texto para los datos proporcionados por el usuario. Una vez que el texto se ha escrito en el campo, la tecla **entrar** mostrará el texto en un mensaje del sistema.

Abra **Resources/layout/activity_main. axml** y agregue el elemento [EditText](xref:Android.Widget.EditText) a un diseño contenedor. En el ejemplo siguiente, **activity_main. axml** tiene una `EditText` que se ha agregado a un `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <EditText
        android:id="@+id/edittext"
        android:layout_width="match_parent"
        android:imeOptions="actionGo"
        android:inputType="text"
        android:layout_height="wrap_content" />
</LinearLayout>
```

En este ejemplo de código, el atributo `EditText` `android:imeOptions` se establece en `actionGo`. Esta configuración cambia la acción de [listo](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) predeterminada a la acción [Go](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) para que al pulsar la tecla **entrar** se desencadene el controlador de entrada `KeyPress`.
(Normalmente, `actionGo` se usa para que la tecla **entrar** lleve al usuario al destino de una dirección URL que se escribe).

Para controlar la entrada de texto del usuario, agregue el código siguiente al final del método [alcrear](xref:Android.App.Activity.OnCreate*) en **MainActivity.CS**:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
    e.Handled = false;
    if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) 
    {
        Toast.MakeText(this, edittext.Text, ToastLength.Short).Show();
        e.Handled = true;
    }
};
```

Además, agregue la siguiente instrucción `using` a la parte superior de **MainActivity.CS** si aún no está presente:

```csharp
using Android.Views;
```

En este ejemplo de código se infla el elemento [EditText](xref:Android.Widget.EditText) del diseño y se agrega un controlador [KeyPress](xref:Android.Views.View.KeyPress) que define la acción que se debe realizar cuando se presiona una tecla mientras el widget tiene el foco. En este caso, el método se define para que escuche la tecla **entrar** (al puntear) y, a continuación, mostrar un mensaje del [sistema](xref:Android.Widget.Toast) con el texto que se ha escrito. Tenga en cuenta que la propiedad [controlada](xref:Android.Views.View.KeyEventArgs.Handled) siempre debe ser `true` si se ha controlado el evento. Esto es necesario para evitar que el evento se propague (lo que daría lugar a un retorno de carro en el campo de texto).

Ejecute la aplicación y escriba texto en el campo de texto. Al presionar la tecla **entrar** , la notificación del sistema se mostrará como se muestra a la derecha:

[![ejemplos de cómo escribir texto en EditText](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*Algunas partes de esta página son modificaciones basadas en el trabajo creado y* [*compartido por el proyecto de código abierto de Android*](https://code.google.com/policies.html) *y que se usan según los términos descritos en la licencia de* [*atribución de Creative Commons 2,5*](https://creativecommons.org/licenses/by/2.5/) *. Este tutorial se basa en el tutorial de* [*formularios de Android*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*

## <a name="related-links"></a>Vínculos relacionados

- [EditTextSample](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-edittextsample)
