---
title: Introducción a ContentProviders
description: El sistema operativo Android usa proveedores de contenido para facilitar el acceso a datos compartidos, como archivos multimedia, contactos e información del calendario. En este artículo se presenta la clase ContentProvider y se proporcionan dos ejemplos de cómo usarla.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 00da8b668ea26aa9146ff0c30d07a60cefe6695a
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454018"
---
# <a name="intro-to-contentproviders"></a>Introducción a ContentProviders

_El sistema operativo Android usa proveedores de contenido para facilitar el acceso a datos compartidos, como archivos multimedia, contactos e información del calendario. En este artículo se presenta la clase ContentProvider y se proporcionan dos ejemplos de cómo usarla._

## <a name="content-providers-overview"></a>Introducción a los proveedores de contenido

*ContentProvider* incluye un repositorio de datos y proporciona una API para acceder a él. El proveedor existe como parte de una aplicación de Android que, normalmente, también proporciona una interfaz de usuario para mostrar o administrar los datos. La principal ventaja de utilizar un proveedor de contenido es que permite a otras aplicaciones acceder fácilmente a los datos encapsulados mediante un objeto de cliente de proveedor (denominado *ContentResolver*). Juntos, un proveedor de contenido y un solucionador de contenido ofrecen una API coherente entre aplicaciones para el acceso a los datos que es fácil de compilar y consumir. Cualquier aplicación puede optar por usar `ContentProviders` para administrar datos internamente y también exponerlo a otras aplicaciones.

Además, se requiere `ContentProvider` para que la aplicación proporcione sugerencias de búsqueda personalizadas. O bien, si quiere proporcionar la capacidad de copiar datos complejos desde la aplicación para pegarlos en otras aplicaciones. En este documento se muestra cómo acceder a `ContentProviders` y compilarlo con Xamarin.Android.

La estructura de esta sección es la siguiente:

- **Cómo funciona** &ndash; Información general sobre para qué se diseñó `ContentProvider` y cómo funciona.

- **Consumo de un proveedor de contenido** &ndash; Un ejemplo de acceso a la lista de contactos.

- **Uso de ContentProvider para compartir datos** &ndash; Escribir y consumir `ContentProvider` en la misma aplicación.

`ContentProviders` y los cursores que trabajan con sus datos se suelen usar para rellenar elementos ListViews. Para obtener más información sobre cómo usar esas clases, consulte la [guía de los elementos ListView y Adapter](~/android/user-interface/layouts/list-view/index.md).

Los elementos `ContentProviders` expuestos por Android (u otras aplicaciones) son una manera fácil de incluir datos de otros orígenes en la aplicación. Permiten obtener acceso a los datos y presentarlos, como la lista de contactos, fotos o eventos de calendario desde la aplicación, así como que el usuario interactúe con estos datos.

Los elementos `ContentProviders` personalizados son una manera cómoda de empaquetar los datos para usarlos dentro de su propia aplicación, o para su uso por parte de otras aplicaciones (incluidos los usos especiales, como la búsqueda personalizada y la acción de copiar y pegar).

En los temas de esta sección se proporcionan algunos ejemplos sencillos del uso y la escritura de código de `ContentProvider`.

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de ContactsAdapter (muestra)](/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (muestra)](/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [Guía para desarrolladores de proveedores de contenido](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Referencia de la clase ContentProvider](xref:Android.Content.ContentProvider)
- [Referencia de la clase ContentResolver](xref:Android.Content.ContentResolver)
- [Referencia de la clase ListView](xref:Android.Widget.ListView)
- [Referencia de la clase CursorAdapter](xref:Android.Widget.CursorAdapter)
- [Referencia de la clase UriMatcher](xref:Android.Content.UriMatcher)
- [Android.Provider](xref:Android.Provider)
- [Referencia de la clase ContactsContract](xref:Android.Provider.ContactsContract)