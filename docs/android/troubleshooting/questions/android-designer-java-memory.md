---
title: Ajuste de los parámetros de memoria de Java para Android Designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: 4a3f3849725f0d3b8e8bc8d43c1cd3f87f044616
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761045"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Ajuste de los parámetros de memoria de Java para Android Designer

Los parámetros de memoria predeterminados que se usan `java` al iniciar el proceso de Android Designer pueden ser incompatibles con algunas configuraciones del sistema.

A partir de Xamarin Studio 5.7.2.7 (y versiones posteriores, Visual Studio para Mac) y Visual Studio Tools para Xamarin 3.9.344, esta configuración se puede personalizar en cada proyecto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nuevas propiedades de Android Designer y las opciones de Java correspondientes

Los nombres de propiedad siguientes corresponden a la [opción de línea de comandos de](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) Java indicada

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra su solución en Visual Studio.

2. Seleccione cada proyecto de Android uno por uno en el Explorador de soluciones y haga clic en [Mostrar todos los archivos](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) dos veces en cada proyecto. Puede omitir proyectos que no contengan `.axml` ningún archivo de diseño. Este paso garantizará que cada directorio del proyecto contenga un `.csproj.user` archivo.

3. Salga de Visual Studio.

4. Busque el `.csproj.user` archivo de cada uno de los proyectos del paso 2.

5. Edite `.csproj.user` cada archivo en un editor de texto.

6. Agregue cualquiera de las nuevas propiedades de memoria de Android Designer o todas `<PropertyGroup>` ellas dentro de un elemento. Puede usar un existente `<PropertyGroup>` o crear uno nuevo. Este es un archivo de `.csproj.user` ejemplo completo que incluye los tres atributos establecidos en sus valores predeterminados:

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

7. Guarde y cierre todos los archivos actualizados `.csproj.user` .

8. Reinicie Visual Studio y vuelva a abrir la solución.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra la solución en Visual Studio para Mac para asegurarse de que el directorio de `.userprefs` la solución contiene un archivo.

2. Salga Visual Studio para Mac.

3. Busque el `.userprefs` archivo en el directorio de la solución.

4. Edite `.userprefs` el archivo en un editor de texto.

5. Busque el elemento XML existente con el siguiente formato. La última parte de este nombre de elemento coincidirá con el nombre del proyecto: "AndroidApplication1" en este ejemplo:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. Si el `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` elemento no existe, créelo en cualquier parte dentro del elemento `<Properties>` envolvente. Asegúrese de reemplazar "AndroidApplication1" por el nombre del proyecto.

7. Agregue cualquiera de las nuevas propiedades de memoria de Android Designer o todas ellas como atributos en el elemento. Este es un archivo de `.userprefs` ejemplo completo que incluye los tres atributos establecidos en sus valores predeterminados:

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

8. Repita los pasos 5-7 para cada proyecto de Android de la solución que `.axml` contenga cualquier archivo de diseño. (Es decir, agregue un `<MonoDevelop.Ide.ItemProperties.ProjectName>` elemento para cada proyecto).

9. Guarde y cierre el `.userprefs` archivo.

10. Reinicie Visual Studio para Mac y vuelva a abrir la solución.

-----
