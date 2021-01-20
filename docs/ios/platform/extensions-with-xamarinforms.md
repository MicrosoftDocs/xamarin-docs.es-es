---
title: Reutilizar páginas de Xamarin.Forms en una extensión de iOS
description: En este documento se describen las extensiones de iOS y cómo usar las páginas de Xamarin. Forms en ellas. Contiene un tutorial sobre cómo crear una extensión, integrar Xamarin. Forms y representar un ContentPage simple de forma nativa en un proyecto de extensión de iOS.
ms.prod: xamarin
ms.assetid: 50076FFD-3EF6-41CC-BC7E-210DE3958F5B
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 05/13/2020
ms.openlocfilehash: ed7b7bae452db0067b330126315d5b029a08ccab
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2021
ms.locfileid: "98608941"
---
# <a name="reuse-xamarinforms-pages-in-an-ios-extension"></a>Reutilizar páginas de Xamarin.Forms en una extensión de iOS

las extensiones de iOS le permiten personalizar el comportamiento del sistema existente agregando funcionalidad adicional a los [puntos de extensión de iOS y MacOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW2), como acciones de contexto personalizadas, Autorrellenar de contraseñas, filtros de llamadas entrantes, modificadores de contenido de notificación, etc. Xamarin. iOS admite extensiones y [esta guía](./extensions.md) le guiará a través de la creación de una extensión de iOS con las herramientas de Xamarin.

Las extensiones se distribuyen como parte de una aplicación de contenedor y se activan desde un punto de extensión específico en una aplicación host. La aplicación contenedora normalmente es una sencilla aplicación de iOS, que proporciona a un usuario información acerca de la extensión, cómo activarla y usarla. Hay tres enfoques principales para compartir el código entre una extensión y una aplicación contenedora:

1. Proyecto de iOS común.

    Puede colocar todo el código compartido entre el contenedor y la extensión en una biblioteca compartida de iOS y hacer referencia a la biblioteca desde ambos proyectos. Normalmente, la biblioteca compartida contiene UIViewControllers nativos y debe ser una biblioteca de Xamarin. iOS.

1. Vínculos de archivo.

    En algunos casos, la aplicación contenedora proporciona la mayor parte de la funcionalidad mientras que la extensión necesita representar un único `UIViewController` . Con pocos archivos para compartir, es habitual agregar un vínculo de archivo a la aplicación de extensión desde el archivo que se encuentra en la aplicación contenedora.

1. Proyecto común de Xamarin. Forms.

    Si las páginas de la aplicación ya están compartidas con otra plataforma, como Android, mediante el marco de trabajo de Xamarin. Forms, el enfoque común es volver a implementar las páginas necesarias de forma nativa en el proyecto de extensión, ya que la extensión de iOS funciona con las páginas UIViewControllers nativas y no con Xamarin. Forms. Tendrá que realizar pasos adicionales para usar Xamarin. Forms en la extensión de iOS, que se explican a continuación.

## <a name="xamarinforms-in-an-ios-extension-project"></a>Xamarin. Forms en un proyecto de extensión de iOS

La capacidad de usar Xamarin. Forms en un proyecto nativo se proporciona a través de [formularios nativos](~/xamarin-forms/platform/native-forms.md). Permite `ContentPage` Agregar páginas derivadas directamente a proyectos de Xamarin. iOS nativos. El `CreateViewController` método de extensión convierte una instancia de una página de Xamarin. Forms en un nativo `UIViewController` , que podría usarse o modificarse como un controlador normal. Dado que una extensión de iOS es una clase especial de un proyecto de iOS nativo, también puede usar **formas nativas** .

> [!IMPORTANT]
> Existen muchas [limitaciones conocidas](./extensions.md#limitations) para las extensiones de iOS. Aunque puede usar Xamarin. Forms en una extensión de iOS, debe hacerlo cuidadosamente, supervisando el uso de memoria y el tiempo de inicio. De lo contrario, iOS podría finalizar la extensión sin ningún modo de controlar esto correctamente.

## <a name="walkthrough"></a>Tutorial

En este tutorial, va a crear una aplicación de Xamarin. Forms, una extensión de Xamarin. iOS y a reutilizar el código compartido en el proyecto de extensión:

1. Abra Visual Studio para Mac, cree un nuevo proyecto de Xamarin. Forms con la plantilla **aplicación de formularios en blanco** y asígnele el nombre **FormsShareExtension**:

    ![Crear proyecto](extensions-xf-images/1.walkthrough-createproject.png)

1. En **FormsShareExtension/mainpage. Xaml**, reemplace el contenido por el siguiente diseño:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage
        x:Class="FormsShareExtension.MainPage"
        xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:d="http://xamarin.com/schemas/2014/forms/design"
        xmlns:local="clr-namespace:FormsShareExtension"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        x:DataType="local:MainPageViewModel"
        BackgroundColor="Orange"
        mc:Ignorable="d">
        <ContentPage.BindingContext>
            <local:MainPageViewModel Message="Hello from Xamarin.Forms!" />
        </ContentPage.BindingContext>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <Label
                Margin="20"
                Text="{Binding Message}"
                VerticalOptions="CenterAndExpand" />
            <Button Command="{Binding DoCommand}" Text="Do the job!" />
        </StackLayout>
    </ContentPage>
    ```

1. Agregue una nueva clase denominada **MainPageViewMode** al proyecto **FormsShareExtension** y reemplace el contenido de la clase por el código siguiente:

    ```csharp
    using System;
    using System.ComponentModel;
    using System.Windows.Input;
    using Xamarin.Forms;

    namespace FormsShareExtension
    {
        public class MainPageViewModel : INotifyPropertyChanged
        {
            public event PropertyChangedEventHandler PropertyChanged;

            private string _message;
            public string Message
            {
                get { return _message; }
                set
                {
                    if (_message != value)
                    {
                        _message = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Message)));
                    }
                }
            }

            private ICommand _doCommand;
            public ICommand DoCommand
            {
                get { return _doCommand; }
                set
                {
                    if(_doCommand != value)
                    {
                        _doCommand = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(DoCommand)));
                    }
                }
            }

            public MainPageViewModel()
            {
                DoCommand = new Command(OnDoCommandExecuted);
            }

            private void OnDoCommandExecuted(object state)
            {
                Message = $"Job {Environment.TickCount} has been completed!";
            }
        }
    }
    ```

    El código se comparte entre todas las plataformas y también lo usará una extensión de iOS.

1. En el panel de solución, haga clic con el botón derecho en la solución, seleccione **agregar > nuevo proyecto >** la extensión de > de iOS > extensión de acción, asígnele el nombre **OnAction** y presione **crear**:

    ![Captura de pantalla que muestra la opción elegir una plantilla con la acción seleccionada.](extensions-xf-images/2.walkthrough-createextension.png)

1. Para usar Xamarin. Forms en la extensión de iOS y el código compartido, debe agregar las referencias necesarias:

    - Haga clic con el botón derecho en la extensión de iOS, seleccione **referencias > Agregar referencia > proyectos > FormsShareExtension** y presione **Aceptar**.

    - Haga clic con el botón derecho en la extensión de iOS, seleccione **paquetes > administrar paquetes NuGet... > Xamarin. Forms**  y presione **Agregar paquete**.

1. Expanda el proyecto de extensión y modifique un punto de entrada para inicializar Xamarin. Forms y crear páginas. Según los requisitos de iOS, una extensión debe definir el punto de entrada en **info. plist** como `NSExtensionMainStoryboard` o `NSExtensionPrincipalClass` . Una vez que se activa el punto de entrada, en este caso es el `ActionViewController.ViewDidLoad` método, se puede crear una instancia de una página de Xamarin. Forms y mostrarla a un usuario. Por lo tanto, abra el punto de entrada y reemplace el `ViewDidLoad` método por la siguiente implementación:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        // Initialize Xamarin.Forms framework
        global::Xamarin.Forms.Forms.Init();
        // Create an instance of XF page with associated View Model
        var xfPage = new MainPage();
        var viewModel = (MainPageViewModel)xfPage.BindingContext;
        viewModel.Message = "Welcome to XF Page created from an iOS Extension";
        // Override the behavior to complete the execution of the Extension when a user press the button
        viewModel.DoCommand = new Command(() => DoneClicked(this));
        // Convert XF page to a native UIViewController which can be consumed by the iOS Extension
        var newController = xfPage.CreateViewController();
        // Present new view controller as a regular view controller
        this.PresentModalViewController(newController, false);
    }
    ```

    `MainPage`Se crea una instancia de con un constructor estándar y antes de poder utilizarlo en la extensión, conviértalo en un nativo mediante `UIViewController` el método de `CreateViewController` extensión. 
    
    Compile y ejecute la aplicación:

    ![Captura de pantalla que muestra un mensaje Hello from Xamarin DOT Forms en un dispositivo móvil.](extensions-xf-images/3.walkthrough-runapp.png)

    Para activar la extensión, navegue hasta el explorador Safari, escriba cualquier dirección web, por ejemplo, [Microsoft.com](https://microsoft.com), presione navegar y, a continuación, presione el icono **compartir** situado en la parte inferior de la página para ver las extensiones de acción disponibles. En la lista de extensiones disponibles, seleccione la extensión **OnAction** ; para ello, puntee en ella:

    ![Captura de pantalla que muestra una página de más información sobre Microsoft Teams con el icono de uso compartido resaltado en un dispositivo móvil.](extensions-xf-images/4.walkthrough-run1.png) ![Captura de pantalla que muestra una página principal oficial con una acción resaltada en un dispositivo móvil.](extensions-xf-images/5.walkthrough-run2.png) ![Captura de pantalla que muestra una página de bienvenida a X F creada a partir de un mensaje de extensión i O S en un dispositivo móvil.](extensions-xf-images/6.walkthrough-run3.png)

    La extensión se activa y se muestra al usuario la página de Xamarin. Forms. Todos los enlaces y comandos funcionan como en la aplicación contenedora.

1. El controlador de vista de punto de entrada original es visible porque iOS lo crea y activa. Para solucionarlo, cambie el estilo de Presentación modal a `UIModalPresentationStyle.FullScreen` para el nuevo controlador; para ello, agregue el código siguiente justo antes de la `PresentModalViewController` llamada:

    ```csharp
    newController.ModalPresentationStyle = UIModalPresentationStyle.FullScreen;
    ```

    Compilar y ejecutar en el simulador de iOS o en un dispositivo:

    ![Xamarin. Forms en la extensión de iOS](extensions-xf-images/7.walkthrough-result.png)

    > [!IMPORTANT]
    > Para la compilación del dispositivo, asegúrese de usar la configuración de compilación adecuada y la configuración de **lanzamiento** como [se describe aquí](~/iOS/platform/extensions.md#debug-and-release-versions-of-extensions).

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de iOS en Xamarin.iOS](~/iOS/platform/extensions.md)
- [Xamarin. Forms en proyectos nativos de Xamarin](~/xamarin-forms/platform/native-forms.md)
- [Optimizar la eficacia y el rendimiento de una extensión de aplicación de iOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionCreation.html#//apple_ref/doc/uid/TP40014214-CH5-SW7)
- [Código fuente de ejemplo](https://github.com/xamcat/xamarin-forms-ios-extension)