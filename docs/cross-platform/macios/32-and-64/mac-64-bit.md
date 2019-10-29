---
title: Actualización de las aplicaciones unificadas de Xamarin. Mac a 64 bits
description: En esta guía se describe cómo actualizar las aplicaciones de Xamarin. Mac para tener como destino 64 bits. También proporciona ejemplos de los tipos de errores que se pueden encontrar al realizar este cambio.
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: davidortinau
ms.author: daortin
ms.date: 02/22/2018
ms.openlocfilehash: 1ae807869e44ad035d2681cf777af0e66ced8255
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016320"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Actualización de las aplicaciones unificadas de Xamarin. Mac a 64 bits

A partir del 2018 de enero, Apple requiere que las nuevas [presentaciones de Mac App Store tengan como destino 64 bits](https://developer.apple.com/news/?id=06282017a). Las aplicaciones que ya están disponibles en Mac App Store deben actualizarse para que tengan un destino de 64 de junio de 2018.

El **archivo**  > **nueva** plantilla de proyecto de Xamarin. Mac crea aplicaciones de 64 bits de forma predeterminada, por lo que cualquier aplicación recién creada ya es compatible con 64 bits y no requiere ningún cambio.

## <a name="targeting-64-bit"></a>Establecer como destino 64 bits

1. Abra la ventana **Opciones del proyecto** para la aplicación de Xamarin. Mac:

   ![Menú contextual del proyecto](mac-64-bit-images/1-contextual_menu-vsmac.png "Menú contextual del proyecto")

2. Seleccione la **compilación de Mac** y establezca las **arquitecturas admitidas** en **x86\_64**:

   [![Establecimiento de las arquitecturas admitidas en x86_64](mac-64-bit-images/2-project_options-vsmac.png "Establecimiento de las arquitecturas admitidas en x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Si la aplicación tiene dependencias externas, como referencias nativas o proyectos de enlace, actualícelos para tener como destino 64 bits.

### <a name="errors"></a>Errores

La primera vez que compile o ejecute la aplicación con compatibilidad con 64 bits, puede encontrar errores de vínculo de Clang o problemas en tiempo de ejecución. Estos errores pueden producirse si las dependencias de terceros, por ejemplo, las referencias nativas de los proyectos de Xamarin. Mac o de enlaces, o los marcos de trabajo para todo el sistema cargados manualmente, no se han actualizado a 64 bits.

> [!TIP]
> Convertir el proyecto en 64 bits es un cambio importante y puede descubrir indirectamente diversos errores de programación. En concreto, puede cambiar el tamaño y la alineación de las estructuras de datos, lo que afectaría a las firmas p/Invoke y al código nativo vinculado en el proyecto. Considere la posibilidad de revisar las advertencias de compilación dadas y probar la aplicación minuciosamente después para detectar posibles problemas.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Error de ejemplo resultante de una dependencia de terceros vinculada dinámicamente que no tiene como destino 64 bits:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Este error se podría seguir en tiempo de ejecución `dlopen` devolver `IntPtr.Zero` en lugar de un identificador esperado.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Error de ejemplo resultante de una dependencia de terceros vinculada estáticamente que no tiene como destino 64 bits:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Para compilar y ejecutar correctamente, actualice estas dependencias a 64 bits y vuelva a compilar la aplicación.
