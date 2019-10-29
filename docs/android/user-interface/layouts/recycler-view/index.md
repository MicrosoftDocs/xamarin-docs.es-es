---
title: RecyclerView
description: RecyclerView es un grupo de vistas para mostrar colecciones; está diseñado para ser un reemplazo más flexible de grupos de vistas más antiguos, como ListView y GridView.  En esta guía se explica cómo usar y personalizar RecyclerView en aplicaciones de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/03/2018
ms.openlocfilehash: bce89be8bec512ac70ca40015521c7d56f3460d3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028812"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView es un grupo de vistas para mostrar colecciones; está diseñado para ser un reemplazo más flexible de grupos de vistas más antiguos, como ListView y GridView.  En esta guía se explica cómo usar y personalizar RecyclerView en aplicaciones de Xamarin. Android._

## <a name="recyclerview"></a>RecyclerView

Muchas aplicaciones necesitan Mostrar colecciones del mismo tipo (por ejemplo, mensajes, contactos, imágenes o canciones); a menudo, esta colección es demasiado grande para caber en la pantalla, por lo que la colección se presenta en una ventana pequeña que se puede desplazar sin problemas a través de todos los elementos de la colección.
`RecyclerView` es un widget de Android que muestra una colección de elementos en una lista o una cuadrícula, lo que permite al usuario desplazarse por la colección. A continuación se muestra una captura de pantalla de una aplicación de ejemplo que usa `RecyclerView` para mostrar el contenido de la bandeja de entrada de correo electrónico en una lista de desplazamiento vertical:

[![aplicación de ejemplo que usa RecyclerView para enumerar mensajes de la bandeja de entrada](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` ofrece dos características atractivas:

- Tiene una arquitectura flexible que le permite modificar su comportamiento mediante la conexión de sus componentes preferidos.

- Es eficaz con las colecciones grandes porque reutiliza las vistas de elementos y requiere el uso de los *titulares* de la vista para almacenar en caché las referencias de la vista.

En esta guía se explica cómo usar `RecyclerView` en aplicaciones de Xamarin. Android; explica cómo agregar el paquete de `RecyclerView` al proyecto de Xamarin. Android y describe cómo `RecyclerView` funciones en una aplicación típica. Se proporcionan ejemplos de código reales para mostrar cómo integrar `RecyclerView` en la aplicación, cómo implementar un clic en la vista de elementos y cómo actualizar `RecyclerView` cuando cambian los datos subyacentes. En esta guía se da por supuesto que está familiarizado con el desarrollo de Xamarin. Android.

### <a name="requirements"></a>Requisitos

Aunque `RecyclerView` suele estar asociado con el círculo de Android 5,0, se ofrece como una biblioteca de soporte &ndash; `RecyclerView` funciona con aplicaciones que tienen como destino el nivel de API 7 (Android 2,1) y versiones posteriores. Se requiere lo siguiente para usar `RecyclerView` en aplicaciones basadas en Xamarin:

- **Xamarin. android** &ndash; Xamarin. Android 4,20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

- El proyecto de la aplicación debe incluir el paquete **Xamarin. Android. support. V7. RecyclerView** . Para obtener más información sobre la instalación de paquetes NuGet, consulte [Tutorial: incluir un NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

### <a name="overview"></a>Información general

`RecyclerView` se puede considerar como sustituto de los widgets `ListView` y `GridView` en Android. Al igual que sus predecesoras, `RecyclerView` está diseñado para mostrar un conjunto de datos grande en una ventana pequeña, pero `RecyclerView` ofrece más opciones de diseño y se optimiza mejor para mostrar colecciones grandes. Si está familiarizado con `ListView`, hay varias diferencias importantes entre `ListView` y `RecyclerView`:

- `RecyclerView` es un poco más complejo de usar: tiene que escribir más código para utilizar `RecyclerView` en comparación con `ListView`.

- `RecyclerView` no proporciona un adaptador predefinido; debe implementar el código de adaptador que tiene acceso al origen de datos. Sin embargo, Android incluye varios adaptadores predefinidos que funcionan con `ListView` y `GridView`.

- `RecyclerView` no ofrece un evento de clic de elemento cuando un usuario pulsa un elemento; en su lugar, los eventos de clic de elemento se controlan mediante las clases auxiliares. Por el contrario, `ListView` ofrece un evento de clic de elemento.

- `RecyclerView` mejora el rendimiento mediante el reciclaje de las vistas y la aplicación del patrón de portaherramienta de la vista, lo que elimina las búsquedas de recursos de diseño innecesarios. El uso del patrón View-portaherramienta es opcional en `ListView`.

- `RecyclerView` se basa en un diseño modular que facilita la personalización. Por ejemplo, puede conectar una directiva de diseño diferente sin cambios significativos en el código de la aplicación.
    Por el contrario, `ListView` es relativamente monolítica en la estructura.

- `RecyclerView` incluye animaciones integradas para agregar y quitar elementos. `ListView` animaciones requieren algún esfuerzo adicional en la parte del desarrollador de la aplicación.

### <a name="sections"></a>Secciones

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Componentes y funcionalidad de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

En este tema se explica cómo funcionan conjuntamente `Adapter`, `LayoutManager`y `ViewHolder` como clases auxiliares para admitir `RecyclerView`.
Proporciona información general de alto nivel de cada una de estas clases auxiliares y explica cómo usarlas en la aplicación.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Este tema se basa en la información proporcionada en las [partes y la funcionalidad de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) proporcionando ejemplos de código reales de cómo se implementan los distintos elementos `RecyclerView` para compilar una aplicación de exploración fotográfica real.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Extender el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

En este tema se agrega código adicional a la aplicación de ejemplo que se presenta en [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) para mostrar cómo controlar los eventos de clic de elemento y actualizar `RecyclerView` cuando cambia el origen de datos subyacente.

### <a name="summary"></a>Resumen

En esta guía se presentó el widget de `RecyclerView` de Android; se explicó cómo agregar la biblioteca de compatibilidad de `RecyclerView` a los proyectos de Xamarin. Android, cómo `RecyclerView` recicla las vistas, cómo aplica el modelo de portaherramienta de la vista para mejorar la eficacia y cómo las diversas clases auxiliares que componen `RecyclerView` colaboran para mostrar colecciones. Se ha proporcionado código de ejemplo para mostrar cómo se integra `RecyclerView` en una aplicación, se ha explicado cómo adaptar la Directiva de diseño de `RecyclerView`mediante la conexión de distintos administradores de diseño y se ha descrito cómo controlar los eventos de clic de elemento y notificar `RecyclerView` de datos. cambios de origen.

Para obtener más información sobre `RecyclerView`, consulte la referencia de la [clase RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).

## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introducción al círculo](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
