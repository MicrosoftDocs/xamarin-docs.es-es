---
ms.openlocfilehash: 93ee0681adcc63fe05b4be88ff67f0aeee3e03ca
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "61384586"
---
Es posible agregar archivos de imagen a proyectos de plataforma y hacer referencia a ellos desde código compartido de Xamarin.Forms. Este método de distribución de imágenes es necesario cuando las imágenes son específicas de la plataforma, por ejemplo, al usar resoluciones diferentes en plataformas diversas, o bien al emplear diseños que difieren ligeramente.

En este ejercicio, modificará la solución **ImageTutorial** para mostrar una imagen local, en lugar de una imagen descargada de un URI. La imagen local es el logotipo de Xamarin. Para descargarla, haga clic en el botón siguiente.

> [!div class="nextstepaction"]
> [Descargar XamarinLogo.png](https://raw.githubusercontent.com/xamarin/xamarin-forms-samples/master/UserInterface/PlatformSpecifics/Droid/Resources/drawable/XamarinLogo.png)

> [!IMPORTANT]
> Para usar una imagen única en todas las plataformas, *es necesario usar el mismo nombre de archivo en todas las plataformas*. Debe ser un nombre de recurso de Android válido, es decir, solo se permiten letras minúsculas, números, el carácter de subrayado y el punto.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En el **Explorador de soluciones**, en el proyecto **ImageTutorial.iOS**, expanda **Catálogos de activos** y haga doble clic en **Activos** para abrirlo. Después, en la pestaña **Assets.xcassets**, haga clic en el botón **Más** y seleccione **Agregar conjunto de imágenes**:

    ![Captura de pantalla de la creación de un nuevo conjunto de imágenes en el catálogo de activos de Visual Studio](../images/vs/new-image-set.png "Nuevo conjunto de imágenes del catálogo de activos")

1. En la pestaña **Assets.xcassets**, seleccione el nuevo conjunto de imágenes. Se mostrará el editor:

    ![Captura de pantalla de un nuevo conjunto de imágenes en el catálogo de activos de Visual Studio](../images/vs/new-image-set-editor.png "Editor de conjunto de imágenes del catálogo de activos")

1. Arrastre **XamarinLogo.png** del sistema de archivos al cuadro **1x** de la categoría **Universal**:

    ![Captura de pantalla del conjunto de imágenes que contiene una imagen en Visual Studio](../images/vs/image-set-with-image.png "Conjunto de imágenes que contiene una imagen")

1. En la pestaña **Assets.xcassets**, haga clic con el botón derecho en el nombre del nuevo conjunto de imágenes y cámbieselo a **XamarinLogo**:

    ![Captura de pantalla del conjunto de imágenes con el nombre cambiado en Visual Studio](../images/vs/rename-image-set.png "Conjunto de imágenes con el nombre cambiado")

    Guárdelo y cierre la pestaña **Assets.xcassets**.

1. En el **Explorador de soluciones**, en el proyecto **ImageTutorial.Android**, expanda la carpeta **Recursos**. Después, arrastre **XamarinLogo.png** del sistema de archivos a la carpeta **drawable**:

    ![Captura de pantalla del archivo de imagen como recurso de Android en Visual Studio](../images/vs/android-resource.png "Archivo de imagen local en la carpeta de recursos de Android")

    > [!NOTE]
    > Visual Studio establecerá automáticamente la acción de compilación para la imagen en **AndroidResource**.

1. En el proyecto **ImageTutorial**, en **MainPage.xaml**, modifique la declaración [`Image`](xref:Xamarin.Forms.Editor) de modo que muestre el archivo local **XamarinLogo**:

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Este código establece la propiedad [`Source`](xref:Xamarin.Forms.Image.Source) en el archivo local que se va a mostrar. La propiedad [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) se establece en 300 unidades independientes del dispositivo en iOS y en 250 unidades independientes del dispositivo en Android. Además, la propiedad [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que la imagen se centrará horizontalmente.

    > [!NOTE]
    > Si se trata de imágenes PNG en iOS, se puede omitir la extensión **.png** del nombre de archivo especificado en la propiedad [`Source`](xref:Xamarin.Forms.Image.Source). En el caso de otros formatos de imagen, la extensión es necesaria.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de una vista de imagen que muestra una imagen local, en iOS y Android](../images/local-file.png "Vista de imagen que muestra una imagen local")](../images/local-file-large.png#lightbox "Vista de imagen que muestra una imagen local")

    Para obtener más información sobre las imágenes locales, vea [Imágenes locales](~/xamarin-forms/user-interface/images.md#local-images) en la guía [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **Panel de solución**, en el proyecto **ImageTutorial.iOS**, haga doble clic en **Assets.xcassets** para abrirlo. Después, en la **Lista de activos**, haga clic con el botón derecho y seleccione **Nuevo conjunto de imágenes**:

    ![Captura de pantalla de la creación de un nuevo conjunto de imágenes en el catálogo de activos de Visual Studio para Mac](../images/vsmac/new-image-set.png "Nuevo conjunto de imágenes del catálogo de activos")

1. En la **Lista de activos**, seleccione el nuevo conjunto de imágenes. Se mostrará el editor:

    ![Captura de pantalla de un nuevo conjunto de imágenes en el catálogo de activos de Visual Studio para Mac](../images/vsmac/new-image-set-editor.png "Editor de conjunto de imágenes del catálogo de activos")

1. Arrastre **XamarinLogo.png** del sistema de archivos al cuadro **1x** de la categoría **Universal**:

    ![Captura de pantalla del conjunto de imágenes que contiene una imagen en Visual Studio para Mac](../images/vsmac/image-set-with-image.png "Conjunto de imágenes que contiene una imagen")

1. En la pestaña **Lista de activos**, haga doble clic con el botón derecho en el nombre del nuevo conjunto de imágenes y cámbieselo a **XamarinLogo**:

    ![Captura de pantalla del conjunto de imágenes con el nombre cambiado en Visual Studio para Mac](../images/vsmac/rename-image-set.png "Conjunto de imágenes con el nombre cambiado")

1. En el **Panel de solución**, en el proyecto **ImageTutorial.Android**, expanda la carpeta **Recursos**. Después, arrastre **XamarinLogo.png** del sistema de archivos a la carpeta **drawable**.

1. En el cuadro de diálogo **Agregar archivo a carpeta**, seleccione **Aceptar**.

    ![Captura de pantalla del archivo de imagen como recurso de Android en Visual Studio para Mac](../images/vsmac/android-resource.png "Archivo de imagen local en la carpeta de recursos de Android")

    > [!NOTE]
    > Visual Studio para Mac establecerá automáticamente la acción de compilación para la imagen en **AndroidResource**.

1. En el proyecto **ImageTutorial**, en **MainPage.xaml**, modifique la declaración [`Image`](xref:Xamarin.Forms.Editor) de modo que muestre el archivo local **XamarinLogo**:

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Este código establece la propiedad [`Source`](xref:Xamarin.Forms.Image.Source) en el archivo local que se va a mostrar. La propiedad [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) se establece en 300 unidades independientes del dispositivo en iOS y en 250 unidades independientes del dispositivo en Android. Además, la propiedad [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que la imagen se centrará horizontalmente.

    > [!NOTE]
    > Si se trata de imágenes PNG en iOS, se puede omitir la extensión **.png** del nombre de archivo especificado en la propiedad [`Source`](xref:Xamarin.Forms.Image.Source). En el caso de otros formatos de imagen, la extensión es necesaria.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de una vista de imagen que muestra una imagen local, en iOS y Android](../images/local-file.png "Vista de imagen que muestra una imagen local")](../images/local-file-large.png#lightbox "Vista de imagen que muestra una imagen local")

    Para obtener más información sobre las imágenes locales, vea [Imágenes locales](~/xamarin-forms/user-interface/images.md#local-images) en la guía [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md).
