---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Guía de migración de aplicaciones de escritorio
description: Una simple explicación de cómo desacoplar las aplicaciones de Windows Forms o WPF existentes para crear aplicaciones multiplataforma que se ejecuten en macOS, iOS, Android, así como UWP/Windows 10.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: bdea1c472d95c86037056a2905679b43e12e0468
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120378"
---
# <a name="desktop-app-porting-guidance"></a>Guía de migración de aplicaciones de escritorio

La mayoría del código de la aplicación se puede clasificar en una de las siguientes áreas:

- Código de la interfaz de usuario (por ejemplo, ventanas y botones)
- controles de terceros (por ejemplo, cartas
- Lógica de negocios (p. ej., reglas de validación)
- Acceso y almacenamiento de datos locales
- Servicios web y acceso a datos remotos

En el caso de las aplicaciones de C# Windows Forms y WPF escritas con (o Visual Basic.net), se puede compartir una cantidad sorprendente de la lógica de negocios, el acceso a datos locales y el código de servicios web entre plataformas.

## <a name="net-portability-analyzer"></a>Analizador de portabilidad de .NET

Visual Studio 2017 y versiones posteriores admiten el analizador de portabilidad de [.net](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer) ([descarga para Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)), que puede examinar las aplicaciones existentes e indicar la cantidad de código que se puede migrar "tal cual" a otras plataformas. Puede obtener más información al respecto desde este [vídeo de Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

También se puede descargar una herramienta de línea de comandos desde el [analizador de portabilidad en github](https://github.com/Microsoft/dotnet-apiport) y se usa para proporcionar los mismos informes.

## <a name="x-of-my-code-is-portable-what-next"></a>"x% de mi código es portátil. ¿Qué es lo siguiente? "

Espero que el analizador muestre que una gran parte del código es portátil, pero ciertamente va a ser algunas partes de cada aplicación que _no_ se pueden mover a otras plataformas.

Los diferentes fragmentos de código probablemente se incluirán en uno de estos depósitos, que se explica con más detalle a continuación:

- Código portable reutilizable
- Código que requiere cambios
- Código que no es portátil y requiere volver a escribir

### <a name="re-useable-portable-code"></a>Código portable reutilizable

El código de .NET que se escribe en las API disponibles en todas las plataformas se puede realizar de forma multiplataforma sin cambios. Idealmente, podrá mover todo este código a una biblioteca de clases portable, una biblioteca compartida o una biblioteca de .NET Standard y, a continuación, probarla dentro de la aplicación existente.

Esa biblioteca compartida se puede Agregar a los proyectos de aplicación para otras plataformas (como Android, iOS, macOS).

### <a name="code-that-requires-changes"></a>Código que requiere cambios

Es posible que algunas API de .NET no estén disponibles en todas las plataformas. Si estas API existen en el código, deberá volver a escribirlas para usar las API multiplataforma.

Entre los ejemplos de esto se incluye el uso de las API de reflexión que están disponibles en .NET 4,6, pero que no están disponibles en todas las plataformas.

Después de volver a escribir el código mediante las API portátiles, debería poder empaquetar ese código en una biblioteca compartida y probarlo dentro de la aplicación existente.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Código que no es portátil y requiere volver a escribir

Entre los ejemplos de código que no es probable que sea multiplataforma, se incluyen:

- **Interfaz de usuario** : las pantallas de Windows Forms o WPF no se pueden usar en proyectos de Android o iOS, por ejemplo. La interfaz de usuario tendrá que volver a escribirse, utilizando esta [comparación de controles](~/cross-platform/desktop/controls/index.md) como referencia.

- Código de **almacenamiento específico** de la plataforma que se basa en una tecnología específica de la plataforma (por ejemplo, una base de datos SQL Server Express local). Tendrá que volver a escribirlo mediante una alternativa multiplataforma (como SQLite para el motor de base de datos).
Es posible que también sea necesario ajustar algunas operaciones del sistema de archivos, ya que UWP tiene API ligeramente diferentes para Android e iOS (por ejemplo, algunos sistemas de archivos distinguen mayúsculas de minúsculas y otros no.

- **componentes** de terceros: Compruebe si los componentes de terceros de las aplicaciones están disponibles en otras plataformas. Algunos, como los paquetes NuGet no visuales, pueden estar disponibles, pero otros (especialmente controles visuales como gráficos o reproductores multimedia).

## <a name="tips-for-making-code-portable"></a>Sugerencias para que el código sea portátil

- **Inserción** de dependencias: proporcione diferentes implementaciones para cada plataforma.

- **Enfoque en capas** , ya sea MVVM, MVC, MVP o algún otro patrón que le ayude a separar el código portable del código específico de la plataforma.

- **Mensajería** : puede usar el paso de mensajes en el código para desacoplar las interacciones entre diferentes partes de la aplicación.
