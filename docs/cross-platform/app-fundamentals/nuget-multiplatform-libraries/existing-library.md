---
title: Creación de un NuGet a partir de proyectos de biblioteca existentes
description: En este documento se describe cómo crear un paquete NuGet a partir de un proyecto de biblioteca existente, lo que permite que el código se comparta con otros desarrolladores.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: conceptdev
ms.author: crdun
ms.date: 04/20/2017
ms.openlocfilehash: e70cf426b2357570585c1c8f1cf21715858cb6f6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758157"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Creación de un NuGet a partir de proyectos de biblioteca existentes

Las bibliotecas PCL o .NET Standard existentes se pueden convertir en paquetes Nuget a través de la ventana **Opciones del proyecto** :

1. Haga clic con el botón derecho en el proyecto de biblioteca en el **Panel de solución** y elija **Opciones**.

2. Vaya a la sección **paquete de NuGet > metadatos** y escriba toda la [información necesaria](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en la pestaña **General** :

   [![](existing-library-images/existing-metadata-sml.png "Especificar los metadatos necesarios")](existing-library-images/existing-metadata.png#lightbox)

3. Opcionalmente, [agregue metadatos adicionales](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en la pestaña **detalles** .

4. Una vez configurados los metadatos, puede hacer clic con el botón derecho en el proyecto y elegir **crear paquete Nuget** y el archivo de paquete Nuget **. nupkg** se guardará en la carpeta **/bin/** (debug o release, en función de la configuración).

   ![](existing-library-images/create-nuget-package.png "Elija crear paquete NuGet en el menú contextual.")

5. Para crear el paquete NuGet en _cada_ compilación o implementación, vaya a la sección **paquete Nuget > compilación** y marque **la casilla crear un paquete Nuget al compilar el proyecto**:

    [![](existing-library-images/existing-tickbox-sml.png "Paso para crear un paquete NuGet")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> La generación del paquete NuGet puede ralentizar el proceso de compilación. Si este cuadro no está marcado, todavía puede generar un paquete NuGet manualmente en cualquier momento en el menú contextual del proyecto (que se muestra en el paso 4 anterior).

## <a name="verifying-the-output"></a>Comprobación del resultado

Los paquetes NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

En esta captura de pantalla se muestra el contenido de un NuGet basado en PCL: solo se incluye un ensamblado PCL:

![](existing-library-images/nuget-output.png "Archivos contenidos en el paquete NuGet")

## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
