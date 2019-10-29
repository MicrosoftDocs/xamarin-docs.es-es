---
title: Introducción a ContentProviders
description: El sistema operativo Android usa proveedores de contenido para facilitar el acceso a datos compartidos, como archivos multimedia, contactos e información de calendario. En este artículo se presenta la clase ContentProvider y se proporcionan dos ejemplos de cómo usarla.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 496e5c092c79f4f71bddaad30bea6acd1d58d375
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027542"
---
# <a name="intro-to-contentproviders"></a>Introducción a ContentProviders

_El sistema operativo Android usa proveedores de contenido para facilitar el acceso a datos compartidos, como archivos multimedia, contactos e información de calendario. En este artículo se presenta la clase ContentProvider y se proporcionan dos ejemplos de cómo usarla._

## <a name="content-providers-overview"></a>Introducción a los proveedores de contenido

Un *ContentProvider* encapsula un repositorio de datos y proporciona una API para acceder a él. El proveedor existe como parte de una aplicación de Android que normalmente también proporciona una interfaz de usuario para mostrar o administrar los datos. La principal ventaja de utilizar un proveedor de contenido es que permite a otras aplicaciones acceder fácilmente a los datos encapsulados mediante un objeto de cliente de proveedor (denominado *ContentResolver devuelvan*). Juntos, un proveedor de contenido y un solucionador de contenido ofrecen una API coherente entre aplicaciones para el acceso a los datos que es fácil de compilar y consumir. Cualquier aplicación puede optar por usar `ContentProviders` para administrar datos internamente y también para exponerlo a otras aplicaciones.

También se requiere una `ContentProvider` para que la aplicación proporcione sugerencias de búsqueda personalizadas, o bien, si desea proporcionar la capacidad de copiar datos complejos desde la aplicación para pegarlos en otras aplicaciones. En este documento se muestra cómo obtener acceso y compilar `ContentProviders` con Xamarin. Android.

La estructura de esta sección es la siguiente:

- **Cómo funciona** &ndash; información general sobre el diseño de la `ContentProvider` y cómo funciona.

- **Utilizar un proveedor de contenido** &ndash; un ejemplo de acceso a la lista de contactos.

- **Usar ContentProvider para compartir datos** &ndash; escribir y consumir un `ContentProvider` en la misma aplicación.

`ContentProviders` y los cursores que operan sobre sus datos se suelen usar para rellenar los controles ListView. Consulte la [Guía de ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md) para obtener más información sobre cómo usar esas clases.

`ContentProviders` que expone Android (u otras aplicaciones) es una manera fácil de incluir datos de otros orígenes en la aplicación. Permiten obtener acceso y presentar datos como la lista de contactos, fotos o eventos de calendario desde dentro de la aplicación, y dejar que el usuario interactúe con los datos.

Los `ContentProviders` personalizados son una manera cómoda de empaquetar los datos para su uso dentro de su propia aplicación, o para su uso por parte de otras aplicaciones (incluidos los usos especiales como la búsqueda personalizada y copiar y pegar).

En los temas de esta sección se proporcionan algunos ejemplos sencillos del uso y la escritura de código de `ContentProvider`.

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de ContactsAdapter (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [Guía para desarrolladores de proveedores de contenido](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Referencia de la clase ContentProvider](xref:Android.Content.ContentProvider)
- [Referencia de la clase ContentResolver devuelvan](xref:Android.Content.ContentResolver)
- [Referencia de clases de ListView](xref:Android.Widget.ListView)
- [CursorAdapter (referencia de clase)](xref:Android.Widget.CursorAdapter)
- [Referencia de la clase UriMatcher](xref:Android.Content.UriMatcher)
- [Android. Provider](xref:Android.Provider)
- [Referencia de la clase ContactsContract](xref:Android.Provider.ContactsContract)
