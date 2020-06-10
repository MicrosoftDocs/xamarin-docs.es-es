---
title: "SkiaSharp Graphics in Xamarin.Forms " Description: "SkiaSharp es un sistema de gráficos 2D para .net y C# basado en el motor de gráficos Skia de código abierto que se usa en gran medida en los productos de Google. En esta guía se explica cómo usar SkiaSharp para gráficos 2D en las Xamarin.Forms aplicaciones ".
MS. Prod: Xamarin ms. AssetID: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB autor: davidbritch ms. Author: dabritch ms. Date: 09/11/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-graphics-in-xamarinforms"></a>Gráficos de SkiaSharpXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Uso de SkiaSharp para gráficos 2D en las Xamarin.Forms aplicaciones_

SkiaSharp es un sistema de gráficos 2D para .NET y C# basado en el motor de gráficos Skia de código abierto que se usa en gran medida en productos de Google. Puede usar SkiaSharp en las Xamarin.Forms aplicaciones para dibujar gráficos vectoriales 2D, mapas de bits y texto. Consulte la guía de [dibujo 2D](~/graphics-games/skiasharp/index.md) para obtener más información general sobre la biblioteca SkiaSharp y otros tutoriales.

En esta guía se da por supuesto que está familiarizado con la Xamarin.Forms programación.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Seminario Web: SkiaSharp paraXamarin.Forms**

## <a name="skiasharp-preliminaries"></a>SkiaSharp Windows°PE

SkiaSharp para Xamarin.Forms se empaqueta como un paquete NuGet. Después de crear una Xamarin.Forms solución en Visual Studio o Visual Studio para Mac, puede usar el administrador de paquetes NuGet para buscar el paquete **SkiaSharp. views. Forms** y agregarlo a la solución. Si comprueba la sección **referencias** de cada proyecto después de agregar SkiaSharp, puede ver que se han agregado varias bibliotecas **SkiaSharp** a cada uno de los proyectos de la solución.

Si su Xamarin.Forms aplicación tiene como destino iOS, use la página de propiedades del proyecto para cambiar el destino de implementación mínimo a ios 8,0.

En cualquier página de C# que use SkiaSharp, querrá incluir una `using` Directiva para el [`SkiaSharp`](xref:SkiaSharp) espacio de nombres, que engloba todas las clases, estructuras y enumeraciones SkiaSharp que usará en la programación de gráficos. También querrá una `using` Directiva para el [`SkiaSharp.Views.Forms`](xref:SkiaSharp.Views.Forms) espacio de nombres para las clases específicas de Xamarin.Forms . Se trata de un espacio de nombres mucho más pequeño, con la clase más importante [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) . Esta clase se deriva de la Xamarin.Forms `View` clase y hospeda la salida de los gráficos de SkiaSharp.

> [!IMPORTANT]
> El `SkiaSharp.Views.Forms` espacio de nombres también contiene una `SKGLView` clase que se deriva de `View` pero utiliza OpenGL para representar gráficos. Por motivos de simplicidad, esta guía se restringe a `SKCanvasView` , pero usar `SKGLView` en su lugar es bastante similar.

## <a name="skiasharp-drawing-basics"></a>[Conceptos básicos de dibujo de SkiaSharp](basics/index.md)

Algunas de las cifras de gráficos más sencillas que se pueden dibujar con SkiaSharp son círculos, óvalos y rectángulos. Al mostrar estas figuras, obtendrá información sobre las coordenadas, los tamaños y los colores de SkiaSharp. La presentación de texto y mapas de bits es más compleja, pero estos artículos también presentan esas técnicas.

## <a name="skiasharp-lines-and-paths"></a>[Trazados y líneas de SkiaSharp](paths/index.md)

Una ruta de acceso de gráficos es una serie de líneas rectas y curvas conectadas. Las rutas de acceso se pueden trazar, rellenar o ambos. En este artículo se incluyen muchos aspectos del dibujo de líneas, incluidos los extremos del trazo y las combinaciones, y las líneas discontinuas y con puntos, pero se detienen pocas geometrías de curva.

## <a name="skiasharp-transforms"></a>[Transformaciones de SkiaSharp](transforms/index.md)

Las transformaciones permiten que los objetos de gráficos se traduzcan, escalen, giren o sesgadon uniformemente. En este artículo también se muestra cómo puede usar una matriz de transformación estándar de 3 por 3 para crear transformaciones no afines y aplicar transformaciones a trazados.

## <a name="skiasharp-curves-and-paths"></a>[Trazados y curvas de SkiaSharp](curves/index.md)

La exploración de las rutas de acceso continúa con la adición de curvas a los objetos de trazado y la explotación de otras características de ruta de acceso eficaces. Verá cómo puede especificar una ruta de acceso completa en una cadena de texto concisa, cómo usar los efectos de la ruta de acceso y cómo profundizar en la ruta de acceso interna.

## <a name="skiasharp-bitmaps"></a>[Mapas de bits de SkiaSharp](bitmaps/index.md)

Los mapas de bits son matrices rectangulares de bits que corresponden a los píxeles de un dispositivo de pantalla. En esta serie de artículos se muestra cómo cargar, guardar, mostrar, crear, dibujar, animar y obtener acceso a los bits de los mapas de bits de SkiaSharp.

## <a name="skiasharp-effects"></a>[Efectos SkiaSharp](effects/index.md)

Los efectos son propiedades que modifican la presentación normal de gráficos, incluidos los degradados lineales y circulares, el mosaico de mapas de bits, los modos de mezcla, el desenfoque y otros.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SkiaSharp con Xamarin.Forms seminario web (vídeo)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
