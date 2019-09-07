---
title: ApiDefinitions & StructsAndEnums archivos
description: En este documento se describen los archivos ApiDefinitions.cs y StructsAndEnums.cs que genera Objective Sharpie. A continuación, estos archivos se usan para tener acceso al código de C#Objective-C desde.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 35ae1ba49ae774b21a8f629beb9144ccf3a5f170
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765695"
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions & StructsAndEnums archivos

Cuando el objetivo Sharpie se ha ejecutado correctamente, `Binding/ApiDefinitions.cs` genera `Binding/StructsAndEnums.cs` archivos y.
Estos dos archivos se agregan a un proyecto de enlace en Visual Studio para Mac o se pasan `btouch` directamente `bmac` a las herramientas o para generar el enlace final.

En *algunos* casos, estos archivos generados podrían ser todo lo que necesita, pero a menudo el desarrollador tendrá que modificar manualmente estos archivos generados para corregir los problemas que no se puedan controlar automáticamente con la herramienta (como los marcados con un [ `Verify`atributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Algunos de los pasos siguientes incluyen:

- **Ajustar nombres**: A veces, querrá ajustar los nombres de los métodos y las clases para que coincidan con las directrices de diseño de .NET Framework.
- **Métodos o propiedades**: A veces, la heurística utilizada por el Sharpie de objetivos seleccionará un método que se va a convertir en una propiedad. En este momento, puede decidir si este es el comportamiento previsto o no.
- **Enlazar eventos**: Puede vincular las clases a las clases de delegado y generar automáticamente eventos para ellas.
- **Enlazar notificaciones**: No es posible extraer el contrato de API de las notificaciones de los archivos de encabezado puro, lo que requiere un viaje a la documentación de la API. Si desea recibir notificaciones fuertemente tipadas, tendrá que actualizar el resultado.
- **API selección**: En este momento, puede elegir proporcionar constructores adicionales, agregar métodos (para permitir la sintaxis de C# inicialización en construcción), sobrecarga de operadores e implementar sus propias interfaces en el archivo de definiciones adicionales.

Vea el [enlace de una](~/cross-platform/macios/binding/objective-c-libraries.md) Descripción de API para ver cómo se ajustan estos archivos en el proceso de enlace, como se muestra en el diagrama siguiente:

![](apidefinitions-structsandenums-images/binding-flowchart.png "En este diagrama se muestra el proceso de enlace")

Consulte la [referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md) para obtener más información sobre el contenido de estos archivos.
