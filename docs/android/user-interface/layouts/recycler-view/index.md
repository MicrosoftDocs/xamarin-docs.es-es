---
title: RecyclerView
description: RecyclerView es un grupo de vistas para mostrar colecciones; está diseñado para ser un reemplazo más flexible de grupos de vistas más antiguos, como ListView y GridView.  En esta guía se explica cómo usar y personalizar RecyclerView en aplicaciones de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/03/2018
ms.openlocfilehash: a972f6686103dc9b3a1317d15985cbf3dc78bd82
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457072"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView es un grupo de vistas para mostrar colecciones; está diseñado para ser un reemplazo más flexible de grupos de vistas más antiguos, como ListView y GridView.  En esta guía se explica cómo usar y personalizar RecyclerView en aplicaciones de Xamarin. Android._

## <a name="recyclerview"></a>RecyclerView

Muchas aplicaciones necesitan Mostrar colecciones del mismo tipo (por ejemplo, mensajes, contactos, imágenes o canciones); a menudo, esta colección es demasiado grande para caber en la pantalla, por lo que la colección se presenta en una ventana pequeña que se puede desplazar sin problemas a través de todos los elementos de la colección.
`RecyclerView` es un widget de Android que muestra una colección de elementos en una lista o una cuadrícula, lo que permite al usuario desplazarse por la colección. A continuación se muestra una captura de pantalla de una aplicación de ejemplo que usa `RecyclerView` para mostrar el contenido de la bandeja de entrada de correo electrónico en una lista de desplazamiento vertical:

[![Aplicación de ejemplo que usa RecyclerView para enumerar mensajes de la bandeja de entrada](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` ofrece dos características atractivas:

- Tiene una arquitectura flexible que le permite modificar su comportamiento mediante la conexión de sus componentes preferidos.

- Es eficaz con las colecciones grandes porque reutiliza las vistas de elementos y requiere el uso de los *titulares* de la vista para almacenar en caché las referencias de la vista.

En esta guía se explica cómo usar `RecyclerView` en las aplicaciones de Xamarin. Android; se explica cómo agregar el `RecyclerView` paquete al proyecto de Xamarin. Android y se describe cómo `RecyclerView` funciona en una aplicación típica. Se proporcionan ejemplos de código reales para mostrar cómo integrar `RecyclerView` en la aplicación, cómo implementar un clic en la vista de elementos y cómo actualizar `RecyclerView` cuando cambian los datos subyacentes. En esta guía se da por supuesto que está familiarizado con el desarrollo de Xamarin. Android.

### <a name="requirements"></a>Requisitos

Aunque `RecyclerView` a menudo se asocia con el círculo de Android 5,0, se ofrece como una biblioteca de soporte técnico &ndash; `RecyclerView` que funciona con aplicaciones que tienen como destino el nivel de API 7 (Android 2,1) y versiones posteriores. Se requiere lo siguiente para usar `RecyclerView` en aplicaciones basadas en Xamarin:

- **Xamarin. Android** &ndash; xamarin. Android 4,20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

- El proyecto de la aplicación debe incluir el paquete **Xamarin. Android. support. V7. RecyclerView** . Para obtener más información sobre la instalación de paquetes NuGet, consulte [Tutorial: incluir un NuGet en el proyecto](/visualstudio/mac/nuget-walkthrough).

### <a name="overview"></a>Información general

`RecyclerView` se puede considerar como sustituto de los `ListView` `GridView` widgets y en Android. Al igual que sus predecesores, `RecyclerView` se ha diseñado para mostrar un conjunto de datos grande en una ventana pequeña, pero `RecyclerView` ofrece más opciones de diseño y está mejor optimizada para mostrar colecciones de gran tamaño. Si está familiarizado con `ListView` , hay varias diferencias importantes entre `ListView` y `RecyclerView` :

- `RecyclerView` es ligeramente más complejo de usar: tiene que escribir más código para su uso en `RecyclerView` comparación con `ListView` .

- `RecyclerView` no proporciona un adaptador predefinido; debe implementar el código de adaptador que tiene acceso al origen de datos. Sin embargo, Android incluye varios adaptadores predefinidos que funcionan con `ListView` y `GridView` .

- `RecyclerView` no ofrece un evento de clic de elemento cuando un usuario pulsa un elemento; en su lugar, los eventos de clic de elemento se controlan mediante las clases auxiliares. Por el contrario, `ListView` ofrece un evento de clic de elemento.

- `RecyclerView` mejora el rendimiento mediante el reciclaje de vistas y la aplicación del patrón de portaherramienta de la vista, lo que elimina las búsquedas de recursos de diseño innecesarios. El uso del patrón View-portaherramienta es opcional en `ListView` .

- `RecyclerView` se basa en un diseño modular que facilita la personalización. Por ejemplo, puede conectar una directiva de diseño diferente sin cambios significativos en el código de la aplicación.
    Por el contrario, `ListView` es relativamente monolítica en la estructura.

- `RecyclerView` incluye animaciones integradas para agregar y quitar elementos. `ListView` las animaciones requieren algún esfuerzo adicional en la parte del desarrollador de la aplicación.

### <a name="sections"></a>Secciones

#### <a name="recyclerview-parts-and-functionality"></a>[Componentes y funcionalidad de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

En este tema se explica cómo `Adapter` , `LayoutManager` y `ViewHolder` funcionan conjuntamente como clases auxiliares para admitir `RecyclerView` .
Proporciona información general de alto nivel de cada una de estas clases auxiliares y explica cómo usarlas en la aplicación.

#### <a name="a-basic-recyclerview-example"></a>[Ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Este tema se basa en la información proporcionada en las [partes y la funcionalidad de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) proporcionando ejemplos de código reales de cómo `RecyclerView` se implementan los distintos elementos para compilar una aplicación de exploración de fotografías en el mundo real.

#### <a name="extending-the-recyclerview-example"></a>[Extender el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

En este tema se agrega código adicional a la aplicación de ejemplo que se presenta en [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) para mostrar cómo controlar los eventos de clic de elemento y actualizar `RecyclerView` cuando cambia el origen de datos subyacente.

### <a name="summary"></a>Resumen

En esta guía se presentó el `RecyclerView` Widget Android; se explicó cómo agregar la `RecyclerView` biblioteca de compatibilidad a los proyectos de Xamarin. Android, cómo `RecyclerView` recicla las vistas, cómo se aplica el modelo de portaherramienta de la vista para mejorar la eficacia y cómo las distintas clases auxiliares que componen la `RecyclerView` colaboración para mostrar colecciones. Se ha proporcionado código de ejemplo para mostrar cómo `RecyclerView` se integra en una aplicación, se ha explicado cómo personalizar `RecyclerView` la Directiva de diseño mediante la conexión a diferentes administradores de diseño y se ha descrito cómo controlar los eventos de clic de elemento y notificar `RecyclerView` los cambios en el origen de datos.

Para obtener más información sobre `RecyclerView` , vea la referencia de la [clase RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).

## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introducción al círculo](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)