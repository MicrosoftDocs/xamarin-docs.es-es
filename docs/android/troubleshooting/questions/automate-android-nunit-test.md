---
title: ¿Cómo se automatiza un proyecto de prueba de NUnit de Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 53dce7cc6cc614cde252aa34248fa299c89fafa3
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887702"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>¿Cómo se automatiza un proyecto de prueba de NUnit de Android?

> [!NOTE]
> En esta guía se explica cómo automatizar un proyecto de prueba de NUnit de Android, no un proyecto de Xamarin. UITest. Las guías de Xamarin. UITest se pueden encontrar [aquí](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Al crear un proyecto de **aplicación de prueba unitaria (Android)** en Visual Studio (o un proyecto de **prueba unitaria de Android** en Visual Studio para Mac), este proyecto no ejecutará automáticamente las pruebas de forma predeterminada.
Para ejecutar pruebas NUnit en un dispositivo de destino, puede crear una subclase de [Android. app. Instrumentation](xref:Android.App.Instrumentation) que se inicie con el siguiente comando: 

```shell
adb shell am instrument 
```

En los pasos siguientes se explica este proceso:

1. Cree un nuevo archivo denominado **TestInstrumentation.CS**: 

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

    En este archivo, `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (de **Xamarin. Android. NUnitLite. dll**) se crean subclases para `TestInstrumentation`crear.

2. Implemente `TestInstrumentation` el constructor y `AddTests` el método. El `AddTests` método controla qué pruebas se ejecutan realmente.

3. Modifique el `.csproj` archivo para agregar **TestInstrumentation.CS**. Por ejemplo:

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

4. Use el siguiente comando para ejecutar las pruebas unitarias. Reemplazar `PACKAGE_NAME` por el nombre del paquete de la aplicación (el nombre del paquete se puede encontrar en `/manifest/@package` el atributo de la aplicación que se encuentra en **archivo AndroidManifest. XML**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. Opcionalmente, puede modificar el `.csproj` archivo para agregar el `RunTests` destino de MSBuild. Esto hace posible invocar las pruebas unitarias con un comando como el siguiente:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    (Tenga en cuenta que no es necesario usar este nuevo destino; `adb` se puede usar el comando anterior en `msbuild`lugar de).

Para obtener más información sobre el `adb shell am instrument` uso del comando para ejecutar pruebas unitarias, vea el tema Android Developer [Running tests with ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) .


> [!NOTE]
> Con la versión [Xamarin. Android 5,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) , los nombres de paquete predeterminados para los contenedores a los que se puede llamar de Android se basarán en el md5sum del nombre calificado con el ensamblado del tipo que se va a exportar. Esto permite proporcionar el mismo nombre completo a partir de dos ensamblados diferentes y no obtener un error de empaquetado. Por lo tanto, asegúrese de que `Name` usa la propiedad `Instrumentation` en el atributo para generar un nombre de clase o ACW legible.

_El nombre de ACW debe usarse en `adb` el comando anterior_.
Para cambiar el nombre y la refactorización de la C# clase, es `RunTests` necesario modificar el comando para que use el nombre de ACW correcto.

