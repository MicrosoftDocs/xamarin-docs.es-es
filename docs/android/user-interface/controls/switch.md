---
title: Conmutador Xamarin. Android
description: Cómo usar el widget switch en una aplicación de Xamarin. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 73becb5e4424854c9be6cdc3554f6cf93507b9a9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029139"
---
# <a name="xamarinandroid-switch"></a>Conmutador Xamarin. Android

El widget `Switch` (que se muestra a continuación) permite que un usuario alterne entre dos Estados, como activado o desactivado. El valor predeterminado de `Switch` es OFF. El widget se muestra a continuación en los Estados activado y desactivado:

[![capturas de pantallas de un widget switch en los Estados OFF y ON](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>Crear un modificador

Para crear un modificador, simplemente declare un elemento `Switch` en XML como se indica a continuación:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Esto crea un modificador básico, como se muestra a continuación:

[![captura de pantalla de la aplicación de demostración que muestra un conmutador en estado desactivado](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>Cambiar valores predeterminados

El texto que el control muestra para los Estados ON y OFF y el valor predeterminado son configurables. Por ejemplo, para que el valor predeterminado del modificador sea ON y Read NO/YES en lugar de OFF/ON, podemos establecer los atributos `checked`, `textOn`y `textOff` en el siguiente código XML.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

## <a name="providing-a-title"></a>Proporcionar un título

El widget `Switch` también admite la inclusión de una etiqueta de texto estableciendo el atributo `text` de la siguiente manera:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Este marcado produce la siguiente captura de pantalla en tiempo de ejecución:

[![captura de pantalla de la aplicación de demostración con texto que precede horizontalmente al widget de conmutador](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Cuando cambia el valor de un `Switch`, genera un evento `CheckedChange`.
Por ejemplo, en el código siguiente se captura este evento y se presenta un widget `Toast` con un mensaje basado en el valor `isChecked` de `Switch`, que se pasa al controlador de eventos como parte del argumento `CompoundButton.CheckedChangeEventArg`.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```

## <a name="related-links"></a>Vínculos relacionados

- [SwitchDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/switchdemo)
- [Tutorial de diseño de pestañas](~/android/user-interface/layouts/tab-layout/index.md)
