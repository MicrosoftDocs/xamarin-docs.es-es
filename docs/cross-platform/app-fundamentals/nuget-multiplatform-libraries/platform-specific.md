---
title: Crear nuevos proyectos de biblioteca específica de la plataforma para NuGet
description: En este documento se describe cómo crear un único paquete de NuGet que contiene código específico de la plataforma para varias plataformas.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 925e08c600c695640c927ada26df376a252b3927
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016725"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Crear nuevos proyectos de biblioteca específica de la plataforma para NuGet

Los proyectos de biblioteca multiplataforma que tienen como destino plataformas específicas, como iOS y Android, funcionan mejor con proyectos compartidos.

NuGet puede contener tanto código iOS como específico de Android, así como código .NET común a ambos.

Se crean y compilan varios ensamblados en un único paquete NuGet. Los estándares de NuGet garantizan que el paquete puede agregarse a todos los tipos de proyecto compatibles, como los proyectos de Xamarin. iOS y Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Pasos para crear una biblioteca multiplataforma NuGet

1. Seleccione **archivo > nueva solución** (o haga clic con el botón derecho en una solución existente y elija **Agregar > nuevo proyecto**).

2. Elija **biblioteca multiplataforma** en la sección **biblioteca de > multiplataforma** :

    [![](platform-specific-images/mulitplatform-library-sml.png "Configure multi-platform library for a single code base")](platform-specific-images/multiplatform-library.png#lightbox)

3. Escriba un **nombre** y una **Descripción**, y elija específico de la **plataforma**:

    [![](platform-specific-images/specific-configure-sml.png "Configure platform-specific library for iOS and Android")](platform-specific-images/specific-configure.png#lightbox)

4. Complete el asistente. Los proyectos siguientes se agregan a la solución:

    - **Proyecto de Android** : el código específico de Android se puede agregar opcionalmente a este proyecto.
    - **proyecto de iOS** : el código específico de iOS se puede agregar opcionalmente a este proyecto.
    - **Proyecto de NuGet** : no se agrega ningún código a este proyecto. Hace referencia a otros proyectos y contiene la configuración de metadatos para la salida del paquete NuGet.
    - **Proyecto compartido** : se debe agregar código común a este proyecto, incluido el código específico de la plataforma dentro de las directivas de compilador de `#if`.

5. Haga clic con el botón derecho en el proyecto de NuGet, elija **Opciones**y, a continuación, abra la sección **paquetes NuGet > metadatos** y escriba los [metadatos necesarios](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (así como cualquier metadato opcional):

    [![](platform-specific-images/specific-metadata-sml.png "Enter required metadata")](platform-specific-images/specific-metadata.png#lightbox)

6. También en la ventana **Opciones del proyecto** , abra la sección **ensamblados de referencia** y elija qué perfiles de PCL admitirá la biblioteca compartida a través de "cebo and switch":

    ![](platform-specific-images/specific-reference-assemblies.png "Also in the Project Options window, open the Reference Assemblies section and choose   which PCL profiles the shared library will support via bait and switch")

    > [!NOTE]
    > "Cebo and switch" significa que los ensamblados PCL solo contendrán la API expuesta por la biblioteca (no puede contener el código específico de la plataforma). Cuando se agrega NuGet a un proyecto de Xamarin, las bibliotecas compartidas se compilarán en la PCL, pero los ensamblados específicos de la plataforma contienen el código que se usa realmente en el proyecto de iOS o Android.

7. Haga clic con el botón derecho en el proyecto y elija **crear paquete Nuget** (o compilar o implementar la solución) y el archivo de paquete Nuget **. nupkg** se guardará en la carpeta **/bin/** (debug o release, en función de la configuración).

    ![](platform-specific-images/create-nuget-package.png "NuGet package file will be saved in the bin folder either Debug or Release, depending on configuration")

## <a name="verifying-the-output"></a>Comprobación del resultado

Los paquetes NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

En esta captura de pantalla se muestra el contenido de un NuGet específico de la plataforma que admite iOS y Android, y con dos ensamblados de referencia seleccionados:

![](platform-specific-images/nuget-output.png "Files contained in the NuGet package")

## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
