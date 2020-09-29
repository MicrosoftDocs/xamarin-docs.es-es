---
title: Conmutador Xamarin. Android
description: Cómo usar el widget switch en una aplicación de Xamarin. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: dabd4b5bd7d6dbd118314e94fe04bc4623cf11e1
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457723"
---
# <a name="xamarinandroid-switch"></a>Conmutador Xamarin. Android

El `Switch` Widget (que se muestra a continuación) permite que un usuario alterne entre dos Estados, como activado o DESactivado. El `Switch` valor predeterminado es OFF. El widget se muestra a continuación en los Estados activado y desactivado:

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

El texto que el control muestra para los Estados ON y OFF y el valor predeterminado son configurables. Por ejemplo, para que el modificador sea ON y Read NO/YES en lugar de OFF/ON, se pueden establecer los `checked` `textOn` atributos, y `textOff` en el código XML siguiente.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

## <a name="providing-a-title"></a>Proporcionar un título

El `Switch` Widget también admite la inclusión de una etiqueta de texto estableciendo el `text` atributo de la siguiente manera:

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

Cuando el `Switch` valor de un cambia, genera un `CheckedChange` evento.
Por ejemplo, en el código siguiente se captura este evento y se presenta un `Toast` widget con un mensaje basado en el `isChecked` valor de `Switch` , que se pasa al controlador de eventos como parte del `CompoundButton.CheckedChangeEventArg` argumento.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```

## <a name="related-links"></a>Vínculos relacionados

- [SwitchDemo (ejemplo)](/samples/xamarin/monodroid-samples/switchdemo)
- [Tutorial de diseño de pestañas](~/android/user-interface/layouts/tab-layout/index.md)