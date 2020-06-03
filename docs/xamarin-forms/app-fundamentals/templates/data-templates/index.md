---
title: Plantillas de datos de Xamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e0961fad18ccd961a5b84b2a5535bca70781dd8d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136128"
---
# <a name="xamarinforms-data-templates"></a>Plantillas de datos de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Las plantillas de datos se usan para especificar el aspecto de los datos en los controles admitidos y normalmente se enlaza a los datos que se van a mostrar._

## <a name="introduction"></a>[Introducción](introduction.md)

Las plantillas de datos de Xamarin.Forms permiten definir la presentación de los datos en los controles admitidos. En este artículo se ofrece una introducción a las plantillas de datos y se analiza por qué son necesarias.

## <a name="creating-a-datatemplate"></a>[Crear una plantilla de datos](creating.md)

Las plantillas de datos se pueden crear en línea, en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), o a partir de un tipo personalizado o un tipo de celda de Xamarin.Forms adecuado. Una plantilla insertada se debe usar si no es necesario volver a utilizarla en otro lugar. Como alternativa, se puede reutilizar una plantilla de datos si se define como un tipo personalizado, o como un recurso de nivel de página o el nivel de aplicación de nivel de control.

## <a name="creating-a-datatemplateselector"></a>[Crear un DataTemplateSelector](selector.md)

Un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) se puede usar para elegir [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) en tiempo de ejecución según el valor de una propiedad enlazada a datos. Esto permite aplicar varias instancias de `DataTemplate` al mismo tipo de objeto para personalizar la apariencia de objetos concretos. En este artículo se explica cómo crear y consumir un `DataTemplateSelector`.

## <a name="related-links"></a>Vínculos relacionados

- [Plantillas de datos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
