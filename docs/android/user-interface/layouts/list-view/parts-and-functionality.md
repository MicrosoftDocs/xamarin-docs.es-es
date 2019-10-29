---
title: Elementos y funcionalidad de ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: b8fd44a70f4c7ecdcf7919dec1c81461200b35bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028866"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Componentes y funcionalidad de ListView de Xamarin. Android

Un `ListView` consta de las siguientes partes:

- **Filas** &ndash; la representación visible de los datos de la lista.

- El **adaptador** &ndash; una clase no visual que enlaza el origen de datos a la vista de lista.

- El **desplazamiento rápido** &ndash; un controlador que permite al usuario desplazarse por la longitud de la lista.

- **Índice de sección** &ndash; un elemento de la interfaz de usuario que flota sobre las filas de desplazamiento para indicar en qué lugar de la lista se encuentran las filas actuales.

Estas capturas de pantallas usan un control de `ListView` básico para mostrar cómo se representan el desplazamiento rápido y el índice de sección:

[![capturas de pantallas de aplicaciones que usan filas antiguas sin formato, desplazamiento rápido e índice de sección](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Los elementos que componen un `ListView` se describen con más detalle a continuación:

## <a name="rows"></a>Filas

Cada fila tiene su propio `View`. La vista puede ser una de las vistas integradas definidas en `Android.Resources`o una vista personalizada. Cada fila puede usar el mismo diseño de vista o puede ser diferente. Hay ejemplos en este documento sobre el uso de diseños integrados y otros que explican cómo definir diseños personalizados.

## <a name="adapter"></a>Adaptador

El control `ListView` requiere un `Adapter` para proporcionar el `View` con formato para cada fila. Android tiene adaptadores y vistas integrados que se pueden usar, o bien se pueden crear clases personalizadas.

## <a name="fast-scrolling"></a>Desplazamiento rápido

Cuando un `ListView` contiene muchas filas de datos, se puede habilitar el desplazamiento rápido para ayudar al usuario a navegar a cualquier parte de la lista. La barra de desplazamiento de desplazamiento rápido se puede habilitar opcionalmente (y personalizarse en el nivel de API 11 y versiones posteriores).

## <a name="section-index"></a>Índice de sección

Al desplazarse por las listas largas, el índice de sección opcional proporciona al usuario información sobre la parte de la lista que está viendo actualmente. Solo es adecuado en listas largas, normalmente junto con desplazamiento rápido.

## <a name="classes-overview"></a>Información general sobre clases

A continuación se muestran las clases principales que se usan para mostrar `ListViews`:

[![diagrama UML que ilustra las relaciones entre ListView y las clases asociadas](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

A continuación se describe el propósito de cada clase:

- **ListView** &ndash; elemento de la interfaz de usuario que muestra una colección de filas desplazable. En los teléfonos, normalmente usa toda la pantalla (en cuyo caso se puede usar la clase `ListActivity`) o podría formar parte de un diseño mayor en teléfonos o dispositivos de tableta.

- **Ver** &ndash; una vista en Android puede ser cualquier elemento de la interfaz de usuario, pero en el contexto de una `ListView` requiere que se proporcione un `View` para cada fila.

- **BaseAdapter** &ndash; clase base para las implementaciones de adaptador para enlazar un `ListView` a un origen de datos.

- **ArrayAdapter** &ndash; clase de adaptador integrada que enlaza una matriz de cadenas a un `ListView` para su presentación. El `ArrayAdapter<T>` genérico hace lo mismo para otros tipos.

- **CursorAdapter** &ndash; usar `CursorAdapter` o `SimpleCursorAdapter` para Mostrar datos basados en una consulta de SQLite.

Este documento contiene ejemplos sencillos que usan un `ArrayAdapter`, así como ejemplos más complejos que requieren implementaciones personalizadas de `BaseAdapter` o `CursorAdapter`.
