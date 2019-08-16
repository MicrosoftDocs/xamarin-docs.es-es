---
title: Introducción al almacenamiento de datos en aplicaciones de Xamarin. iOS
description: En este documento se describen los distintos medios de almacenamiento de datos en una aplicación de Xamarin. iOS y se proporciona información específica sobre las ventajas de SQLite.
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 375144804fceb473ec4642dbcf451497c55bcba7
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527322"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>Introducción al almacenamiento de datos en aplicaciones de Xamarin. iOS

## <a name="when-to-use-a-database"></a>Cuándo usar una base de datos

Aunque las capacidades de almacenamiento y procesamiento de los dispositivos móviles están aumentando, los teléfonos y las tabletas siguen &amp; retrasando sus homólogos de equipos portátiles de escritorio. Por esta razón, merece la pena dedicar algún tiempo a planear la arquitectura de almacenamiento de datos de la aplicación en lugar de asumir simplemente que una base de datos es la respuesta correcta en todo momento. Hay varias opciones diferentes que se adaptan a diferentes requisitos, como:

- **Preferencias** : iOS ofrece un mecanismo integrado para almacenar pares clave-valor simples de datos. Si va a almacenar una configuración de usuario simple o pequeñas partes de datos (como información de personalización), use las características nativas de la plataforma para almacenar este tipo de información. En el caso de iOS, también puede aprovechar la sincronización de iCloud para estos datos, tanto para la copia de seguridad como para la sincronización de usuarios con varios dispositivos.
- **Archivos de texto** : datos proporcionados por el usuario o memorias caché del contenido descargado (por ejemplo, HTML) se puede almacenar directamente en el sistema de archivos. Use una Convención de nomenclatura de archivos adecuada para ayudarle a organizar los archivos y a encontrar los datos.
- **Archivos de datos serializados: los** objetos se pueden almacenar como XML o JSON en el sistema de archivos. .NET Framework incluye bibliotecas que facilitan la serialización y la deserialización de objetos. Use los nombres adecuados para organizar los archivos de datos.
- **Base** de datos: el motor de base de datos de SQLite está disponible en iOS y es útil para almacenar datos estructurados que necesita consultar, ordenar o manipular de otra manera. El almacenamiento de base de datos es adecuado para listas de datos con muchas propiedades.
- **Archivos de imagen** : aunque es posible almacenar datos binarios en la base de datos en un dispositivo móvil, se recomienda almacenarlos directamente en el sistema de archivos. Si es necesario, puede almacenar los nombres de archivo en una base de datos para asociar la imagen a otros datos. Cuando se trabaja con imágenes de gran tamaño, o muchas imágenes, es recomendable planear una estrategia de almacenamiento en caché que elimine los archivos que ya no necesita para evitar consumir todo el espacio de almacenamiento del usuario.


Si una base de datos es el mecanismo de almacenamiento adecuado para su aplicación, en el resto de este documento se explica cómo usar SQLite en la plataforma de Xamarin.

## <a name="advantages-of-using-a-database"></a>Ventajas del uso de una base de datos

Hay varias ventajas en el uso de una base de datos SQL en la aplicación móvil:

- Las bases de datos SQL permiten un almacenamiento eficaz de datos estructurados.
- Los datos específicos se pueden extraer con consultas complejas.
- Los resultados de la consulta se pueden ordenar.
- Los resultados de la consulta se pueden agregar.
- Los desarrolladores con conocimientos de bases de datos existentes pueden aprovechar sus conocimientos para diseñar la base de datos y el código de acceso a datos.
- El modelo de datos del componente de servidor de una aplicación conectada se puede volver a usar (en su totalidad o en parte) en la aplicación móvil.


## <a name="sqlite-database-engine"></a>Motor de base de datos SQLite

SQLite es un motor de base de datos de código abierto que ha adoptado Apple para su plataforma móvil. El motor de base de datos SQLite está integrado en iOS, por lo que no hay trabajo adicional para que los desarrolladores puedan aprovecharlo. SQLite es idóneo para el desarrollo móvil multiplataforma porque:

- El motor de base de datos es pequeño, rápido y fácil de transportar.
- Una base de datos se almacena en un único archivo, que es fácil de administrar en dispositivos móviles.
- El formato de archivo es fácil de usar entre plataformas: si se trata de sistemas de 32 o 64 bits y de Big-Endian.
- Implementa la mayor parte del estándar SQL92.


Dado que SQLite está diseñado para ser pequeño y rápido, hay algunas advertencias sobre su uso:

- No se admiten algunas sintaxis de combinación externa.
- Solo se admiten Rename de tabla y ADDCOLUMN. No se pueden realizar otras modificaciones en el esquema.
- Las vistas son de solo lectura.


Puede obtener más información acerca de SQLite en el sitio Web- [SQLite.org](http://SQLite.org) ; sin embargo, toda la información que necesita para usar SQLite con Xamarin se incluye en este documento y en los ejemplos asociados. El motor de base de datos SQLite está integrado en todas las versiones de iOS.
Aunque no se trata en este capítulo, SQLite también está disponible para su uso en Windows Phone y aplicaciones Windows.

## <a name="windows-and-windows-phone"></a>Windows y Windows Phone

SQLite también puede usarse en plataformas Windows, aunque dichas plataformas no se describen en este documento.
Lea más en la [tarea](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) y en los casos prácticos de [Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky) con tareas y revise el [blog de Tim Heuer](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).



## <a name="related-links"></a>Vínculos relacionados

- [Acceso a datos básico (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [Acceso a la configuración avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acceso a datos de Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
