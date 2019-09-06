---
title: Compilación previa de Xamarin. Mac
description: En este documento se describe la compilación por encima de la hora en Xamarin. Mac. Compara la compilación de AOT con la compilación JIT, explica cómo habilitar AOT y examina el AOT híbrido.
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: e1f1e2e1e5dbec7dc8f2310b3f9565d0bc209c00
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283691"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Compilación previa de Xamarin. Mac

## <a name="overview"></a>Información general

La compilación de antemano de tiempo (AOT) es una técnica eficaz de optimización para mejorar el rendimiento de inicio. Sin embargo, también afecta al tiempo de compilación, al tamaño de la aplicación y a la ejecución del programa de maneras profundas. Para comprender los inconvenientes que impone, vamos a profundizar un poco en la compilación y la ejecución de una aplicación.

El código escrito en lenguajes administrados C# , F#como y, se compila en una representación intermedia denominada Il. Este IL, almacenado en su biblioteca y ensamblados de programa, es relativamente compacto y portátil entre arquitecturas de procesador. Sin embargo, IL solo es un conjunto intermedio de instrucciones y, en algún momento, el IL deberá traducirse en código máquina específico del procesador.

Hay dos puntos en los que se puede realizar este procesamiento:

- **Just-in-Time (JIT)** : durante el inicio y la ejecución de la aplicación, el Il se compila en la memoria para el código máquina.
- Por **encima del tiempo (AOT)** : durante la compilación, el Il se compila y se escribe en bibliotecas nativas y se almacena en el lote de aplicaciones.

Cada opción tiene una serie de ventajas e inconvenientes:

- **JIT**
  - **Hora de inicio** : la compilación JIT debe realizarse en el inicio. Para la mayoría de las aplicaciones, esto se encuentra en el orden de 100 MS, pero para las aplicaciones grandes este tiempo puede ser mucho más.
  - **Ejecución** : como el código JIT se puede optimizar para el procesador específico que se está usando, se puede generar código ligeramente mejor. En la mayoría de las aplicaciones, esto es un número de puntos porcentuales más rápido.
- **AOT**
  - **Tiempo de inicio** : la carga de dylibs precompilados es significativamente más rápida que los ensamblados JIT.
  - **Espacio en disco** : los dylibs pueden tener una cantidad significativa de espacio en disco. Dependiendo de los ensamblados que son AOTed, puede duplicar el tamaño de la parte de código de la aplicación.
  - **Tiempo de compilación** : la compilación de AOT es significativamente más lenta que JIT y ralentizará las compilaciones con él. Esta ralentización puede oscilar desde segundos hasta un minuto o más, en función del tamaño y el número de ensamblados compilados.
  - **Ofuscación** : como el Il, que es significativamente más fácil de usar para el ingeniería inversa que el código máquina, no es necesario que se pueda quitar para ayudar a ofuscar el código confidencial. Esto requiere la opción "híbrida" que se describe a continuación.

## <a name="enabling-aot"></a>Habilitación de AOT

Las opciones de AOT se agregarán al panel compilación de Mac en una actualización futura. Hasta entonces, la habilitación de AOT requiere pasar un argumento de línea de comandos mediante el campo "argumentos MMP adicionales" en la compilación de Mac. Las opciones son las siguientes:

```
--aot[=VALUE]          Specify assemblies that should be AOT compiled
                          - none - No AOT (default)
                          - all - Every assembly in MonoBundle
                          - core - Xamarin.Mac, System, mscorlib
                          - sdk - Xamarin.Mac.dll and BCL assemblies
                          - |hybrid after option enables hybrid AOT which
                          allows IL stripping but is slower (only valid
                          for 'all')
                          - Individual files can be included for AOT via +
                          FileName.dll and excluded via -FileName.dll

                          Examples:
                            --aot:all,-MyAssembly.dll
                            --aot:core,+MyOtherAssembly.dll,-mscorlib.dll
```


## <a name="hybrid-aot"></a>AOT híbrido

Durante la ejecución de una aplicación macOS, el tiempo de ejecución utiliza de forma predeterminada el código máquina cargado de las bibliotecas nativas generadas por la compilación de AOT. Sin embargo, hay algunas áreas de código como trampolines, donde la compilación JIT puede generar resultados significativamente más optimizados. Esto requiere que el IL de los ensamblados administrados esté disponible. En iOS, las aplicaciones están restringidas a cualquier uso de la compilación JIT; Estas secciones de código también se compilan en AOT.

La opción híbrido indica al compilador que compile estas secciones (como iOS), pero también para suponer que el IL no estará disponible en tiempo de ejecución. Después, este IL puede quitarse después de la compilación. Como se indicó anteriormente, el tiempo de ejecución se verá obligado a usar rutinas menos optimizadas en algunos lugares.

## <a name="further-considerations"></a>Consideraciones adicionales

Las consecuencias negativas de la escala del AOT con los tamaños y el número de ensamblados procesados. El [marco de trabajo de destino](~/mac/platform/target-framework.md) completo, por ejemplo, contiene una biblioteca de clases base (BCL) significativamente mayor que la moderna y, por tanto, el AOT tardará mucho más en generar paquetes más grandes. Esto se compone de la incompatibilidad completa de la plataforma de destino con la vinculación, que elimina el código no utilizado. Considere la posibilidad de mover la aplicación a moderno y habilitar la vinculación para obtener los mejores resultados.

Una ventaja adicional de AOT incluye interacciones mejoradas con la depuración nativa y la generación de perfiles de cadenas. Puesto que una gran mayoría del código base se compilará con anterioridad, tendrá nombres de función y símbolos que son más fáciles de leer en los informes de bloqueos nativos, la generación de perfiles y la depuración. Las funciones generadas JIT no tienen estos nombres y a menudo se muestran como desplazamientos hexadecimales sin nombre que son muy difíciles de resolver.
