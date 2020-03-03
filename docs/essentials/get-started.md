---
title: Introducción a Xamarin.Essentials
description: Xamarin.Essentials brinda una API multiplataforma única que funciona con cualquier aplicación iOS, Android o UWP accesible desde código compartido, sin importar cómo se creó la interfaz de usuario.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 07/10/2019
ms.openlocfilehash: daab2b3a18eeea2f50c9518144b3396fbba90928
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545124"
---
# <a name="get-started-with-xamarinessentials"></a>Introducción a Xamarin.Essentials

Xamarin.Essentials brinda una API multiplataforma única que funciona con cualquier aplicación iOS, Android o UWP accesible desde código compartido, sin importar cómo se creó la interfaz de usuario. Para obtener más información sobre los sistemas operativos compatibles, consulte la [guía de compatibilidad con plataformas y características](platform-feature-support.md).

## <a name="installation"></a>Instalación

Xamarin.Essentials está disponible como paquete NuGet y se incluye en todos los proyectos nuevos de Visual Studio. También se puede agregar a cualquier proyecto existente usando Visual Studio con los pasos siguientes.

1. Descargue e instale [Visual Studio](https://visualstudio.microsoft.com/) con [Visual Studio Tools para Xamarin](~/get-started/installation/index.md).

2. Abra un proyecto existente o cree uno nuevo con la plantilla de aplicación vacía en **Visual Studio C#** (Android, iPhone e iPad o multiplataforma).

    > [!IMPORTANT]
    > Si se agrega a un proyecto de UWP, asegúrese de que en las propiedades del proyecto esté establecida la compilación 16299 u otra posterior.

3. Agregue el paquete NuGet [**Xamarin.Essentials**](https://www.nuget.org/packages/Xamarin.Essentials/) a cada proyecto:

    <!--markdownlint-disable MD023 -->
    # <a name="visual-studio"></a>[Visual Studio](#tab/windows)

    En el panel del Explorador de soluciones, haga clic con el botón derecho en el nombre de la solución y seleccione **Administrar paquetes NuGet**. Busque **Xamarin.Essentials** e instale el paquete en **TODOS** los proyectos, incluidos Android, iOS, UWP y las bibliotecas de .NET Standard.

    # <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

    En el panel del Explorador de soluciones, haga clic con el botón derecho en el nombre del proyecto y seleccione **Agregar > Agregar paquetes NuGet...**. Busque **Xamarin.Essentials** e instale el paquete en **TODOS** los proyectos, incluidos Android, iOS y las bibliotecas de .NET Standard.

    -----

4. Agregue una referencia a Xamarin.Essentials en cualquier clase de C# para hacer referencia a las API.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials requiere una configuración específica de plataforma:

    # <a name="android"></a>[Android](#tab/android)

    La versión mínima de Android compatible con Xamarin.Essentials es la 4.4, que corresponde a un nivel de API 19, pero la versión de destino de Android para compilar debe ser la 9.0, correspondiente al nivel de API 28. (En Visual Studio, estas dos versiones se establecen en el cuadro de diálogo Propiedades del proyecto correspondiente al proyecto de Android en la pestaña Manifiesto de Android). En Visual Studio para Mac, se establecen en el cuadro de diálogo Opciones del proyecto correspondiente al proyecto de Android, en la pestaña Aplicación de Android).

    Xamarin.Essentials instala la versión 28.0.0.3 de las bibliotecas de Xamarin.Android.Support que necesita. Las demás bibliotecas de Xamarin.Android.Support que requiere la aplicación también se deben actualizar a la versión 28.0.0.3 con el administrador de paquetes NuGet. Todas las bibliotecas de Xamarin.Android.Support que la aplicación usa deben ser iguales y la versión debe ser al menos 28.0.0.3. Consulte la [página de solución de problemas](troubleshooting.md) si no puede agregar el paquete NuGet de Xamarin.Essentials ni actualizar los paquetes NuGet de la solución.

    En `MainLauncher` del proyecto Android o en cualquier `Activity` que se inicia, Xamarin.Essentials se debe inicializar en el método `OnCreate`:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
        //...
    ```

    Para controlar los permisos en tiempo de ejecución de Android, Xamarin.Essentials debe recibir cualquier `OnRequestPermissionsResult`. Agregue el código siguiente a todas las clases `Activity`:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="ios"></a>[iOS](#tab/ios)

    No se requiere configuración adicional.

    # <a name="uwp"></a>[UWP](#tab/uwp)

    No se requiere configuración adicional.

    -----

6. Siga las [guías de Xamarin.Essentials](index.md) para poder copiar y pegar fragmentos de código para cada característica.

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials : API multiplataformas para Mobile Apps (video)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>Otros recursos

Es recomendable que los desarrolladores que trabajan por primera vez con Xamarin visiten [Introducción al desarrollo de Xamarin](~/cross-platform/getting-started/index.md).

Visite el [repositorio GitHub de Xamarin.Essentials](https://github.com/xamarin/Essentials) para ver el código fuente actual, qué viene más adelante, ejecutar ejemplos y clonar el repositorio. Estaremos encantados de recibir cualquier colaboración de la comunidad.

Examine la [documentación de la API](xref:Xamarin.Essentials) para conocer cada característica de Xamarin.Essentials.
