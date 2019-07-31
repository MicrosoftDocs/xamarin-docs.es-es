---
title: Gráficos de SkiaSharp en Xamarin.Forms
description: SkiaSharp es un sistema de gráficos 2D para .NET y C# funciona con el motor de gráficos de Skia de código abierto que se usa habitualmente en productos de Google. Esta guía explica cómo usar SkiaSharp para gráficos 2D en las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 6b85cdcd92c4680ced9f75d7b8c5a69c9512d6c4
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656149"
---
# <a name="skiasharp-graphics-in-xamarinforms"></a>Gráficos de SkiaSharp en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Uso SkiaSharp para gráficos 2D en las aplicaciones de Xamarin.Forms_

SkiaSharp es un sistema de gráficos 2D para .NET y C# funciona con el motor de gráficos de Skia de código abierto que se usa habitualmente en productos de Google. Puede usar SkiaSharp en sus aplicaciones de Xamarin.Forms para dibujar texto, mapas de bits y gráficos vectoriales en 2D. Consulte la [plano 2D](~/graphics-games/skiasharp/index.md) guía para obtener información general sobre la biblioteca de SkiaSharp y algunos otros tutoriales.

En esta guía se da por supuesto que está familiarizado con la programación de Xamarin.Forms.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Seminario Web SkiaSharp para Xamarin. Forms**

## <a name="skiasharp-preliminaries"></a>Pasos preliminares de SkiaSharp

SkiaSharp para Xamarin.Forms está empaquetado como un paquete de NuGet. Después de crear una solución de Xamarin.Forms en Visual Studio o Visual Studio para Mac, puede usar el Administrador de paquetes de NuGet para buscar el **SkiaSharp.Views.Forms** empaquetar y agregarlo a la solución. Si activa la **referencias** sección de cada proyecto después de agregar SkiaSharp, puede ver que varios **SkiaSharp** bibliotecas se han agregado a cada uno de los proyectos de la solución.

Si la aplicación de Xamarin.Forms tiene como destino iOS, use la página de propiedades de proyecto para cambiar el destino de implementación mínimo IOS 8.0.

En cualquier página de C# que utiliza SkiaSharp desea incluir un `using` la directiva para el [`SkiaSharp`](xref:SkiaSharp) espacio de nombres, que abarca todas las  clases de SkiaSharp, estructuras y enumeraciones que va a usar en los gráficos la programación. También conviene un `using` la directiva para el [ `SkiaSharp.Views.Forms` ](xref:SkiaSharp.Views.Forms) espacio de nombres para las clases específicas de Xamarin.Forms. Esto es una cantidad menor espacio de nombres, con la clase más importante que es [ `SKCanvasView` ](xref:SkiaSharp.Views.Forms.SKCanvasView). Esta clase se deriva de Xamarin.Forms `View`  y hospede su salida gráfica de SkiaSharp.

> [!IMPORTANT]
> El `SkiaSharp.Views.Forms` espacio de nombres también contiene un `SKGLView` clase que derive de `View` pero usa OpenGL para representar gráficos. Para fines de simplicidad, esta guía restringe a sí mismo a `SKCanvasView`, pero al usar `SKGLView` en su lugar, es bastante similar.

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[Conceptos básicos de dibujo de SkiaSharp](basics/index.md)

Algunas de las cifras más sencillas de gráficos que puede dibujar con SkiaSharp son círculos, elipses y rectángulos. Mostrar estas cifras, aprenderá acerca de las coordenadas de SkiaSharp, tamaños y colores. La presentación de texto y mapas de bits es más compleja, pero estos artículos también presentan estas técnicas.

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[Trazados y líneas de SkiaSharp](paths/index.md)

Una ruta de acceso de gráficos es una serie de líneas rectas conectadas y curvas. Se trazan las rutas de acceso, rellenado, o ambos. En este artículo abarca muchos aspectos del dibujo de líneas, incluidos los extremos de trazo y uniones y guiones y las líneas de puntos, pero no permite las geometrías de curva.

## <a name="skiasharp-transformstransformsindexmd"></a>[Transformaciones de SkiaSharp](transforms/index.md)

Las transformaciones permiten que los objetos gráficos uniformemente traducido, escalar, girar, o sesgar. En este artículo también muestra cómo puede utilizar una matriz de transformación de 3 por 3 estándar para crear transformaciones no afines y aplicar transformaciones a rutas de acceso.

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[Trazados y curvas de SkiaSharp](curves/index.md)

La exploración de las rutas de acceso se continúa con la adición de curvas para los objetos de una ruta de acceso y aprovechando otras características eficaces de la ruta de acceso. Verá cómo puede especificar una ruta de acceso completa en una cadena de texto conciso, cómo usar los efectos de la ruta de acceso y cómo profundizar en los aspectos internos de ruta de acceso.

## <a name="skiasharp-bitmapsbitmapsindexmd"></a>[Mapas de bits de SkiaSharp](bitmaps/index.md)

Los mapas de bits son matrices rectangulares de bits que corresponden a los píxeles de un dispositivo de pantalla. Esta serie de artículos muestra cómo cargar, guardar, mostrar, crear, dibuje en, animar y tener acceso a los bits de SkiaSharp mapas de bits.

## <a name="skiasharp-effectseffectsindexmd"></a>[Efectos de SkiaSharp](effects/index.md)

Los efectos son propiedades que cambian la visualización de gráficos, incluidos los degradados lineales y circulares normal, disposición en mosaico de mapa de bits, blend modos de desenfoque y otros usuarios.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SkiaSharp con Xamarin.Forms seminario Web (vídeo)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
