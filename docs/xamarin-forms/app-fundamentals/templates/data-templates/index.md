---
title: Plantillas de datos de Xamarin.Forms
description: Las plantillas de datos se usan para especificar el aspecto de los datos en los controles admitidos y normalmente se enlaza a los datos que se van a mostrar.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 6cb828f59505159c972d40905f22995055f207df
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646857"
---
# <a name="xamarinforms-data-templates"></a>Plantillas de datos de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Las plantillas de datos se usan para especificar el aspecto de los datos en los controles admitidos y normalmente se enlaza a los datos que se van a mostrar._

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

Las plantillas de datos de Xamarin.Forms permiten definir la presentación de los datos en los controles admitidos. En este artículo se ofrece una introducción a las plantillas de datos y se analiza por qué son necesarias.

## <a name="creating-a-datatemplatecreatingmd"></a>[Crear una plantilla de datos](creating.md)

Las plantillas de datos se pueden crear en línea, en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), o a partir de un tipo personalizado o un tipo de celda de Xamarin.Forms adecuado. Las plantillas en línea deben usarse si no es necesario volver a usar la plantilla de datos en otro lugar. Como alternativa, se puede reutilizar una plantilla de datos si se define como un tipo personalizado, o como un recurso de nivel de página o el nivel de aplicación de nivel de control.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Crear un DataTemplateSelector](selector.md)

Un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) se puede usar para elegir [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) en tiempo de ejecución según el valor de una propiedad enlazada a datos. Esto permite aplicar varias instancias de `DataTemplate` al mismo tipo de objeto para personalizar la apariencia de objetos concretos. En este artículo se explica cómo crear y consumir un `DataTemplateSelector`.


## <a name="related-links"></a>Vínculos relacionados

- [Plantillas de datos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
