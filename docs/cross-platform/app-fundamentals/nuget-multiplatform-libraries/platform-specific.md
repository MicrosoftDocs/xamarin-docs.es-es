---
title: Crear nuevos proyectos de biblioteca específica de la plataforma para NuGet
description: En este documento se describe cómo crear un único paquete de NuGet que contiene código específico de la plataforma para varias plataformas.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 73f44acad3e30e4301a69e5f2422cd4dd1a3dbf5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766570"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Crear nuevos proyectos de biblioteca específica de la plataforma para NuGet

Los proyectos de biblioteca multiplataforma que tienen como destino plataformas específicas, como iOS y Android, funcionan mejor con proyectos compartidos.

NuGet puede contener tanto código iOS como específico de Android, así como código .NET común a ambos.

Se crean y compilan varios ensamblados en un único paquete NuGet. Los estándares de NuGet garantizan que el paquete puede agregarse a todos los tipos de proyecto compatibles, como los proyectos de Xamarin. iOS y Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Pasos para crear una biblioteca multiplataforma NuGet

1. Seleccione **archivo > nueva solución** (o haga clic con el botón derecho en una solución existente y elija **Agregar > nuevo proyecto**).

2. Elija **biblioteca multiplataforma** en la sección **biblioteca de > multiplataforma** :

    [![](platform-specific-images/mulitplatform-library-sml.png "Configurar la biblioteca multiplataforma para un solo código base")](platform-specific-images/multiplatform-library.png#lightbox)

3. Escriba un **nombre** y una **Descripción**, y elija específico de la **plataforma**:

    [![](platform-specific-images/specific-configure-sml.png "Configuración de una biblioteca específica de la plataforma para iOS y Android")](platform-specific-images/specific-configure.png#lightbox)

4. Complete el asistente. Los proyectos siguientes se agregan a la solución:

    - **Proyecto de Android** : el código específico de Android se puede agregar opcionalmente a este proyecto.
    - **proyecto de iOS** : el código específico de iOS se puede agregar opcionalmente a este proyecto.
    - **Proyecto de NuGet** : no se agrega ningún código a este proyecto. Hace referencia a otros proyectos y contiene la configuración de metadatos para la salida del paquete NuGet.
    - **Proyecto compartido** : se debe agregar código común a este proyecto, incluido el código específico de la `#if` plataforma dentro de las directivas de compilador.

5. Haga clic con el botón derecho en el proyecto de NuGet, elija **Opciones**y, a continuación, abra la sección **paquetes NuGet > metadatos** y escriba los [metadatos necesarios](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (así como cualquier metadato opcional):

    [![](platform-specific-images/specific-metadata-sml.png "Especificar los metadatos necesarios")](platform-specific-images/specific-metadata.png#lightbox)

6. También en la ventana **Opciones del proyecto** , abra la sección **ensamblados de referencia** y elija qué perfiles de PCL admitirá la biblioteca compartida a través de "cebo and switch":

    ![](platform-specific-images/specific-reference-assemblies.png "También en la ventana Opciones del proyecto, abra la sección de ensamblados de referencia y elija qué perfiles de PCL admitirá la biblioteca compartida a través del cebo y el conmutador.")

    > [!NOTE]
    > "Cebo and switch" significa que los ensamblados PCL solo contendrán la API expuesta por la biblioteca (no puede contener el código específico de la plataforma). Cuando se agrega NuGet a un proyecto de Xamarin, las bibliotecas compartidas se compilarán en la PCL, pero los ensamblados específicos de la plataforma contienen el código que se usa realmente en el proyecto de iOS o Android.

7. Haga clic con el botón derecho en el proyecto y elija **crear paquete Nuget** (o compilar o implementar la solución) y el archivo de paquete Nuget **. nupkg** se guardará en la carpeta **/bin/** (debug o release, en función de la configuración).

    ![](platform-specific-images/create-nuget-package.png "El archivo de paquete NuGet se guardará en la carpeta bin Debug o release, en función de la configuración.")

## <a name="verifying-the-output"></a>Comprobación del resultado

Los paquetes NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

En esta captura de pantalla se muestra el contenido de un NuGet específico de la plataforma que admite iOS y Android, y con dos ensamblados de referencia seleccionados:

![](platform-specific-images/nuget-output.png "Archivos contenidos en el paquete NuGet")

## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
