---
title: Inicio rápido de Hello, iOS
description: En este tutorial se muestra cómo compilar una aplicación Xamarin.iOS simple denominada Hello, iOS. Durante el proceso, se presentan las herramientas y los conceptos fundamentales que se deben entender para compilar aplicaciones Xamarin.iOS.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: D3868F3A-4EED-BDDF-45AA-665102C39634
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 5dcc37730008e6e39b96128bc1368f022daa2d06
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022992"
---
# <a name="hello-ios--quickstart"></a>Inicio rápido de Hello, iOS

Esta guía describe cómo crear una aplicación que convierte un número de teléfono alfanumérico escrito por el usuario en un número de teléfono numérico y, después, llama a ese número. La aplicación final tiene este aspecto:

 [![](hello-ios-quickstart-images/image1.png "The Hello.iOS Quickstart app")](hello-ios-quickstart-images/image1.png#lightbox)

## <a name="requirements"></a>Requisitos

El desarrollo de iOS con Xamarin requiere:

- Un equipo Mac que ejecute macOS High Sierra (10.13) o una versión posterior.
- La versión más reciente de Xcode y el SDK de iOS instalada desde el [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).

::: zone pivot="macos"

Xamarin.iOS funciona con la siguiente configuración:

- Versión más reciente de Visual Studio para Mac que se ajuste a las especificaciones anteriores.

La [guía de instalación de Mac para Xamarin.iOS](~/ios/get-started/installation/mac.md) está disponible para obtener instrucciones de instalación paso a paso

::: zone-end
::: zone pivot="windows"

Xamarin.iOS funciona con la siguiente configuración:

- La versión más reciente de Visual Studio 2019 o Visual Studio 2017 Community, Professional o Enterprise en Windows 10, emparejada con un host de compilación de Mac que se ajuste a las especificaciones anteriores.

Se puede consultar la [guía de instalación de Xamarin.iOS Windows](~/ios/get-started/installation/windows/index.md) para obtener instrucciones de instalación paso a paso.

::: zone-end

Antes de comenzar, descargue el [conjunto de iconos de aplicación de Xamarin](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true).

::: zone pivot="macos"

## <a name="visual-studio-for-mac-walkthrough"></a>Tutorial de Visual Studio para Mac

En este tutorial se muestra cómo crear una aplicación llamada Phoneword que convierte un número de teléfono alfanumérico en un número de teléfono numérico.

1. Inicie Visual Studio para Mac desde la carpeta **Aplicaciones** o desde **Spotlight**:

    ![](hello-ios-quickstart-images/image2new.png "The Launch screen")

    En la pantalla Inicio, haga clic en **Nuevo proyecto...** para crear una nueva solución de Xamarin.iOS:

    ![](hello-ios-quickstart-images/image3new.png "iOS solution")

2. Desde el **cuadro de diálogo Nueva solución**, elija la plantilla **iOS > Aplicación > Aplicación de vista única**, asegurándose de que C# está seleccionado. Haga clic en **Siguiente**:

    ![](hello-ios-quickstart-images/image4new.png "Choose Single View Application")

3. Configure la aplicación. Asígnele el **nombre** `Phoneword_iOS` y deje todo lo demás en su valor predeterminado. Haga clic en **Siguiente**:

    ![](hello-ios-quickstart-images/image5new.png "Enter the app name")

4. Deje el nombre del proyecto y de la solución tal como están. Elija la ubicación del proyecto aquí o manténgala con el valor predeterminado:

    ![](hello-ios-quickstart-images/image6new.png "Choose the location of the project")

5. Haga clic en **Crear** para crear la **solución**.

6. Haga doble clic en el archivo **Main.storyboard** en el **Panel de solución** para abrirlo. Esto proporciona una manera de crear visualmente una interfaz de usuario:

    ![](hello-ios-quickstart-images/image7new.png "The iOS Designer")

    Tenga en cuenta que las _clases de tamaño_ están habilitadas de forma predeterminada. Vea la guía [Guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para más información sobre ellos.

7. En el **panel del cuadro de herramientas**, escriba "etiqueta" en la barra de búsqueda y arrastre una **etiqueta** a la superficie de diseño (el área en el centro):

    ![](hello-ios-quickstart-images/image8new.png "Drag a Label onto the design surface the area in the center")

    > [!NOTE]
    > Puede mostrar el **Panel de propiedades** o el **Cuadro de herramientas** en cualquier momento si selecciona **Vista > Paneles**.

8. Seleccione los manipuladores de los *Controles de arrastre* (los círculos que rodean el control) y aumente el ancho de la etiqueta:

    ![](hello-ios-quickstart-images/image9.png "Make the label wider")

9. Con la **Etiqueta** seleccionada en la superficie de diseño, use el **Panel de propiedades** para cambiar la propiedad **Texto** de la **Etiqueta** por "Escriba una Phoneword:"

    ![](hello-ios-quickstart-images/image10.png "Set the label to Enter a Phoneword")

10. Busque "campo de texto" dentro del Cuadro de herramientas y arrastre un **Campo de texto** desde el **Cuadro de herramientas** a la superficie de diseño y colóquelo bajo la **Etiqueta**. Ajuste el ancho hasta que el **Campo de texto** tenga el mismo ancho que la **Etiqueta**:

    ![](hello-ios-quickstart-images/image12new.png "Make the Text Field the same width as the Label")

11. Con el **Campo de texto** seleccionado en la superficie de diseño, cambie la propiedad **Nombre** del **Campo de texto** en la sección Identidad del **Panel de propiedades** por `PhoneNumberText` y cambie la propiedad **Texto** por "1-855-XAMARIN":

    ![](hello-ios-quickstart-images/image13new.png "Change the Title property to 1-855-XAMARIN")

12. Arrastre un **Botón** desde el **Cuadro de herramientas** hasta la superficie de diseño y colóquelo debajo del **Campo de texto**. Ajuste el ancho para que el **Botón** tenga el mismo ancho que el **Campo de texto** y la **Etiqueta**:

    ![](hello-ios-quickstart-images/image14new.png "Adjust the width so the Button is as wide as the Text Field and Label")

13. Con el **Botón** seleccionado en la superficie de diseño, cambie la propiedad **Nombre** en la sección **Identidad** del **Panel de propiedades** a `TranslateButton`. Cambie la propiedad **Título** a "Traducir":

    ![](hello-ios-quickstart-images/image15new.png "Change the Title property to Translate")

14. Repita los dos pasos anteriores y arrastre un **Botón** desde el **Cuadro de herramientas** a la superficie de diseño y colóquelo debajo del primer **Botón**. Ajuste el ancho de modo que el **Botón** sea tan ancho como el primer **Botón**:

    ![](hello-ios-quickstart-images/image16new.png "Adjust the width so the Button is as wide as the first Button")

15. Con el segundo **Botón** seleccionado en la superficie de diseño, cambie la propiedad **Nombre** en la sección **Identidad** del **Panel de propiedades** a `CallButton`. Cambie la propiedad **Título** a "Llamar":

    ![](hello-ios-quickstart-images/image17new.png "Change the Title property to Call")

    Para guardar los cambios, vaya a **Archivo > Guardar** o presione **⌘ + s**.

16. Debe agregarse lógica a la aplicación para convertir números de teléfono de formato alfanumérico a numérico. Agregue un nuevo archivo al proyecto. Para ello, haga clic con el botón derecho en el proyecto **Phoneword_iOS** en el **Panel de solución** y seleccione **Agregar > Nuevo archivo…** , o presione **⌘ + n**:

    ![](hello-ios-quickstart-images/image18.png "Add a new file to the Project")

17. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Clase vacía** y asigne el nombre `PhoneTranslator` al nuevo archivo:

    ![](hello-ios-quickstart-images/image19.png "Select Empty Class and name the new file PhoneTranslator")

18. Esto crea una nueva clase de C#. Quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword_iOS
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
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
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    Guarde el archivo **PhoneTranslator.cs** y ciérrelo.

19. Agregue código para conectar la interfaz de usuario. Para hacer esto, haga doble clic en **ViewController.cs** en el **Panel de solución** para abrirlo:

    ![](hello-ios-quickstart-images/image20new.png "Add code to wire up the user interface")

20. Empiece por la conexión del `TranslateButton`. En la clase **ViewController**, busque el método `ViewDidLoad` y agregue el código siguiente debajo de la llamada a `base.ViewDidLoad()`:

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(
            PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call ", UIControlState.Normal);
            CallButton.Enabled = false;
        } else {
            CallButton.SetTitle ("Call " + translatedNumber,
                UIControlState.Normal);
            CallButton.Enabled = true;
        }
    };
    ```

    Incluya `using Phoneword_iOS;` si el espacio de nombres del archivo es diferente.

21. Agregue código para responder cuando el usuario pulse el segundo botón, que se denomina `CallButton`. Coloque el código siguiente debajo del código para el `TranslateButton` y agregue `using Foundation;` a la parte superior del archivo:

    ```csharp
        CallButton.TouchUpInside += (object sender, EventArgs e) => {
            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl ("tel:" + translatedNumber);

            // ...otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl (url)) {
                var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                PresentViewController (alert, true, null);
            }
        };
    ```

22. Guarde los cambios y compile la aplicación. Para ello, haga clic en **Compilar > Compilar todo** o presione **⌘ + B**.  Si se compila la aplicación, aparecerá un mensaje de correcto en la parte superior del IDE:

    ![](hello-ios-quickstart-images/image21.png "A success message will appear at the top of the IDE")

    Si hay errores, repase los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente.

23. Por último, pruebe la aplicación en el **Simulador de iOS**. En la esquina superior izquierda del IDE, elija **Depurar** en la primera lista desplegable, **iPhone XR iOS 12.0** (u otro simulador disponible) en la segunda lista desplegable y haga clic en **Iniciar** (el botón triangular similar a un botón de reproducción):

    ![](hello-ios-quickstart-images/image27.png "Select a simulator and press start")

    > [!NOTE]
    > En este momento, debido a un requisito de Apple, puede ser necesario tener un certificado de desarrollo o una *identidad de firma* para compilar el código para el dispositivo o el simulador. Siga los pasos de la [guía Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/manual-provisioning.md) para configurar esta opción.

24. Esto iniciará la aplicación dentro del Simulador de iOS:

    ![](hello-ios-quickstart-images/image28.png "The application running inside the iOS Simulator")

    No se admiten llamadas de teléfono en el simulador de iOS. Verá un cuadro de diálogo de alerta cuando intente realizar una llamada:

    ![](hello-ios-quickstart-images/image29.png "The alert dialog when trying to place a call")

::: zone-end
::: zone pivot="windows"

## <a name="visual-studio-walkthrough"></a>Tutorial de Visual Studio

En este tutorial se muestra cómo crear una aplicación llamada Phoneword que convierte un número de teléfono alfanumérico en un número de teléfono numérico.

> [!NOTE]
> En este tutorial se utiliza Visual Studio Enterprise 2017 en una máquina virtual de Windows 10. Su configuración puede diferir, mientras cumpla los requisitos anteriores, pero tenga en cuenta que algunas capturas de pantalla pueden parecer diferentes a las de su instalación.

> [!NOTE]
> Antes de continuar con este tutorial, se debe haber conectado ya a su Mac desde Visual Studio. Esto es porque Xamarin.iOS se basa en las herramientas de Apple para generar e iniciar las aplicaciones y el diseñador de iOS. Para realizar la configuración, siga los pasos de la guía [Emparejar con Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie Visual Studio desde el menú **Inicio**:

    ![](hello-ios-quickstart-images/image001-.png "The Start screen")

    Cree una nueva solución de Xamarin.iOS al seleccionar **Archivo > Nuevo > Proyecto... > Visual C# > iPhone y iPad > Aplicación de iOS (Xamarin)** :

    ![Selección del tipo de proyecto de aplicación iOS (Xamarin)](hello-ios-quickstart-images/image002.w157.png "Selección del tipo de proyecto de aplicación iOS (Xamarin)")

    En el siguiente cuadro de diálogo que aparece, seleccione la plantilla **Aplicación de vista única** y presione **Aceptar** para crear el proyecto:

    ![Selección de una plantilla de proyecto de vista única](hello-ios-quickstart-images/image002-2.w157.png "Selección de una plantilla de proyecto de vista única")

1. Confirme que el icono del agente de Mac Xamarin en la barra de herramientas es verde.

    ![Confirme que el icono de Xamarin Mac Agent en la barra de herramientas es verde.](hello-ios-quickstart-images/vs-image4.png)

    Si no es así, no hay ninguna conexión a su host de compilación de Mac. Siga los pasos descritos en la [guía de configuración](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para conectarse.

1. Abra el archivo **Main.storyboard** en el diseñador de iOS haciendo doble clic en él en el **Explorador de soluciones**:

    ![](hello-ios-quickstart-images/vs-image7.png "The iOS Designer")

1. Abra la pestaña **Cuadro de herramientas**, escriba "etiqueta" en la barra de búsqueda y arrastre una **etiqueta** a la superficie de diseño (el área en el centro):

    ![](hello-ios-quickstart-images/vs-image8.png "Drag a Label onto the design surface the area in the center")

1. A continuación, seleccione los manipuladores de los *Controles de arrastre* y aumente el ancho de la etiqueta:

    ![](hello-ios-quickstart-images/vs-image9.png "Make the label wider")

1. Con la **Etiqueta** seleccionada en la superficie de diseño, use la **Ventana Propiedades** para cambiar la propiedad **Texto** de la **Etiqueta** por "Escriba una Phoneword:"

    ![](hello-ios-quickstart-images/vs-image10.png "Change the Text property of the Label to `Enter a Phoneword`")

    > [!NOTE]
    > Puede mostrar las **Propiedades** o el **Cuadro de herramientas** en cualquier momento si navega al menú **Vista**.

1. Busque "campo de texto" dentro del Cuadro de herramientas y arrastre un **Campo de texto** desde el **Cuadro de herramientas** a la superficie de diseño y colóquelo bajo la **Etiqueta**. Ajuste el ancho hasta que el **Campo de texto** tenga el mismo ancho que la **Etiqueta**:

    ![](hello-ios-quickstart-images/vs-image12.png "Adjust the width until the Text Field is the same width as the Label")

1. Con el **Campo de texto** seleccionado en la superficie de diseño, cambie la propiedad **Nombre** del **Campo de texto** en la sección Identidad de las **Propiedades** por `PhoneNumberText` y cambie la propiedad **Texto** por "1-855-XAMARIN":

    ![](hello-ios-quickstart-images/vs-image13.png "Change the Text property to 1-855-XAMARIN")

1. Arrastre un **Botón** desde el **Cuadro de herramientas** hasta la superficie de diseño y colóquelo debajo del **Campo de texto**. Ajuste el ancho para que el **Botón** tenga el mismo ancho que el **Campo de texto** y la **Etiqueta**:

    ![](hello-ios-quickstart-images/vs-image14.png "Adjust the width so the Button is as wide as the Text Field and Label")

1. Con el **Botón** seleccionado en la superficie de diseño, cambie la propiedad **Nombre** en la sección **Identidad** de las **Propiedades** a `TranslateButton`. Cambie la propiedad **Título** a "Traducir":

    ![](hello-ios-quickstart-images/vs-image15.png "Change the Title property to Translate")

1. Repita los dos pasos anteriores y arrastre un **Botón** desde el **Cuadro de herramientas** a la superficie de diseño y colóquelo debajo del primer **Botón**. Ajuste el ancho de modo que el **Botón** sea tan ancho como el primer **Botón**:

    ![](hello-ios-quickstart-images/vs-image16.png "Adjust the width so the Button is as wide as the first Button")

1. Con el segundo **Botón** seleccionado en la superficie de diseño, cambie la propiedad **Nombre** en la sección **Identidad** de las **Propiedades** a `CallButton`. Cambie la propiedad **Título** a "Llamar":

    ![](hello-ios-quickstart-images/vs-image17.png "Change the Title property to Call")

    Guarde los cambios, seleccionando **Archivo > Guardar todo** o presionando **Ctrl + s**.

1. Agregue código para convertir números de teléfono de formato alfanumérico a numérico. Para hacerlo, agregue un nuevo archivo al proyecto haciendo clic con el botón derecho en el proyecto **Phoneword** en el **Explorador de soluciones** y seleccionando **Agregar > Nuevo elemento...** , o presionando **Ctrl + Mayús + A**:

    ![](hello-ios-quickstart-images/vs-image18.png "Add some code to translate phone numbers from alphanumeric to numeric")

1. En el cuadro de diálogo **Agregar nuevo elemento** (haga clic con el botón derecho en el proyecto, elija Agregar > Nuevo elemento...), seleccione **Apple > Clase** y asigne el nombre `PhoneTranslator` al nuevo archivo:

    ![](hello-ios-quickstart-images/vs-image19.w157.png "Add a new class named PhoneTranslator")

    > [!IMPORTANT]
    > Asegúrese de que selecciona la plantilla "clase" con C# en el icono. De lo contrario, no podrá hacer referencia a esta nueva clase.

1. Esto crea una nueva clase de C#. Quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
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
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    Guarde el archivo **PhoneTranslator.cs** y ciérrelo.

1. Haga doble clic en **ViewController.cs** en el **Explorador de soluciones** para abrirlo y poder agregar lógica para controlar las interacciones con los botones:

    ![](hello-ios-quickstart-images/vs-image20.png "Logic added to handle interactions with the buttons")

1. Empiece por la conexión del `TranslateButton`. En la clase **ViewController**, busque el método `ViewDidLoad`. Agregue lo siguiente al código del botón dentro de `ViewDidLoad`, debajo de la llamada a `base.ViewDidLoad()`:

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {

        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call", UIControlState.Normal);
            CallButton.Enabled = false;
            }
        else {
            CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
            CallButton.Enabled = true;
            }
    };
    ```

    Incluya `using Phoneword;` si el espacio de nombres del archivo es diferente.

1. Agregue código para responder cuando el usuario pulse el segundo botón, que se denomina `CallButton`. Coloque el código siguiente debajo del código para el `TranslateButton` y agregue `using Foundation;` a la parte superior del archivo:

    ```csharp
    CallButton.TouchUpInside += (object sender, EventArgs e) => {
        var url = new NSUrl ("tel:" + translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app,
            // otherwise show an alert dialog

        if (!UIApplication.SharedApplication.OpenUrl (url)) {
                        var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                        alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        PresentViewController (alert, true, null);
                    }
    };
    ```

1. Guarde los cambios y, después, compile la aplicación eligiendo **Compilar > Compilar solución** o presionando **Ctrl + Mayús + B**.  Si se compila la aplicación, aparecerá un mensaje de correcto en la parte inferior del IDE:

    ![](hello-ios-quickstart-images/vs-image21.png "A success message will appear at the bottom of the IDE")

    Si hay errores, repase los pasos anteriores y corrija cualquier error hasta que la aplicación se compile correctamente.

1. Por último, pruebe la aplicación en el **simulador remoto de iOS**. En la barra de herramientas del IDE, elija **Depurar** e **iPhone 8 Plus iOS x.x** en los menús desplegables y pulse **Iniciar** (el triángulo verde que se parece a un botón de reproducción):

    ![](hello-ios-quickstart-images/vs-image27.png "Press Start")

1. Esto iniciará la aplicación dentro del Simulador de iOS:

    ![](hello-ios-quickstart-images/vs-image28.png "The application running inside the iOS Simulator")

    No se admiten llamadas de teléfono en el simulador de iOS. Se mostrará un cuadro de diálogo de alerta cuando intente realizar una llamada:

    ![](hello-ios-quickstart-images/vs-image29.png "An alert dialog will display when trying to place a call")

::: zone-end

Enhorabuena por completar su primera aplicación de Xamarin.iOS.

Ahora es el momento de analizar minuciosamente las herramientas y los conocimientos mostrados en esta guía en [Hello, iOS: análisis detallado](~/ios/get-started/hello-ios/hello-ios-deepdive.md).

## <a name="related-links"></a>Vínculos relacionados

- [Iconos de aplicaciones e imágenes de inicio de Xamarin (ejemplo)](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true)
- [Hola, iOS (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Directrices de la interfaz humana de iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portal de aprovisionamiento de iOS](https://developer.apple.com/ios/manage/overview/index.action)
