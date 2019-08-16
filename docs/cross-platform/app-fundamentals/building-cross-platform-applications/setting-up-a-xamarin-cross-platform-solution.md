---
title: 'Parte 3: configuración de una solución multiplataforma de Xamarin'
description: En este documento se describe cómo configurar una solución multiplataforma en Xamarin. Describe diversas estrategias de uso compartido de código, como proyectos compartidos y .NET Standard.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: a33c924df3da8642f4b765868f213e6e196f7866
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526807"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3: configuración de una solución multiplataforma de Xamarin

Independientemente de las plataformas que se usen, todos los proyectos de Xamarin usan el mismo formato de archivo de solución (el formato de archivo **. sln** de Visual Studio). Las soluciones pueden compartirse entre entornos de desarrollo, incluso cuando no se pueden cargar proyectos individuales (por ejemplo, un proyecto de Windows en Visual Studio para Mac).



Al crear una nueva aplicación multiplataforma, el primer paso es crear una solución en blanco. En esta sección se explica lo que ocurre después: configurar los proyectos para compilar aplicaciones móviles multiplataforma.

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>Uso compartido de código

Consulte el documento [Opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md) para obtener una descripción detallada de cómo implementar el uso compartido de código entre plataformas.

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>Proyectos compartidos

El enfoque más sencillo para compartir archivos de código es usar un [proyecto compartido](~/cross-platform/app-fundamentals/shared-projects.md).

Este método permite compartir el mismo código en diferentes proyectos de la plataforma y usar directivas de compilador para incluir rutas de acceso de código diferentes específicas de la plataforma.

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>Bibliotecas de clases portables (PCL)

Históricamente, un archivo de proyecto .NET (y el ensamblado resultante) se ha destinado a una versión específica de Framework. Esto evita que el proyecto o el ensamblado se compartan con diferentes marcos.

Una biblioteca de clases portable (PCL) es un tipo especial de proyecto que se puede usar en distintas plataformas de la CLI, como Xamarin. iOS y Xamarin. Android, así como WPF, Plataforma universal de Windows y Xbox. La biblioteca solo puede emplear un subconjunto de la versión completa de .NET Framework, limitada por las plataformas de destino.

Puede leer más sobre la compatibilidad de Xamarin con las [bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md) y seguir las instrucciones para ver cómo funciona el [ejemplo TaskyPortable](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) .


### <a name="net-standard"></a>.NET Standard

Los proyectos de [.net Standard](~/cross-platform/app-fundamentals/net-standard.md) , que se introdujeron en 2016, proporcionan una manera sencilla de compartir código entre plataformas y generar ensamblados que se pueden usar en Windows, plataformas de Xamarin (iOS, Android, Mac) y Linux.

.NET Standard bibliotecas se pueden crear y usar como PCL, salvo que las API disponibles en cada versión (de 1,0 a 1,6) se detectan más fácilmente y cada versión es compatible con versiones anteriores con números de versión inferiores.



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>Rellenar la solución

Independientemente del método que se use para compartir código, la estructura general de la solución debe implementar una arquitectura en capas que fomente el uso compartido de código.
El enfoque de Xamarin es agrupar el código en dos tipos de proyecto:

- **Proyecto principal** : Escriba código que se pueda volver a usar en un solo lugar para que se comparta en distintas plataformas. Use los principios de la encapsulación para ocultar los detalles de implementación siempre que sea posible.
- **Proyectos de aplicación específicos de la plataforma** : consume el código reutilizable con el menor acoplamiento posible. En este nivel se agregan características específicas de la plataforma, que se basan en los componentes expuestos en el proyecto principal.


 <a name="Core_Project" />


### <a name="core-project"></a>Proyecto principal

Los proyectos de código compartidos solo deben hacer referencia a ensamblados que están disponibles en todas las plataformas: IE. los espacios de nombres de Common `System`Framework `System.Core` como `System.Xml`, y.

Los proyectos compartidos deben implementar tanta funcionalidad que no sea de interfaz de usuario como sea posible, lo que podría incluir los siguientes niveles:

- **Capa de datos** : código que se encarga del almacenamiento físico de datos, por ejemplo.  [SQLite-net](https://github.com/praeclarum/sqlite-net), una base de datos alternativa como [Realm.IO](https://realm.io/products/realm-mobile-database/) o incluso archivos XML. Normalmente, la capa de acceso a datos solo utiliza las clases del nivel de datos.
- **Capa de acceso a datos** : define una API que admite las operaciones de datos necesarias para la funcionalidad de la aplicación, como los métodos para tener acceso a las listas de datos, los elementos de datos individuales y también crearlos, modificarlos y eliminarlos.
- **Nivel de acceso de servicio** : una capa opcional para proporcionar servicios en la nube a la aplicación. Contiene código que obtiene acceso a los recursos de la red remota (servicios Web, descargas de imágenes, etc.) y, posiblemente, almacenamiento en caché de los resultados.
- **Capa de negocio** : definición de las clases de modelo y las clases de fachada o de administrador que exponen la funcionalidad a las aplicaciones específicas de la plataforma.


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>Proyectos de aplicación específicos de la plataforma

Los proyectos específicos de la plataforma deben hacer referencia a los ensamblados necesarios para enlazar con el SDK de cada plataforma (Xamarin. iOS, Xamarin. Android, Xamarin. Mac o Windows), así como el proyecto de código compartido principal.

Los proyectos específicos de la plataforma deben implementar:

- **Capa de aplicación** : funcionalidad específica de la plataforma y enlace/conversión entre los objetos de nivel de negocio y la interfaz de usuario.
- **Capa** de la interfaz de usuario: pantallas, controles de interfaz de usuario personalizados, presentación de la lógica de validación.


<a name="Example" />


### <a name="example"></a>Ejemplo

En este diagrama se ilustra la arquitectura de la aplicación:

 [![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "En este diagrama se ilustra la arquitectura de la aplicación.")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

En esta captura de pantalla se muestra una configuración de soluciones con los proyectos de proyecto principal, iOS y aplicación de Android compartidos. El proyecto compartido contiene código relacionado con cada uno de los niveles arquitectónicos (código de negocio, servicio, datos y acceso a datos):

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "El proyecto compartido contiene código relacionado con cada uno de los niveles arquitectónicos (negocio, servicio, datos y código de acceso a datos)")


 <a name="Project_References" />


## <a name="project-references"></a>Referencias del proyecto

Las referencias de proyecto reflejan las dependencias de un proyecto. Los proyectos principales limitan sus referencias a ensamblados comunes para que el código sea fácil de compartir.
Los proyectos de aplicación específicos de la plataforma hacen referencia al código compartido, además de cualquier otro ensamblado específico de la plataforma que necesiten aprovechar la plataforma de destino.

La aplicación proyecta cada proyecto compartido de referencia y contiene el código de interfaz de usuario necesario para presentar la funcionalidad al usuario, como se muestra en estas capturas de pantallas:

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "La aplicación de los proyectos cada referencia de proyecto compartido") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "la aplicación de los proyectos cada referencia de proyecto compartido")


En los casos prácticos se proporcionan ejemplos específicos de cómo se deben estructurar los proyectos.

 <a name="Adding_Files" />


## <a name="adding-files"></a>Agregar archivos

 <a name="Build_Action" />


### <a name="build-action"></a>Acción de compilación

Es importante establecer la acción de compilación correcta para determinados tipos de archivo. En esta lista se muestra la acción de compilación para algunos tipos de archivo comunes:

- **Todos C# los archivos** : acción de compilación: Compile
- **Imágenes en Xamarin. iOS & Windows** : acción de compilación: Contenido
- **Archivos Xib y Storyboard en Xamarin. iOS** : acción de compilación: InterfaceDefinition
- **Imágenes y diseños AXML en Android** : acción de compilación: AndroidResource
- **Archivos XAML en proyectos de Windows** : acción de compilación: Página
- **Archivos XAML de Xamarin. Forms** : acción de compilación: EmbeddedResource


Por lo general, el IDE detectará el tipo de archivo y sugerirá la acción de compilación correcta.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Distinción de mayúsculas y minúsculas

Por último, recuerde que algunas plataformas tienen sistemas de archivos que distinguen mayúsculas de minúsculas (por ejemplo,
iOS y Android) por lo tanto, asegúrese de usar un estándar de nomenclatura de archivos coherente y asegúrese de que los nombres de archivo que usa en el código coinciden exactamente con el sistema de archivos. Esto es especialmente importante para las imágenes y otros recursos a los que se hace referencia en el código.
