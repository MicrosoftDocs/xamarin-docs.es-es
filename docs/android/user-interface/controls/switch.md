---
title: Conmutador Xamarin. Android
description: Cómo usar el widget switch en una aplicación de Xamarin. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: 7fe4ffc733a71ed0f372da8288c7f1b63a961442
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642569"
---
# <a name="xamarinandroid-switch"></a>Conmutador Xamarin. Android

El `Switch` widget (que se muestra a continuación) permite que un usuario alterne entre dos Estados, como activado o desactivado. El `Switch` valor predeterminado es OFF. El widget se muestra a continuación en los Estados activado y desactivado:

[![Capturas de pantallas de un widget de conmutador en Estados desactivado y activado](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>Crear un modificador

Para crear un modificador, simplemente declare un `Switch` elemento en XML como se indica a continuación:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Esto crea un modificador básico, como se muestra a continuación:

[![Captura de pantalla de la aplicación de demostración que muestra un conmutador en estado desactivado](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>Cambiar valores predeterminados

El texto que el control muestra para los Estados ON y OFF y el valor predeterminado son configurables. Por ejemplo, para que el modificador sea on y Read no/Yes en lugar de OFF/ON, se pueden establecer los `checked`atributos `textOn`, y `textOff` en el código XML siguiente.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>Proporcionar un título

El `Switch` widget también admite la inclusión de una etiqueta de texto `text` estableciendo el atributo de la siguiente manera:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Este marcado produce la siguiente captura de pantalla en tiempo de ejecución:

[![Captura de pantalla de la aplicación de demostración con texto delante del widget de conmutador](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Cuando el `Switch`valor de un cambia, genera un `CheckedChange` evento.
Por ejemplo, en el código siguiente se captura este evento y se presenta `Toast` un widget con un mensaje basado en `isChecked` el valor `Switch`de, que se pasa `CompoundButton.CheckedChangeEventArg` al controlador de eventos como parte del argumento.

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
