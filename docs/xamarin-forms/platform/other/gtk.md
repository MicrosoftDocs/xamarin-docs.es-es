---
title: 'Configuración de la plataforma GTK #'
description: 'Xamarin. Forms ahora tiene compatibilidad con vista previa para la plataforma GTK #'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: 56075949a5b5c01873af3ff79a4cf8f6cefcb142
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644554"
---
# <a name="gtk-platform-setup"></a>Configuración de la plataforma GTK #

![Vista previa](~/media/shared/preview.png)

Xamarin. Forms ahora tiene compatibilidad con vista previa para aplicaciones GTK #. GTK # es un kit de herramientas de interfaz gráfica de usuario que vincula los GTK + Toolkit y varias bibliotecas de GNOME, lo que permite el desarrollo de aplicaciones de gráficos de GNOME totalmente nativas con mono y .NET. En este artículo se muestra cómo agregar un proyecto de GTK # a una solución de Xamarin. Forms.

Antes de empezar, cree una nueva solución de Xamarin. Forms o use una solución de Xamarin. Forms existente, por ejemplo, [**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife).

> [!NOTE]
> Aunque este artículo se centra en agregar una aplicación de GTK# a una solución de Xamarin. Forms en VS2017 y Visual Studio para Mac, también se puede realizar en [MonoDevelop](http://www.monodevelop.com/) para Linux.

## <a name="adding-a-gtk-app"></a>Agregar una aplicación GTK #

GTK # para macOS y Linux se instala como parte de [mono](https://www.mono-project.com/download/stable/). GTK # para .NET se puede instalar en Windows con el [instalador GTK #](https://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Siga estas instrucciones para agregar una aplicación GTK # que se ejecutará en el escritorio de Windows:

1. En Visual Studio 2019, haga clic con el botón derecho en el nombre de la solución en **Explorador de soluciones** y elija **Agregar > nuevo proyecto.** ...

2. En la ventana **nuevo proyecto** , en la parte izquierda, seleccione **Visual C#**  y **escritorio clásico de Windows**. En la lista de tipos de proyecto, elija **biblioteca de clases (.NET Framework)** y asegúrese de que la lista desplegable **marco** esté establecida en un mínimo de .NET Framework 4,7.

3. Escriba un nombre para el proyecto con una extensión **GTK** , por ejemplo **GameOfLife. GTK**. Haga clic en el botón **examinar** , seleccione la carpeta que contiene los otros proyectos de la plataforma y presione **Seleccionar carpeta**. Esto colocará el proyecto GTK en el mismo directorio que los demás proyectos de la solución.

    ![Agregar un nuevo proyecto GTK](gtk-images/win/add-new-project.png "Agregar un nuevo proyecto GTK")

    Presione el botón **Aceptar** para crear el proyecto.

4. En el **Explorador de soluciones**, haga clic con el botón derecho en el nuevo proyecto GTK y seleccione **administrar paquetes NuGet**. Seleccione la pestaña **examinar** y busque **Xamarin. Forms** 3,0 o superior.

    ![Seleccione el paquete de NuGet de Xamarin. Forms](gtk-images/win/select-forms-nuget-package.png "Seleccione el paquete de NuGet de Xamarin. Forms")

    Seleccione el paquete y haga clic en el botón **instalar** .

5. Ahora busque el paquete **Xamarin. Forms. Platform. GTK** 3,0 o superior.

    ![Seleccione el paquete de NuGet Xamarin. Forms. Platform. GTK] . (gtk-images/win/select-forms-platform-nuget-package.png "Seleccione el paquete de NuGet Xamarin. Forms. Platform. GTK") .

    Seleccione el paquete y haga clic en el botón **instalar** .

6. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre de la solución y seleccione **administrar paquetes NuGet para la solución**. Seleccione la pestaña **Actualizar** y el paquete **Xamarin. Forms** . Seleccione todos los proyectos y actualícelos en la misma versión de Xamarin. Forms que usa el proyecto GTK.

7. En el **Explorador de soluciones**, haga clic con el botón derecho en **referencias** en el proyecto GTK. En el cuadro de diálogo **Administrador de referencias** , seleccione **proyectos** a la izquierda y active la casilla adyacente al .net Standard o proyecto compartido:

    ![Referencia al proyecto compartido](gtk-images/win/reference-shared-project.png "Referencia al proyecto compartido")

8. En el cuadro de diálogo **Administrador de referencias** , presione el botón **examinar** y vaya a la carpeta c:\Archivos de programa **(x86) \GtkSharp\2.12\lib** y seleccione **ATK-Sharp. dll**, **GDK-Sharp. dll**, **Glade-Sharp. dll.**  **archivos glib-Sharp. dll**, **GTK-dotnet. dll**, **gtk-sharp. dll** .

    ![Referencia a las bibliotecas de GTK #](gtk-images/win/reference-gtk-libraries.png "Referencia a las bibliotecas de GTK #")

    Presione el botón **Aceptar** para agregar las referencias.

9. En el proyecto GTK, cambie el nombre de **Class1.CS** a **Program.CS**.

10. En el proyecto GTK, edite el archivo **Program.CS** para que sea similar al código siguiente:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Este código inicializa GTK # y Xamarin. Forms, crea una ventana de la aplicación y ejecuta la aplicación.

11. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto GTK y seleccione **propiedades**.

12. En la ventana **propiedades** , seleccione la pestaña **aplicación** y cambie la lista desplegable **tipo de salida** a **aplicación Windows**.

    ![Cambiar el tipo de salida del proyecto](gtk-images/win/change-project-output-type.png "Cambiar el tipo de salida del proyecto")

13. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto GTK y seleccione **establecer como proyecto de inicio**. Presione F5 para ejecutar el programa con el depurador de Visual Studio en el escritorio de Windows:

    ![Juego de vida de GTK #](gtk-images/win/gtk-gameoflife.png "Juego de vida de GTK #")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Siga estas instrucciones para agregar una aplicación GTK # que se ejecutará en el escritorio Mac:

1. En Visual Studio para Mac, haga clic con el botón derecho en la solución de Xamarin. Forms y elija **agregar > agregar nuevo proyecto..** ..

2. En la ventana **nuevo proyecto** , elija **otro proyecto > .net > GTK # 2,0** y presione **siguiente**.

3. Escriba un nombre para el proyecto con una extensión **GTK** , por ejemplo **GameOfLife. GTK**, y presione **crear**.

4. En el **Panel de solución**, haga clic con el botón derecho en **paquetes > agregar paquetes...** para el proyecto GTK y agregue el paquete NuGet de la versión preliminar de Xamarin. Forms 3,0 o superior.

    ![Seleccione el paquete de NuGet de Xamarin. Forms](gtk-images/mac/select-forms-nuget-package.png "Seleccione el paquete de NuGet de Xamarin. Forms")

5. En el **Panel de solución**, haga clic con el botón derecho en **paquetes > agregar paquetes...** para el proyecto GTK y agregue el paquete NuGet de la versión preliminar de Xamarin. Forms. Platform. GTK 3,0 o superior.

    ![Seleccione el paquete de NuGet Xamarin. Forms. Platform. GTK] . (gtk-images/mac/select-forms-platform-nuget-package.png "Seleccione el paquete de NuGet Xamarin. Forms. Platform. GTK") .

6. Actualice los otros proyectos de la plataforma para usar la misma versión de Xamarin. Forms que usa el proyecto GTK.

7. En el **Panel de solución**, haga clic con el botón derecho en **referencias > Editar referencias...** para el proyecto GTK y agregue una referencia al proyecto de Xamarin. Forms (ya sea .net Standard o proyecto compartido).

    ![Referencia al proyecto compartido](gtk-images/mac/reference-shared-project.png "Referencia al proyecto compartido")

8. Edite el archivo **Program.CS** del proyecto GTK para que sea similar al código siguiente:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Este código inicializa GTK # y Xamarin. Forms, crea una ventana de la aplicación y ejecuta la aplicación.

9. En el **Panel de solución**, haga clic con el botón derecho en el proyecto GTK y seleccione **establecer como proyecto de inicio**.

10. En la barra de herramientas Visual Studio para Mac, presione el botón **Inicio** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación.

    ![Juego de vida de GTK #](gtk-images/mac/gtk-gameoflife.png "Juego de vida de GTK #")

-----

## <a name="next-steps"></a>Pasos siguientes

### <a name="platform-specifics"></a>Funcionalidad específica de plataforma

Puede determinar la plataforma en la que se ejecuta la aplicación de Xamarin. Forms desde XAML o desde código. Esto le permite cambiar las características del programa cuando se ejecuta en GTK #. En el código, compare el valor `Device.RuntimePlatform` de con `Device.GTK` la constante (que es igual a la cadena "GTK"). Si hay una coincidencia, la aplicación se ejecuta en GTK #.

En XAML, puede usar la `OnPlatform` etiqueta para seleccionar un valor de propiedad específico de la plataforma:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>Icono de aplicación

Puede establecer el icono de la aplicación en el inicio:

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>Temas

Hay una amplia variedad de temas disponibles para GTK # y se pueden usar desde una aplicación de Xamarin. Forms:

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Formularios nativos

Los formularios nativos permiten el consumo [`ContentPage`](xref:Xamarin.Forms.ContentPage)de páginas derivadas de Xamarin. Forms en proyectos nativos, incluidos los proyectos de GTK #. Esto se puede lograr creando una instancia de la [`ContentPage`](xref:Xamarin.Forms.ContentPage)página derivada de y convirtiéndola en el tipo de GTK # nativo mediante el `CreateContainer` método de extensión:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Para obtener más información sobre los formularios nativos, vea [formularios nativos](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Problemas

Se trata de una versión preliminar, por lo que debe esperar que no todo esté listo para la producción. Para el estado actual de la implementación, consulte [estado](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md) y para los problemas conocidos actuales, consulte [problemas conocidos y pendientes](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
