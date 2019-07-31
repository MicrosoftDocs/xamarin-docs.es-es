---
title: Introducción a ContentProviders
description: El sistema operativo Android usa proveedores de contenido para facilitar el acceso a datos compartidos, como archivos multimedia, contactos e información de calendario. En este artículo se presenta la clase ContentProvider y se proporcionan dos ejemplos de cómo usarla.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 2533ad80571e2c8fe94cb4a2dcb0ec0ff0dd68cb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643584"
---
# <a name="intro-to-contentproviders"></a>Introducción a ContentProviders

_El sistema operativo Android usa proveedores de contenido para facilitar el acceso a datos compartidos, como archivos multimedia, contactos e información de calendario. En este artículo se presenta la clase ContentProvider y se proporcionan dos ejemplos de cómo usarla._


## <a name="content-providers-overview"></a>Introducción a los proveedores de contenido

Un *ContentProvider* encapsula un repositorio de datos y proporciona una API para acceder a él. El proveedor existe como parte de una aplicación de Android que normalmente también proporciona una interfaz de usuario para mostrar o administrar los datos. La principal ventaja de utilizar un proveedor de contenido es que permite a otras aplicaciones acceder fácilmente a los datos encapsulados mediante un objeto de cliente de proveedor (denominado *ContentResolver devuelvan*). Juntos, un proveedor de contenido y un solucionador de contenido ofrecen una API coherente entre aplicaciones para el acceso a los datos que es fácil de compilar y consumir. Cualquier aplicación puede optar por `ContentProviders` usar para administrar datos internamente y también para exponerlo a otras aplicaciones.

También `ContentProvider` es necesario que la aplicación proporcione sugerencias de búsqueda personalizadas, o bien, si desea proporcionar la capacidad de copiar datos complejos desde la aplicación para pegarlos en otras aplicaciones. En este documento se muestra cómo obtener acceso `ContentProviders` y compilar con Xamarin. Android.

La estructura de esta sección es la siguiente:

- **Cómo funciona** Información general de `ContentProvider` lo que se ha diseñado para y cómo funciona. &ndash;

- **Consumir un proveedor de contenido** &ndash; Ejemplo de acceso a la lista de contactos.

- **Uso de ContentProvider para compartir datos** Escribir y consumir `ContentProvider` en la misma aplicación. &ndash;

`ContentProviders`y los cursores que operan sobre sus datos se suelen usar para rellenar los controles ListView. Consulte la [Guía de ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md) para obtener más información sobre cómo usar esas clases.

`ContentProviders`expuesto por Android (u otras aplicaciones) es una manera fácil de incluir datos de otros orígenes en la aplicación. Permiten obtener acceso y presentar datos como la lista de contactos, fotos o eventos de calendario desde dentro de la aplicación, y dejar que el usuario interactúe con los datos.

Personalizar `ContentProviders` es una manera cómoda de empaquetar los datos para su uso en su propia aplicación, o para su uso por parte de otras aplicaciones (incluidos los usos especiales como la búsqueda personalizada y copiar y pegar).

En los temas de esta sección se proporcionan algunos ejemplos sencillos del uso `ContentProvider` y la escritura de código.



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de ContactsAdapter (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [Guía para desarrolladores de proveedores de contenido](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Referencia de la clase ContentProvider](xref:Android.Content.ContentProvider)
- [Referencia de la clase ContentResolver devuelvan](xref:Android.Content.ContentResolver)
- [Referencia de clases de ListView](xref:Android.Widget.ListView)
- [CursorAdapter (referencia de clase)](xref:Android.Widget.CursorAdapter)
- [Referencia de la clase UriMatcher](xref:Android.Content.UriMatcher)
- [Android.Provider](xref:Android.Provider)
- [Referencia de la clase ContactsContract](xref:Android.Provider.ContactsContract)
