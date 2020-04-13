---
title: Diagnóstico de diseño de Android
description: Explica los diagnósticos de diseño de Android y cómo empezar
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 746f74e68fa4816f1f7979980af9506dc0173542
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987587"
---
# <a name="android-layout-diagnostics"></a>Diagnóstico de diseño de Android

Los diagnósticos de diseño de Android están diseñados para ayudar a mejorar la calidad de los archivos de diseño de Android destacando problemas de calidad comunes y optimizaciones útiles. Esta característica está disponible para Visual Studio 16.5+ y Visual Studio para Mac 8.5+.

Se proporciona un conjunto predeterminado de analizadores para una amplia gama de problemas y cada uno se puede personalizar para cubrir las necesidades específicas de un proyecto. Los analizadores se basan libremente en el sistema de linting Android.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>Habilitar diagnósticos de diseño de Android en Visual Studio 2019

Asegúrese de que la configuración de diagnóstico de diseño, **Habilitar diagnóstico de diseño**, está habilitada. Para acceder a esta **Tools**página de opciones, elija > **Opciones**de herramientas y, a continuación, elija **Editor** > de texto**Android XML** > **Advanced:**

![Cuadro de diálogo Opciones que muestra cómo habilitar la opción de diagnóstico](diagnostics-images/AndroidDiagnosticsEnableOption.png)

Una vez habilitado, el editor de diseño de Android mostrará problemas:

![Diagnóstico de Android habilitado en Visual Studio 2019](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>Habilitar diagnósticos de diseño de Android en Visual Studio para Mac

Asegúrese de que la configuración de diagnóstico de diseño, **Habilitar diagnóstico de diseño**, está habilitada. Para acceder a esta página de opciones, **Text Editor** > elija Preferencias de **Visual Studio...** > **Preferences...** y, a continuación, elija Text Editor**Android XML**:

![Diálogo de preferencias que muestra cómo habilitar la opción de diagnóstico](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

Una vez habilitado, el editor de diseño de Android mostrará problemas:

![Diagnóstico de Android habilitado en Visual Studio para Mac](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>Características

En las secciones siguientes se describen las características disponibles en los diagnósticos de diseño de Android.

### <a name="analyzers"></a>Analizadores

Los analizadores se utilizan para ayudar a detectar problemas en los archivos de diseño, reducir los valores codificados de forma rígida, mejorar el rendimiento y marcar errores. Para obtener una lista de analizadores, consulte [Analizadores](diagnostic-analyzers.md) de diagnóstico de diseñador de Android

### <a name="diagnostic-configuration"></a>Configuración de diagnóstico

Los analizadores se pueden configurar mediante un archivo XML, lo que le permite cambiar el nivel de gravedad predeterminado, ignorar determinados archivos y pasar variables.

Puede usar un archivo de línea base si tiene un conjunto de configuraciones que desea compartir entre varias aplicaciones De Android. Para utilizar esta característica, cree un `-baseline` nuevo archivo de configuración y anexe al nombre de archivo. Las configuraciones de línea base se aplican primero y, a continuación, los archivos de configuración restantes.

> [!TIP]
> Esto puede ser útil si desea ignorar un conjunto de problemas en una aplicación Android nueva o existente.

El formato es:

```xml
<?xml version="1.0" encoding="utf-8" ?> 
<configuration>
    <issue id="DuplicateIDs" severity="warning">
        <ignore path="Resources/layout/layout1.xml" />
    </issue>
    <issue id="HardcodedText" severity="informational">
        <ignore path="Resources/layout/layout1.xml" />
        <ignore path="Resource/layout/layout2.xml" />
    </issue>
    <issue id="TooManyViews">
        <variable name="MAX_VIEW_COUNT" value="12" />
    </issue>
    <issue id="TooDeepLayout">
        <variable name="MAX_DEPTH" value="12" />
    </issue>
</configuration>
```

> [!NOTE]
> Actualmente las únicas `MAX_VIEW_COUNT` variables son `MAX_DEPTH` (predeterminado: 80) `TooManyViews` `TooDeepLayout` y (predeterminado: 10) for y respectivamente.

Los niveles de gravedad son:

- Sugerencia
- Información
- Advertencia
- Error
- Ignore

### <a name="add-a-configuration-file"></a>Adición de un archivo de configuración

Cree un nuevo archivo XML en la raíz de un proyecto de aplicación Android. El nombre del archivo no es importante, `AndroidLayoutDiagnostics.xml`pero este ejemplo utiliza:

![Agregar un nuevo elemento](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

Una vez agregado el nuevo archivo XML, debería aparecer en el árbol de proyectos de la aplicación Android:

![Android App Project Tree](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

Asegúrese de que la acción de compilación está establecida en **AndroidResourceAnalysisConfig** en el panel de propiedades.
La forma más fácil de extraer el panel de propiedades del nuevo archivo es hacer clic con el botón derecho en el archivo y seleccionar propiedades. Una vez que se muestra el panel de propiedades, debe cambiar la acción de **compilación** a **AndroidResourceAnalysisConfig**:

![Establecer acción de compilación en las propiedades del elemento](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

Ahora que tiene un archivo XML en `<configuration>` blanco, debe agregar el elemento raíz. En este punto, puede ajustar el comportamiento predeterminado de los problemas admitidos.
Si desea asegurarse de que las cadenas codificadas de forma rígida se tratan como errores agregar:

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![Archivo de configuración de diagnósticos](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

Ahora que el texto codificado de forma rígida se considera un error, ahora se marca con un ondulado rojo en el editor de diseño:

![Diseño mediante configuración de diagnósticos](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> Para que los nuevos cambios en los archivos de configuración surtan efecto, es necesario volver a abrir los archivos de diseño abiertos actualmente.
>

## <a name="troubleshooting"></a>Solución de problemas

Estos son algunos posibles problemas comunes.

- Asegúrese de que no hay ningún error de formato XML.
- La acción de compilación se establece correctamente en **AndroidResourceAnalysisConfig**.

## <a name="known-issues"></a>Problemas conocidos

- El panel de errores no se rellena hasta después de cambiar el archivo la primera vez.

## <a name="related-links"></a>Vínculos relacionados

- [Android Lint Cheques](http://tools.android.com/tips/lint-checks)
- [Mejora tu código con comprobaciones de pelusas](https://developer.android.com/studio/write/lint)
