---
title: Botón personalizado
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 4504045eb1692d95ee1e981bbec3da3a45699db3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758928"
---
# <a name="custom-button"></a>Botón personalizado

En esta sección, creará un botón con una imagen personalizada en lugar de texto, con el [`Button`](xref:Android.Widget.Button) widget y un archivo XML que define tres imágenes diferentes que se usarán para los distintos Estados del botón. Cuando se presiona el botón, se muestra un mensaje breve.

Haga clic con el botón derecho y descargue las tres imágenes siguientes y, a continuación, cópielos en el directorio **Resources/drawable** del proyecto. Se usarán para los distintos Estados de botón.

 Icono verde de Android para el icono de Android de estado [ ![](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [ ![normal con el estado](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) de foco [ ![icono de Android amarillo para el estado presionado](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

Cree un nuevo archivo en el directorio **Resources/Drawing** llamado **android_button. XML**. Inserte el siguiente código XML:

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

Define un único recurso que se va a dibujar, que cambiará su imagen en función del estado actual del botón. El primero `<item>` define **android_pressed. png** como la imagen cuando se presiona el botón (se activa); el segundo `<item>` define **android_focused. png** como la imagen cuando el botón está enfocado (cuando el botón es resaltado mediante el panel de control o la almohadilla direccional); y el tercero `<item>` define **android_normal. png** como la imagen para el estado normal (cuando no se presiona ni se centra). Ahora, este archivo XML representa un único recurso dibujable y, cuando se hace [`Button`](xref:Android.Widget.Button) referencia a él en el fondo, la imagen que se muestra cambiará en función de estos tres Estados.

> [!NOTE]
> El orden de los `<item>` elementos es importante. Cuando se hace referencia a esta dibujable, `<item>`los s se recorren en orden para determinar cuál es el adecuado para el estado actual del botón.
> Dado que la imagen "normal" es la última, solo se aplica cuando las `android:state_pressed` condiciones `android:state_focused` y se evalúan como false.

Abra el archivo **Resources/layout/main. axml** y [`Button`](xref:Android.Widget.Button) agregue el elemento:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

El `android:background` atributo especifica el recurso de dibujo que se va a usar para el fondo del botón (que, cuando se guarda en **Resources/drawable/Android. XML**, se hace referencia como `@drawable/android`). Esto reemplaza la imagen de fondo normal que se usa para los botones en todo el sistema. Para que el objeto drawable cambie su imagen en función del estado del botón, la imagen debe aplicarse al fondo.

Para hacer que el botón haga algo cuando se presiona, agregue el código siguiente al final de la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Esto captura el [`Button`](xref:Android.Widget.Button) del diseño y, a continuación, agrega [`Toast`](xref:Android.Widget.Toast) un mensaje [`Button`](xref:Android.Widget.Button) que se mostrará cuando se haga clic en.

Ahora ejecute la aplicación.

*Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la*
[*licencia de atribución de Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/).
