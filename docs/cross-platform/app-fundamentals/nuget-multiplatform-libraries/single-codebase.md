---
title: Creación de una nueva biblioteca multiplataforma para NuGet
description: En este documento se describe cómo crear una biblioteca multiplataforma para su uso con NuGet. Esta técnica es adecuada para la lógica de negocios y los algoritmos que se pueden expresar completamente en la biblioteca de clases base de .NET y, por tanto, se ejecutan en todas las plataformas de destino sin código específico de la plataforma.
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: ca874149874c420801c839fcde7afac11f46c5e1
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628922"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Creación de una nueva biblioteca multiplataforma para NuGet

La creación de un proyecto de biblioteca multiplataforma que usa PCL o .NET Standard significa que el NuGet resultante puede agregarse a cualquier proyecto de .NET que admita el perfil de destino, incluidos los proyectos de ASP.NET o las aplicaciones de escritorio que usan WinForms, WPF o UWP.

La biblioteca solo puede contener código compatible con la PCL o el perfil de .NET Standard seleccionado, así como con cualquier otro paquetes Nuget que se agregue.
Esto es adecuado para la lógica de negocios y los algoritmos que se pueden expresar completamente en la biblioteca de clases base de .NET.

Se crea un ensamblado único y se compila en un paquete NuGet.

Si más adelante necesita una funcionalidad específica de la plataforma, [se pueden agregar proyectos específicos de la plataforma](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Pasos para crear una biblioteca multiplataforma NuGet

1. Seleccione **archivo > nueva solución** (o haga clic con el botón derecho en una solución existente y elija **Agregar > nuevo proyecto**).

2. Elija **biblioteca multiplataforma** en la sección **biblioteca de > multiplataforma** :

   [![Captura de pantalla que muestra elegir una plantilla con la biblioteca multiplataforma seleccionada.](single-codebase-images/mulitplatform-library-sml.png)](single-codebase-images/mulitplatform-library.png#lightbox)

3. Escriba un **nombre** y una **Descripción**, y elija **solo para todas las plataformas**:

   [![Captura de pantalla que muestra los valores especificados para el nombre, la descripción y la implementación.](single-codebase-images/single-configure-sml.png)](single-codebase-images/single-configure.png#lightbox)

4. Complete el asistente. Se crea un proyecto de biblioteca único en la solución.

5. Haga clic con el botón derecho en el nuevo proyecto de biblioteca y seleccione **Opciones**. En la sección **compilación > general** se permite establecer la **plataforma de destino** : elija un perfil de PCL portable de .net o una versión .net Standard:

   [![Elegir PCL o .NET Standard para el tipo de biblioteca](single-codebase-images/single-choose-type-sml.png)](single-codebase-images/single-choose-type.png#lightbox)

6. También en la ventana **Opciones del proyecto** , abra la sección **paquete NuGet > metadatos** y escriba los [metadatos necesarios](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (así como cualquier metadato opcional):

   [![Especificar los metadatos necesarios](single-codebase-images/single-metadata-sml.png)](single-codebase-images/single-metadata.png#lightbox)

7. Haga clic con el botón derecho en el proyecto de biblioteca y elija **crear paquete Nuget** (o compilar o implementar la solución) y el archivo de paquete Nuget **. nupkg** se guardará en la carpeta **/bin/** (debug o release, en función de la configuración):

   ![El archivo de paquete NuGet se guardará en la carpeta bin Debug o release, en función de la configuración.](single-codebase-images/create-nuget-package.png)

## <a name="verifying-the-output"></a>Comprobación del resultado

Los paquetes NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

En esta captura de pantalla se muestra el contenido de un NuGet basado en PCL: solo se incluye un ensamblado PCL:

![Archivos contenidos en el paquete NuGet](single-codebase-images/nuget-output.png)

<a name="add-platforms"></a>

## <a name="adding-platform-specific-code"></a>Agregar código de Platform-Specific

Los proyectos basados en PCL y los proyectos basados en .NET Standard no pueden contener referencias específicas de la plataforma (como la funcionalidad de iOS o Android).

Si un proyecto de PCL o un proyecto de .NET Standard existente debe expandirse para incluir código específico de la plataforma, puede hacer clic con el botón derecho en el proyecto y seleccionar **agregar > agregar implementación de plataforma.**..:

[![Menú Agregar implementación de plataforma](single-codebase-images/add-later-sml.png)](single-codebase-images/add-later.png#lightbox)

Se pueden agregar uno o varios proyectos de plataforma a la solución, y la biblioteca PCL o .NET Standard existente se puede convertir opcionalmente en un proyecto compartido:

[![Agregar opciones de plataforma como iOS, Android y proyecto compartido](single-codebase-images/add-later-platforms-sml.png)](single-codebase-images/add-later-platforms-sml.png#lightbox)

Después de realizar la conversión a un proyecto compartido, visite la sección **Opciones de proyecto > paquete NuGet > ensamblados de referencia** 
 [](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) y asegúrese de que se seleccionan los perfiles necesarios (para que NuGet siga siendo compatible con los proyectos en los que se usó anteriormente).

## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
