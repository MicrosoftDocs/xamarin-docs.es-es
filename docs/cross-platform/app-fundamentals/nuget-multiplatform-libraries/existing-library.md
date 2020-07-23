---
title: Creación de un NuGet a partir de proyectos de biblioteca existentes
description: En este documento se describe cómo crear un paquete NuGet a partir de un proyecto de biblioteca existente, lo que permite que el código se comparta con otros desarrolladores.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: davidortinau
ms.author: daortin
ms.date: 04/20/2017
ms.openlocfilehash: 058904236e100a670e8a38417dcaec805178d6d2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936815"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Creación de un NuGet a partir de proyectos de biblioteca existentes

Las bibliotecas PCL o .NET Standard existentes se pueden convertir en paquetes Nuget a través de la ventana **Opciones del proyecto** :

1. Haga clic con el botón derecho en el proyecto de biblioteca en el **Panel de solución** y elija **Opciones**.

2. Vaya a la sección **paquete de NuGet > metadatos** y escriba toda la [información necesaria](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en la pestaña **General** :

   [![Especificar los metadatos necesarios](existing-library-images/existing-metadata-sml.png)](existing-library-images/existing-metadata.png#lightbox)

3. Opcionalmente, [agregue metadatos adicionales](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en la pestaña **detalles** .

4. Una vez configurados los metadatos, puede hacer clic con el botón derecho en el proyecto y elegir **crear paquete Nuget** y el archivo de paquete Nuget **. nupkg** se guardará en la carpeta **/bin/** (debug o release, en función de la configuración).

   ![Elija crear paquete NuGet en el menú contextual.](existing-library-images/create-nuget-package.png)

5. Para crear el paquete NuGet en _cada_ compilación o implementación, vaya a la sección **paquete Nuget > compilación** y marque **la casilla crear un paquete Nuget al compilar el proyecto**:

    [![Paso para crear un paquete NuGet](existing-library-images/existing-tickbox-sml.png)](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> La generación del paquete NuGet puede ralentizar el proceso de compilación. Si este cuadro no está marcado, todavía puede generar un paquete NuGet manualmente en cualquier momento en el menú contextual del proyecto (que se muestra en el paso 4 anterior).

## <a name="verifying-the-output"></a>Comprobación del resultado

Los paquetes NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

En esta captura de pantalla se muestra el contenido de un NuGet basado en PCL: solo se incluye un ensamblado PCL:

![Archivos contenidos en el paquete NuGet](existing-library-images/nuget-output.png)

## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
