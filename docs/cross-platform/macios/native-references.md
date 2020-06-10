---
title: Referencias nativas de los proyectos de iOS, Mac y enlaces
description: Las referencias nativas le ofrecen la posibilidad de insertar un marco nativo en un proyecto de Xamarin. iOS, Xamarin. Mac o de enlace.
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: a06bc0cb91ff3d3894bb7be5bbb275aca35da07d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570965"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>Referencias nativas en proyectos de iOS, Mac y enlaces

_Las referencias nativas le permiten insertar un marco nativo en un proyecto de Xamarin. iOS o Xamarin. Mac o en un proyecto de enlace._

Dado que iOS 8,0 es posible crear un marco de trabajo incrustado para compartir el código entre las extensiones de aplicación y la aplicación principal en Xcode. Con la característica de referencia nativa, es posible usar estos marcos de trabajo incrustados (creados con Xcode) en Xamarin. iOS.

> [!IMPORTANT]
> No será posible crear marcos de trabajo insertados a partir de cualquier tipo de proyectos de Xamarin. iOS o Xamarin. Mac, las referencias nativas solo permiten el consumo de marcos nativos (Objective-C) existentes.

<a name="Terminology"></a>

## <a name="terminology"></a>Terminología

En iOS 8 (y versiones posteriores), los **marcos de trabajo incrustados** pueden estar vinculados estáticamente y vinculados dinámicamente. Para distribuirlos correctamente, debe convertirlos en marcos de "FAT" que incluyan todos sus _segmentos_ para cada arquitectura de dispositivo que desee admitir con la aplicación.

<a name="Static-vs-Dynamic-Frameworks"></a>

### <a name="static-vs-dynamic-frameworks"></a>Marcos estáticos y dinámicos

Los **marcos de trabajo estáticos** se vinculan en tiempo de compilación, donde los **Marcos dinámicos** están vinculados en tiempo de ejecución y se pueden modificar sin volver a vincular. Si ha usado algún marco de terceros antes de iOS 8, estaba usando un **marco de trabajo estático** compilado en la aplicación. Consulte la documentación de [programación de biblioteca dinámica](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) de Apple para obtener más detalles.

<a name="Embedded-vs-System-Frameworks"></a>

### <a name="embedded-vs-system-frameworks"></a>Marcos de trabajo incrustados y del sistema

Los **marcos de trabajo incrustados** se incluyen en el lote de aplicaciones y solo se puede acceder a ellos en su aplicación específica a través de su espacio aislado. Los **marcos de trabajo del sistema** se almacenan en el nivel del sistema operativo y están disponibles para todas las aplicaciones del dispositivo. Actualmente, solo Apple tiene la capacidad de crear marcos de nivel de sistema operativo.

<a name="Thin-vs-Fat-Frameworks"></a>

### <a name="thin-vs-fat-frameworks"></a>Marcos finos frente a FAT

Los **marcos finos** contienen solo el código compilado para una arquitectura de sistema específica en la que los **Marcos FAT** contienen código para varias arquitecturas. Cada código base específico de la arquitectura compilada en un marco de trabajo se conoce como un _segmento_. Por lo tanto, por ejemplo, si tenemos un marco que se compiló para las dos arquitecturas del simulador de iOS (i386 y X86_64), contendría dos segmentos.

Si intentó distribuir este marco de ejemplo con la aplicación, se ejecutaría correctamente en el simulador, pero se producirá un error en el dispositivo, ya que el marco de trabajo no contiene ningún segmento específico del código para un dispositivo iOS. Para asegurarse de que un marco de trabajo funcione en todas las instancias, también debe incluir segmentos específicos del dispositivo como arm64, ARMv7 y armv7s.

<a name="Working-with-Embedded-Frameworks"></a>

## <a name="working-with-embedded-frameworks"></a>Trabajar con marcos de trabajo incrustados

Hay dos pasos que se deben completar para trabajar con marcos de trabajo insertados en una aplicación de Xamarin. iOS o Xamarin. Mac: creación de un marco de trabajo de Fat e inserción del marco.

<a name="Overview"></a>

### <a name="creating-a-fat-framework"></a>Creación de un marco de Fat

Como se indicó anteriormente, para poder usar un marco de trabajo incrustado en la aplicación, debe ser un marco FAT que incluya todos los segmentos de arquitectura del sistema de los dispositivos en los que se ejecutará la aplicación.

Cuando el marco y la aplicación de consumo están en el mismo proyecto de Xcode, esto no es un problema, ya que Xcode compilará el marco de trabajo y la aplicación con la misma configuración de compilación. Dado que las aplicaciones de Xamarin no pueden crear marcos de trabajo incrustados, no se puede usar esta técnica.

Para solucionar este problema, `lipo` se puede usar la herramienta de línea de comandos para combinar dos o más marcos en un marco de Fat que contenga todos los segmentos necesarios. Para obtener más información sobre cómo trabajar con el `lipo` comando, consulte la documentación [vinculación de bibliotecas nativas](~/ios/platform/native-interop.md) .

<a name="Embedding-a-Framework"></a>

### <a name="embedding-a-framework"></a>Incrustar un marco de trabajo

El paso siguiente es necesario para incrustar un marco de trabajo en un proyecto de Xamarin. iOS o Xamarin. Mac mediante referencias nativas:

1. Cree un nuevo o abra un proyecto de Xamarin. iOS, Xamarin. Mac o de enlace existente.
2. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto y seleccione **Agregar**  >  **Agregar referencia nativa**: 

    [![](native-references-images/ref01.png "In the Solution Explorer, right-click on the project name and select Add Native Reference")](native-references-images/ref01.png#lightbox)
3. En el cuadro de diálogo **abrir** , seleccione el nombre del marco nativo que desea incrustar y haga clic en el botón **abrir** : 

    [![](native-references-images/ref02.png "Select the name of the Native Framework to embed and click the Open button")](native-references-images/ref02.png#lightbox)
4. El marco de trabajo se agregará al árbol del proyecto: 

    [![](native-references-images/ref03.png "The framework will be added to the projects tree")](native-references-images/ref03.png#lightbox)

Cuando se compila el proyecto, el marco nativo se incrustará en el lote de la aplicación.

<a name="App-Extensions-and-Embedded-Frameworks"></a>

## <a name="app-extensions-and-embedded-frameworks"></a>Extensiones de aplicaciones y marcos de trabajo incrustados

De forma interna, Xamarin. iOS puede aprovechar esta característica para vincular con el tiempo de ejecución de mono como un marco (cuando el destino de implementación es >= iOS 8,0), lo que reduce significativamente el tamaño de la aplicación para las aplicaciones con extensiones (ya que el tiempo de ejecución de mono solo se incluirá una vez para todo el paquete de aplicaciones, en lugar de una vez para la

Las extensiones se vincularán con el tiempo de ejecución de mono como un marco, ya que todas las extensiones requieren iOS 8,0.

Aplicaciones que no tienen extensiones y aplicaciones destinadas a iOS 

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada de la inserción de un marco de trabajo nativo en una aplicación Xamarin. iOS o Xamarin. Mac.
