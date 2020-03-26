---
title: Diagnóstico de diseño de Android
description: Explica los diagnósticos de diseño de Android y cómo empezar
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 5c29a1a80d8c1f599f0bbc750d22d8334ddb3494
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247819"
---
# <a name="android-layout-diagnostics"></a>Diagnóstico de diseño de Android

Los diagnósticos de diseño de Android están diseñados para ayudar a mejorar la calidad de los archivos de diseño de Android resaltando problemas de calidad comunes y optimizaciones útiles. Esta característica está disponible para Visual Studio 16,5 + y Visual Studio para Mac 8.5 +.

Se proporciona un conjunto predeterminado de analizadores para una amplia gama de problemas y cada uno se puede personalizar para cubrir las necesidades específicas de un proyecto. Los analizadores se basan de forma flexible en el sistema de detección de errores de Android.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>Habilitar diagnósticos de diseño de Android en Visual Studio 2019

Asegúrese de que está habilitada la configuración de diagnóstico de diseño **Habilitar diagnósticos de diseño**. Para acceder a esta página de opciones, elija **herramientas** > **Opciones**y, a continuación, elija **Editor de texto** > **Android XML** > **avanzado**:

![Cuadro de diálogo de opciones que muestra cómo habilitar la opción de diagnóstico](diagnostics-images/AndroidDiagnosticsEnableOption.png)

Una vez habilitado, el editor de diseño de Android mostrará problemas:

![Diagnósticos de Android habilitados en Visual Studio 2019](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>Habilitar diagnósticos de diseño de Android en Visual Studio para Mac

Asegúrese de que está habilitada la configuración de diagnóstico de diseño **Habilitar diagnósticos de diseño**. Para acceder a esta página de opciones, elija **Visual Studio** > **preferencias...** y, a continuación, elija **Editor de texto** > **Android XML**:

![Cuadro de diálogo de preferencias que muestra cómo habilitar la opción de diagnóstico](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

Una vez habilitado, el editor de diseño de Android mostrará problemas:

![Diagnósticos de Android habilitado en Visual Studio para Mac](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>Características

En las secciones siguientes se describen las características disponibles en diagnósticos de diseño de Android.

### <a name="analyzers"></a>Analizadores

Los analizadores se usan para ayudar a detectar problemas en los archivos de diseño. Algunas son maneras de reducir valores codificados de forma rígida, mejorar el rendimiento y marcar errores.

### <a name="diagnostic-configuration"></a>Configuración de diagnóstico

Los analizadores se pueden configurar mediante un archivo XML, lo que le permite cambiar el nivel de gravedad predeterminado, omitir determinados archivos y pasar variables.

Puede usar un archivo de línea base si tiene un conjunto de configuraciones que desea compartir entre varias aplicaciones Android. Para usar esta característica, cree un nuevo archivo de configuración y anexe `-baseline` al nombre de archivo. Primero se aplican las configuraciones de línea de base y, a continuación, los archivos de configuración restantes.

> [!TIP]
> Esto puede ser útil si quiere omitir un conjunto de problemas en una aplicación Android nueva o existente.

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
> Actualmente las únicas variables son `MAX_VIEW_COUNT` (valor predeterminado: 80) y `MAX_DEPTH` (valor predeterminado: 10) para `TooManyViews` y `TooDeepLayout` respectivamente.

Los niveles de gravedad son:

- Sugerencia
- Información
- Advertencia
- Error
- Ignore

### <a name="add-a-configuration-file"></a>Adición de un archivo de configuración

Cree un nuevo archivo XML en la raíz de un proyecto de aplicación de Android. El nombre del archivo no es importante, pero en este ejemplo se usa `AndroidLayoutDiagnostics.xml`:

![Agregar un nuevo elemento](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

Una vez agregado el nuevo archivo XML, debe aparecer en el árbol del proyecto de aplicación de Android:

![Árbol de proyecto de aplicación de Android](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

Asegúrese de que la acción de compilación está establecida en **AndroidResourceAnalysisConfig** en el panel Propiedades.
La forma más fácil de extraer el panel de propiedades del nuevo archivo es hacer clic con el botón derecho en el archivo y seleccionar Propiedades. Una vez que se muestra el panel Propiedades, debe cambiar la **acción de compilación** a **AndroidResourceAnalysisConfig**:

![Establecer acción de compilación en las propiedades del elemento](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

Ahora que tiene un archivo XML en blanco, debe agregar el elemento raíz `<configuration>`. En este momento, puede ajustar el comportamiento predeterminado de los problemas admitidos.
Si desea asegurarse de que las cadenas codificadas de forma rígida se traten como errores, agregue:

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![Archivo de configuración de diagnósticos](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

Ahora que el texto codificado de forma rígida se considera un error, ahora está marcado con un subrayado ondulado de color rojo en el editor de diseño:

![Diseño mediante la configuración de diagnósticos](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> Para que los nuevos cambios en los archivos de configuración surtan efecto, es necesario volver a abrir los archivos de diseño abiertos actualmente.
>

## <a name="troubleshooting"></a>Solución de problemas

Estos son algunos posibles problemas comunes.

- Asegúrese de que no hay ningún error de formato XML.
- La acción de compilación está establecida correctamente en **AndroidResourceAnalysisConfig**.

## <a name="known-issues"></a>Problemas conocidos

- El panel de errores no se rellena hasta que se cambia el archivo por primera vez.

## <a name="related-links"></a>Vínculos relacionados

- [Comprobaciones de pelusa de Android](http://tools.android.com/tips/lint-checks)
- [Mejorar el código con comprobaciones de pelusa](https://developer.android.com/studio/write/lint)
