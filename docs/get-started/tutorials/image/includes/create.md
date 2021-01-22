---
ms.openlocfilehash: 11427616ce20e47fb2b4d87c62098ececd3ed972
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689642"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para completar este tutorial debe tener Visual Studio 2019 (versión más reciente), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada. Además, necesita un equipo Mac emparejado para compilar la aplicación del tutorial en iOS. Para obtener información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **ImageTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **ImageTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, en el proyecto **ImageTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Image`](xref:Xamarin.Forms.Image) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Image.Source`](xref:Xamarin.Forms.Image.Source) especifica la imagen que se mostrará mediante un URI. La propiedad [`Image.Source`](xref:Xamarin.Forms.Image.Source) es del tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), que permite que las imágenes se obtengan de archivos, URI o recursos. Para obtener más información, vea [Mostrar imágenes](~/xamarin-forms/user-interface/images.md#display-images) en la guía [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    La propiedad [`HeightRequest`](xref:Xamarin.Forms.VisualElement) especifica el alto del elemento `Image` en unidades independientes de dispositivo.

    > [!NOTE]
    > No es necesario establecer la propiedad [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) en este ejemplo. Esto se debe a que, de forma predeterminada, el elemento [`Image`](xref:Xamarin.Forms.Image) mantiene la relación de aspecto de la imagen.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de una imagen en iOS y Android](../images/create-image.png "Vista de imagen en la que se muestra una imagen")](../images/create-image-large.png#lightbox "Vista de imagen en la que se muestra una imagen")

    > [!NOTE]
    > La vista [`Image`](xref:Xamarin.Forms.Image) almacena en caché automáticamente las imágenes descargadas durante un período de 24 horas. Para obtener más información, vea [Almacenamiento en caché de la imagen descargada](~/xamarin-forms/user-interface/images.md#downloaded-image-caching) en la guía [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para completar este tutorial debe tener instalado Visual Studio para Mac (versión más reciente) con compatibilidad con la plataforma de iOS y Android. Además, también necesitará Xcode (versión más reciente). Para obtener más información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md).

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **ImageTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **ImageTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Panel de solución**, en el proyecto **ImageTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Image`](xref:Xamarin.Forms.Image) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Image.Source`](xref:Xamarin.Forms.Image.Source) especifica la imagen que se mostrará mediante un URI. La propiedad [`Image.Source`](xref:Xamarin.Forms.Image.Source) es del tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), que permite que las imágenes se obtengan de archivos, URI o recursos. Para obtener más información, vea [Mostrar imágenes](~/xamarin-forms/user-interface/images.md#display-images) en la guía [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    La propiedad [`HeightRequest`](xref:Xamarin.Forms.VisualElement) especifica el alto del elemento `Image` en unidades independientes de dispositivo.

    > [!NOTE]
    > No es necesario establecer la propiedad [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) en este ejemplo. Esto se debe a que, de forma predeterminada, el elemento [`Image`](xref:Xamarin.Forms.Image) mantiene la relación de aspecto de la imagen.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de una imagen en iOS y Android](../images/create-image.png "Vista de imagen en la que se muestra una imagen")](../images/create-image-large.png#lightbox "Vista de imagen en la que se muestra una imagen")

    > [!NOTE]
    > La vista [`Image`](xref:Xamarin.Forms.Image) almacena en caché automáticamente las imágenes descargadas durante un período de 24 horas. Para obtener más información, vea [Almacenamiento en caché de la imagen descargada](~/xamarin-forms/user-interface/images.md#downloaded-image-caching) en la guía [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md).
