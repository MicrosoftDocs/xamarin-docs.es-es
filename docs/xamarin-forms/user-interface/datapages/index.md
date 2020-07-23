---
title: Xamarin.FormsPáginas de página
description: En este artículo se presentan Xamarin.Forms las páginas de datos, que proporcionan una API para enlazar de forma rápida y sencilla un origen de datos a vistas precompiladas.
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e1ea94e42e98609b3f77f0198e125b94e2b437d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928840"
---
# <a name="xamarinforms-datapages"></a>Xamarin.FormsPáginas de página

![Esta API está actualmente en versión preliminar](~/media/shared/preview.png)

> [!IMPORTANT]
> Las páginas de página requieren una Xamarin.Forms referencia de tema que se va a representar. Esto implica la instalación de [ Xamarin.Forms . Paquete NuGet Theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) en el proyecto, seguido de [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) o [ Xamarin.Forms . Paquetes NuGet Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Xamarin.FormsLas páginas de usuarios se anunciaron a evolucionar 2016 y están disponibles como vista previa para que los clientes intenten enviar comentarios.

Las páginas de datos proporcionan una API para enlazar de forma rápida y sencilla un origen de datos a vistas precompiladas. Los elementos de lista y las páginas de detalles representarán los datos automáticamente y se pueden personalizar con temas.

Para ver cómo funciona la demo de la ponencia de evolución, consulte la [Guía de introducción](get-started.md).

[![Aplicación de ejemplo de páginas de página](images/demo-sml.png)](images/demo.png#lightbox "Aplicación de ejemplo de páginas de página")

## <a name="introduction"></a>Introducción

Los orígenes de datos y las páginas de datos asociadas permiten a los desarrolladores consumir de forma rápida y sencilla un origen de datos compatible y representarlo mediante la técnica de la interfaz de usuario integrada que se puede personalizar con los temas.

Las páginas de página se agregan a una Xamarin.Forms aplicación incluyendo el ** Xamarin.Forms . **Paquete NuGet de páginas.

### <a name="data-sources"></a>Orígenes de datos

La vista previa tiene algunos orígenes de datos predefinidos disponibles para su uso:

* **JsonDataSource**
* **AzureDataSource** (NuGet independiente)
* **AzureEasyTableDataSource** (NuGet independiente)

Vea la [Guía de introducción](get-started.md) para obtener un ejemplo de uso de `JsonDataSource` .

### <a name="pages--controls"></a>Páginas & controles

Se incluyen las siguientes páginas y controles para permitir un enlace sencillo a los orígenes de datos proporcionados:

* **ListDataPage** : vea el [ejemplo de introducción](get-started.md).
* **DirectoryPage** : una lista con la agrupación habilitada.
* **PersonDetailPage** : una sola vista de elemento de datos personalizada para un tipo de objeto específico (una entrada de contacto).
* **DataView** : una vista para exponer los datos del origen de forma genérica.
* **CardView** : vista con estilo que contiene una imagen, texto de título y texto de descripción.
* **HeroImage** : vista de representación de imágenes.
* **ListItem** : vista pregenerada con un diseño similar a los elementos de lista nativos de iOS y Android.

Vea la [referencia de controles de páginas de página](controls.md) para obtener ejemplos.

### <a name="under-the-hood"></a>Una mirada al interior

Un Xamarin.Forms origen de datos se adhiere a la `IDataSource` interfaz.

La Xamarin.Forms infraestructura interactúa con un origen de datos a través de las siguientes propiedades:

* `Data`: una lista de solo lectura de los elementos de datos que se pueden mostrar.
* `IsLoading`: un valor booleano que indica si los datos se cargan y están disponibles para su representación.
* `[key]`: un indexador para recuperar elementos.

Hay dos métodos `MaskKey` `UnmaskKey` que se pueden usar para ocultar (o mostrar) las propiedades de los elementos de datos (es decir, impedir que se representen).
La clave corresponde a una propiedad con nombre en el objeto de elemento de datos.
