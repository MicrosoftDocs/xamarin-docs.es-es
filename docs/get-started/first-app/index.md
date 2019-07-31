---
title: Creación de una primera aplicación de Xamarin.Forms
description: Guía de vídeo que muestra cómo crear una primera aplicación de Xamarin.Forms en Visual Studio.
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 05/23/2019
ms.openlocfilehash: 245b41eea556ef36c81b337b57ce58d922e4e8fd
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653682"
---
# <a name="build-your-first-xamarinforms-app"></a>Creación de una primera aplicación de Xamarin.Forms

_Vea este vídeo y siga el tutorial para crear una primera aplicación móvil con Xamarin.Forms._

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-Android-App-with-Visual-Studio-2019-and-Xamarin/player]

## <a name="step-by-step-instructions-for-windows"></a>Instrucciones paso a paso para Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Siga estos pasos, junto con el vídeo anterior:

1. Elija **archivo > nuevo proyecto de >.** .. o presione el botón **crear nuevo proyecto...** :

    [![Crear un nuevo proyecto](images/win-2019/01-sml.png)](images/win-2019/01.png#lightbox)

2. Busque "Xamarin" o elija **móvil** en el menú **tipo de proyecto** . Seleccione el tipo de proyecto **aplicación móvil (Xamarin. Forms)** :

    [![Filtrar para proyectos de Xamarin](images/win-2019/02-sml.png)](images/win-2019/02.png#lightbox)

3. Elegir un nombre &ndash; de proyecto en el ejemplo se usa "AwesomeApp":

    [![Elegir un nombre de proyecto](images/win-2019/03-sml.png)](images/win-2019/03.png#lightbox)

4. Haga clic en el tipo de proyecto **en blanco** y asegúrese de que están seleccionados **Android** e **iOS** :

    [![Android y iOS, con .NET Standard](images/win-2019/04-sml.png)](images/win-2019/04.png#lightbox)

5. Espere hasta que se restauren los paquetes de NuGet (aparecerá un mensaje de "Restauración completada" en la barra de estado).

6. Inicie Android Emulator presionando el botón de depuración (o el elemento de menú **Depurar > Iniciar depuración**).

7. Edite **MainPage.xaml**, agregando este XAML antes del final de `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

8. Edite **MainPage.xaml**, agregando este código al final de la clase:

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

9. Depurar la aplicación en Android:

    ![Aplicación de Android](images/win/07-sml.png)

    > [!TIP]
    > Es posible crear y depurar la aplicación de iOS desde Visual Studio con un equipo Mac en red. Consulte las [instrucciones de configuración](~/ios/get-started/installation/windows/index.md) para obtener más información.

## <a name="build-an-ios-app-in-visual-studio-2019"></a>Compilar una aplicación de iOS en Visual Studio 2019

En este vídeo se describe el proceso de creación y prueba de una aplicación iOS con Visual Studio 2019 en Windows:

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-iOS-App-with-Visual-Studio-2019-and-Xamarin/player]

::: zone-end
::: zone pivot="win-vs2017"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Instrucciones paso a paso para Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Siga estos pasos, junto con el vídeo anterior:

1. Elija **Archivo > Nuevo > Proyecto...** o presione el botón **Crear proyecto nuevo...**  y, a continuación, seleccione **Visual C# > Multiplataforma > Aplicación móvil (Xamarin.Forms)** :

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

- [Inicio rápido de una sola página](~/get-started/quickstarts/single-page.md) &ndash; Cree una aplicación más funcional.
- [Ejemplos de Xamarin.Forms](~/xamarin-forms/samples/index.yml): Descarga y ejecución de ejemplos de código y aplicaciones de muestra.
- [Libro electrónico Creating Mobile Apps (Creación de Mobile Apps)](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md): Capítulos detallados que enseñan el desarrollo de Xamarin.Forms, disponible como archivo PDF y que incluye cientos de ejemplos adicionales.
