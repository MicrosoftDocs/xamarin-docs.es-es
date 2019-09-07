---
title: Accesibilidad en Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 982d5b81a22d6e69227081420a5947aed4d3aab1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755682"
---
# <a name="accessibility-on-android"></a>Accesibilidad en Android

En esta página se describe cómo usar las API de accesibilidad de Android para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).
Consulte las páginas de accesibilidad de [iOS](~/ios/app-fundamentals/accessibility.md) y [OS X](~/mac/app-fundamentals/accessibility.md) para otras API de la plataforma.

## <a name="describing-ui-elements"></a>Describir elementos de la interfaz de usuario

Android proporciona una `ContentDescription` propiedad que se usa en las API de lectura de pantalla para proporcionar una descripción accesible del propósito del control.

La descripción del contenido se puede establecer en C# o en el archivo de diseño AXML.

**C#**

La descripción se puede establecer en el código en cualquier cadena (o un recurso de cadena):

```csharp
saveButton.ContentDescription = "Save data";
```

**Diseño de AXML**

En los diseños XML, use `android:contentDescription` el atributo:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Use Hint para TextView

En `EditText` el `TextView` caso de los controles y para la `Hint` entrada de datos, use la propiedad para proporcionar una descripción de la entrada `ContentDescription`que se espera (en lugar de).
Cuando se escribe texto, el texto en sí se "Lee" en lugar de la sugerencia.

**C#**

Establezca la `Hint` propiedad en el código:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Diseño de AXML**

En los archivos de diseño XML `android:hint` , use el atributo:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```

### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor vincula los campos de entrada con etiquetas

Para asociar una etiqueta a un control de entrada de datos `LabelFor` , use la propiedad para

**C#**

En C#, establezca la `LabelFor` propiedad en el identificador de recurso del control que describe este contenido (normalmente, esta propiedad se establece en una etiqueta y hace referencia a otro control de entrada):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Diseño de AXML**

En XML de diseño, `android:labelFor` use la propiedad para hacer referencia a otro identificador de control:

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Anunciar para accesibilidad

Utilice el `AnnounceForAccessibility` método en cualquier control de vista para comunicar un evento o un cambio de estado a los usuarios cuando la accesibilidad esté habilitada. Este método no es necesario para la mayoría de las operaciones en las que la narración integrada proporciona suficientes comentarios, pero debe usarse cuando la información adicional sea útil para el usuario.

En el código siguiente se muestra un ejemplo `AnnounceForAccessibility`simple que llama a:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Cambiar la configuración del foco

La navegación accesible se basa en controles que tienen el foco para ayudar al usuario a comprender qué operaciones están disponibles. Android proporciona una `Focusable` propiedad que puede etiquetar controles como capaces de recibir el foco específicamente durante la navegación.

**C#**

Para evitar que un control tenga C#el foco, establezca la `Focusable` propiedad en: `false`

```csharp
label.Focusable = false;
```

**Diseño de AXML**

En los archivos XML de diseño `android:focusable` , establezca el atributo:

```xml
<android:focusable="false" />
```

También puede controlar el orden de foco con `nextFocusDown`los `nextFocusLeft`atributos `nextFocusRight`, `nextFocusUp` ,, que se establecen normalmente en el diseño AXML. Utilice estos atributos para asegurarse de que el usuario puede navegar fácilmente por los controles de la pantalla.

## <a name="accessibility-and-localization"></a>Accesibilidad y localización

En los ejemplos anteriores está la descripción de la sugerencia y contenido establece directamente en el valor de presentación. Es preferible usar valores en un archivo **Strings. XML** , como el siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

A continuación se muestra el uso de texto de un C# archivo de cadenas en y archivos de diseño AXML:

**C#**

En lugar de utilizar literales de cadena en el código, buscar valores traducidos de archivos `Resources.GetText`de cadenas con:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

En diseño, los atributos de `hint` accesibilidad `contentDescription` XML como y se pueden establecer en un identificador de cadena:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

La ventaja de almacenar texto en un archivo independiente es que se pueden proporcionar varias traducciones de idioma del archivo en la aplicación. Consulte la [Guía de localización de Android](~/android/app-fundamentals/localization.md) para obtener información sobre cómo agregar archivos de cadena localizados a un proyecto de aplicación.

## <a name="testing-accessibility"></a>Probar la accesibilidad

Siga [estos pasos](https://developer.android.com/training/accessibility/testing.html#how-to) para habilitar talkback y explorar por toque para probar la accesibilidad en dispositivos Android.

Es posible que tenga que instalar [talkback](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) desde Google Play si no aparece en **Configuración > accesibilidad**.

## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [API de accesibilidad de Android](https://developer.android.com/guide/topics/ui/accessibility/index.html)
