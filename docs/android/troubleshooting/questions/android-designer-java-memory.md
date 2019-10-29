---
title: Ajuste de los parámetros de memoria de Java para Android Designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 9c9b9f5a205a2eef7db9f27e8d09b10ce65a4318
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027055"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Ajuste de los parámetros de memoria de Java para Android Designer

Los parámetros de memoria predeterminados que se usan al iniciar el proceso de `java` para Android Designer podrían ser incompatibles con algunas configuraciones del sistema.

A partir de Xamarin Studio 5.7.2.7 (y versiones posteriores, Visual Studio para Mac) y Visual Studio Tools para Xamarin 3.9.344, esta configuración se puede personalizar en cada proyecto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nuevas propiedades de Android Designer y las opciones de Java correspondientes

Los nombres de propiedad siguientes corresponden a la [opción de línea de comandos de](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) Java indicada

- **AndroidDesignerJavaRendererMinMemory** -XMS

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX: MaxPermSize

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra su solución en Visual Studio.

2. Seleccione cada proyecto de Android uno por uno en el Explorador de soluciones y haga clic en [Mostrar todos los archivos](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) dos veces en cada proyecto. Puede omitir proyectos que no contengan ningún archivo de diseño `.axml`. Este paso garantizará que cada directorio del proyecto contenga un archivo `.csproj.user`.

3. Salga de Visual Studio.

4. Busque el archivo de `.csproj.user` para cada uno de los proyectos del paso 2.

5. Edite cada archivo de `.csproj.user` en un editor de texto.

6. Agregue cualquiera de las nuevas propiedades de memoria de Android Designer o todas ellas dentro de un elemento `<PropertyGroup>`. Puede usar una `<PropertyGroup>` existente o crear una nueva. Este es un ejemplo completo `.csproj.user` archivo que incluye los tres atributos establecidos en sus valores predeterminados:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7. Guarde y cierre todos los archivos de `.csproj.user` actualizados.

8. Reinicie Visual Studio y vuelva a abrir la solución.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra la solución en Visual Studio para Mac para asegurarse de que el directorio de la solución contiene un archivo `.userprefs`.

2. Salga Visual Studio para Mac.

3. Busque el archivo de `.userprefs` en el directorio de la solución.

4. Edite el archivo de `.userprefs` en un editor de texto.

5. Busque el elemento XML existente con el siguiente formato. La última parte de este nombre de elemento coincidirá con el nombre del proyecto: "AndroidApplication1" en este ejemplo:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. Si el elemento `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` no existe, créelo en cualquier parte dentro del elemento `<Properties>` envolvente. Asegúrese de reemplazar "AndroidApplication1" por el nombre del proyecto.

7. Agregue cualquiera de las nuevas propiedades de memoria de Android Designer o todas ellas como atributos en el elemento. Este es un ejemplo completo `.userprefs` archivo que incluye los tres atributos establecidos en sus valores predeterminados:

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8. Repita los pasos 5-7 para cada proyecto de Android de la solución que contenga cualquier archivo de diseño `.axml`. (Es decir, agregue un elemento `<MonoDevelop.Ide.ItemProperties.ProjectName>` para cada proyecto).

9. Guarde y cierre el archivo de `.userprefs`.

10. Reinicie Visual Studio para Mac y vuelva a abrir la solución.

-----
