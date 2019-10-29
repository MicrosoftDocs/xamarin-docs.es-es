---
title: ApiDefinitions & StructsAndEnums archivos
description: En este documento se describen los archivos ApiDefinitions.cs y StructsAndEnums.cs que genera Objective Sharpie. A continuación, estos archivos se usan para tener acceso al código de C#Objective-C desde.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 870733554f3f69b7a0cd9b35c1b89e24c62b264d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016194"
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions & StructsAndEnums archivos

Cuando el objetivo Sharpie se ha ejecutado correctamente, genera archivos de `Binding/ApiDefinitions.cs` y `Binding/StructsAndEnums.cs`.
Estos dos archivos se agregan a un proyecto de enlace en Visual Studio para Mac o se pasan directamente a las herramientas `btouch` o `bmac` para generar el enlace final.

En *algunos* casos, estos archivos generados podrían ser todo lo que necesita, pero a menudo el desarrollador tendrá que modificar manualmente estos archivos generados para corregir los problemas que no se puedan controlar automáticamente con la herramienta (por ejemplo, los marcados con una [`Verify` atributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Algunos de los pasos siguientes incluyen:

- **Ajustar nombres**: en ocasiones, querrá ajustar los nombres de los métodos y las clases para que coincidan con las directrices de diseño de .NET Framework.
- **Métodos o propiedades**: la heurística utilizada por Objective Sharpie a veces seleccionará un método que se va a convertir en una propiedad. En este momento, puede decidir si este es el comportamiento previsto o no.
- **Enlazar eventos**: puede vincular las clases a las clases de delegado y generar automáticamente eventos para ellas.
- **Enlazar notificaciones**: no es posible extraer el contrato de API de las notificaciones de los archivos de encabezado puro, lo que requiere un viaje a la documentación de la API. Si desea recibir notificaciones fuertemente tipadas, tendrá que actualizar el resultado.
- **API selección**: en este momento, puede elegir proporcionar constructores adicionales, agregar métodos (para permitir la sintaxis de C# inicialización en construcción), sobrecarga de operadores e implementar sus propias interfaces en el archivo de definiciones adicionales.

Vea el [enlace de una](~/cross-platform/macios/binding/objective-c-libraries.md) Descripción de API para ver cómo se ajustan estos archivos en el proceso de enlace, como se muestra en el diagrama siguiente:

![](apidefinitions-structsandenums-images/binding-flowchart.png "The binding process is shown in this diagram")

Consulte la [referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md) para obtener más información sobre el contenido de estos archivos.
