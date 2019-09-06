---
title: ¿Cómo se puede cambiar a una versión anterior un paquete NuGet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: 33f542d0da48f0cd3f7e1bcb85ae06137d8be3cd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288326"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>¿Cómo se puede cambiar a una versión anterior un paquete NuGet?

Visual Studio para Mac & Visual Studio tienen características para seleccionar versiones anteriores de paquetes e instalarlas automáticamente. similar al funcionamiento de los paquetes de actualización. A continuación se describen estos pasos.

## <a name="visual-studio"></a>Visual Studio

1. Vaya a **herramientas > administrador de paquetes NuGet > consola del administrador de paquetes**
2. Establecer el proyecto en **proyecto predeterminado**
3. Use esta sintaxis:

    > Install-Package [PackageName]-version [pestaña para el menú de versión]

También puede copiar y pegar el comando exacto desde la página NuGet del paquete. Ejemplo de Xamarin. Forms:[https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. En el proyecto, haga clic con el botón derecho en la carpeta Packages & Seleccione **agregar paquetes** .
2. En barra, puede usar la siguiente sintaxis para buscar los paquetes necesarios:

    `[PackageName] version:*`

### <a name="examples"></a>Ejemplos 
- Enumera todos los paquetes de Xamarin. Forms: 

    `Xamarin.Forms version:`

- Enumera todos los paquetes de Xamarin. Forms 1.4. x: 

    `Xamarin.Forms version:1.4`

*Nota: Si agrega un espacio entre `version:` & el número de versión, la búsqueda se comportará como si no se hubiera especificado ninguna versión.*
