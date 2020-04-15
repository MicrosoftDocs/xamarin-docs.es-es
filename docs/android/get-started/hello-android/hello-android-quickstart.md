---
title: 'Hello, Android: Inicio rápido'
description: Esta guía en dos partes le ayudará a compilar su primera aplicación de Xamarin.Android (mediante Visual Studio o Visual Studio para Mac) y a comprender los aspectos básicos del desarrollo de aplicaciones de Android con Xamarin. A lo largo del proceso, le presentaremos las herramientas, los conceptos y los pasos necesarios para compilar e implementar una aplicación de Xamarin.Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 44007FA1-3ABC-4935-BF52-4613AF0553A6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: ab401c24fc486ba69fe01aff76e1a9b7d53122d0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73028030"
---
# <a name="hello-android-quickstart"></a>Hello, Android: Inicio rápido

_Esta guía, que consta de dos partes, le ayudará a compilar su primera aplicación de Xamarin.Android mediante Visual Studio y a comprender los aspectos básicos del desarrollo de aplicaciones de Android con Xamarin._

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword)

Creará una aplicación que convierte un número de teléfono alfanumérico (escrito por el usuario) en un número de teléfono numérico y muestra ese número al usuario. La aplicación final tiene este aspecto:

[![Captura de pantalla de la aplicación una vez completada](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

::: zone pivot="windows"

## <a name="windows-requirements"></a>Requisitos de Windows

Para seguir este tutorial, necesitará lo siguiente:

- Windows 10.

- Visual Studio 2019 o Visual Studio 2017 (versión 15.8 o posterior): Community, Professional o Enterprise.

::: zone-end
::: zone pivot="macos"

## <a name="macos-requirements"></a>Requisitos de macOS

Para seguir este tutorial, necesitará lo siguiente:

- La versión más reciente de Visual Studio para Mac.

- Un equipo Mac que ejecute macOS High Sierra (10.13) o una versión posterior.

::: zone-end

En este tutorial se presupone que está instalada la última versión de Xamarin.Android y que se está ejecutando en la plataforma que ha seleccionado. Para obtener una guía de instalación de Xamarin.Android, vea las guías [Instalación de Xamarin.Android](~/android/get-started/installation/index.md).

## <a name="configuring-emulators"></a>Configuración de los emuladores

Si está usando el emulador de Android, le recomendamos que lo configure para usar la aceleración de hardware. Las instrucciones para configurar la aceleración de hardware están disponibles en [Aceleración de hardware para el rendimiento del emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md).

## <a name="create-the-project"></a>Crear el proyecto

::: zone pivot="windows"

Inicie Visual Studio. Para crear un proyecto, haga clic en **Archivo > Nuevo > Proyecto**.

En el cuadro de diálogo **Nuevo proyecto**, haga clic en la plantilla **Aplicación Android**.
Asigne un nombre al nuevo proyecto `Phoneword` y haga clic en **Aceptar**:

[![El nuevo proyecto es Phoneword](hello-android-quickstart-images/vs/01-new-project-name-w158-sml.png)](hello-android-quickstart-images/vs/01-new-project-name-w158.png#lightbox)

En el cuadro de diálogo **New Android App** (Nueva aplicación Android), haga clic en **Aplicación vacía** y en **Aceptar** para crear el proyecto nuevo:

[![Selección de la plantilla de aplicación vacía](hello-android-quickstart-images/vs/02-blank-app-w158-sml.png)](hello-android-quickstart-images/vs/02-blank-app-w158.png#lightbox)

## <a name="create-a-layout"></a>Crear un diseño

> [!TIP]
> Las versiones más recientes de Visual Studio admiten la apertura de archivos .xml dentro de Android Designer.
>
> Android Designer admite tanto archivos .axml como .xml.

Después de que el nuevo proyecto se haya creado, expanda la carpeta **Recursos** y, después, la carpeta **Diseño** en el **Explorador de soluciones**.
Haga doble clic en **activity_main.axml** para abrirla en Android Designer. Este es el archivo de diseño para la pantalla de la aplicación:

[![Archivo axml de actividad abierto](hello-android-quickstart-images/vs/03-open-layout-w158-sml.png)](hello-android-quickstart-images/vs/03-open-layout-w158.png#lightbox)

> [!TIP]
> Las versiones más recientes de Visual Studio contienen una plantilla de aplicación ligeramente diferente.
>
> 1. En lugar de **activity_main.axml**, el diseño está en **content_main.axml**.
> 2. El diseño predeterminado será `RelativeLayout`. Para que el resto de los pasos de esta página funcionen, debe cambiar la etiqueta `<RelativeLayout>` por `<LinearLayout>` y agregar otro atributo `android:orientation="vertical"` a la etiqueta de apertura `LinearLayout`.

En el **Cuadro de herramientas** (el área de la izquierda), escriba `text` en el campo de búsqueda y arrastre un widget de **Texto (grande)** a la superficie de diseño (el área del centro):

[![Agregar un widget de texto grande](hello-android-quickstart-images/vs/04-large-text-w158-sml.png)](hello-android-quickstart-images/vs/04-large-text-w158.png#lightbox)

Con el control de **Texto (grande)** seleccionado en la superficie de diseño, use el panel **Propiedades** para cambiar la propiedad `Text` del widget de **Texto (grande)** a `Enter a Phoneword:`:

[![Establecer las propiedades de texto grande](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158-sml.png)](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158.png#lightbox)

Arrastre un widget de **Texto sin formato** del **Cuadro de herramientas** a la superficie de diseño y colóquelo debajo del widget de **Texto (grande)** . La selección de ubicación del widget no se producirá hasta que mueva el puntero del mouse a un lugar en el diseño que pueda aceptar el widget. En las capturas de pantalla siguientes, el widget no se puede colocar (tal y como se muestra en la parte izquierda) hasta que el puntero del mouse se mueva justo debajo del `TextView` anterior (como se muestra en la parte derecha):

[![El mouse indica dónde se puede colocar el widget](hello-android-quickstart-images/vs/06a-cant-drop-w158-sml.png)](hello-android-quickstart-images/vs/06a-cant-drop-w158.png#lightbox)

Cuando el **texto sin formato** (un widget `EditText`) esté colocado correctamente, aparecerá tal y como se muestra en la captura de pantalla siguiente:

[![Agregar un widget de texto sin formato](hello-android-quickstart-images/vs/06b-plain-text-w158-sml.png)](hello-android-quickstart-images/vs/06b-plain-text-w158.png#lightbox)

Con el widget de **Texto sin formato** seleccionado en la superficie de diseño, use el panel **Propiedades** para cambiar la propiedad `Id` del widget de **Texto sin formato** a `@+id/PhoneNumberText` y cambiar la propiedad `Text` a `1-855-XAMARIN`:

[![Establecer las propiedades de texto sin formato](hello-android-quickstart-images/vs/07-add-properties-w158-sml.png)](hello-android-quickstart-images/vs/07-add-properties-w158.png#lightbox)

Arrastre un **Botón** del **Cuadro de herramientas** a la superficie de diseño y colóquelo debajo del widget de **Texto sin formato**:

[![Arrastrar el botón Traducir al diseño](hello-android-quickstart-images/vs/08-drag-button-w158-sml.png)](hello-android-quickstart-images/vs/08-drag-button-w158.png#lightbox)

Con el **botón** seleccionado en la superficie de diseño, use el panel **Propiedades** para cambiar su propiedad `Text` a `Translate` y cambiar su propiedad `Id` a `@+id/TranslateButton`:

[![Establecer las propiedades del botón Traducir](hello-android-quickstart-images/vs/09-translate-button-w158-sml.png)](hello-android-quickstart-images/vs/09-translate-button-w158.png#lightbox)

Arrastre la **vista de texto** desde **Cuadro de herramientas** hasta la superficie de diseño y colóquela debajo del widget **Botón**. Cambie la propiedad `Text` de **TextView** por una cadena vacía y establezca su propiedad `Id` en `@+id/TranslatedPhoneword`:

[![Establecer las propiedades en la vista de texto.](hello-android-quickstart-images/vs/10-textview-properties-w158-sml.png)](hello-android-quickstart-images/vs/10-textview-properties-w158.png#lightbox)

Guarde su trabajo presionando **CTRL+S**.

## <a name="write-some-code"></a>Escritura de algo de código

El siguiente paso consiste en agregar código para convertir números de teléfono de formato alfanumérico a numérico. Agregue un nuevo archivo al proyecto haciendo clic con el botón derecho en el proyecto **Phoneword** en el panel del **Explorador de soluciones** y seleccionando **Agregar > Nuevo elemento...** , como se muestra a continuación:

[![Agregar nuevo elemento](hello-android-quickstart-images/vs/12-add-new-item-w158-sml.png)](hello-android-quickstart-images/vs/12-add-new-item-w158.png#lightbox)

En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Visual C# > Código > Archivo de código** y asigne al nuevo archivo de código el nombre **PhoneTranslator.cs**:

[![Agregar PhoneTranslator.cs](hello-android-quickstart-images/vs/14-add-class-w158-sml.png)](hello-android-quickstart-images/vs/14-add-class-w158.png#lightbox)

Esto crea una nueva clase de C# vacía. Inserte el código siguiente en este archivo:

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
                }
                // otherwise we've skipped a non-numeric char
            }
            return newNumber.ToString();
        }
        static bool Contains (this string keyString, char c)
        {
            return keyString.IndexOf(c) >= 0;
        }
        static int? TranslateToNumber(char c)
        {
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Guarde los cambios en el archivo **PhoneTranslator.cs** haciendo clic en **Archivo > Guardar** (o presionando **CTRL+S**) y, después, cierre el archivo.

## <a name="wire-up-the-user-interface"></a>Conectar la interfaz de usuario

El siguiente paso consiste en agregar código para conectar la interfaz de usuario al insertar código auxiliar en la clase `MainActivity`. Empiece con la conexión del botón **Traducir**. En la clase `MainActivity`, busque el método `OnCreate`. El siguiente paso consiste en agregar el código de botón dentro de `OnCreate`, debajo de las llamadas `base.OnCreate(savedInstanceState)` y `SetContentView(Resource.Layout.activity_main)`. En primer lugar, modifique el código de plantilla para que el método `OnCreate` sea similar al siguiente:

```csharp
using Android.App;
using Android.OS;
using Android.Support.V7.App;
using Android.Runtime;
using Android.Widget;

namespace Phoneword
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);

            // New code will go here
        }
    }
}
```

Obtenga una referencia de los controles que se han creado en el archivo de diseño con Android Designer. Agregue el código siguiente dentro del método `OnCreate`, después de la llamada a `SetContentView`:

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneword);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Agregue el código que responda a las pulsaciones del usuario del botón **Traducir**.
Agregue el siguiente código al método `OnCreate` (después de las líneas que se han agregado en el paso anterior):

```csharp
// Add code to translate number
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    string translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Guarde su trabajo seleccionando **Archivo > Guardar todo** (o presionando **CTRL-MAYÚS-S**) y compile la aplicación seleccionando **Compilar > Recompilar solución** (o presionando **CTRL-MAYÚS-B**). 

Si hay errores, repase los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente. Si obtiene un error de compilación como _El recurso no existe en el contexto actual_, compruebe que el nombre del espacio de nombres en **MainActivity.cs** coincida con el nombre del proyecto (`Phoneword`) y, después, recompile completamente la solución. Si sigue obteniendo errores de compilación, compruebe que ha instalado las últimas actualizaciones de Visual Studio.

## <a name="set-the-app-name"></a>Establecer el nombre de la aplicación

Ahora que debería tener una aplicación que funciona, es el momento de establecer su nombre. Expanda la carpeta **valores** (dentro de la carpeta **Recursos**) y abra el archivo **strings.xml**. Cambie la cadena de nombre de la aplicación por `Phone Word` como se muestra aquí:

```xml
<resources>
    <string name="app_name">Phone Word</string>
    <string name="action_settings">Settings</string>
</resources>
```

## <a name="run-the-app"></a>Ejecutar la aplicación

Pruebe la aplicación; para ello, ejecútela en un dispositivo o emulador Android.
Haga clic en el botón **TRANSLATE** para convertir **1-855-XAMARIN** en un número de teléfono:

[![Captura de pantalla de la aplicación en ejecución](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

Para ejecutar la aplicación en un dispositivo Android, vea cómo [configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

::: zone-end
::: zone pivot="macos"

Inicie Visual Studio para Mac desde la carpeta **Aplicaciones** o desde **Spotlight**.

Para crear un proyecto, haga clic en **Nuevo proyecto...**

En el cuadro de diálogo **Choose a template for your new project (Elegir una plantilla para el nuevo proyecto)** , haga clic en **Android > Aplicación** y seleccione la plantilla **Aplicación de Android**. Haga clic en **Siguiente**.

[![Elegir la plantilla Aplicación de Android](hello-android-quickstart-images/xs/03-choose-template-sml.png)](hello-android-quickstart-images/xs/03-choose-template.png#lightbox)

En el cuadro de diálogo **Configure su aplicación de Android**, asigne a la nueva aplicación el nombre `Phoneword` y haga clic en **Siguiente**.

[![Configurar la aplicación de Android](hello-android-quickstart-images/xs/04-configure-android-app-sml.png)](hello-android-quickstart-images/xs/04-configure-android-app.png#lightbox)

En el cuadro de diálogo **Configure su aplicación de Android**, deje los nombres de solución y proyecto establecidos en `Phoneword` y haga clic en **Crear** para crear el proyecto.

## <a name="create-a-layout"></a>Crear un diseño

> [!TIP]
> Las versiones más recientes de Visual Studio admiten la apertura de archivos .xml dentro de Android Designer.
>
> Android Designer admite tanto archivos .axml como .xml.

Después de que el nuevo proyecto se haya creado, expanda la carpeta **Recursos** y, después, la carpeta **Diseño** en el panel de **Solución**.
Haga doble clic en **Main.axml** para abrirlo en Android Designer. Este es el archivo de diseño de la pantalla cuando se visualiza en Android Designer:

[![Abrir Main.axml](hello-android-quickstart-images/xs/05-open-layout-sml.png)](hello-android-quickstart-images/xs/05-open-layout.png#lightbox)

Seleccione el botón **Hello World, Click Me! (Hola a todos, haz clic aquí)** **Botón** en la superficie de diseño; pulse la tecla **Eliminar** para quitarlo. 

Desde el **Cuadro de herramientas** (el área de la derecha), escriba `text` en el campo de búsqueda y arrastre un widget de **Texto (grande)** a la superficie de diseño (el área del centro):

[![Agregar un widget de texto grande](hello-android-quickstart-images/xs/06-large-text-sml.png)](hello-android-quickstart-images/xs/06-large-text.png#lightbox)

Con el widget de **Texto (grande)** seleccionado en la superficie de diseño, puede usar el panel **Propiedades** para cambiar la propiedad `Text` del widget de **Texto (grande)** a `Enter a Phoneword:` como se muestra a continuación:

[![Establecer las propiedades del widget de texto grande](hello-android-quickstart-images/xs/07-enter-a-phoneword-sml.png)](hello-android-quickstart-images/xs/07-enter-a-phoneword.png#lightbox)

Después, arrastre un widget de **Texto sin formato** del **Cuadro de herramientas** a la superficie de diseño y colóquelo debajo del widget de **Texto (grande)** . Tenga en cuenta que puede usar el campo de búsqueda para ayudarle a buscar widgets por el nombre:

[![Agregar un widget de texto sin formato](hello-android-quickstart-images/xs/08-plain-text-sml.png)](hello-android-quickstart-images/xs/08-plain-text.png#lightbox)

Con el widget de **Texto sin formato** seleccionado en la superficie de diseño, puede usar el panel **Propiedades** para cambiar la propiedad `Id` del widget de **Texto sin formato** a `@+id/PhoneNumberText` y cambiar la propiedad `Text` a `1-855-XAMARIN`:

[![Establecer las propiedades del widget de texto sin formato](hello-android-quickstart-images/xs/09-add-properties-sml.png)](hello-android-quickstart-images/xs/09-add-properties.png#lightbox)

Arrastre un **Botón** del **Cuadro de herramientas** a la superficie de diseño y colóquelo debajo del widget de **Texto sin formato**:

[![Agregar un botón](hello-android-quickstart-images/xs/10-drag-button-sml.png)](hello-android-quickstart-images/xs/10-drag-button.png#lightbox)

Con el **Botón** seleccionado en la superficie de diseño, puede usar el panel **Propiedades** para cambiar la propiedad `Id` del **Botón** a `@+id/TranslateButton` y cambiar la propiedad `Text` a `Translate`:

[![Configurar como el botón Traducir](hello-android-quickstart-images/xs/11-translate-button-sml.png)](hello-android-quickstart-images/xs/11-translate-button.png#lightbox)

Arrastre la **vista de texto** desde **Cuadro de herramientas** hasta la superficie de diseño y colóquela debajo del widget **Botón**. Con la **vista de texto** seleccionada, establezca la propiedad `id` de la **vista de texto** en `@+id/TranslatedPhoneWord` y cambie el `text` por una cadena vacía:

[![Establecer las propiedades en la vista de texto.](hello-android-quickstart-images/xs/12-textview-properties-sml.png)](hello-android-quickstart-images/xs/12-textview-properties.png#lightbox)    

Guarde su trabajo presionando **&#8984; + S**.

## <a name="write-some-code"></a>Escritura de algo de código

Ahora, agregue código para convertir números de teléfono de formato alfanumérico a numérico. Agregue un nuevo archivo al proyecto haciendo clic en el icono de engranaje junto al proyecto **Phoneword** en el panel de **Solución** y seleccionando **Agregar > Nuevo archivo...** :

[![Agregar un nuevo archivo al proyecto](hello-android-quickstart-images/xs/14-add-new-file-sml.png)](hello-android-quickstart-images/xs/14-add-new-file.png#lightbox)

En el cuadro de diálogo **Nuevo archivo**, haga clic en **General > Clase vacía**, asigne al archivo nuevo el nombre **PhoneTranslator** y haga clic en **Nuevo**. Esto crea una nueva clase de C# vacía.

Quite todo el código de plantilla de la clase nueva y reemplácelo por el siguiente código:

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
                }
                // otherwise we've skipped a non-numeric char
            }
            return newNumber.ToString();
        }
        static bool Contains (this string keyString, char c)
        {
            return keyString.IndexOf(c) >= 0;
        }
        static int? TranslateToNumber(char c)
        {
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Guarde los cambios en **PhoneTranslator.cs** pulsando **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo. Asegúrese de que no hay errores de tiempo de compilación al volver a compilar la solución.

## <a name="wire-up-the-user-interface"></a>Conectar la interfaz de usuario

El siguiente paso consiste en agregar código para conectar la interfaz de usuario al agregar código auxiliar en la clase `MainActivity`.
Haga doble clic en el archivo **MainActivity.cs** en **Panel de solución** para abrirlo.

Para empezar, agregue un controlador de eventos al botón **Traducir**. En la clase `MainActivity`, busque el método `OnCreate`. Agregue el código de botón dentro de `OnCreate`, debajo de las llamadas a `base.OnCreate(bundle)` y `SetContentView (Resource.Layout.Main)`. Quite cualquier código de control de botón existente (es decir, cualquier código que hace referencia a `Resource.Id.myButton` y crea un controlador de clic para él) de manera que el método `OnCreate` tenga el aspecto siguiente:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;

namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Our code will go here
        }
    }
}
```

Después, se necesita una referencia de los controles que se han creado en el archivo de diseño con Android Designer. Agregue el código siguiente dentro del método `OnCreate` (después de la llamada a `SetContentView`):

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Agregue el código que responda a las pulsaciones del usuario del botón **Traducir** agregando el siguiente código al método `OnCreate` (después de las líneas que se han agregado en el último paso):

```csharp
// Add code to translate number
string translatedNumber = string.Empty;

translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Guarde el trabajo y compile la aplicación seleccionando **Compilar > Compilar todo** (o presionando **&#8984; + B**). Si la aplicación se compila, obtendrá un mensaje de operación correcta en la parte superior de Visual Studio para Mac:

Si hay errores, repase los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente. Si obtiene un error de compilación como _El recurso no existe en el contexto actual_, compruebe que el nombre del espacio de nombres en **MainActivity.cs** coincida con el nombre del proyecto (`Phoneword`) y, después, recompile completamente la solución. Si sigue obteniendo errores de compilación, compruebe que ha instalado las últimas actualizaciones de Xamarin.Android y Visual Studio para Mac.

## <a name="set-the-label-and-app-icon"></a>Establecer la etiqueta y el icono de la aplicación

Ahora que tiene una aplicación que funciona, es el momento de agregar los toques finales. Empiece editando `Label` para `MainActivity`.
`Label` es lo que Android muestra en la parte superior de la pantalla para que los usuarios conozcan el lugar en el que se encuentran en la aplicación. En la parte superior de la clase `MainActivity`, cambie `Label` por `Phone Word` como se muestra aquí:

```csharp
namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        ...
    }
}
```

Ahora es el momento de establecer el icono de la aplicación. De forma predeterminada, Visual Studio para Mac proporcionará un icono predeterminado para el proyecto. Elimine estos archivos de la solución y reemplácelos por otro icono. Expanda la carpeta **Recursos** en **Panel de solución**. Observe que hay cinco carpetas que tienen el prefijo **mipmap-** y que cada una de estas carpetas contiene un archivo **Icon.png**:

[![carpetas mipmap- y archivos Icon.png](hello-android-quickstart-images/xs/23-mipmap-folders-sml.png)](hello-android-quickstart-images/xs/23-mipmap-folders.png#lightbox)

Es necesario eliminar del proyecto todos estos archivos de icono. Haga clic con el botón derecho en cada uno de los archivos **Icon.png** y haga clic en **Quitar** en el menú contextual:

[![Eliminar archivo Icon.png predeterminado](hello-android-quickstart-images/xs/23-delete-icon-sml.png)](hello-android-quickstart-images/xs/23-delete-icon.png#lightbox)

Haga clic en el botón **Eliminar** en el cuadro de diálogo.

Luego, descargue y descomprima el [conjunto de iconos de aplicación de Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true). Este archivo ZIP contiene los iconos de la aplicación. Todos los iconos son idénticos visualmente, pero se representan correctamente con resoluciones diferentes en distintos dispositivos con distintas densidades de pantalla.  El conjunto de archivos debe copiarse en el proyecto Xamarin.Android. En Visual Studio para Mac, en **Panel de solución**, haga clic con el botón derecho en la carpeta **mipmap-hdpi** y seleccione **Agregar > Agregar archivos**:

[![Agregar archivos](hello-android-quickstart-images/xs/24-add-files-sml.png)](hello-android-quickstart-images/xs/24-add-files.png#lightbox)

En el cuadro de diálogo de selección, vaya al directorio descomprimido de los iconos de aplicación de Xamarin y abra la carpeta **mipmap-hdpi**. Seleccione **Icon.png** y haga clic en **Abrir**.

En el cuadro de diálogo **Add File to Folder (Agregar archivo a carpeta)** , seleccione **Copy the file into the directory (Copiar el archivo en el directorio)** y haga clic en **Aceptar**:

[![Cuadro de diálogo Copiar el archivo en el directorio](hello-android-quickstart-images/xs/26-copy-to-directory-sml.png)](hello-android-quickstart-images/xs/26-copy-to-directory.png#lightbox)

Repita estos pasos para cada una de las carpetas **mipmap-** hasta que el contenido de las carpetas de iconos de aplicación de Xamarin **mipmap-** se copie en las carpetas **mipmap-** equivalentes en el proyecto **Phoneword**.

Después de copiar todos los iconos en el proyecto de Xamarin.Android, abra el cuadro de diálogo **Opciones del proyecto** haciendo clic con el botón derecho en el proyecto en **Panel de solución**. Haga clic en **Compilar > Aplicación de Android** y seleccione `@mipmap/icon` en el cuadro combinado **Icono de aplicación**:

[![Establecer el icono del proyecto](hello-android-quickstart-images/xs/28-set-project-icon-sml.png)](hello-android-quickstart-images/xs/28-set-project-icon.png#lightbox)

## <a name="run-the-app"></a>Ejecutar la aplicación

Por último, pruebe la aplicación ejecutándola en un dispositivo o emulador Android y traduciendo un Phoneword:

[![Captura de pantalla de la aplicación una vez completada](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

Para ejecutar la aplicación en un dispositivo Android, vea cómo [configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

::: zone-end

Ha completado su primera aplicación de Xamarin.Android.
Ahora es el momento de analizar las herramientas y los conocimientos que ha aprendido. Lo siguiente es el [Análisis detallado de Hello, Android](~/android/get-started/hello-android/hello-android-deepdive.md).

## <a name="related-links"></a>Vínculos relacionados

- [Iconos de aplicación de Xamarin para Android (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword)
