---
title: Obtener acceso a bits de píxel de mapa de bits SkiaSharp
description: Descubra las diversas técnicas para obtener acceso y modificar los bits de píxeles de los mapas de bits de SkiaSharp.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6e3edf7d0e7630429f8f1c76009987ee8a4b737a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375335"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>Obtener acceso a bits de píxel de mapa de bits SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como vio en el artículo [**Guardar mapas de bits de SkiaSharp en archivos**](saving.md), los mapas de bits se almacenan generalmente en archivos con formato comprimido, como JPEG o PNG. En el restraer, un mapa de bits de SkiaSharp almacenado en memoria no se comprime. Se almacena como una serie secuencial de píxeles. Este formato sin comprimir facilita la transferencia de mapas de bits a una superficie de presentación.

El bloque de memoria ocupado por un mapa de bits SkiaSharp se organiza de manera muy sencilla: comienza con la primera fila de píxeles, de izquierda a derecha y, a continuación, continúa con la segunda fila. En el caso de los mapas de bits de color completo, cada píxel consta de cuatro bytes, lo que significa que el espacio de memoria total necesario para el mapa de bits es cuatro veces el producto de su ancho y alto.

En este artículo se describe cómo una aplicación puede obtener acceso a esos píxeles, ya sea directamente mediante el acceso al bloque de memoria de píxeles del mapa de bits o indirectamente. En algunos casos, es posible que un programa desee analizar los píxeles de una imagen y crear un histograma de algún tipo. Por lo general, las aplicaciones pueden construir imágenes únicas mediante la creación algorítmica de los píxeles que componen el mapa de bits:

![Ejemplos de bits de píxeles](pixel-bits-images/PixelBitsSample.png "Ejemplo de bits de píxeles")

## <a name="the-techniques"></a>Las técnicas

SkiaSharp proporciona varias técnicas para tener acceso a los bits de píxeles de un mapa de bits. Lo que se elige normalmente es un compromiso entre la comodidad de codificación (que está relacionado con el mantenimiento y la facilidad de depuración) y el rendimiento. En la mayoría de los casos, usará uno de los siguientes métodos y propiedades de `SKBitmap` para tener acceso a los píxeles del mapa de bits:

- Los `GetPixel` `SetPixel` métodos y permiten obtener o establecer el color de un solo píxel.
- La `Pixels` propiedad obtiene una matriz de colores de píxeles para el mapa de bits completo o establece la matriz de colores.
- `GetPixels` Devuelve la dirección de la memoria de píxeles utilizada por el mapa de bits.
- `SetPixels` reemplaza la dirección de la memoria de píxeles utilizada por el mapa de bits.

Puede considerar las dos primeras técnicas como "alto nivel" y la segunda dos como "bajo nivel". Hay otros métodos y propiedades que puede usar, pero estos son los más valiosos.

Para que pueda ver las diferencias de rendimiento entre estas técnicas, la aplicación [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contiene una página denominada **mapa de bits de degradado** que crea un mapa de bits con píxeles que combinan sombras de color rojo y azul para crear un degradado. El programa crea ocho copias diferentes de este mapa de bits, todo ello con distintas técnicas para establecer los píxeles del mapa de bits. Cada uno de estos ocho mapas de bits se crea en un método independiente que también establece una breve descripción de texto de la técnica y calcula el tiempo necesario para establecer todos los píxeles. Cada método recorre en bucle la lógica de configuración de píxeles 100 veces para obtener una estimación mejor del rendimiento.

### <a name="the-setpixel-method"></a>El método SetPixel

Si solo necesita establecer u obtener varios píxeles individuales, los [`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) [`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) métodos y son ideales. Para cada uno de estos dos métodos, especifique la columna y la fila de enteros. Independientemente del formato de píxel, estos dos métodos permiten obtener o establecer el píxel como un `SKColor` valor:

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

El `col` argumento debe estar comprendido entre 0 y uno menos que la `Width` propiedad del mapa de bits, y `row` va de 0 a uno menos que la `Height` propiedad.

Este es el método en el **mapa de bits de degradado** que establece el contenido de un mapa de bits mediante el `SetPixel` método. El mapa de bits es 256 por 256 píxeles y los `for` bucles están codificados de forma rígida con el intervalo de valores:

```csharp
public class GradientBitmapPage : ContentPage
{
    const int REPS = 100;

    Stopwatch stopwatch = new Stopwatch();
    ···
    SKBitmap FillBitmapSetPixel(out string description, out int milliseconds)
    {
        description = "SetPixel";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        for (int rep = 0; rep < REPS; rep++)
            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    bitmap.SetPixel(col, row, new SKColor((byte)col, 0, (byte)row));
                }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
}
```

El conjunto de colores para cada píxel tiene un componente rojo igual a la columna Bitmap y un componente azul igual a la fila. El mapa de bits resultante es negro en la parte superior izquierda, rojo en la esquina superior derecha, azul en la parte inferior izquierda y magenta en la esquina inferior derecha, con degradados en otro lugar.

El `SetPixel` método se llama 65.536 veces y, con independencia de lo eficaz que pueda ser este método, por lo general no es una buena idea hacer esa cantidad de llamadas API si está disponible una alternativa. Afortunadamente, hay varias alternativas.

### <a name="the-pixels-property"></a>La propiedad píxeles

`SKBitmap` define una [`Pixels`](xref:SkiaSharp.SKBitmap.Pixels) propiedad que devuelve una matriz de `SKColor` valores para el mapa de bits completo. También puede usar `Pixels` para establecer una matriz de valores de color para el mapa de bits:

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

Los píxeles se organizan en la matriz empezando por la primera fila, de izquierda a derecha, de la segunda fila, y así sucesivamente. El número total de colores de la matriz es igual al producto del ancho y el alto del mapa de bits.

Aunque parece que esta propiedad es eficaz, tenga en cuenta que los píxeles se copian del mapa de bits en la matriz y, de nuevo, de la matriz al mapa de bits, y los píxeles se convierten de `SKColor` los valores y a.

Este es el método de la `GradientBitmapPage` clase que establece el mapa de bits mediante la `Pixels` propiedad. El método asigna una `SKColor` matriz del tamaño requerido, pero podría haber usado la `Pixels` propiedad para crear esa matriz:

```csharp
SKBitmap FillBitmapPixelsProp(out string description, out int milliseconds)
{
    description = "Pixels property";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    SKColor[] pixels = new SKColor[256 * 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                pixels[256 * row + col] = new SKColor((byte)col, 0, (byte)row);
            }

    bitmap.Pixels = pixels;

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Observe que el índice de la `pixels` matriz debe calcularse a partir de `row` las `col` variables y. La fila se multiplica por el número de píxeles de cada fila (256 en este caso) y, a continuación, se agrega la columna.

`SKBitmap` también define una `Bytes` propiedad similar, que devuelve una matriz de bytes para el mapa de bits completo, pero es más complicada para los mapas de bits de color completo.

### <a name="the-getpixels-pointer"></a>Puntero GetPixels

Potencialmente, la técnica más eficaz para tener acceso a los píxeles del mapa de bits es [`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels) , no confundirse con el `GetPixel` método o la `Pixels` propiedad. Observará inmediatamente una diferencia `GetPixels` en que devuelve algo que no es muy común en la programación de C#:

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

El [`IntPtr`](xref:System.IntPtr) tipo .net representa un puntero. Se llama a este método `IntPtr` porque es la longitud de un entero en el procesador nativo del equipo en el que se ejecuta el programa, generalmente de 32 bits o 64 bits de longitud. `IntPtr`Que `GetPixels` devuelve es la dirección del bloque de memoria real que el objeto de mapa de bits utiliza para almacenar sus píxeles.

Puede convertir `IntPtr` en un tipo de puntero de C# mediante el [`ToPointer`](xref:System.IntPtr.ToPointer) método. La sintaxis de puntero de C# es la misma que la de C y C++:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

La `ptr` variable es de tipo _puntero de bytes_. Esta `ptr` variable permite tener acceso a los bytes de memoria individuales que se usan para almacenar los píxeles del mapa de bits. Use código similar al siguiente para leer un byte de esta memoria o escribir un byte en la memoria:

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

En este contexto, el asterisco es el _operador de direccionamiento indirecto_ de C# y se utiliza para hacer referencia al contenido de la memoria a la que apunta `ptr` . Inicialmente, `ptr` apunta al primer byte del primer píxel de la primera fila del mapa de bits, pero puede realizar operaciones aritméticas en la `ptr` variable para moverla a otras ubicaciones del mapa de bits.

Una desventaja es que solo se puede usar esta `ptr` variable en un bloque de código marcado con la `unsafe` palabra clave. Además, el ensamblado se debe marcar como que permita bloques no seguros. Esto se hace en las propiedades del proyecto.

El uso de punteros en C# es muy eficaz, pero también muy peligroso. Tenga cuidado de no tener acceso a la memoria más allá de lo que se supone que debe hacer referencia al puntero. Este es el motivo por el que el uso del puntero está asociado a la palabra "unsafe".

Este es el método de la `GradientBitmapPage` clase que usa el `GetPixels` método. Observe el `unsafe` bloque que engloba todo el código mediante el puntero de bytes:

```csharp
SKBitmap FillBitmapBytePtr(out string description, out int milliseconds)
{
    description = "GetPixels byte ptr";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            byte* ptr = (byte*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (byte)(col);   // red
                    *ptr++ = 0;             // green
                    *ptr++ = (byte)(row);   // blue
                    *ptr++ = 0xFF;          // alpha
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Cuando la `ptr` variable se obtiene por primera vez desde el `ToPointer` método, apunta al primer byte del píxel situado más a la izquierda de la primera fila del mapa de bits. Los `for` bucles de `row` y `col` se configuran de modo que se `ptr` puedan incrementar con el `++` operador después de establecer cada byte de cada píxel. En el caso de los demás bucles 99 a través de los píxeles, el `ptr` se debe volver a establecer al principio del mapa de bits.

Cada píxel tiene cuatro bytes de memoria, por lo que cada byte debe establecerse por separado. En el código siguiente se supone que los bytes están en el orden rojo, verde, azul y alfa, que es coherente con el `SKColorType.Rgba8888` tipo de color. Es posible que recuerde que este es el tipo de color predeterminado para iOS y Android, pero no para el Plataforma universal de Windows. De forma predeterminada, UWP crea mapas de bits con el `SKColorType.Bgra8888` tipo de color. Por esta razón, espere ver algunos resultados diferentes en esa plataforma.

Es posible convertir el valor devuelto de `ToPointer` en un `uint` puntero en lugar de un `byte` puntero. Esto permite tener acceso a un píxel completo en una instrucción. Al aplicar el `++` operador a ese puntero, se incrementa en cuatro bytes para que apunte al siguiente píxel:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    SKBitmap FillBitmapUintPtr(out string description, out int milliseconds)
    {
        description = "GetPixels uint ptr";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        IntPtr pixelsAddr = bitmap.GetPixels();

        unsafe
        {
            for (int rep = 0; rep < REPS; rep++)
            {
                uint* ptr = (uint*)pixelsAddr.ToPointer();

                for (int row = 0; row < 256; row++)
                    for (int col = 0; col < 256; col++)
                    {
                        *ptr++ = MakePixel((byte)col, 0, (byte)row, 0xFF);
                    }
            }
        }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    uint MakePixel(byte red, byte green, byte blue, byte alpha) =>
            (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

El píxel se establece mediante el `MakePixel` método, que construye un píxel entero a partir de los componentes rojo, verde, azul y alfa. Tenga en cuenta que el `SKColorType.Rgba8888` formato tiene un orden de bytes de píxel similar al siguiente:

RR GG BB AA

Pero el entero correspondiente a esos bytes es:

AABBGGRR

El byte menos significativo del entero se almacena primero de acuerdo con la arquitectura little-endian. Este `MakePixel` método no funcionará correctamente para los mapas de bits con el `Bgra8888` tipo de color.

El `MakePixel` método se marca con la [`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions) opción para animar al compilador a evitar que este sea un método independiente, pero en su lugar para compilar el código donde se llama al método. Esto debería mejorar el rendimiento.

Curiosamente, la `SKColor` estructura define una conversión explícita de `SKColor` a un entero sin signo, lo que significa que `SKColor` se puede crear un valor y se puede usar una conversión a en `uint` lugar de `MakePixel` :

```csharp
SKBitmap FillBitmapUintPtrColor(out string description, out int milliseconds)
{
    description = "GetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            uint* ptr = (uint*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (uint)new SKColor((byte)col, 0, (byte)row);
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

La única pregunta es esto: ¿es el formato de entero del `SKColor` valor en el orden del `SKColorType.Rgba8888` tipo de color, o el `SKColorType.Bgra8888` tipo de color o es algo más completo? La respuesta a esa pregunta se revelará en breve.

### <a name="the-setpixels-method"></a>El método SetPixels

`SKBitmap` también define un método denominado [`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)) , que se llama de la siguiente manera:

```csharp
bitmap.SetPixels(intPtr);
```

Recuerde que `GetPixels` obtiene una `IntPtr` referencia al bloque de memoria utilizado por el mapa de bits para almacenar sus píxeles. La `SetPixels` llamada _reemplaza_ ese bloque de memoria por el bloque de memoria al que hace referencia el `IntPtr` especificado como el `SetPixels` argumento. A continuación, el mapa de bits libera el bloque de memoria que estaba usando previamente. La próxima vez `GetPixels` que se llame a, obtendrá el bloque de memoria establecido con `SetPixels` .

En primer lugar, parece que si `SetPixels` no proporciona más potencia y rendimiento que `GetPixels` al ser menos práctico. Con `GetPixels` , obtiene el bloque de memoria de mapa de bits y accede a él. Con, `SetPixels` asigna y obtiene acceso a una memoria y, a continuación, la establece como el bloque de memoria de mapa de bits.

Pero `SetPixels` el uso de ofrece una ventaja sintáctica distinta: permite tener acceso a los bits de píxel de mapa de bits mediante una matriz. Este es el método de `GradientBitmapPage` que muestra esta técnica. El método define primero una matriz de bytes multidimensional que corresponde a los bytes de los píxeles del mapa de bits. La primera dimensión es la fila, la segunda dimensión es la columna y la tercera dimensión corresponde a los cuatro componentes de cada píxel:

```csharp
SKBitmap FillBitmapByteBuffer(out string description, out int milliseconds)
{
    description = "SetPixels byte buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    byte[,,] buffer = new byte[256, 256, 4];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col, 0] = (byte)col;   // red
                buffer[row, col, 1] = 0;           // green
                buffer[row, col, 2] = (byte)row;   // blue
                buffer[row, col, 3] = 0xFF;        // alpha
            }

    unsafe
    {
        fixed (byte* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Después, una vez que la matriz se ha rellenado con píxeles, `unsafe` se usa un bloque y una `fixed` instrucción para obtener un puntero de bytes que señala a esta matriz. A continuación, ese puntero de bytes se puede convertir en para `IntPtr` pasar a `SetPixels` .

La matriz que cree no tiene que ser una matriz de bytes. Puede ser una matriz de enteros con solo dos dimensiones para la fila y la columna:

```csharp
SKBitmap FillBitmapUintBuffer(out string description, out int milliseconds)
{
    description = "SetPixels uint buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = MakePixel((byte)col, 0, (byte)row, 0xFF);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

El `MakePixel` método se utiliza de nuevo para combinar los componentes de color en un píxel de 32 bits.

Solo por integridad, este es el mismo código pero con un `SKColor` valor que se convierte en un entero sin signo:

```csharp
SKBitmap FillBitmapUintBufferColor(out string description, out int milliseconds)
{
    description = "SetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = (uint)new SKColor((byte)col, 0, (byte)row);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

### <a name="comparing-the-techniques"></a>Comparar las técnicas

El constructor de la página **color de degradado** llama a los ocho métodos mostrados anteriormente y guarda los resultados:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    string[] descriptions = new string[8];
    SKBitmap[] bitmaps = new SKBitmap[8];
    int[] elapsedTimes = new int[8];

    SKCanvasView canvasView;

    public GradientBitmapPage ()
    {
        Title = "Gradient Bitmap";

        bitmaps[0] = FillBitmapSetPixel(out descriptions[0], out elapsedTimes[0]);
        bitmaps[1] = FillBitmapPixelsProp(out descriptions[1], out elapsedTimes[1]);
        bitmaps[2] = FillBitmapBytePtr(out descriptions[2], out elapsedTimes[2]);
        bitmaps[4] = FillBitmapUintPtr(out descriptions[4], out elapsedTimes[4]);
        bitmaps[6] = FillBitmapUintPtrColor(out descriptions[6], out elapsedTimes[6]);
        bitmaps[3] = FillBitmapByteBuffer(out descriptions[3], out elapsedTimes[3]);
        bitmaps[5] = FillBitmapUintBuffer(out descriptions[5], out elapsedTimes[5]);
        bitmaps[7] = FillBitmapUintBufferColor(out descriptions[7], out elapsedTimes[7]);

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

El constructor finaliza creando un `SKCanvasView` para mostrar los mapas de bits resultantes. El `PaintSurface` controlador divide su superficie en ocho rectángulos y llama `Display` a para mostrar cada una de ellas:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        int width = info.Width;
        int height = info.Height;

        canvas.Clear();

        Display(canvas, 0, new SKRect(0, 0, width / 2, height / 4));
        Display(canvas, 1, new SKRect(width / 2, 0, width, height / 4));
        Display(canvas, 2, new SKRect(0, height / 4, width / 2, 2 * height / 4));
        Display(canvas, 3, new SKRect(width / 2, height / 4, width, 2 * height / 4));
        Display(canvas, 4, new SKRect(0, 2 * height / 4, width / 2, 3 * height / 4));
        Display(canvas, 5, new SKRect(width / 2, 2 * height / 4, width, 3 * height / 4));
        Display(canvas, 6, new SKRect(0, 3 * height / 4, width / 2, height));
        Display(canvas, 7, new SKRect(width / 2, 3 * height / 4, width, height));
    }

    void Display(SKCanvas canvas, int index, SKRect rect)
    {
        string text = String.Format("{0}: {1:F1} msec", descriptions[index],
                                    (double)elapsedTimes[index] / REPS);

        SKRect bounds = new SKRect();

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.TextSize = (float)(12 * canvasView.CanvasSize.Width / canvasView.Width);
            textPaint.TextAlign = SKTextAlign.Center;
            textPaint.MeasureText("Tly", ref bounds);

            canvas.DrawText(text, new SKPoint(rect.MidX, rect.Bottom - bounds.Bottom), textPaint);
            rect.Bottom -= bounds.Height;
            canvas.DrawBitmap(bitmaps[index], rect, BitmapStretch.Uniform);
        }
    }
}
```

Para permitir que el compilador optimice el código, esta página se ejecutó en modo de **versión** . Esta es la página que se ejecuta en un simulador de iPhone 8 en un MacBook Pro, un teléfono de Nexus 5 Android y Surface Pro 3 con Windows 10. Debido a las diferencias de hardware, evite comparar los tiempos de rendimiento entre los dispositivos, pero en su lugar, mire los tiempos relativos en cada dispositivo:

[![Mapa de bits de degradado](pixel-bits-images/GradientBitmap.png "Mapa de bits de degradado")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Esta es una tabla que consolida los tiempos de ejecución en milisegundos:

| API       | Tipo de datos | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3,17 |   10,77 | 3.49 |
| píxeles    |           | 0,32 |    1.23 | 0,07 |
| GetPixels | byte      | 0,09 |    0,24 | 0,10 |
|           | uint      | 0,06 |    0,26 | 0,05 |
|           | SKColor   | 0,29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6,78 | 0,11 |
|           | uint      | 0.14 |    0.69 | 0,06 |
|           | SKColor   | 0.35 |    1,93 | 0,10 |

Como se esperaba, `SetPixel` la llamada a 65.536 veces es la forma menos effeicient de establecer los píxeles de un mapa de bits. El rellenado de una `SKColor` matriz y el establecimiento de la `Pixels` propiedad es mucho mejor e incluso se compara de forma favorable con algunas de las `GetPixels` `SetPixels` técnicas y. Trabajar con `uint` valores de píxeles suele ser más rápido que establecer `byte` componentes independientes y convertir el `SKColor` valor en un entero sin signo agrega cierta sobrecarga al proceso.

También es interesante comparar los distintos degradados: las primeras filas de cada plataforma son las mismas y muestran el degradado tal como estaba previsto. Esto significa que el `SetPixel` método y la `Pixels` propiedad crean correctamente píxeles a partir de los colores, independientemente del formato de píxel subyacente.

Las dos filas siguientes de las capturas de pantallas de iOS y Android también son las mismas, lo que confirma que el `MakePixel` método Little está definido correctamente para el `Rgba8888` formato de píxel predeterminado para estas plataformas.

La fila inferior de las capturas de pantallas de iOS y Android es hacia atrás, lo que indica que el entero sin signo obtenido al convertir un `SKColor` valor tiene el formato:

AARRGGBB

Los bytes están en el orden:

BB GG RR AA

Esta es la `Bgra8888` ordenación en lugar de la `Rgba8888` ordenación. El `Brga8888` formato es el predeterminado para la plataforma universal de Windows, que es el motivo por el que los degradados de la última fila de esa captura de pantalla son los mismos que en la primera fila. Pero las dos filas centrales son incorrectas porque el código que crea esos mapas de bits asumió una `Rgba8888` ordenación.

Si desea utilizar el mismo código para tener acceso a los bits de píxeles en cada plataforma, puede crear explícitamente un `SKBitmap` mediante el `Rgba8888` `Bgra8888` formato o. Si desea convertir `SKColor` valores en píxeles de mapa de bits, use `Bgra8888` .

## <a name="random-access-of-pixels"></a>Acceso aleatorio de píxeles

Los `FillBitmapBytePtr` `FillBitmapUintPtr` métodos y de la página de **mapa de bits de degradado** se benefician de los `for` bucles diseñados para rellenar el mapa de bits secuencialmente, de la fila superior a la inferior y en cada fila de izquierda a derecha. El píxel se puede establecer con la misma instrucción que incrementó el puntero.

A veces es necesario tener acceso a los píxeles aleatoriamente en lugar de hacerlo secuencialmente. Si utiliza el `GetPixels` enfoque, deberá calcular los punteros basados en la fila y la columna. Esto se muestra en la página **sinusoidal de arco iris** , que crea un mapa de bits que muestra un arco iris en forma de un ciclo de una curva de seno.

Los colores del arco iris son más fáciles de crear con el modelo de color HSL (matiz, saturación y luminosidad). El `SKColor.FromHsl` método crea un `SKColor` valor usando valores de matiz que van de 0 a 360 (como los ángulos de un círculo pero van de rojo, a verde y azul, y de nuevo a rojo), y los valores de saturación y luminosidad comprendidos entre 0 y 100. En el caso de los colores de un arco iris, la saturación debe establecerse en un máximo de 100 y la luminosidad en un punto medio de 50.

**Arcoseno de arco iris** crea esta imagen mediante un bucle a través de las filas del mapa de bits y, a continuación, se recorren los valores de matiz 360. A partir de cada valor de matiz, calcula una columna de mapa de bits que también se basa en un valor de seno:

```csharp
public class RainbowSinePage : ContentPage
{
    SKBitmap bitmap;

    public RainbowSinePage()
    {
        Title = "Rainbow Sine";

        bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);

        unsafe
        {
            // Pointer to first pixel of bitmap
            uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();

            // Loop through the rows
            for (int row = 0; row < bitmap.Height; row++)
            {
                // Calculate the sine curve angle and the sine value
                double angle = 2 * Math.PI * row / bitmap.Height;
                double sine = Math.Sin(angle);

                // Loop through the hues
                for (int hue = 0; hue < 360; hue++)
                {
                    // Calculate the column
                    int col = (int)(360 + 360 * sine + hue);

                    // Calculate the address
                    uint* ptr = basePtr + bitmap.Width * row + col;

                    // Store the color value
                    *ptr = (uint)SKColor.FromHsl(hue, 100, 50);
                }
            }
        }

        // Create the SKCanvasView
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

Observe que el constructor crea el mapa de bits basándose en el `SKColorType.Bgra8888` formato:

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Esto permite que el programa utilice la conversión de `SKColor` valores en `uint` píxeles sin preocuparse por ello. Aunque no desempeña un rol en este programa en particular, siempre que use la `SKColor` conversión para establecer píxeles, también debe especificar `SKAlphaType.Unpremul` porque `SKColor` no multiplica sus componentes de color por el valor alfa.

A continuación, el constructor usa el `GetPixels` método para obtener un puntero al primer píxel del mapa de bits:

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Para cualquier fila y columna en particular, se debe agregar un valor de desplazamiento a `basePtr` . Este desplazamiento es la fila multiplicado por el ancho del mapa de bits, más la columna:

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

El `SKColor` valor se almacena en memoria mediante este puntero:

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

En el `PaintSurface` controlador de `SKCanvasView` , el mapa de bits se ajusta para rellenar el área de presentación:

[![Arcoseno de arco iris](pixel-bits-images/RainbowSine.png "Arcoseno de arco iris")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>De un mapa de bits a otro

Muchas tareas de procesamiento de imágenes implican la modificación de los píxeles a medida que se transfieren de un mapa de bits a otro. Esta técnica se muestra en la página **ajuste de color** . La página carga uno de los recursos de mapa de bits y, a continuación, le permite modificar la imagen mediante tres `Slider` vistas:

[![Ajuste de color](pixel-bits-images/ColorAdjustment.png "Ajuste de color")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Para cada color de píxel, el primero `Slider` agrega un valor de 0 a 360 al matiz, pero, a continuación, usa el operador de módulo para mantener el resultado entre 0 y 360, desplazando eficazmente los colores a lo largo del espectro (como se muestra en la captura de pantalla de UWP). La segunda `Slider` permite seleccionar un factor de multiplicación entre 0,5 y 2 para aplicarlo a la saturación, y el tercero `Slider` hace lo mismo para la luminosidad, tal y como se muestra en la captura de pantalla de Android.

El programa mantiene dos mapas de bits, el mapa de bits de origen original denominado `srcBitmap` y el mapa de bits de destino ajustado denominado `dstBitmap` . Cada vez `Slider` que se mueve un, el programa calcula todos los píxeles nuevos en `dstBitmap` . Por supuesto, los usuarios experimentarán moviendo las `Slider` vistas con mucha rapidez, por lo que querrá el mejor rendimiento que pueda administrar. Esto implica el `GetPixels` método para los mapas de bits de origen y de destino.

La página **ajuste de color** no controla el formato de color de los mapas de bits de origen y de destino. En su lugar, contiene una lógica ligeramente diferente para los `SKColorType.Rgba8888` `SKColorType.Bgra8888` formatos y. El origen y el destino pueden tener distintos formatos y el programa seguirá funcionando.

Este es el programa, excepto el `TransferPixels` método fundamental que transfiere los píxeles del origen al destino. El constructor establece un `dstBitmap` valor igual a `srcBitmap` . El `PaintSurface` controlador muestra `dstBitmap` :

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    SKBitmap srcBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKBitmap dstBitmap;

    public ColorAdjustmentPage()
    {
        InitializeComponent();

        dstBitmap = new SKBitmap(srcBitmap.Width, srcBitmap.Height);
        OnSliderValueChanged(null, null);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        float hueAdjust = (float)hueSlider.Value;
        hueLabel.Text = $"Hue Adjustment: {hueAdjust:F0}";

        float saturationAdjust = (float)Math.Pow(2, saturationSlider.Value);
        saturationLabel.Text = $"Saturation Adjustment: {saturationAdjust:F2}";

        float luminosityAdjust = (float)Math.Pow(2, luminositySlider.Value);
        luminosityLabel.Text = $"Luminosity Adjustment: {luminosityAdjust:F2}";

        TransferPixels(hueAdjust, saturationAdjust, luminosityAdjust);
        canvasView.InvalidateSurface();
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(dstBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

El `ValueChanged` controlador de las `Slider` vistas calcula los valores de ajuste y las llamadas `TransferPixels` .

El `TransferPixels` método completo se marca como `unsafe` . Comienza por obtener punteros de bytes a los bits de píxel de ambos mapas de bits y, a continuación, recorre en bucle todas las filas y columnas. En el mapa de bits de origen, el método obtiene cuatro bytes para cada píxel. Pueden estar en el `Rgba8888` `Bgra8888` orden o. La comprobación del tipo de color permite `SKColor` crear un valor. Los componentes HSL se extraen, ajustan y se usan para volver a crear el `SKColor` valor. Dependiendo de si el mapa de bits de destino es `Rgba8888` o `Bgra8888` , los bytes se almacenan en el bitmp de destino:

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    ···
    unsafe void TransferPixels(float hueAdjust, float saturationAdjust, float luminosityAdjust)
    {
        byte* srcPtr = (byte*)srcBitmap.GetPixels().ToPointer();
        byte* dstPtr = (byte*)dstBitmap.GetPixels().ToPointer();

        int width = srcBitmap.Width;       // same for both bitmaps
        int height = srcBitmap.Height;

        SKColorType typeOrg = srcBitmap.ColorType;
        SKColorType typeAdj = dstBitmap.ColorType;

        for (int row = 0; row < height; row++)
        {
            for (int col = 0; col < width; col++)
            {
                // Get color from original bitmap
                byte byte1 = *srcPtr++;         // red or blue
                byte byte2 = *srcPtr++;         // green
                byte byte3 = *srcPtr++;         // blue or red
                byte byte4 = *srcPtr++;         // alpha

                SKColor color = new SKColor();

                if (typeOrg == SKColorType.Rgba8888)
                {
                    color = new SKColor(byte1, byte2, byte3, byte4);
                }
                else if (typeOrg == SKColorType.Bgra8888)
                {
                    color = new SKColor(byte3, byte2, byte1, byte4);
                }

                // Get HSL components
                color.ToHsl(out float hue, out float saturation, out float luminosity);

                // Adjust HSL components based on adjustments
                hue = (hue + hueAdjust) % 360;
                saturation = Math.Max(0, Math.Min(100, saturationAdjust * saturation));
                luminosity = Math.Max(0, Math.Min(100, luminosityAdjust * luminosity));

                // Recreate color from HSL components
                color = SKColor.FromHsl(hue, saturation, luminosity);

                // Store the bytes in the adjusted bitmap
                if (typeAdj == SKColorType.Rgba8888)
                {
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Alpha;
                }
                else if (typeAdj == SKColorType.Bgra8888)
                {
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Alpha;
                }
            }
        }
    }
    ···
}
```

Es probable que el rendimiento de este método se pueda mejorar aún más creando métodos independientes para las distintas combinaciones de tipos de color de los mapas de bits de origen y de destino, y evite comprobar el tipo de cada píxel. Otra opción es tener varios `for` bucles para la `col` variable basándose en el tipo de color.

## <a name="posterization"></a>Posterización

Otro trabajo común que implica el acceso a bits de píxeles es la _posterización_. Número si se reducen los colores codificados en los píxeles de un mapa de bits para que el resultado se parezca a un póster dibujado a mano mediante una paleta de colores limitada.

La página de **posterización** realiza este proceso en una de las imágenes Monkey:

```csharp
public class PosterizePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public PosterizePage()
    {
        Title = "Posterize";

        unsafe
        {
            uint* ptr = (uint*)bitmap.GetPixels().ToPointer();
            int pixelCount = bitmap.Width * bitmap.Height;

            for (int i = 0; i < pixelCount; i++)
            {
                *ptr++ &= 0xE0E0E0FF;
            }
        }

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform;
    }
}
```

El código del constructor accede a cada píxel, realiza una operación and bit a bit con el valor 0xE0E0E0FF y, a continuación, almacena el resultado en el mapa de bits. Los valores 0xE0E0E0FF mantienen los 3 bits superiores de cada componente de color y establecen los 5 bits inferiores en 0. En lugar de<sup>2 o</sup> 16.777.216 colores, el mapa de bits se reduce<sup>9</sup> a 2 o 512 colores:

[![Posterizar](pixel-bits-images/Posterize.png "Posterizar")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)