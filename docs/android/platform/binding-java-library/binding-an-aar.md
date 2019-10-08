---
title: Enlace de un .AAR
description: En este tutorial se proporcionan instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin. Android desde un dispositivo Android. Archivo AAR.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: cdd68def895fea362d9ad3147e3d622471d73a63
ms.sourcegitcommit: 4ff181101d76f048b949c9613b2c72cf02618f8b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2019
ms.locfileid: "71994894"
---
# <a name="binding-an-aar"></a>Enlace de un .AAR

_En este tutorial se proporcionan instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin. Android desde un dispositivo Android. Archivo AAR._

## <a name="overview"></a>Información general

El *archivo Android (. AAR)* es el formato de archivo de las bibliotecas de Android.
Alternativa. El archivo AAR es un. Archivo ZIP que contiene lo siguiente:

- Código Java compilado
- Identificadores de recursos
- Recursos
- Metadatos (por ejemplo, declaraciones de actividad, permisos)

En esta guía se detallan los conceptos básicos de la creación de una biblioteca de enlaces para un único. Archivo AAR. Para obtener información general sobre el enlace de la biblioteca de Java en general (con un ejemplo de código básico), vea [enlazar una biblioteca de Java](~/android/platform/binding-java-library/index.md).

> [!IMPORTANT]
> Un proyecto de enlace solo puede incluir uno. Archivo AAR. Si el. AAR dependencias en otros. AAR, estas dependencias deben estar contenidas en su propio proyecto de enlace y, a continuación, se puede hacer referencia a ellas. Vea el [error 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).

## <a name="walkthrough"></a>Tutorial

Vamos a crear una biblioteca de enlaces para un archivo de almacenamiento de Android de ejemplo creado en Android Studio [textanalyzer. AAR](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). El. AAR contiene una clase `TextCounter` con métodos estáticos que cuentan el número de vocales y consonantes en una cadena. Además, **textanalyzer. AAR** contiene un recurso de imagen que ayuda a mostrar los resultados del recuento.

Usaremos los pasos siguientes para crear una biblioteca de enlaces desde. Archivo AAR:

1. Cree un nuevo proyecto de biblioteca de enlaces de Java.

2. Agregue un único. Archivo AAR en el proyecto. Un proyecto de enlace solo puede contener un único. AAR.

3. Establezca la acción de compilación adecuada para. Archivo AAR.

4. Elija una versión de .NET Framework de destino que. AAR admite.

5. Compile la biblioteca de enlaces.

Una vez creada la biblioteca de enlaces, desarrollaremos una pequeña aplicación de Android que pida al usuario una cadena de texto, llama a. Los métodos de AAR para analizar el texto, recuperan la imagen de. AAR y muestra los resultados junto con la imagen.

La aplicación de ejemplo accederá a la clase `TextCounter` de **textanalyzer. AAR**:

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

Además, esta aplicación de ejemplo recuperará y mostrará un recurso de imagen que está empaquetado en **textanalyzer. AAR**:

[@no__t 1Xamarin Monkey](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Este recurso de imagen reside en **res/drawable/Monkey. png** en **textanalyzer. AAR**.

### <a name="creating-the-bindings-library"></a>Crear la biblioteca de enlaces

Antes de comenzar con los pasos siguientes, descargue el archivo de almacenamiento de Android de ejemplo [textanalyzer. AAR](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) :

1. Cree un nuevo proyecto de biblioteca de enlaces a partir de la plantilla de la biblioteca de enlaces de Android. Puede usar Visual Studio para Mac o Visual Studio (las capturas de pantallas siguientes muestran Visual Studio, pero Visual Studio para Mac es muy similar). Asigne a la solución el nombre **AarBinding**:

    [@no__t 1Create AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. La plantilla incluye una carpeta **jar** donde se agrega el. AAR (s) al proyecto de biblioteca de enlaces. Haga clic con el botón derecho en la carpeta **jar** y seleccione **Agregar > elemento existente**:

    [![Agregar elemento existente](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)

3. Navegue hasta el archivo **textanalyzer. AAR** descargado anteriormente, selecciónelo y haga clic en **Agregar**:

    [@no__t 1Add textanalayzer. AAR](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)

4. Compruebe que el archivo **textanalyzer. AAR** se ha agregado correctamente al proyecto:

    [se agregó ![El archivo textanalyzer. AAR](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. Establezca la acción de compilación para **textanalyzer. AAR** en `LibraryProjectZip`. En Visual Studio para Mac, haga clic con el botón secundario en **textanalyzer. AAR** para establecer la acción de compilación. En Visual Studio, la acción de compilación se puede establecer en el panel de **propiedades** :

    [![Setting la acción de compilación textanalyzer. AAR a LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. Abra las propiedades del proyecto para configurar la versión de *.NET Framework de destino*. Si el. AAR usa cualquier API de Android, establezca la plataforma de destino en el nivel de API que el. AAR espera. (Para obtener más información sobre la configuración de la plataforma de destino y los niveles de la API de Android en general, consulte Descripción de los [niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)).

    Establezca el nivel de API de destino para la biblioteca de enlaces. En este ejemplo, podemos usar el nivel de API de plataforma más reciente (nivel de API 23) porque nuestro **textanalyzer** no tiene una dependencia en las API de Android:

    [@no__t: 1Setting el nivel de destino a la API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. Compile la biblioteca de enlaces. El proyecto de biblioteca de enlaces se debe compilar correctamente y generar una salida. DLL en la siguiente ubicación: **AarBinding/bin/Debug/AarBinding.dll**

### <a name="using-the-bindings-library"></a>Uso de la biblioteca de enlaces

Para consumir este. DLL en la aplicación de Xamarin. Android, primero debe agregar una referencia a la biblioteca de enlaces. Siga estos pasos:

1. Vamos a crear esta aplicación en la misma solución que la biblioteca de enlaces para simplificar este tutorial. (La aplicación que usa la biblioteca de enlaces también puede residir en una solución diferente). Cree una nueva aplicación de Xamarin. Android: haga clic con el botón derecho en la solución y seleccione **Agregar nuevo proyecto**. Asigne al nuevo proyecto el nombre **BindingTest**:

    [@no__t 1Create nuevo proyecto de BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. Haga clic con el botón secundario en el nodo **referencias** del proyecto **BindingTest** y seleccione **Agregar referencia..** .:

    [![Click Agregar referencia](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. Seleccione el proyecto **AarBinding** creado anteriormente y haga clic en **Aceptar**:

    [@no__t 1Check el proyecto de enlace de AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. Abra el nodo **References** del proyecto **BindingTest** para comprobar que la referencia **AarBinding** está presente:

    [![AarBinding aparece en referencias](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)

Si desea ver el contenido del proyecto de biblioteca de enlaces, puede hacer doble clic en la referencia para abrirla en el **Examinador de objetos**. Puede ver el contenido asignado del espacio de nombres `Com.Xamarin.Textcounter` (asignado desde el paquete Java `com.xamarin.textanalyzezr`) y puede ver los miembros de la clase `TextCounter`:

[@no__t 1Viewing el Examinador de objetos](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

En la captura de pantalla anterior se resaltan los dos métodos `TextAnalyzer` a los que llamará la aplicación de ejemplo: `NumConsonants` (que incluye el método Java `numConsonants` subyacente) y `NumVowels` (que incluye el método subyacente de Java `numVowels`).

### <a name="accessing-aar-types"></a>Acceso a. Tipos de AAR

Después de agregar una referencia a la aplicación que apunta a la biblioteca de enlaces, puede tener acceso a los tipos de Java en el. AAR como se haría con C# los tipos de acceso ( C# gracias a los contenedores). C#el código de la aplicación puede llamar a métodos `TextAnalyzer`, tal como se muestra en este ejemplo:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

En el ejemplo anterior, llamamos a métodos estáticos en la clase `TextCounter`. Sin embargo, también puede crear instancias de clases y llamar a métodos de instancia. Por ejemplo, si su. AAR ajusta una clase llamada `Employee` que tiene el método de instancia `buildFullName`, puede crear una instancia de `MyClass` y usarla como se muestra aquí:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

En los pasos siguientes se agrega código a la aplicación para que solicite texto al usuario, usa `TextCounter` para analizar el texto y, a continuación, muestra los resultados.

Reemplace el diseño de **BindingTest** (**Main. axml**) con el siguiente código XML. Este diseño tiene un `EditText` para la entrada de texto y dos botones para iniciar recuentos de vocales y consonantes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

Reemplace el contenido de **MainActivity.CS** por el código siguiente. Tal y como se aprecia en este ejemplo, los controladores de eventos de botón llaman a los métodos encapsulados `TextCounter` que residen en. AAR y use la notificación del sistema para mostrar los resultados. Observe la instrucción `using` para el espacio de nombres de la biblioteca enlazada (en este caso, `Com.Xamarin.Textcounter`):

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

Compile y ejecute el proyecto **BindingTest** . La aplicación se iniciará y presentará la captura de pantalla a la izquierda (el `EditText` se inicializa con algún texto, pero puede puntear para cambiarlo). Al pulsar en **recuento de vocales**, un sistema de notificación muestra el número de vocales, como se muestra a la derecha:

[@no__t 1Screenshots de ejecutar BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Pruebe a pulsar el botón **recuento de CONsonantes** . Además, puede modificar la línea de texto y puntear de nuevo en estos botones para probar los diferentes recuentos de vocales y consonantes.

### <a name="accessing-aar-resources"></a>Acceso a. Recursos de AAR

Las herramientas de Xamarin combinan los datos de **R** de. AAR en la clase de **recursos** de la aplicación. Como resultado, puede tener acceso a. AAR recursos del diseño (y del código subyacente) de la misma manera en que tendría acceso a los recursos que se encuentran en la ruta de acceso a **los recursos** del proyecto.

Para tener acceso a un recurso de imagen, use el nombre de **recurso. drawable** para la imagen empaquetada dentro de. AAR. Por ejemplo, puede hacer referencia a **Image. png** en. Archivo AAR con `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

También puede tener acceso a los diseños de recursos que residen en el. AAR. Para ello, utilice el nombre **Resource. layout** para el diseño empaquetado dentro de. AAR. Por ejemplo:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

El ejemplo de **textanalyzer. AAR** contiene un archivo de imagen que reside en **res/drawable/Monkey. png**. Vamos a acceder a este recurso de imagen y usarlo en nuestra aplicación de ejemplo:

Edite el diseño de **BindingTest** (**Main. axml**) y agregue un `ImageView` al final del contenedor `LinearLayout`. Este `ImageView` muestra la imagen que se encuentra en **\@drawable/Monkey**; Esta imagen se cargará desde la sección de recursos de **textanalyzer. AAR**:

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

Compile y ejecute el proyecto **BindingTest** . La aplicación se iniciará y presentará la captura de pantalla de la izquierda &ndash; cuando pulse **recuento de CONsonantes**, los resultados se muestran como se muestra a la derecha:

[![BindingTest mostrar el recuento de consonantes](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)

¡Enhorabuena! Ha enlazado correctamente una biblioteca de Java. AAR!

## <a name="summary"></a>Resumen

En este tutorial, creamos una biblioteca de enlaces para un. Archivo AAR, agregó la biblioteca de enlaces a una aplicación de prueba mínima y ejecutó la aplicación para comprobar que C# el código puede llamar al código Java que reside en. Archivo AAR.
Además, extendimos la aplicación para obtener acceso y mostrar un recurso de imagen que se encuentra en el. Archivo AAR.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de una biblioteca de enlaces de Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Enlace de un .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Error 44573: un proyecto no puede enlazar varios archivos. AAR](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
