---
title: Edición de metadatos de NuGet
description: En este documento se describe cómo usar las opciones de proyecto para editar metadatos de NuGet para bibliotecas multiplataforma. Describe los metadatos necesarios y opcionales.
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 5965b3f78bfc086b1cc6030a02f1cabafaae29d3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016750"
---
# <a name="editing-nuget-metadata"></a>Edición de metadatos de NuGet

_Usar las opciones de proyecto para editar metadatos de NuGet para bibliotecas multiplataforma_

Los tipos de proyecto de biblioteca (como PCL o .NET Standard, o el nuevo tipo de proyecto de NuGet) tienen una sección de **paquete Nuget** en la ventana **Opciones del proyecto** .

En la sección de **metadatos** se configuran los valores utilizados en el [archivo de manifiesto del paquete NuGet **. nuspec** ](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Información necesaria

La pestaña **General** contiene cuatro campos que se deben especificar para generar un paquete de NuGet:

[![](metadata-images/metadata-general-sml.png "NuGet package required metadata window")](metadata-images/metadata-general.png#lightbox)

- **ID** : el identificador del paquete, que debe ser único en Nuget.org (o donde se distribuirá el paquete). Siga esta [Guía](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) y use únicamente caracteres que sean válidos en una dirección URL (sin espacios y eviten la mayoría de los caracteres especiales).
- **Versión** : elija un número de versión coherente con [las reglas de control de versiones de NuGet](https://docs.microsoft.com/nuget/create-packages/dependency-versions).
- **Autores** : lista de nombres separados por comas.
- **Descripción** : información general de las características del paquete que se muestra cuando los usuarios seleccionan el paquete.

> [!NOTE]
> Recuerde incrementar el número de versión al crear nuevas versiones para la distribución a NuGet u otros usuarios.

Para obtener más información, consulte la [referencia de elementos necesarios](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements) para obtener más información, así como estas instrucciones detalladas sobre cómo [elegir un identificador de paquete único y establecer el número de versión](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) y [establecer un tipo de paquete](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> Se deben especificar todos los campos de esta pestaña; de lo contrario, aparecerá un mensaje de error: _"el proyecto no tiene metadatos de Nuget, por lo que no se creará un paquete Nuget. Los metadatos del paquete NuGet se pueden especificar en la sección metadatos de opciones del proyecto "_ .

## <a name="optional-metadata"></a>Metadatos opcionales

La pestaña **detalles** contiene los campos opcionales que se van a incluir en el archivo de manifiesto del paquete NuGet.

[![](metadata-images/metadata-detail-sml.png "NuGet package optional metadata window")](metadata-images/metadata-detail.png#lightbox)

Consulte la [referencia de elementos opcionales](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements) para obtener más información sobre los campos obligatorios y opcionales.

> [!NOTE]
> Si el paquete de NuGet se distribuye en [NuGet.org](https://www.nuget.org) , se recomienda proporcionar tanta información como sea posible.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia de .nuspec](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
