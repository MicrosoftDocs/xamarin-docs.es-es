---
title: Configuración de la plataforma WPF
description: Xamarin. Forms ahora tiene compatibilidad con versión preliminar para la plataforma WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: 38d9b42b3a29ea46d05a1d1cc4e38641d2445786
ms.sourcegitcommit: 4cf434b126eb7df6b2fd9bb1d71613bf2b6aac0e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2019
ms.locfileid: "71997172"
---
# <a name="wpf-platform-setup"></a>Configuración de la plataforma WPF

![Vista previa](~/media/shared/preview.png)

Xamarin. Forms ahora tiene compatibilidad con vista previa para el Windows Presentation Foundation (WPF). En este artículo se muestra cómo agregar un proyecto de WPF a una solución de Xamarin. Forms.

> [!IMPORTANT]
> La comunidad proporciona compatibilidad con Xamarin. Forms para WPF. Para obtener más información, consulte [compatibilidad con la plataforma de Xamarin. Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Antes de empezar, cree una nueva solución de Xamarin. Forms en Visual Studio 2019, o use una solución de Xamarin. Forms existente, por ejemplo, [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). Solo puede agregar aplicaciones de WPF a una solución de Xamarin. Forms en Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Agregar un proyecto de WPF a una aplicación de Xamarin. Forms con Xamarin. University

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Vídeo de soporte de Xamarin. Forms 3,0 WPF**

## <a name="adding-a-wpf-app"></a>Agregar una aplicación WPF

Siga estas instrucciones para agregar una aplicación de WPF que se ejecutará en los equipos de escritorio de Windows 7, 8 y 10:

1. En Visual Studio 2019, haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones** y elija **Agregar > nuevo proyecto.** ...

2. En la ventana **nuevo proyecto** , en la parte izquierda, seleccione **Visual C#**  y **escritorio clásico de Windows**. En la lista de tipos de proyecto, elija **aplicación WPF (.NET Framework)** .

3. Escriba un nombre para el proyecto con una extensión de **WPF** , por ejemplo, **BoxViewClock. WPF**. Haga clic en el botón **examinar** , seleccione la carpeta **BoxViewClock** y, a continuación, presione **Seleccionar carpeta**. Esto colocará el proyecto de WPF en el mismo directorio que los demás proyectos de la solución.

    ![Agregar un nuevo proyecto de WPF](wpf-images/add-new-project.png "Agregar un nuevo proyecto de WPF")

    Haga clic en Aceptar para crear el proyecto.

4. En el **Explorador de soluciones**, haga clic con el botón derecho en el nuevo proyecto **BoxViewClock. WPF** y seleccione **administrar paquetes NuGet**. Seleccione la pestaña **examinar** , haga clic en la casilla **incluir versión preliminar** y busque **Xamarin. Forms**.

    ![Seleccionar el paquete Nuget](wpf-images/select-nuget-package.png "seleccionar el paquete Nuget")

    Seleccione ese paquete y haga clic en el botón **instalar** .

5. Ahora busque el paquete **Xamarin. Forms. Platform. WPF** e instálelo. Asegúrese de que el paquete proviene de Microsoft.

6. Haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones** y seleccione **administrar paquetes NuGet para la solución**. Seleccione la pestaña **Actualizar** y el paquete **Xamarin. Forms** . Seleccione todos los proyectos y actualícelos en la misma versión de Xamarin. Forms:

    ![Actualización del paquete Nuget](wpf-images/update-nuget-package.png "actualización del paquete Nuget")

7. En el proyecto de WPF, haga clic con el botón derecho en **referencias**. En el cuadro de diálogo **Administrador de referencias** , seleccione **proyectos** a la izquierda y active la casilla situada junto al proyecto **BoxViewClock** :

    ![Referencia al proyecto compartido](wpf-images/reference-shared-project.png "Referencia del proyecto compartido")

8. Edite el archivo **MainWindow. Xaml** del proyecto de WPF. En la etiqueta `Window`, agregue una declaración de espacio de nombres XML para el ensamblado y el espacio de nombres de **Xamarin. Forms. Platform. WPF** :

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Ahora cambie la etiqueta `Window` a `wpf:FormsApplicationPage`. Cambie el valor de `Title` al nombre de la aplicación, por ejemplo, **BoxViewClock**. El archivo XAML completado debe tener el siguiente aspecto:

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>

        </Grid>
    </wpf:FormsApplicationPage>
    ```

9. Edite el archivo **MainWindow.Xaml.CS** del proyecto de WPF. Agregue dos nuevas directivas `using`:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Cambie la clase base de `MainWindow` de `Window` a `FormsApplicationPage`. Después de la llamada `InitializeComponent`, agregue las dos instrucciones siguientes:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    Excepto en el caso de los comentarios y las directivas `using` sin usar, el archivo **MainWindows.Xaml.CS** completo debe tener el siguiente aspecto:

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

10. Haga clic con el botón derecho en el proyecto WPF en el **Explorador de soluciones** y seleccione **establecer como proyecto de inicio**. Presione F5 para ejecutar el programa con el depurador de Visual Studio en el escritorio de Windows:

    ![BoxView de WPF reloj]de(wpf-images/wpf-boxviewclock.png "BoxView de WPF" )

## <a name="next-steps"></a>Pasos siguientes

### <a name="platform-specifics"></a>Funcionalidad específica de plataforma

Puede determinar la plataforma en la que se ejecuta la aplicación de Xamarin. Forms desde código o XAML. Esto le permite cambiar las características del programa cuando se ejecuta en WPF. En el código, compare el valor de `Device.RuntimePlatform` con la constante `Device.WPF` (que es igual a la cadena "WPF"). Si hay una coincidencia, la aplicación se ejecuta en WPF.

En XAML, puede usar la etiqueta `OnPlatform` para seleccionar un valor de propiedad específico de la plataforma:

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

### <a name="window-size"></a>Tamaño de ventana

Puede ajustar el tamaño inicial de la ventana en el archivo **MainWindow. Xaml** de WPF:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemas

Se trata de una versión preliminar, por lo que debe esperar que no todo esté listo para la producción. No todos los paquetes NuGet de Xamarin. Forms están listos para WPF y es posible que algunas características no funcionen por completo.
