---
title: Elementos y funcionalidad de ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 4566ee5d203b5d098133aebe2c32dbaec712e17a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764209"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Componentes y funcionalidad de ListView de Xamarin. Android

Un `ListView` consta de las siguientes partes:

- **Filas** de &ndash; Representación visible de los datos de la lista.

- **Adaptador** de &ndash; Una clase no visual que enlaza el origen de datos a la vista de lista.

- **Desplazamiento rápido** &ndash; Identificador que permite al usuario desplazarse por la longitud de la lista.

- **Índice de sección** &ndash; Un elemento de la interfaz de usuario que flota sobre las filas de desplazamiento para indicar en qué lugar de la lista se encuentran las filas actuales.

Estas capturas de pantallas `ListView` usan un control básico para mostrar cómo se representan el desplazamiento rápido y el índice de sección:

[![Capturas de pantallas de aplicaciones que usan filas antiguas sin formato, desplazamiento rápido e índice de sección](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Los elementos que componen `ListView` un se describen con más detalle a continuación:

## <a name="rows"></a>Filas

Cada fila tiene su propia `View`. La vista puede ser una de las vistas integradas definidas en `Android.Resources`o una vista personalizada. Cada fila puede usar el mismo diseño de vista o puede ser diferente. Hay ejemplos en este documento sobre el uso de diseños integrados y otros que explican cómo definir diseños personalizados.

## <a name="adapter"></a>Adaptador

El `ListView` control `View` requiere que proporcione el formato para cada fila. `Adapter` Android tiene adaptadores y vistas integrados que se pueden usar, o bien se pueden crear clases personalizadas.

## <a name="fast-scrolling"></a>Desplazamiento rápido

Cuando un `ListView` contiene muchas filas de datos, se puede habilitar el desplazamiento rápido para ayudar al usuario a desplazarse a cualquier parte de la lista. La barra de desplazamiento de desplazamiento rápido se puede habilitar opcionalmente (y personalizarse en el nivel de API 11 y versiones posteriores).

## <a name="section-index"></a>Índice de sección

Al desplazarse por las listas largas, el índice de sección opcional proporciona al usuario información sobre la parte de la lista que está viendo actualmente. Solo es adecuado en listas largas, normalmente junto con desplazamiento rápido.

## <a name="classes-overview"></a>Información general sobre clases

A continuación se muestran las clases `ListViews` principales que se usan para mostrar:

[![Diagrama UML que ilustra las relaciones entre ListView y las clases asociadas](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

A continuación se describe el propósito de cada clase:

- **Vista** de &ndash; elemento de la interfaz de usuario que muestra una colección desplazable de filas. En los teléfonos, normalmente usa toda la pantalla (en cuyo caso se puede `ListActivity` usar la clase) o podría formar parte de un diseño mayor en teléfonos o dispositivos de tableta.

- **Vista** de una vista en Android puede ser cualquier elemento de la interfaz de usuario, pero en el `ListView` contexto de, `View` requiere que se proporcione un para cada fila. &ndash;

- **BaseAdapter** Clase base para las implementaciones de adaptador para `ListView` enlazar un a un origen de datos. &ndash;

- **ArrayAdapter** Clase de adaptador integrado que enlaza una matriz de cadenas a un `ListView` para su presentación. &ndash; El genérico `ArrayAdapter<T>` hace lo mismo para otros tipos.

- **CursorAdapter** &ndash; Use opara`SimpleCursorAdapter` Mostrar datos basados en una consulta de SQLite. `CursorAdapter`

Este documento contiene ejemplos sencillos que usan `ArrayAdapter` , así como ejemplos más complejos que requieren implementaciones personalizadas de `BaseAdapter` o `CursorAdapter`.
