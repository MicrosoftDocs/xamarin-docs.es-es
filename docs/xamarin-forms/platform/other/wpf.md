---
title: Configuración de la plataforma WPF
description: Xamarin.Forms tiene compatibilidad con la versión preliminar para la plataforma WPF.
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 948dd586a3b60897531cc96187f288687668f60b
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2021
ms.locfileid: "98609097"
---
# <a name="wpf-platform-setup"></a>Configuración de la plataforma WPF

![Etiqueta de vista previa](~/media/shared/preview.png)

Xamarin.Forms tiene compatibilidad con la versión preliminar de Windows Presentation Foundation (WPF), en .NET Framework y en .NET Core 3. En este artículo se muestra cómo agregar un proyecto de WPF que tiene como destino .NET Framework a una Xamarin.Forms solución.

> [!IMPORTANT]
> Xamarin.Forms la comunidad proporciona compatibilidad con WPF. Para obtener más información, consulte [ Xamarin.Forms compatibilidad con plataformas](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Antes de empezar, cree una nueva Xamarin.Forms solución en Visual Studio 2019 o use una solución existente Xamarin.Forms , por ejemplo, [**BoxViewClock**](/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). Solo puede agregar aplicaciones de WPF a una Xamarin.Forms solución en Windows.

## <a name="add-a-wpf-application"></a>Agregar una aplicación WPF

Siga estas instrucciones para agregar una aplicación de WPF que se ejecutará en los equipos de escritorio de Windows 7, 8 y 10:

1. En Visual Studio 2019, haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones** y elija **Agregar > nuevo proyecto.**...

2. En la ventana **Agregar un nuevo proyecto** , seleccione **C#** en la lista desplegable **idiomas** , seleccione **Windows** en el menú desplegable **plataformas** y seleccione **escritorio** en la lista desplegable tipo de **proyecto** . En la lista de tipos de proyecto, elija **aplicación WPF (.NET Framework)**:

    ![Captura de pantalla que muestra el cuadro de diálogo Agregar un nuevo proyecto con la aplicación W P F seleccionada.](wpf-images/add-project.png "Agregar un nuevo proyecto de WPF")

    Presione el botón **siguiente** .

    > [!NOTE]
    > Xamarin.Forms 4,7 incluye compatibilidad con aplicaciones de WPF que se ejecutan en .NET Core 3.

3. En la ventana **configurar el nuevo proyecto** , escriba un nombre para el proyecto con una extensión de **WPF** , por ejemplo, **BoxViewClock. WPF**. Haga clic en el botón **examinar** , seleccione la carpeta **BoxViewClock** y presione **Seleccionar carpeta** para colocar el proyecto de WPF en el mismo directorio que los demás proyectos de la solución:

    ![Captura de pantalla que muestra el cuadro de diálogo Configurar el nuevo proyecto con valores para el nombre, la ubicación y el marco de trabajo del proyecto.](wpf-images/configure-project.png "Agregar un nuevo proyecto de WPF")

    Presione el botón **crear** para crear el proyecto.

4. En el **Explorador de soluciones**, haga clic con el botón derecho en el nuevo proyecto **BoxViewClock. WPF** y seleccione **administrar paquetes NuGet..**.. Seleccione la pestaña **examinar** y busque **Xamarin.Forms . Plataforma. WPF**:

    ![Seleccionar el paquete NuGet](wpf-images/select-nuget-package.png "Seleccionar el paquete NuGet")

    Seleccione el paquete y haga clic en el botón **instalar** .

5. Haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones** y seleccione **administrar paquetes NuGet para la solución..**.. Seleccione la pestaña **actualizaciones** y, a continuación, seleccione el **Xamarin.Forms** paquete. Seleccione todos los proyectos y actualícelos a la misma Xamarin.Forms versión:

    ![Actualización del paquete NuGet](wpf-images/update-nuget-package.png "Actualización del paquete NuGet")

6. En el proyecto de WPF, haga clic con el botón derecho en **referencias** y seleccione **Agregar referencia..**.. En el cuadro de diálogo **Administrador de referencias** , seleccione **proyectos** a la izquierda y active la casilla situada junto al proyecto **BoxViewClock** :

    ![Referencia al proyecto compartido](wpf-images/reference-shared-project.png "Referencia al proyecto compartido")

    Presione el botón **Aceptar** .

7. Edite el archivo **MainWindow. Xaml** del proyecto de WPF. En la `Window` etiqueta, agregue una declaración de espacio de nombres XML para **Xamarin.Forms .** Ensamblado y espacio de nombres de Platform. WPF:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Ahora cambie la `Window` etiqueta a `wpf:FormsApplicationPage` . Cambie la `Title` configuración al nombre de la aplicación, por ejemplo, **BoxViewClock**. El archivo XAML completado debe tener el siguiente aspecto:

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"            
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>

        </Grid>
    </wpf:FormsApplicationPage>
    ```

8. Edite el archivo **MainWindow.Xaml.CS** del proyecto de WPF. Agregue dos nuevas `using` directivas:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Cambie la clase base de `MainWindow` de `Window` a `FormsApplicationPage` . Después de la `InitializeComponent` llamada, agregue las dos instrucciones siguientes:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    A excepción de los comentarios y `using` las directivas no utilizadas, el archivo **MainWindows.Xaml.CS** completo debe tener el siguiente aspecto:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

9. Haga clic con el botón derecho en el proyecto WPF en el **Explorador de soluciones** y seleccione **establecer como proyecto de inicio**. Presione F5 para ejecutar el programa con el depurador de Visual Studio en el escritorio de Windows:

    ![Reloj de BoxView de WPF](wpf-images/wpf-boxviewclock.png "Reloj de BoxView de WPF" )

## <a name="platform-specifics"></a>Características específicas de la plataforma

Puede determinar la plataforma en la Xamarin.Forms que se ejecuta la aplicación desde código o XAML. Esto le permite cambiar las características del programa cuando se ejecuta en WPF. En el código, compare el valor de `Device.RuntimePlatform` con la `Device.WPF` constante (que es igual a la cadena "WPF"). Si hay una coincidencia, la aplicación se ejecuta en WPF.

En XAML, puede usar la `OnPlatform` etiqueta para seleccionar un valor de propiedad específico de la plataforma:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

## <a name="window-size"></a>Tamaño de la ventana

Puede ajustar el tamaño inicial de la ventana en el archivo **MainWindow. Xaml** de WPF:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Incidencias

Se trata de una versión preliminar, por lo que debe esperar que no todo esté listo para la producción. No todos los paquetes NuGet para Xamarin.Forms están preparados para WPF y es posible que algunas características no funcionen por completo.

## <a name="related-video"></a>Vídeo relacionado

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms vídeo de soporte de WPF 3,0**