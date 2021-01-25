---
ms.openlocfilehash: 623cf26608501f541725a93bc48a8e0f66914d39
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690198"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para completar este tutorial debe tener Visual Studio 2019 (versión más reciente), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada. Además, necesita un equipo Mac emparejado para compilar la aplicación del tutorial en iOS. Para obtener información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **ButtonTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **ButtonTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, en el proyecto **ButtonTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>    
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ButtonTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
           <Button Text="Click me" />
       </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Button`](xref:Xamarin.Forms.Button) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifica el texto que aparece en el elemento `Button`.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de un botón en iOS y Android](../images/create-button.png "Botón que contiene texto")](../images/create-button-large.png#lightbox "Botón que contiene texto")

    Tenga en cuenta que, de forma predeterminada un objeto [`Button`](xref:Xamarin.Forms.Button) tiende a ocupar todo el espacio del que dispone; en este caso, el ancho completo de su elemento primario (el objeto [`StackLayout`](xref:Xamarin.Forms.StackLayout)).

    En Visual Studio, detenga la aplicación.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para completar este tutorial debe tener instalado Visual Studio para Mac (versión más reciente) con compatibilidad con la plataforma de iOS y Android. Además, también necesitará Xcode (versión más reciente). Para obtener más información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md).

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **ButtonTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **ButtonTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Panel de solución**, en el proyecto **ButtonTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ButtonTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
           <Button Text="Click me" />
       </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Button`](xref:Xamarin.Forms.Button) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifica el texto que aparece en el elemento `Button`.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de un botón en iOS y Android](../images/create-button.png "Botón que contiene texto")](../images/create-button-large.png#lightbox "Botón que contiene texto")

    Tenga en cuenta que, de forma predeterminada un objeto [`Button`](xref:Xamarin.Forms.Button) tiende a ocupar todo el espacio del que dispone; en este caso, el ancho completo de su elemento primario (el objeto [`StackLayout`](xref:Xamarin.Forms.StackLayout)).

    En Visual Studio para Mac, detenga la aplicación.
