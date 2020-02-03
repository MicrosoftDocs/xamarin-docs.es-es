---
title: Páginas de formularios de Xamarin. Forms
description: En este artículo se presentan las páginas de datos de Xamarin. Forms, que proporcionan una API para enlazar de forma rápida y sencilla un origen de datos a vistas precompiladas.
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 4569240d7419254bad41957d30b4ad652c7a3f4e
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "75727945"
---
# <a name="xamarinforms-datapages"></a>Páginas de formularios de Xamarin. Forms

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> Las páginas de formularios requieren una referencia de tema de Xamarin. Forms para representar. Esto implica la instalación del paquete de Nuget [Xamarin. Forms. theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) en el proyecto, seguido de los paquetes de Nuget [Xamarin. Forms. theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) o [Xamarin. Forms. theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Las páginas de formularios de Xamarin. Forms se anunciaron en evolucione 2016 y están disponibles como vista previa para que los clientes puedan probar y proporcionar comentarios.

DataSourceControl proporciona una API para enlazar de forma rápida y sencilla un origen de datos a vistas precompiladas. Los elementos de lista y las páginas de detalles representarán los datos automáticamente y se pueden personalizar con temas.

Para ver cómo funciona la demo de la ponencia de evolución, consulte la [Guía de introducción](get-started.md).

[![](images/demo-sml.png "DataPages Sample Application")](images/demo.png#lightbox "DataPages Sample Application")

## <a name="introduction"></a>Introducción

Los orígenes de datos y las páginas de datos asociadas permiten a los desarrolladores consumir de forma rápida y sencilla un origen de datos compatible y representarlo mediante la técnica de la interfaz de usuario integrada que se puede personalizar con los temas.

Las páginas de tipos se agregan a una aplicación de Xamarin. Forms incluyendo el paquete de NuGet de **Xamarin. Forms. pages** .

### <a name="data-sources"></a>Orígenes de datos

La vista previa tiene algunos orígenes de datos predefinidos disponibles para su uso:

* **JsonDataSource**
* **AzureDataSource** (NuGet independiente)
* **AzureEasyTableDataSource** (NuGet independiente)

Vea la [Guía de introducción](get-started.md) para obtener un ejemplo de uso de un `JsonDataSource`.

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

Un origen de datos de Xamarin. Forms se adhiere a la interfaz `IDataSource`.

La infraestructura de Xamarin. Forms interactúa con un origen de datos a través de las siguientes propiedades:

* `Data`: lista de solo lectura de los elementos de datos que se pueden mostrar.
* `IsLoading`: un valor booleano que indica si los datos se cargan y están disponibles para su representación.
* `[key]`: un indexador para recuperar elementos.

Hay dos métodos `MaskKey` y `UnmaskKey` que se pueden usar para ocultar (o mostrar) las propiedades de los elementos de datos (es decir, impedir que se representen).
La clave corresponde a una propiedad con nombre en el objeto de elemento de datos.
