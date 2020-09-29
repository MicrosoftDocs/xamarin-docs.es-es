---
title: Edición de metadatos de NuGet
description: En este documento se describe cómo usar las opciones de proyecto para editar metadatos de NuGet para bibliotecas multiplataforma. Describe los metadatos necesarios y opcionales.
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: a3e1e8d1be1f84f707c80c42adb6b8d1e3f234a9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457021"
---
# <a name="editing-nuget-metadata"></a>Edición de metadatos de NuGet

_Usar las opciones de proyecto para editar metadatos de NuGet para bibliotecas multiplataforma_

Los tipos de proyecto de biblioteca (como PCL o .NET Standard, o el nuevo tipo de proyecto de NuGet) tienen una sección de **paquete Nuget** en la ventana **Opciones del proyecto** .

En la sección de **metadatos** se configuran los valores utilizados en el [archivo de manifiesto del paquete NuGet **. nuspec** ](/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Información necesaria

La pestaña **General** contiene cuatro campos que se deben especificar para generar un paquete de NuGet:

[![Ventana de metadatos necesaria del paquete NuGet](metadata-images/metadata-general-sml.png)](metadata-images/metadata-general.png#lightbox)

- **ID** : el identificador del paquete, que debe ser único en NuGet.org (o donde se distribuirá el paquete). Siga esta [Guía](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) y use únicamente caracteres que sean válidos en una dirección URL (sin espacios y eviten la mayoría de los caracteres especiales).
- **Versión** : elija un número de versión coherente con [las reglas de control de versiones de NuGet](/nuget/create-packages/dependency-versions).
- **Autores** : lista de nombres separados por comas.
- **Descripción** : información general de las características del paquete que se muestra cuando los usuarios seleccionan el paquete.

> [!NOTE]
> Recuerde incrementar el número de versión al crear nuevas versiones para la distribución a NuGet u otros usuarios.

Para obtener más información, consulte la [referencia de elementos necesarios](/nuget/schema/nuspec#required-metadata-elements) para obtener más información, así como estas instrucciones detalladas sobre cómo [elegir un identificador de paquete único y establecer el número de versión](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) y [establecer un tipo de paquete](/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> Se deben especificar todos los campos de esta pestaña; de lo contrario, aparecerá un mensaje de error: _"el proyecto no tiene metadatos de Nuget, por lo que no se creará un paquete Nuget. Los metadatos del paquete NuGet se pueden especificar en la sección metadatos de opciones del proyecto "_ .

## <a name="optional-metadata"></a>Metadatos opcionales

La pestaña **detalles** contiene los campos opcionales que se van a incluir en el archivo de manifiesto del paquete NuGet.

[![Ventana de metadatos opcional del paquete NuGet](metadata-images/metadata-detail-sml.png)](metadata-images/metadata-detail.png#lightbox)

Consulte la [referencia de elementos opcionales](/nuget/schema/nuspec#optional-metadata-elements) para obtener más información sobre los campos obligatorios y opcionales.

> [!NOTE]
> Si el paquete de NuGet se distribuye en [NuGet.org](https://www.nuget.org) , se recomienda proporcionar tanta información como sea posible.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia de. nuspec](/nuget/schema/nuspec#general-form-and-schema)