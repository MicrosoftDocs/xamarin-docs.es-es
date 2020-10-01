---
title: ¿Cómo se automatiza un proyecto de prueba de NUnit de Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/29/2018
ms.openlocfilehash: c8c9e721bc46d9071bb2af479a5e1d37b93fce27
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458204"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>¿Cómo se automatiza un proyecto de prueba de NUnit de Android?

> [!NOTE]
> En esta guía se explica cómo automatizar un proyecto de prueba de NUnit de Android, no un proyecto de Xamarin.UITest. Las guías de Xamarin.UITest se encuentran [aquí](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest).

Al crear un proyecto de **Aplicación de prueba unitaria (Android)** en Visual Studio (o un proyecto de **Android Unit Test** [Prueba unitaria de Android] en Visual Studio para Mac), este proyecto no ejecutará automáticamente las pruebas de forma predeterminada.
Para ejecutar pruebas de NUnit en un dispositivo de destino, puede crear una subclase [Android.App.Instrumentation](xref:Android.App.Instrumentation) que se inicie con el siguiente comando: 

```shell
adb shell am instrument 
```

En los pasos siguientes se explica este proceso:

1. Cree un nuevo archivo llamado **TestInstrumentation.cs**: 

    ```cs 
    using System;
    using System.Reflection;
    using Android.App;
    using Android.Content;
    using Android.Runtime;
    using Xamarin.Android.NUnitLite;

    namespace App.Tests {

        [Instrumentation(Name="app.tests.TestInstrumentation")]
        public class TestInstrumentation : TestSuiteInstrumentation {

            public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
            {
            }

            protected override void AddTests ()
            {
                AddTest (Assembly.GetExecutingAssembly ());
            }
        }
    }
    ```

    En este archivo, se crea una subclase de `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (de **Xamarin.Android.NUnitLite.dll**) para crear `TestInstrumentation`.

2. Implemente el constructor `TestInstrumentation` y el método `AddTests`. El método `AddTests` controla qué pruebas se ejecutan realmente.

3. Modifique el archivo `.csproj` para agregar **TestInstrumentation.cs**. Por ejemplo:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

4. Use el comando siguiente para ejecutar las pruebas unitarias. Reemplace `PACKAGE_NAME` por el nombre del paquete de la aplicación (el nombre del paquete se puede encontrar en el atributo `/manifest/@package` de la aplicación que se encuentra en **AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. Opcionalmente, puede modificar el archivo `.csproj` para agregar el destino de MSBuild `RunTests`. Esto hace posible invocar las pruebas unitarias con un comando como el siguiente:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    (Tenga en cuenta que no es necesario usar este nuevo destino; se puede usar el comando `adb` anterior en lugar de `msbuild`).

Para obtener más información sobre el uso del comando `adb shell am instrument` para ejecutar pruebas unitarias, consulte el tema [Cómo ejecutar pruebas con ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) de Android Developer.

> [!NOTE]
> Con la versión [Xamarin.Android 5.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming), los nombres de paquete predeterminados para los contenedores de Android que se pueden llamar se basarán en MD5SUM del nombre de tipo calificado con el ensamblado del tipo que se va a exportar. Esto permite proporcionar el mismo nombre completo a partir de dos ensamblados diferentes y no obtener un error de empaquetado. Por lo tanto, asegúrese de usar la propiedad `Name` en el atributo `Instrumentation` para generar un nombre de clase o ACW legible.

_El nombre de ACW debe usarse en el comando `adb` anterior_.
El cambio de nombre y la refactorización de la clase C# requerirán, por tanto, la modificación del comando `RunTests` para usar el nombre de ACW correcto.