---
title: Creación de un NuGet a partir de proyectos de biblioteca existentes
description: En este documento se describe cómo crear un paquete NuGet a partir de un proyecto de biblioteca existente, lo que permite que el código se comparta con otros desarrolladores.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: davidortinau
ms.author: daortin
ms.date: 04/20/2017
ms.openlocfilehash: 30158056b8adbdd5aba4cc311220a22502ea9968
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016787"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Creación de un NuGet a partir de proyectos de biblioteca existentes

Las bibliotecas PCL o .NET Standard existentes se pueden convertir en paquetes Nuget a través de la ventana **Opciones del proyecto** :

1. Haga clic con el botón derecho en el proyecto de biblioteca en el **Panel de solución** y elija **Opciones**.

2. Vaya a la sección **paquete de NuGet > metadatos** y escriba toda la [información necesaria](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en la pestaña **General** :

   [![](existing-library-images/existing-metadata-sml.png "Enter required metadata")](existing-library-images/existing-metadata.png#lightbox)

3. Opcionalmente, [agregue metadatos adicionales](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en la pestaña **detalles** .

4. Una vez configurados los metadatos, puede hacer clic con el botón derecho en el proyecto y elegir **crear paquete Nuget** y el archivo de paquete Nuget **. nupkg** se guardará en la carpeta **/bin/** (debug o release, en función de la configuración).

   ![](existing-library-images/create-nuget-package.png "Choose Create NuGet Package from the right-click menu")

5. Para crear el paquete NuGet en _cada_ compilación o implementación, vaya a la sección **paquete Nuget > compilación** y marque **la casilla crear un paquete Nuget al compilar el proyecto**:

    [![](existing-library-images/existing-tickbox-sml.png "Tick to create a NuGet package")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> La generación del paquete NuGet puede ralentizar el proceso de compilación. Si este cuadro no está marcado, todavía puede generar un paquete NuGet manualmente en cualquier momento en el menú contextual del proyecto (que se muestra en el paso 4 anterior).

## <a name="verifying-the-output"></a>Comprobación del resultado

Los paquetes NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

En esta captura de pantalla se muestra el contenido de un NuGet basado en PCL: solo se incluye un ensamblado PCL:

![](existing-library-images/nuget-output.png "Files contained in the NuGet package")

## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
