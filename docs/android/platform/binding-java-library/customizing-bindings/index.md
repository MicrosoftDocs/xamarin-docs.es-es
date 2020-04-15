---
title: Personalización de enlaces
description: Se puede personalizar un enlace de Xamarin.Android editando los metadatos que controlan el proceso de enlace. Estas modificaciones manuales suelen ser necesarias para resolver errores de compilación y para dar forma a la API resultante para que sea más coherente con C# o .NET. En estas guías se explica la estructura de estos metadatos, cómo modificar los metadatos y cómo usar JavaDoc para recuperar los nombres de los parámetros del método.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 04f3720d8684129476c955819390e91330a7800a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020653"
---
# <a name="customizing-bindings"></a>Personalización de enlaces

_Se puede personalizar un enlace de Xamarin.Android editando los metadatos que controlan el proceso de enlace. Estas modificaciones manuales suelen ser necesarias para resolver errores de compilación y para dar forma a la API resultante para que sea más coherente con C# o .NET. En estas guías se explica la estructura de estos metadatos, cómo modificar los metadatos y cómo usar JavaDoc para recuperar los nombres de los parámetros del método._

## <a name="overview"></a>Información general

Xamarin.Android automatiza gran parte del proceso de enlace; sin embargo, en algunos casos, se requiere una modificación manual para solucionar los problemas siguientes:

- La resolución de errores de compilación causados por tipos que faltan, tipos ofuscados, nombres duplicados, problemas de visibilidad de clases y otras situaciones que las herramientas de Xamarin.Android no pueden resolver. 

- El cambio de la asignación que Xamarin.Android usa para enlazar la API de Android a tipos diferentes en C# (por ejemplo, muchos desarrolladores prefieren asignar las constantes `int` de Java a las constantes `enum` de C#).

- La eliminación de los tipos no usados que no es necesario enlazar. 

- La adición de tipos que no tienen homólogos en la API de Java subyacente. 

Puede realizar algunos o todos estos cambios modificando los metadatos que controlan el proceso de enlace.

## <a name="guides"></a>Guías

En las guías siguientes se describen los metadatos que controlan el proceso de enlace y se explica cómo modificarlos para solucionar estos problemas:

- En los [Metadatos de enlaces de Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) se proporciona una visión general de los metadatos que pasan van en un enlace de Java.
    Se describen los distintos pasos manuales que a veces se requieren para completar una biblioteca de enlaces de Java, y explica cómo dar forma a una API expuesta por un enlace para seguir más estrechamente las instrucciones de diseño de .NET.

- En [Nomenclatura de parámetros con Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) se explica cómo recuperar nombres de parámetros en un proyecto de enlace de Java mediante el uso del Javadoc generado a partir del proyecto de Java enlazado.
