---
title: Compilación de la primera aplicación Xamarin.Forms
description: Guía de vídeo en la que se muestra cómo compilar una primera aplicación de Xamarin.Forms en Visual Studio.
zone_pivot_groups: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da56bde956a0ff7730ef6737e2802c3723d6d716
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133489"
---
# <a name="build-your-first-xamarinforms-app"></a>Compilación de la primera aplicación de Xamarin.Forms

_Vea este vídeo y siga el tutorial para crear una primera aplicación móvil con Xamarin.Forms._

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-Android-App-with-Visual-Studio-2019-and-Xamarin/player]

## <a name="step-by-step-instructions-for-windows"></a>Instrucciones paso a paso para Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Siga estos pasos, junto con el vídeo anterior:

1. Elija **Archivo > Nuevo > Proyecto...** o presione el botón **Crear nuevo proyecto...** :

    [![Crear un nuevo proyecto](images/win-2019/01-sml.png)](images/win-2019/01.png#lightbox)

2. Busque "Xamarin" o elija **Dispositivos móviles** en el menú **Tipo de proyecto**. Seleccione el tipo de proyecto **Aplicación móvil (Xamarin.Forms)** :

    [![Filtrado por proyectos de Xamarin](images/win-2019/02-sml.png)](images/win-2019/02.png#lightbox)

3. Elija un nombre de proyecto; en el ejemplo se usa "AwesomeApp":

    [![Elección de un nombre de proyecto](images/win-2019/03-sml.png)](images/win-2019/03.png#lightbox)

4. Haga clic en el tipo de proyecto **En blanco** y asegúrese de que **Android** e **iOS** estén seleccionados:

    [![Android y iOS, con .NET Standard](images/win-2019/04-sml.png)](images/win-2019/04.png#lightbox)

5. Espere hasta que se restauren los paquetes de NuGet (aparecerá un mensaje de "Restauración completada" en la barra de estado).

6. Las nuevas instalaciones de Visual Studio 2019 no tendrán un emulador de Android configurado. Haga clic en la flecha desplegable del botón **Depurar** y elija **Crear Android Emulator** para iniciar la pantalla de creación del emulador:

    ![Lista desplegable Crear Android Emulator](images/win-2019/debug-dropdown.png)

7. En la pantalla de creación del emulador, use la configuración predeterminada y haga clic en el botón **Crear**:

    [![Pantalla de creación de Android Emulator](images/win-2019/create-emulator-sml.png)](images/win-2019/create-emulator.png#lightbox)

8. Al crear un emulador, se le devolverá a la ventana Administrador de dispositivos. Haga clic en el botón **Iniciar** para iniciar el nuevo emulador:

    ![Emulador de Android en el Administrador de dispositivos](images/win-2019/start-emulator.png)

9. Ahora en Visual Studio 2019 se debería mostrar el nombre del nuevo emulador en el botón **Depurar**:

    ![Nombre del emulador de Android en el botón Depurar](images/win-2019/debug-emulator-name.png)

10. Haga clic en el botón **Depurar** para compilar e implementar la aplicación en el emulador de Android:

    ![Aplicación mostrada en el emulador de Android](images/win-2019/android-emulator.png)

## <a name="customize-the-application"></a>Personalización de la aplicación

La aplicación se puede personalizar para agregar funcionalidad interactiva. Realice los pasos siguientes para agregar la interacción del usuario a la aplicación:

1. Edite **MainPage.xaml**, agregando este XAML antes del final de `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

2. Edite **MainPage.xaml**, agregando este código al final de la clase:

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

3. Depurar la aplicación en Android:

    ![Aplicación de Android](images/win/07-sml.png)

> [!NOTE]
> En la aplicación de ejemplo se incluye la funcionalidad interactiva adicional que no se trata en el vídeo.

## <a name="build-an-ios-app-in-visual-studio-2019"></a>Compilación de una aplicación iOS en Visual Studio 2019

Es posible crear y depurar la aplicación iOS desde Visual Studio con un equipo Mac en red. Consulte las [instrucciones de configuración](~/ios/get-started/installation/windows/index.md) para obtener más información.

En este vídeo se describe el proceso de compilación y prueba de una aplicación iOS con Visual Studio 2019 en Windows:

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-iOS-App-with-Visual-Studio-2019-and-Xamarin/player]

::: zone-end
::: zone pivot="win-vs2017"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Instrucciones paso a paso para Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Siga estos pasos, junto con el vídeo anterior:

1. Elija **Archivo > Nuevo > Proyecto…** o presione el botón **Crear proyecto nuevo…** y, luego, seleccione **Visual C# > Multiplataforma > Aplicación móvil (Xamarin.Forms)** :

    [![Aplicación móvil (Xamarin.Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. Asegúrese de que **Android** y **iOS** están seleccionados, con uso compartido del código **.NET Standard**:

    [![Android y iOS, con .NET Standard](images/win/02-sml.png)](images/win/02.png#lightbox)

3. Espere hasta que se restauren los paquetes de NuGet (aparecerá un mensaje de "Restauración completada" en la barra de estado).

4. Inicie Android Emulator presionando el botón de depuración (o el elemento de menú **Depurar > Iniciar depuración**).

5. Edite **MainPage.xaml**, agregando este XAML antes del final de `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

6. Edite **MainPage.xaml**, agregando este código al final de la clase:

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Depurar la aplicación en Android:

    ![Aplicación de Android](images/win/07-sml.png)

    > [!TIP]
    > Es posible crear y depurar la aplicación de iOS desde Visual Studio con un equipo Mac en red. Consulte las [instrucciones de configuración](~/ios/get-started/installation/windows/index.md) para obtener más información.

::: zone-end
::: zone pivot="macos"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-iOS--Android-App-in-Visual-Studio-for-Mac/player]

## <a name="step-by-step-instructions-for-mac"></a>Instrucciones paso a paso para Mac

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Siga estos pasos, junto con el vídeo anterior:

1. Elija **Archivo > Nueva solución...** o presione el botón **Nuevo proyecto...** y, posteriormente, seleccione **Multiplataforma > Aplicación > Aplicación de Forms en blanco**:

    [![Aplicación de Forms en blanco](images/01-sml.png)](images/01.png#lightbox)

2. Asegúrese de que **Android** y **iOS** están seleccionados, con uso compartido del código **.NET Standard**:

    [![Android y iOS, con .NET Standard](images/02-sml.png)](images/02.png#lightbox)

3. Restaurar paquetes de NuGet, haciendo clic en el botón derecho en la solución:

    ![Aplicación de Android](images/03-sml.png)

4. Inicie Android Emulator presionando el botón de depuración (o **Ejecutar > Iniciar depuración**).

5. Edite **MainPage.xaml**, agregando este XAML antes del final de `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Handle_Clicked" />
    ```

6. Edite **MainPage.xaml**, agregando este código al final de la clase:

    ```csharp
    int count = 0;
    void Handle_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Depurar la aplicación en Android:

    ![Aplicación de Android](images/07-sml.png)

8. Haga clic con el botón derecho para establecer iOS en el **Proyecto de inicio**:

    [![Establecer el proyecto de inicio en iOS](images/08-sml.png)](images/08.png#lightbox)

9. Depurar la aplicación en iOS:

    ![Aplicación de iOS](images/09-sml.png)

::: zone-end

Puede descargar el código completo desde la [galería de ejemplos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/) o puede verlo en [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp).

## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido de una sola página](~/get-started/quickstarts/single-page.md): compilación de una aplicación más funcional.
- [Ejemplos de Xamarin.Forms](~/xamarin-forms/samples/index.md): descarga y ejecución de ejemplos de código y de aplicaciones.
- [Libro electrónico Creación de aplicaciones móviles](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md): capítulos detallados en los que se describe el desarrollo de Xamarin.Forms, disponible como archivo PDF y con cientos de ejemplos adicionales.
