---
ms.openlocfilehash: 76eab5a08f5ccc53a4db5370bf996aed75c142e7
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689753"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para completar este tutorial debe tener Visual Studio 2019 (versión más reciente), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada. Además, necesita un equipo Mac emparejado para compilar la aplicación del tutorial en iOS. Para obtener información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **EditorTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **EditorTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, en el proyecto **EditorTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Editor`](xref:Xamarin.Forms.Editor) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) especifica el texto de marcador de posición que se muestra cuando el objeto `Editor` aparece por primera vez. Además, la propiedad [`HeightRequest`](xref:Xamarin.Forms.VisualElement) especifica el alto del elemento `Editor` en unidades independientes de dispositivo.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de un editor en iOS y Android](../images/create-editor.png "Editor que contiene texto de marcador de posición")](../images/create-editor-large.png#lightbox "Editor que contiene texto de marcador de posición")

    > [!NOTE]
    > Mientras que Android indica el alto del objeto [`Editor`](xref:Xamarin.Forms.Editor), iOS no lo hace.

    En Visual Studio, detenga la aplicación.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para completar este tutorial debe tener instalado Visual Studio para Mac (versión más reciente) con compatibilidad con la plataforma de iOS y Android. Además, también necesitará Xcode (versión más reciente). Para obtener más información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md).

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **EditorTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **EditorTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Panel de solución**, en el proyecto **EditorTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Editor`](xref:Xamarin.Forms.Editor) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) especifica el texto de marcador de posición que se muestra cuando el objeto `Editor` aparece por primera vez. Además, la propiedad [`HeightRequest`](xref:Xamarin.Forms.VisualElement) especifica el alto del elemento `Editor` en unidades independientes de dispositivo.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de un editor en iOS y Android](../images/create-editor.png "Editor que contiene texto de marcador de posición")](../images/create-editor-large.png#lightbox "Editor que contiene texto de marcador de posición")

    > [!NOTE]
    > Mientras que Android indica el alto del objeto [`Editor`](xref:Xamarin.Forms.Editor), iOS no lo hace.

    En Visual Studio para Mac, detenga la aplicación.
