---
title: Botón personalizado
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: d85c67cf18c61af04cf12bfab58a5b516d380f62
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029352"
---
# <a name="custom-button"></a>Botón personalizado

En esta sección, creará un botón con una imagen personalizada en lugar de texto, con el widget de [`Button`](xref:Android.Widget.Button) y un archivo XML que define tres imágenes diferentes que se usarán para los distintos Estados de botón. Cuando se presiona el botón, se muestra un mensaje breve.

Haga clic con el botón derecho y descargue las tres imágenes siguientes y, a continuación, cópielos en el directorio **Resources/drawable** del proyecto. Se usarán para los distintos Estados de botón.

 [![icono verde de Android para el estado normal](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [![icono de Android naranja para el estado de foco](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [![icono de Android amarillo para el estado presionado](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

Cree un nuevo archivo en el directorio **Resources/Drawing** denominado **android_button. XML**. Inserte el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/android_pressed"
          android:state_pressed="true" />
    <item android:drawable="@drawable/android_focused"
          android:state_focused="true" />
    <item android:drawable="@drawable/android_normal" />
</selector>
```

Define un único recurso que se va a dibujar, que cambiará su imagen en función del estado actual del botón. La primera `<item>` define **android_pressed. png** como imagen cuando se presiona el botón (se activa); en el segundo `<item>` se define **android_focused. png** como imagen cuando el botón está enfocado (cuando el botón está resaltado mediante el panel de control de escritura o el panel de dirección). y el tercer `<item>` define **android_normal. png** como imagen para el estado normal (cuando no se presiona ni se centra). Ahora, este archivo XML representa un único recurso dibujado y, cuando se hace referencia a él en un [`Button`](xref:Android.Widget.Button) para su fondo, la imagen que se muestra cambiará en función de estos tres Estados.

> [!NOTE]
> El orden de los elementos `<item>` es importante. Cuando se hace referencia a esta dibujable, los `<item>`s se recorren en orden para determinar cuál es el adecuado para el estado actual del botón.
> Dado que la imagen "normal" es la última, solo se aplica cuando las condiciones `android:state_pressed` y `android:state_focused` se evalúan como false.

Abra el archivo **Resources/layout/main. axml** y agregue el elemento [`Button`](xref:Android.Widget.Button) :

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

El atributo `android:background` especifica el recurso que se va a usar para el fondo del botón (que, cuando se guarda en **Resources/drawable/Android. XML**, se hace referencia como `@drawable/android`). Esto reemplaza la imagen de fondo normal que se usa para los botones en todo el sistema. Para que el objeto drawable cambie su imagen en función del estado del botón, la imagen debe aplicarse al fondo.

Para hacer que el botón haga algo cuando se presiona, agregue el siguiente código al final de la [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Esto captura el [`Button`](xref:Android.Widget.Button) del diseño y, a continuación, agrega un [`Toast`](xref:Android.Widget.Toast) mensaje que se mostrará cuando se haga clic en el [`Button`](xref:Android.Widget.Button) .

Ahora ejecute la aplicación.

*Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la licencia de* [*atribución
Creative Commons 2,5*](https://creativecommons.org/licenses/by/2.5/).
