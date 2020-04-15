---
title: Mi archivo Resource.designer.cs de Android no se actualizará
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/19/2017
ms.openlocfilehash: 4683bbaa5aa48c7b5de5fb9a87a4cd3fbc0aeada
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019510"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Mi archivo Resource.designer.cs de Android no se actualizará

> [!NOTE]
> Este problema se ha resuelto en Xamarin Studio 5.1.4 y versiones posteriores. Sin embargo, si el problema se produce en Visual Studio para Mac, cree un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

Un error en Xamarin.Studio 5.1 previamente dañó los archivos .csproj al eliminar parcial o completamente el código xml en el archivo .csproj. Esto generó errores en partes importantes del sistema de compilación de Android (como la actualización de Resource.designer.cs de Android). A partir de la versión 5.1.4 estable del 15 de julio, se ha corregido este error, pero, en muchos casos, el archivo de proyecto tiene que repararse manualmente, tal y como se describe a continuación.

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Dos enfoques posibles para corregir el archivo de proyecto

**Opciones:**

1. Cree un nuevo proyecto de aplicación de Xamarin.Android, establezca todas las propiedades del proyecto para que coincidan con el proyecto anterior y agregue todos los recursos, archivos de código fuente, etc. al proyecto.

   **O**

2. Realice una copia de seguridad del archivo .csproj del proyecto original, ábralo en un editor de texto y vuelva a agregar los elementos que faltan desde un archivo .csproj generado correctamente.

### <a name="if-this-does-not-solve-the-problem"></a>Si esto no soluciona el problema

Después de experimentar con estos elementos, es posible que observe que, después de agregar los elementos y volver a generar el proyecto, el archivo Resource.designer.cs se actualice, pero quizá tenga que cerrar y volver a abrir la solución para obtener la finalización del código para reconocer los nuevos tipos contenidos en Resource.designer.cs. 
