---
title: ¿Se puede cambiar la ruta de acceso de salida del archivo IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: b8006b1ffe253ac57c1ab435690c5b378cc709fb
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278663"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>¿Se puede cambiar la ruta de acceso de salida del archivo IPA?

## <a name="for-cycle-7-and-higher"></a>Para ciclo 7 y versiones posteriores
Sí, puede usar destinos de MSBuild personalizados para lograr esto. La opción más sencilla es probablemente copiar el `.ipa` archivo después de que se haya compilado.

Estos pasos funcionarán en cualquier proyecto de iOS que use el motor de compilación de MSBuild en Mac o Windows. (Nota: todos los proyectos de Unified API usan el motor de compilación de MSBuild).

1. Abra el `.csproj` archivo para el proyecto de aplicación de iOS en un editor de texto y, a continuación, agregue las siguientes líneas al final `</Project>` (inmediatamente antes de la etiqueta de cierre):

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(IpaPackagePath)"
            DestinationFolder="$(OutputPath)"
        />
    </Target>
    ```

2. Establezca el valor de DestinationFolder en la carpeta de salida deseada. Como es habitual, puede usar las propiedades de MSBuild (como $ (OutputPath)) dentro de este argumento si lo desea.

## <a name="notes"></a>Notas
- La `CreateIpaDependsOn` propiedad se define en el `Xamarin.iOS.Common.targets` archivo que forma parte de Xamarin. iOS. Se comporta tal y como se describe en la sección [invalidar destinos predefinidos](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) del [artículo How to: Extienda el proceso](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)de compilación de Visual Studio.

- Puede usar una tarea de **movimiento** en lugar de una tarea de **copia** si lo prefiere. Si elige esta opción y está compilando en Windows, deberá usar el nombre `<Microsoft.Build.Tasks.Move>` completo de la tarea para evitar una ambigüedad con las tareas de compilación de XamarinVS.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Para las versiones anteriores a Xamarin Studio 6.0.0.5174 | Xamarin para Visual Studio 4.1.0.530

Sí, puede usar destinos de MSBuild personalizados para lograr esto. La opción más sencilla es probablemente copiar el `.ipa` archivo después de que se haya compilado.

Estos pasos funcionarán en cualquier proyecto de iOS que use el motor de compilación de MSBuild en Mac o Windows. (Nota: todos los proyectos de Unified API usan el motor de compilación de MSBuild).

1. Abra el `.csproj` archivo para el proyecto de aplicación de iOS en un editor de texto y, a continuación, agregue las siguientes líneas al final `</Project>` (inmediatamente antes de la etiqueta de cierre).

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
            $(CreateIpaDependsOn);
            CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>

    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(OutputPath)$(IpaPackageName)"
            DestinationFolder="/Users/macuser/Desktop/"
        />
    </Target>
    ```

2. `DestinationFolder` Establezca en la carpeta de salida deseada. Como es habitual, puede usar las propiedades de `$(OutputPath)`MSBuild (como) dentro de este argumento si lo desea.

## <a name="notes"></a>Notas
- La `CreateIpaDependsOn` propiedad se define en el `Xamarin.iOS.Common.targets` archivo que forma parte de Xamarin. iOS. t se comporta tal y como se describe en la sección [invalidar destinos predefinidos](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) del artículo [How to: Extienda el proceso](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)de compilación de Visual Studio.

- Puede usar una tarea de **movimiento** en lugar de una tarea de **copia** si lo prefiere. Si elige esta opción y está compilando en Windows, deberá usar el nombre `<Microsoft.Build.Tasks.Move>` completo de la tarea para evitar una ambigüedad con las tareas de compilación de XamarinVS.
