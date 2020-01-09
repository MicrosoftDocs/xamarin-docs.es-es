---
title: 'Parte 3: configuración de una solución multiplataforma de Xamarin'
description: En este documento se describe cómo configurar una solución multiplataforma en Xamarin. Describe diversas estrategias de uso compartido de código, como proyectos compartidos y .NET Standard.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: e7cde22115830a845ed82aa907195521f36b6866
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663278"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3: configuración de una solución multiplataforma de Xamarin

Independientemente de las plataformas que se usen, todos los proyectos de Xamarin usan el mismo formato de archivo de solución (el formato de archivo **. sln** de Visual Studio). Las soluciones pueden compartirse entre entornos de desarrollo, incluso cuando no se pueden cargar proyectos individuales (por ejemplo, un proyecto de Windows en Visual Studio para Mac).

Al crear una nueva aplicación multiplataforma, el primer paso es crear una solución en blanco. En esta sección se explica lo que ocurre después: configurar los proyectos para compilar aplicaciones móviles multiplataforma.

## <a name="sharing-code"></a>Compartir código

Consulte el documento [Opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md) para obtener una descripción detallada de cómo implementar el uso compartido de código entre plataformas.

### <a name="net-standard"></a>Estándar .NET

Los proyectos de [.net Standard](~/cross-platform/app-fundamentals/net-standard.md) proporcionan una manera fácil de compartir código entre plataformas, lo que genera ensamblados que se pueden usar en Windows, plataformas de Xamarin (iOS, Android, Mac) y Linux.
Esta es la manera recomendada de compartir código para las soluciones de Xamarin.

### <a name="other-options"></a>Otras opciones

Históricamente, se usaban [bibliotecas de clases portables (PCL)](~/cross-platform/app-fundamentals/pcl.md)] y [proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md). Ninguno de ellos se recomienda para los proyectos nuevos; Además, debe considerar la posibilidad de migrar las aplicaciones existentes para usar .NET Standard.

## <a name="populating-the-solution"></a>Rellenar la solución

Independientemente del método que se use para compartir código, la estructura general de la solución debe implementar una arquitectura en capas que fomente el uso compartido de código.
El enfoque de Xamarin es agrupar el código en dos tipos de proyecto:

- **Proyecto básico (o "compartido")** : Escriba código reutilizable en un solo lugar para que se comparta en distintas plataformas. Use los principios de la encapsulación para ocultar los detalles de implementación siempre que sea posible.
- **Proyectos de aplicación específicos de la plataforma** : consume el código reutilizable con el menor acoplamiento posible. En este nivel se agregan características específicas de la plataforma, que se basan en los componentes expuestos en el proyecto principal.

### <a name="core-project"></a>Proyecto principal

Los proyectos principales que comparten código deben ser .NET Standard y solo los ensamblados de referencia que están disponibles en todas las plataformas: IE. los espacios de nombres comunes del marco como `System`, `System.Core` y `System.Xml`.

Los proyectos principales deben implementar tanta funcionalidad que no sea de interfaz de usuario como sea posible, lo que podría incluir los siguientes niveles:

- **Capa de datos** : código que se encarga del almacenamiento físico de datos, por ejemplo. [SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/) o incluso archivos XML. Normalmente, la capa de acceso a datos solo utiliza las clases del nivel de datos.
- **Capa de acceso a datos** : define una API que admite las operaciones de datos necesarias para la funcionalidad de la aplicación, como los métodos para tener acceso a las listas de datos, los elementos de datos individuales y también crearlos, modificarlos y eliminarlos.
- **Nivel de acceso de servicio** : una capa opcional para proporcionar servicios en la nube a la aplicación. Contiene código que obtiene acceso a los recursos de la red remota (servicios Web, descargas de imágenes, etc.) y, posiblemente, almacenamiento en caché de los resultados.
- **Capa de negocio** : definición de las clases de modelo y las clases de fachada o de administrador que exponen la funcionalidad a las aplicaciones específicas de la plataforma.

### <a name="platform-specific-application-projects"></a>Proyectos de aplicación específicos de la plataforma

Los proyectos específicos de la plataforma deben hacer referencia a los ensamblados necesarios para enlazar con el SDK de cada plataforma (Xamarin. iOS, Xamarin. Android, Xamarin. Mac o Windows), así como el proyecto .NET Standard.

Los proyectos específicos de la plataforma deben implementar:

- **Capa de aplicación** : funcionalidad específica de la plataforma y enlace/conversión entre los objetos de nivel de negocio y la interfaz de usuario.
- **Capa** de la interfaz de usuario: pantallas, controles de interfaz de usuario personalizados, presentación de la lógica de validación.

## <a name="project-references"></a>Referencias de proyecto

Las referencias de proyecto reflejan las dependencias de un proyecto. Los proyectos principales limitan sus referencias a ensamblados comunes para que el código sea fácil de compartir.
Los proyectos de aplicación específicos de la plataforma hacen referencia al proyecto de .NET Standard, además de a cualquier otro ensamblado específico de la plataforma que necesiten aprovechar la plataforma de destino.

En los casos prácticos se proporcionan ejemplos específicos de cómo se deben estructurar los proyectos.

## <a name="adding-files"></a>Adición de archivos

### <a name="build-action"></a>Acción de generación

Es importante establecer la acción de compilación correcta para determinados tipos de archivo. En esta lista se muestra la acción de compilación para algunos tipos de archivo comunes:

- **Todos C# los archivos** : acción de compilación: compilar
- **Imágenes en Xamarin. iOS & Windows** : acción de compilación: contenido
- **Archivos Xib y Storyboard en Xamarin. iOS** : acción de compilación: InterfaceDefinition
- **Imágenes y diseños XML en Android** : acción de compilación: AndroidResource
- **Archivos XAML en proyectos de Windows** : acción de compilación: Página
- **Archivos XAML de Xamarin. Forms** : acción de compilación: EmbeddedResource

Por lo general, el IDE detectará el tipo de archivo y sugerirá la acción de compilación correcta.

### <a name="case-sensitivity"></a>Distinción de mayúsculas y minúsculas

Por último, recuerde que algunas plataformas tienen sistemas de archivos que distinguen mayúsculas de minúsculas (por ejemplo,
iOS y Android) por lo tanto, asegúrese de usar un estándar de nomenclatura de archivos coherente y asegúrese de que los nombres de archivo que usa en el código coinciden exactamente con el sistema de archivos. Esto es especialmente importante para las imágenes y otros recursos a los que se hace referencia en el código.
