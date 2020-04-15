---
title: 'Hello, Android Multiscreen: Inicio rápido'
description: Esta guía de dos partes expande la aplicación Phoneword para controlar una segunda pantalla. Por el camino, se presentan bloques de creación básicos de aplicaciones Android con un análisis más profundo de la arquitectura de Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: ED99584A-BA3B-429A-AEE5-CF3CB0116762
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: b8f7d882306ed5c00404ae7a050da8e02a49d609
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "75607911"
---
# <a name="hello-android-multiscreen-quickstart"></a>Hello, Android Multiscreen: Inicio rápido

_Esta guía de dos partes expande la aplicación Phoneword para controlar una segunda pantalla. Por el camino, se presentan bloques de creación básicos de aplicaciones Android con un análisis más profundo de la arquitectura de Android._

En la parte del tutorial de esta guía agregará una segunda pantalla a la aplicación [Phoneword](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword) para realizar un seguimiento del historial de números traducidos mediante la aplicación. La [aplicación final](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phonewordmultiscreen) tendrá una segunda pantalla en la que se mostrarán los números "traducidos", como se muestra en la captura de pantalla de la derecha:

[![Capturas de pantalla de la aplicación de ejemplo](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

La sección complementaria [Análisis detallado](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md) revisa lo que se creó y describe la arquitectura, la navegación y otros conceptos de Android nuevos que encontrará en el camino.

## <a name="requirements"></a>Requisitos

Dado que esta guía continúa desde donde lo deja [Hello, Android](~/android/get-started/hello-android/index.md), requiere la finalización de [Hello, Android: inicio rápido](~/android/get-started/hello-android/hello-android-quickstart.md).
Si quiere pasar directamente al tutorial siguiente, puede descargar la versión completa de [Phoneword](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword) (de Hello, Android: inicio rápido) y usarla para empezar el tutorial.

## <a name="walkthrough"></a>Tutorial

En este tutorial agregará una pantalla **Translation History** (Historial de traducciones) a la aplicación **Phoneword**.

::: zone pivot="windows"

Para empezar, abra la aplicación **Phoneword** en Visual Studio y edite el archivo **Main.axml** en el **Explorador de soluciones**.

> [!TIP]
> Las versiones más recientes de Visual Studio admiten la apertura de archivos .xml dentro de Android Designer.
>
> Android Designer admite tanto archivos .axml como .xml.

### <a name="updating-the-layout"></a>Actualización del diseño

En el **cuadro de herramientas**, arrastre un **botón** hasta la superficie de diseño y colóquelo debajo de la vista de texto **TranslatedPhoneWord**. En el panel **Propiedades**, cambie el **Id** del botón por `@+id/TranslationHistoryButton`.

[![Arrastrar un botón nuevo](hello-android-multiscreen-quickstart-images/vs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/vs/02-new-button.png#lightbox)

Establezca la propiedad **Text** del botón en `@string/translationHistory`. Android Designer lo interpretará literalmente, pero va a realizar algunos cambios para que el texto del botón se muestre correctamente:

[![Establecer el texto del botón Translation History (Historial de traducciones)](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string-sml.png)](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string.png#lightbox)

Expanda el nodo **valores** bajo la carpeta **Recursos** en el **Explorador de soluciones** y haga doble clic en el archivo de recursos de cadena, **Strings.xml**:

[![Abrir Strings.xml](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file.png#lightbox)

Agregue el nombre y el valor de cadena `translationHistory` al archivo **Strings.xml** y guárdelo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

El texto del botón **Translation History** (Historial de traducciones) debería actualizarse para reflejar el nuevo valor de cadena:

[![El botón refleja el nuevo valor de cadena](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png)](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png#lightbox)

Con el botón **Translation History** (Historial de traducciones) seleccionado en la superficie de diseño, busque el ajuste `enabled` en el panel **Propiedades** y establezca su valor en `false` para deshabilitar el botón. Esto hará que el botón se vuelva más oscuro en la superficie de diseño:

[![Deshabilitar el botón Translation History (Historial de traducciones)](hello-android-multiscreen-quickstart-images/vs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/vs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Creación de la segunda actividad

Cree una segunda actividad para activar la segunda pantalla. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Phoneword** y seleccione **Agregar > Nuevo elemento...** :

[![Agregar un archivo nuevo](hello-android-multiscreen-quickstart-images/vs/07-add-new-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/07-add-new-file.png#lightbox)

En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Actividad** y asigne al archivo de actividad el nombre **TranslationHistoryActivity.cs**.

Reemplace el código de plantilla de **TranslationHistoryActivity.cs** por lo siguiente:

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

En esta clase, se crea una `ListActivity` y se rellena mediante programación, por lo que no es necesario crear un nuevo archivo de diseño para esta actividad. Esto se explica con más detalle en [Hello, Android Multiscreen: análisis detallado](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

### <a name="adding-a-list"></a>Adición de una lista

Esta aplicación recopila números de teléfono (que el usuario ha traducido en la primera pantalla) y los pasa a la segunda pantalla. Los números de teléfono se almacenan como una lista de cadenas. Para admitir listas (y también Intents, que se usan más adelante), agregue las directivas `using` siguientes a la parte superior de **MainActivity.cs**:

```csharp
using System.Collections.Generic;
using Android.Content;
```

Después, cree una lista vacía que se pueda rellenar con números de teléfono.
La clase `MainActivity` tendrá este aspecto:

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

En la clase `MainActivity`, agregue el código siguiente para registrar el botón **Translation History** (Historial de traducciones) (coloque esta línea después de la declaración `translateButton`):

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Agregue el código siguiente al final del método `OnCreate` para conectar el botón **Translation History** (Historial de traducciones):

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Actualice el botón **Traducir** para agregar el número de teléfono a la lista de `phoneNumbers`. El controlador `Click` de `translateButton` debería ser similar al código siguiente:

```csharp
// Add code to translate number
string translatedNumber = string.Empty;
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

Guarde y compile la aplicación para asegurarse de que no existen errores.

### <a name="running-the-app"></a>Ejecutar la aplicación

Implemente la aplicación en un dispositivo o emulador. En las siguientes capturas de pantalla se muestra la aplicación **Phoneword** en ejecución:

[![Capturas de pantalla de ejemplo](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end
::: zone pivot="macos"

Para empezar, abra el proyecto **Phoneword** en Visual Studio para Mac y edite el archivo **Main.axml** en **Panel de solución**.

> [!TIP]
> Las versiones más recientes de Visual Studio admiten la apertura de archivos .xml dentro de Android Designer.
>
> Android Designer admite tanto archivos .axml como .xml.

### <a name="updating-the-layout"></a>Actualización del diseño

En el **cuadro de herramientas**, arrastre un **botón** hasta la superficie de diseño y colóquelo debajo de la vista de texto **TranslatedPhoneWord**. En el panel **Propiedades**, cambie el **Id.** del botón por `@+id/TranslationHistoryButton`:

[![Arrastrar un botón nuevo](hello-android-multiscreen-quickstart-images/xs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/xs/02-new-button.png#lightbox)

Establezca la propiedad **Text** del botón en `@string/translationHistory`. Android Designer lo interpretará literalmente, pero va a realizar algunos cambios para que el texto del botón se muestre correctamente:

[![Establecer el texto del botón Translation History (Historial de traducciones)](hello-android-multiscreen-quickstart-images/xs/03-call-history-string-sml.png)](hello-android-multiscreen-quickstart-images/xs/03-call-history-string.png#lightbox)

Expanda el nodo **values** situado debajo de la carpeta **Recursos** en **Panel de solución** y haga doble clic en el archivo de recursos de cadena, **Strings.xml**:

[![Cadenas abiertas](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file.png#lightbox)

Agregue el nombre y el valor de cadena `translationHistory` al archivo **Strings.xml** y guárdelo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

El texto del botón **Translation History** (Historial de traducciones) debería actualizarse para reflejar el nuevo valor de cadena:

[![El botón refleja el nuevo valor de cadena](hello-android-multiscreen-quickstart-images/xs/05-new-string-value-sml.png)](hello-android-multiscreen-quickstart-images/xs/05-new-string-value.png#lightbox)

Con el botón **Translation History** (Historial de traducciones) seleccionado en la superficie de diseño, abra la pestaña **Comportamiento** en el **Panel de propiedades** y haga doble clic en la casilla **Habilitado** para deshabilitar el botón. Esto hará que el botón se vuelva más oscuro en la superficie de diseño:

[![Deshabilitar el botón Translation History (Historial de traducciones)](hello-android-multiscreen-quickstart-images/xs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/xs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Creación de la segunda actividad

Cree una segunda actividad para activar la segunda pantalla. En el **Panel de solución**, haga clic en el icono de engranaje de color gris junto al proyecto **Phoneword** y seleccione **Agregar > Nuevo archivo...** :

En el cuadro de diálogo **Nuevo archivo**, elija **Android > Actividad**, asigne a la actividad el nombre `TranslationHistoryActivity` y haga clic en **Agregar**.

Reemplace el código de plantilla de `TranslationHistoryActivity` con lo siguiente:

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

En esta clase, se crea una `ListActivity` y se rellena mediante programación, por lo que no es necesario crear un nuevo archivo de diseño para esta actividad. Esto se explica con más detalle en la [Hello, Android Multiscreen: análisis detallado](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

### <a name="adding-a-list"></a>Adición de una lista

Esta aplicación recopila números de teléfono (que el usuario ha traducido en la primera pantalla) y los pasa a la segunda pantalla. Los números de teléfono se almacenan como una lista de cadenas. Para admitir listas (y también Intents, que se usan más adelante), agregue las directivas `using` siguientes a la parte superior de **MainActivity.cs**:

```csharp
using System.Collections.Generic;
using Android.Content;
```

Después, cree una lista vacía que se pueda rellenar con números de teléfono. La clase `MainActivity` tendrá este aspecto:

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

En la clase `MainActivity`, agregue el código siguiente para registrar el botón **TranslationHistory** (coloque esta línea después de la declaración `TranslationHistoryButton`):

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Agregue el código siguiente al final del método `OnCreate` para conectar el botón **Translation History** (Historial de traducciones):

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Actualice el botón **Traducir** para agregar el número de teléfono a la lista de `phoneNumbers`. El controlador `Click` de `TranslateHistoryButton` debería ser similar al código siguiente:

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

### <a name="running-the-app"></a>Ejecutar la aplicación

Implemente la aplicación en un dispositivo o emulador. En las siguientes capturas de pantalla se muestra la aplicación **Phoneword** en ejecución:

[![Capturas de pantalla de ejemplo](hello-android-multiscreen-quickstart-images/screenshot.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end

Enhorabuena por completar su primera aplicación multipantalla de Xamarin.Android. Ahora es el momento de analizar minuciosamente las herramientas y los conocimientos que acaba de aprender &ndash; a continuación [Hello, Android Multiscreen: análisis detallado](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

## <a name="related-links"></a>Vínculos relacionados

- [Iconos de aplicación y pantallas de inicio (ZIP) de Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword)
- [PhonewordMultiscreen (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phonewordmultiscreen)
