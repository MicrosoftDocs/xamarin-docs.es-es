---
title: Información general sobre el uso compartido de código
description: 'En este documento se comparan los distintos métodos para compartir código entre proyectos multiplataforma: proyectos compartidos, bibliotecas de clases portables y .NET Standard, incluidas las ventajas y desventajas de cada uno.'
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: davidortinau
ms.author: daortin
ms.date: 08/06/2018
ms.openlocfilehash: b097a0549db6178576d9e10eb3282c88e96b9ddb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453303"
---
# <a name="sharing-code-overview"></a>Información general sobre el uso compartido de código

_En este documento se comparan los distintos métodos para compartir código entre proyectos multiplataforma: .NET Standard, proyectos compartidos y bibliotecas de clases portables, incluidas las ventajas y desventajas de cada uno._

Hay tres métodos para compartir código entre aplicaciones multiplataforma:

- [**.Net Standard bibliotecas**](#Net_Standard) : los proyectos de .net Standard pueden implementar código que se va a compartir entre varias plataformas y pueden tener acceso a un gran número de API de .net (dependiendo de la versión). .NET Standard 1,0-1,6 implementan conjuntos de API progresivamente mayores, mientras que .NET Standard 2,0 proporciona la mejor cobertura de la BCL de .NET (incluidas las API de .NET disponibles en las aplicaciones de Xamarin).
- [**Proyectos compartidos**](#Shared_Projects) : Use el tipo de proyecto de recursos compartidos para organizar el código fuente y use `#if` directivas de compilador según sea necesario para administrar los requisitos específicos de la plataforma.
- [**Bibliotecas de clases portables**](#Portable_Class_Libraries) (en desuso): las bibliotecas de clases portables (PCL) pueden tener como destino varias plataformas con una superficie de API común y usar interfaces para proporcionar funcionalidad específica de la plataforma. Los PCL están en desuso en las versiones más recientes de Visual Studio &ndash; usan .net Standard en su lugar.

El objetivo de una estrategia de uso compartido de código es admitir la arquitectura que se muestra en este diagrama, donde varias plataformas pueden utilizar un solo código base.

 ![Arquitectura de aplicación de código compartido](code-sharing-images/conceptualarchitecture.png "Arquitectura de aplicación de código compartido")

En este artículo se comparan los métodos disponibles para ayudarle a elegir el tipo de proyecto adecuado para las aplicaciones.

<a name="Net_Standard"></a>

## <a name="net-standard-libraries"></a>Bibliotecas de .NET Standard

Las bibliotecas de [.net Standard](~/cross-platform/app-fundamentals/net-standard.md) proporcionan un conjunto bien definido de las bibliotecas de clases base a las que se puede hacer referencia en diferentes tipos de proyecto, incluidos proyectos multiplataforma como Xamarin. Android y Xamarin. iOS. Se recomienda .NET Standard 2,0 para obtener la máxima compatibilidad con el código de .NET Framework existente.

![Diagrama de .NET Standard](code-sharing-images/netstandard.png "Diagrama de .NET Standard")

### <a name="benefits"></a>Ventajas

- Permite compartir código en varios proyectos.
- Las operaciones de refactorización siempre actualizan todas las referencias afectadas.
- Un área expuesta mayor de la biblioteca de clases base (BCL) de .NET está disponible en lugar de los perfiles PCL. En concreto, .NET Standard 2,0 tiene casi la misma superficie de API que la .NET Framework y se recomienda para las nuevas aplicaciones y migrar los PCL existentes.

### <a name="disadvantages"></a>Desventajas

- No se pueden usar directivas de compilador como `#if __IOS__` .

### <a name="remarks"></a>Comentarios

.NET Standard es [similar a PCL](/dotnet/standard/net-standard#comparison-to-portable-class-libraries), pero con un modelo más sencillo para la compatibilidad con plataformas y un mayor número de clases de la BCL.

<a name="Shared_Projects"></a>

## <a name="shared-projects"></a>Proyectos compartidos

Los [proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md) contienen archivos de código y recursos que se incluyen en cualquier proyecto que haga referencia a ellos. Los proyectos de uso compartido no producen resultados compilados por sí solos.

Esta captura de pantalla muestra un archivo de solución que contiene tres proyectos de aplicación (para Android, iOS y Windows), con un proyecto **compartido** que contiene archivos de código fuente de C# comunes:

![Solución de proyecto compartido](code-sharing-images/sharedsolution.png "Solución de proyecto compartido")

La arquitectura conceptual se muestra en el diagrama siguiente, donde cada proyecto incluye todos los archivos de código fuente compartidos:

![Diagrama del proyecto compartido](code-sharing-images/sharedassetproject.png "Diagrama del proyecto compartido")

### <a name="example"></a>Ejemplo

Una aplicación multiplataforma que admita iOS, Android y Windows requerirá un proyecto de aplicación para cada plataforma. El código común reside en el proyecto compartido.

Una solución de ejemplo contendría las siguientes carpetas y proyectos (los nombres de proyecto se han elegido para la expresividad, los proyectos no tienen que seguir estas instrucciones de nomenclatura):

- **Compartido** : proyecto compartido que contiene el código común a todos los proyectos.
- **AppAndroid** : proyecto de aplicación Xamarin. Android.
- **AppiOS** : proyecto de aplicación Xamarin. iOS.
- **AppWindows** : proyecto de aplicación para Windows.

De esta manera, los tres proyectos de aplicación comparten el mismo código fuente (los archivos de C# en Shared). Cualquier modificación del código compartido se compartirá entre los tres proyectos.

### <a name="benefits"></a>Ventajas

- Permite compartir código en varios proyectos.
- El código compartido se puede bifurcar en función de la plataforma mediante directivas de compilador (por ejemplo, con  `#if __ANDROID__` , como se describe en el documento  [creación de aplicaciones multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) ).
- Los proyectos de aplicación pueden incluir referencias específicas de la plataforma que puede utilizar el código compartido (como  `Community.CsharpSqlite.WP7` el uso de en el ejemplo de tarea para Windows Phone).

### <a name="disadvantages"></a>Desventajas

- Las refactorizaciones que afectan al código dentro de las directivas de compilador ' Inactive ' no actualizarán el código dentro de esas directivas.
- A diferencia de la mayoría de los demás tipos de proyecto, un proyecto compartido no tiene ningún ensamblado ' Output '. Durante la compilación, los archivos se tratan como parte del proyecto de referencia y se compilan en ese ensamblado. Si desea compartir el código como un ensamblado, .NET Standard o las bibliotecas de clases portables son una solución mejor.

<a name="Shared_Remarks"></a>

### <a name="remarks"></a>Comentarios

Una buena solución para que los desarrolladores de aplicaciones escriban código que solo está diseñado para compartir en su aplicación (y no se distribuye a otros desarrolladores).

<a name="Portable_Class_Libraries"></a>

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables

> [!TIP]
> Se recomiendan .NET Standard bibliotecas de 2,0 a través de bibliotecas de clases portables.

Las bibliotecas de clases portables se [describen en detalle aquí](~/cross-platform/app-fundamentals/pcl.md).

![Diagrama de la biblioteca de clases portable](code-sharing-images/portableclasslibrary.png "Diagrama de la biblioteca de clases portable")

### <a name="benefits"></a>Ventajas

- Permite compartir código en varios proyectos.
- Las operaciones de refactorización siempre actualizan todas las referencias afectadas.

### <a name="disadvantages"></a>Desventajas

- En desuso en las versiones más recientes de Visual Studio, en su lugar se recomienda .NET Standard bibliotecas. Consulte esta [explicación de las diferencias](/dotnet/standard/net-standard#comparison-to-portable-class-libraries) entre PCL y .net Standard.
- No se pueden usar directivas de compilador.
- Solo un subconjunto de .NET Framework está disponible para su uso, determinado por el perfil seleccionado (consulte  [Introducción a PCL](~/cross-platform/app-fundamentals/pcl.md) para obtener más información).

### <a name="remarks"></a>Comentarios

La plantilla PCL se considera en desuso en las versiones más recientes de Visual Studio.

## <a name="summary"></a>Resumen

La estrategia de uso compartido de código que elija dependerá de las plataformas de destino. Elija un método que funcione mejor para su proyecto.

.NET Standard es la mejor opción para compilar bibliotecas de código que se pueda compartir (especialmente publicar en NuGet). Los proyectos compartidos funcionan bien para los desarrolladores de aplicaciones que planean usar una gran cantidad de funcionalidad específica de la plataforma en sus aplicaciones multiplataforma.

Aunque los proyectos PCL siguen siendo compatibles con Visual Studio, se recomienda .NET Standard para los proyectos nuevos.

## <a name="related-links"></a>Vínculos relacionados

- [Compilar aplicaciones multiplataforma (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md)
- [Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Caso práctico: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Ejemplo de tarea (GitHub)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Ejemplo de tarea con PCL (GitHub)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)