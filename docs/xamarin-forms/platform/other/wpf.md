---
title: Configuración de la plataforma WPF
description: Xamarin.Forms ahora tiene compatibilidad con la vista previa para la plataforma WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: c9ec9fec2391d7d7a24f97f2ec20208a7d69dbc1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992371"
---
# <a name="wpf-platform-setup"></a>Configuración de la plataforma WPF

![Vista previa](~/media/shared/preview.png)

Xamarin.Forms ahora tiene compatibilidad con la vista previa para Windows Presentation Foundation (WPF). En este artículo se muestra cómo agregar un WPFWPF proyecto a una solución de Xamarin.Forms.

> [!IMPORTANT]
> La comunidad proporciona compatibilidad con Xamarin.Forms para WPFWPF. Para obtener más información, vea [Compatibilidad con la plataforma Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Antes de empezar, cree una nueva solución de Xamarin.Forms en Visual Studio 2019 o use una solución existente de Xamarin.Forms, por ejemplo, [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). Solo puede agregar aplicaciones WPF a una solución de Xamarin.Forms en Windows.

## <a name="add-a-wpf-application"></a>Agregar una aplicación WPF

Siga estas instrucciones para agregar una aplicación WPF que se ejecutará en los escritorios de Windows 7, 8 y 10:

1. En Visual Studio 2019, haga clic con el botón secundario en el nombre de la solución en el **Explorador** de soluciones y elija **Agregar > nuevo proyecto...**.

2. En la ventana **Agregar un nuevo proyecto,** seleccione **C-** en el menú desplegable **Idiomas,** seleccione **Windows** en el menú desplegable **Plataformas** y seleccione **Escritorio** en el menú desplegable Tipo de **proyecto.** En la lista de tipos de proyecto, elija **WPF App (.NET Framework):**

    ![Agregar un nuevo proyecto WPF](wpf-images/add-project.png "Agregar un nuevo proyecto WPF")

    Pulse el botón **Siguiente.**

3. En la ventana Configurar el **nuevo proyecto,** escriba un nombre para el proyecto con una extensión **WPF,** por ejemplo, **BoxViewClock.WPF**. Haga clic en el botón **Examinar,** seleccione la carpeta **BoxViewClock** y presione **Seleccionar carpeta** para colocar el proyecto WPF en el mismo directorio que los demás proyectos de la solución:

    ![Agregar un nuevo proyecto WPF](wpf-images/configure-project.png "Agregar un nuevo proyecto WPF")

    Pulse el botón **Crear** para crear el proyecto.

4. En el **Explorador**de soluciones , haga clic con el botón derecho en el nuevo proyecto **BoxViewClock.WPF** y seleccione **Administrar paquetes NuGet...**. Seleccione la pestaña **Examinar** y busque **Xamarin.Forms.Platform.WPF:**

    ![Seleccione el paquete NuGet](wpf-images/select-nuget-package.png "Seleccione el paquete NuGet")

    Seleccione el paquete y haga clic en el botón **Instalar.**

5. Haga clic con el botón derecho en el nombre de la solución en el **Explorador** de soluciones y seleccione **Administrar paquetes NuGet para solución...**. Seleccione el **actualizaciones** pestaña y, a continuación, seleccione el **Paquete Xamarin.Forms.** Seleccione todos los proyectos y actualícelos a la misma versión de Xamarin.Forms:

    ![Actualizar el paquete NuGet](wpf-images/update-nuget-package.png "Actualizar el paquete NuGet")

6. En el proyecto WPF, haga clic con el botón derecho en **Referencias** y seleccione **Agregar referencia...**. En el cuadro de diálogo Administrador de **referencias,** seleccione **Proyectos** a la izquierda y marque la casilla de verificación adyacente al proyecto **BoxViewClock:**

    ![Hacer referencia al proyecto compartido](wpf-images/reference-shared-project.png "Hacer referencia al proyecto compartido")

    Pulse el botón **OK.**

7. Edite el archivo **MainWindow.xaml** del proyecto WPF. En `Window` la etiqueta, agregue una declaración de espacio de nombres XML para el ensamblado y espacio de **nombres Xamarin.Forms.Platform.WPF:**

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Ahora cambie `Window` la `wpf:FormsApplicationPage`etiqueta a . Cambie `Title` la configuración al nombre de la aplicación, por ejemplo, **BoxViewClock**. El archivo XAML completado debería tener este aspecto:

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

8. Edite el archivo **MainWindow.xaml.cs** del proyecto WPFWPF. Agregue dos `using` nuevas directivas:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Cambie la clase `MainWindow` `Window` base `FormsApplicationPage`de de a . Después `InitializeComponent` de la llamada, agregue las dos instrucciones siguientes:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    Excepto por los `using` comentarios y las directivas no utilizadas, el archivo **de MainWindows.xaml.cs** completo debería tener este aspecto:

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

9. Haga clic con el botón secundario en el proyecto WPF en el **Explorador** de soluciones y seleccione **Establecer como proyecto**de inicio . Presione F5 para ejecutar el programa con el depurador de Visual Studio en el escritorio de Windows:

    ![RELOJ WPF BoxView](wpf-images/wpf-boxviewclock.png "RELOJ WPF BoxView" )

## <a name="platform-specifics"></a>Detalles de la plataforma

Puede determinar en qué plataforma se ejecuta la aplicación Xamarin.Forms desde código o XAML. Esto le permite cambiar las características del programa cuando se ejecuta en WPFWPF. En el código, `Device.RuntimePlatform` compare `Device.WPF` el valor de con la constante (que es igual a la cadena "WPF"). Si hay una coincidencia, la aplicación se ejecuta en WPFWPF.

En XAML, puedes `OnPlatform` usar la etiqueta para seleccionar un valor de propiedad específico de la plataforma:

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

Puede ajustar el tamaño inicial de la ventana en el archivo **MainWindow.xaml** de WPF:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Issues

Esta es una vista previa, por lo que debe esperar que no todo esté listo para la producción. No todos los paquetes NuGet para Xamarin.Forms están listos para WPFWPF y algunas características podrían no funcionar completamente.

## <a name="related-video"></a>Vídeo relacionado

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 WPFWPF admite vídeo**
