---
title: Acceso a los bits de píxeles del mapa de bits de SkiaSharp
description: Descubra las distintas técnicas para obtener acceso y modificar los bits de píxeles de SkiaSharp mapas de bits.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 6c066f89dc8f558a9154138bf38ad4326fe21291
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642523"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>Acceso a los bits de píxeles del mapa de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como se vio en el artículo [ **SkiaSharp guardar mapas de bits archivos**](saving.md), mapas de bits se almacenan normalmente en archivos en un formato comprimido, como JPEG o PNG. Al contrario, no se comprime un mapa de bits de SkiaSharp almacenada en memoria. Se almacena como una serie secuencial de píxeles. Este formato no comprimido facilita a la transferencia de los mapas de bits a una superficie de pantalla.

El bloque de memoria ocupado por un mapa de bits de SkiaSharp se organiza de una manera muy sencilla: Comienza con la primera fila de píxeles, de izquierda a derecha y, a continuación, continúa con la segunda fila. Para los mapas de bits de color completo, cada píxel consta de cuatro bytes, lo que significa que el espacio de memoria total requerido por el mapa de bits es cuatro veces el producto de su ancho y alto.

Este artículo describe cómo una aplicación puede obtener acceso a los píxeles, ya sea directamente mediante el acceso a bloque de memoria de píxel del mapa de bits, directa o indirectamente. En algunos casos, es posible que un programa desea analizar los píxeles de una imagen y generar un histograma de algún tipo. Más comúnmente, las aplicaciones pueden crear imágenes únicas mediante la creación de forma algorítmica los píxeles que componen el mapa de bits:

![Ejemplos de Bits de píxeles](pixel-bits-images/PixelBitsSample.png "ejemplo de Bits de píxeles")

## <a name="the-techniques"></a>Las técnicas

SkiaSharp proporciona varias técnicas para tener acceso a los bits de píxeles de un mapa de bits. Cuál elegir suele ser un equilibrio entre la codificación comodidad (que está relacionado con mantenimiento y la facilidad de depuración) y el rendimiento. En la mayoría de los casos, usará uno de los siguientes métodos y propiedades de `SKBitmap` para tener acceso a los píxeles del mapa de bits:

- El `GetPixel` y `SetPixel` métodos le permiten obtener o establecer el color de un solo píxel.
- El `Pixels` propiedad obtiene una matriz de colores de los píxeles del mapa de bits completo, o establece la matriz de colores.
- `GetPixels` Devuelve la dirección de la memoria de píxeles utilizada por el mapa de bits.
- `SetPixels` reemplaza la dirección de la memoria de píxeles utilizada por el mapa de bits.

Puede pensar en las primeras dos técnicas como "alto nivel" y los dos segundos como "bajo nivel." Hay algunos otros métodos y propiedades que puede usar, pero estos son los más valiosos.

Para que pueda ver las diferencias de rendimiento entre estas técnicas, el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) aplicación contiene una página denominada **mapa de bits degradado** que crea un mapa de bits con píxeles que combinan tonos de color rojo y azules para crear un degradado. El programa crea ocho copias diferentes del mapa de bits, todos utilizan diferentes técnicas para establecer los píxeles del mapa de bits. Cada uno de estos mapas de bits de ocho se crea en un método independiente que también establece una breve descripción de la técnica y calcula el tiempo necesario para establecer todos los píxeles. Cada método recorre en bucle la lógica de la configuración de píxel 100 veces para obtener una estimación más precisa del rendimiento.

### <a name="the-setpixel-method"></a>El método SetPixel

Si solo tiene que establecer u obtener varios píxeles individuales, el [ `SetPixel` ](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) y [ `GetPixel` ](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) métodos son ideales. Para cada uno de estos dos métodos, especifique la columna de tipo entero y la fila. Independientemente del formato de píxel, estos dos métodos le permiten obtener o establecer el píxel como un `SKColor` valor:

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

El `col` argumento debe oscilar entre 0 y una menor que el `Width` propiedad del mapa de bits, y `row` varía entre 0 y una menor que el `Height` propiedad.

Este es el método **mapa de bits degradado** que establece el contenido de un mapa de bits mediante el `SetPixel` método. El mapa de bits es de 256 x 256 píxeles y el `for` bucles están codificados con el intervalo de valores:

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

El conjunto de colores de cada píxel tiene un igual del componente rojo a la columna de mapa de bits y un igual del componente azul a la fila. El mapa de bits resultante es negro en la esquina superior izquierda, roja en la esquina superior derecha, azul en la parte inferior izquierda y magenta en la esquina inferior derecha, con degradados en otro lugar.

El `SetPixel` método es llamado 65.536 veces y, a continuación, sin tener en cuenta cómo eficaz este método podría ser, por lo general no es una buena idea hacer que muchas llamadas de API si está disponible una alternativa. Afortunadamente, hay varias alternativas.

### <a name="the-pixels-property"></a>La propiedad de píxeles

`SKBitmap` define un [ `Pixels` ](xref:SkiaSharp.SKBitmap.Pixels) propiedad que devuelve una matriz de `SKColor` valores para el mapa de bits completo. También puede usar `Pixels` para establecer una matriz de valores de color para el mapa de bits:

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

Los píxeles se organizan en la matriz, empezando con la primera fila, de izquierda a derecha, a continuación, la segunda fila y así sucesivamente. El número total de colores de la matriz es igual al producto del alto y ancho del mapa de bits.

Aunque esta propiedad aparece sean eficaces, tenga en cuenta que se copian los píxeles del mapa de bits en la matriz y de la matriz en el mapa de bits, y los píxeles se convierten desde y hacia `SKColor` valores.

Este es el método el `GradientBitmapPage` clase que establece el mapa de bits utilizando el `Pixels` propiedad. El método asigna un `SKColor` matriz de tamaño necesario, pero podría haber usado el `Pixels` propiedad para crear esa matriz:

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

Tenga en cuenta que el índice de la `pixels` matriz debe calcularse desde el `row` y `col` variables. La fila se multiplica por el número de píxeles en cada fila (256 en este caso) y, a continuación, se agrega la columna.

`SKBitmap` También define un proceso similar `Bytes` propiedad, que devuelve una matriz de bytes para el mapa de bits completo, pero es más complicado para los mapas de bits de color completo.

### <a name="the-getpixels-pointer"></a>El puntero GetPixels

Potencialmente la técnica más eficaz para tener acceso a los píxeles del mapa de bits es [ `GetPixels` ](xref:SkiaSharp.SKBitmap.GetPixels), para que no debe confundirse con el `GetPixel` método o la `Pixels` propiedad. Observará una diferencia con `GetPixels` en que devuelve un valor no es muy común en la programación de C#:

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [ `IntPtr` ](xref:System.IntPtr) tipo representa un puntero. Se llama `IntPtr` porque es la longitud de un entero en el procesador nativo de la máquina en la que se ejecuta el programa, normalmente 32 bits o 64 bits de longitud. El `IntPtr` que `GetPixels` devuelve es la dirección del bloque de memoria que está usando el objeto de mapa de bits para almacenar sus píxeles real.

Puede convertir el `IntPtr` en un C# puntero tipo mediante el [ `ToPointer` ](xref:System.IntPtr.ToPointer) método. La sintaxis de C# puntero es igual que C y C++:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

El `ptr` variable es de tipo _puntero de byte_. Esto `ptr` variable permite obtener acceso a los bytes de memoria individuales que se usan para almacenar los píxeles del mapa de bits. Use código similar al siguiente para leer un byte de esta memoria o escribir un byte en la memoria:

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

En este contexto, el asterisco es el de C# _operador de direccionamiento indirecto_ y se usa para hacer referencia al contenido de la memoria que apunta `ptr`. Inicialmente, `ptr` puntos al primer byte del primer píxel de la primera fila del mapa de bits, pero pueden realizar operaciones aritméticas en el `ptr` variable para moverlo a otras ubicaciones del mapa de bits.

Una desventaja es que puede usar esto `ptr` variable solo en un bloque de código marcado con el `unsafe` palabra clave. Además, el ensamblado debe estar marcado como Permitir bloques inseguros. Esto se realiza en las propiedades del proyecto.

Uso de punteros en C# es muy eficaz, pero también muy peligroso. Debe tener cuidado de que no tiene acceso a memoria más allá de lo que el puntero se supone que hacen referencia. Por eso uso del puntero está asociado con la palabra "unsafe".

Este es el método el `GradientBitmapPage` clase que usa el `GetPixels` método. Tenga en cuenta el `unsafe` bloque que abarca todo el código usando el puntero de bytes:

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

Cuando el `ptr` variable primero se obtiene de la `ToPointer` método, apunta al primer byte del píxel más a la izquierda de la primera fila del mapa de bits. El `for` bucles para `row` y `col` se configuran para que `ptr` se puede incrementar con la `++` operador después de que se establece cada byte de cada píxel. Para los demás 99 bucles a través de los píxeles, la `ptr` debe volver a establecer el principio del mapa de bits.

Cada píxel es de cuatro bytes de memoria, por lo que cada byte debe establecerse por separado. Este código se da por supuesto que los bytes que se encuentran en el rojo de orden, verde, azul y alfa, que es coherente con la `SKColorType.Rgba8888` tipo de color. Puede que recuerde que esto es el tipo de color predeterminado para iOS y Android, pero no para la plataforma Universal de Windows. De forma predeterminada, la UWP crea los mapas de bits con el `SKColorType.Bgra8888` tipo de color. Por este motivo, se esperaba ver algunos resultados diferentes en esa plataforma.

Es posible convertir el valor devuelto de `ToPointer` a un `uint` puntero en lugar de un `byte` puntero. Esto permite que un píxel completo tener acceso en una sola instrucción. Aplicar el `++` operador a ese puntero incrementa en cuatro bytes para que apunte al píxel en el siguiente:

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

El píxel se establece mediante la `MakePixel` método, que construye un píxel de entero de los componentes rojos, verdes, azules y alfabéticos. Tenga en cuenta que el `SKColorType.Rgba8888` formato tiene un byte de píxel ordenación similar al siguiente:

RR GG BB AA

Pero es el entero correspondiente a esos bytes:

AABBGGRR

Se almacena el primero el byte menos significativo del entero de acuerdo con la arquitectura de little-endian. Esto `MakePixel` método no funcionará correctamente para los mapas de bits con el `Bgra8888` tipo de color.

El `MakePixel` método se marca con el [ `MethodImplOptions.AggressiveInlining` ](xref:System.Runtime.CompilerServices.MethodImplOptions) opción fomentar al compilador que evite realizar esto un método independiente, sino más bien para compilar el código donde se llama al método. Esto debe mejorar el rendimiento.

Curiosamente, el `SKColor` estructura define una conversión explícita de `SKColor` en un entero sin signo, lo que significa que un `SKColor` se puede crear valor y una conversión a `uint` puede usarse en lugar de `MakePixel`:

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

Esta es la única pregunta: ¿Es el formato de entero del `SKColor` valor en el orden `SKColorType.Rgba8888` del tipo de color, o el `SKColorType.Bgra8888` tipo de color, o es algo más completo? La respuesta a esa pregunta deberá mostrarse en breve.

### <a name="the-setpixels-method"></a>El método SetPixels

`SKBitmap` También define un método denominado [ `SetPixels` ](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)), que se llama similar al siguiente:

```csharp
bitmap.SetPixels(intPtr);
```

Recuerde que `GetPixels` Obtiene un `IntPtr` que hacen referencia a un bloque de memoria usado por el mapa de bits para almacenar sus píxeles. El `SetPixels` llamar _reemplaza_ ese bloque de memoria con el bloque de memoria al que hace referencia el `IntPtr` especificado como el `SetPixels` argumento. El mapa de bits, a continuación, libera el bloque de memoria que se estaba usando anteriormente. La próxima vez `GetPixels` es llamado, obtiene el bloque de memoria establecido con `SetPixels`.

En primer lugar, parece como si `SetPixels` le nada más potencia y rendimiento que `GetPixels` al tiempo que es poco práctica. Con `GetPixels` obtener el bloque de memoria de mapa de bits y tener acceso a él. Con `SetPixels` asignar y obtener acceso a parte de la memoria y, a continuación, configúrelo como el bloque de memoria de mapa de bits.

Pero el `SetPixels` uso de ofrece una ventaja sintáctica distinta: Permite tener acceso a los bits de píxel de mapa de bits mediante una matriz. Este es el método `GradientBitmapPage` que muestra esta técnica. En primer lugar, el método define una matriz de bytes multidimensional correspondientes a los bytes de los píxeles del mapa de bits. La primera dimensión es la fila, la segunda dimensión es la columna y la tercera dimensión corresponde a los cuatro componentes de cada píxel:

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

A continuación, una vez se ha rellenado la matriz de píxeles, una `unsafe` bloque y un `fixed` instrucción se usa para obtener un puntero de bytes que señala a esta matriz. A continuación, se puede convertir a ese puntero de bytes en un `IntPtr` para pasar a `SetPixels`.

La matriz que cree no tiene que ser una matriz de bytes. Puede ser una matriz de enteros con solo dos dimensiones para la fila y columna:

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

El `MakePixel` método nuevo se usa para combinar los componentes de color en un píxel de 32 bits.

Para comprobarlo, este es el mismo código pero con un `SKColor` valor convertidos a un entero sin signo:

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

### <a name="comparing-the-techniques"></a>Comparación de las técnicas

El constructor de la **degradado de Color** página llama a los ocho de los métodos que se muestra arriba y guarda los resultados:

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

El constructor concluye mediante la creación de un `SKCanvasView` para mostrar los mapas de bits resultante. El `PaintSurface` controlador divide su superficie en ocho rectángulos y las llamadas `Display` para mostrar cada uno de ellos:

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

Para permitir que el compilador optimice el código, esta página se ejecutó en **versión** modo. Aquí es esa página se ejecuta en un simulador de iPhone 8 en un MacBook Pro, un teléfono Android de Nexus 5 y Surface Pro 3 que ejecutan Windows 10. Debido a las diferencias de hardware, evitar comparar los tiempos de rendimiento entre los dispositivos, pero en su lugar, examinar las horas relativas en cada dispositivo:

[![Mapa de bits degradado](pixel-bits-images/GradientBitmap.png "mapa de bits de degradado")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Esta es una tabla que consolida los tiempos de ejecución en milisegundos:

| API       | Tipo de datos | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3.17 |   10.77 | 3,49 |
| píxeles    |           | 0,32 |    1.23 | 0,07 |
| GetPixels | byte      | 0,09 |    0,24 | 0.10 |
|           | uint      | 0,06 |    0,26 | 0,05 |
|           | SKColor   | 0.29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6.78 | 0.11 |
|           | uint      | 0,14 |    0.69 | 0,06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

Según lo previsto, una llamada a `SetPixel` 65.536 veces es la forma de effeicient mínimos para establecer los píxeles del mapa de bits. Rellenar un `SKColor` matriz y la configuración de la `Pixels` propiedad es mucho mejor e incluso compara favorablemente con algunos de los `GetPixels` y `SetPixels` técnicas. Trabajar con `uint` valores de píxel es generalmente más rápidos que la configuración independiente `byte` componentes y convertir la `SKColor` valor entero sin signo que agrega una sobrecarga al proceso.

También es interesante comparar los distintos degradados: Las primeras filas de cada plataforma son las mismas y muestran el degradado tal como estaba previsto. Esto significa que el `SetPixel` método y el `Pixels` propiedad crear correctamente los píxeles de colores independientemente del formato de píxel subyacente.

Las dos filas siguientes de iOS y Android capturas de pantalla también son las mismas, que confirma que el pequeño `MakePixel` método esté definido correctamente para el valor predeterminado `Rgba8888` formato de píxel para estas plataformas.

La fila inferior de las capturas de pantalla de Android y iOS con versiones anteriores, es lo que indica que el entero sin signo se obtuvo mediante la conversión de un `SKColor` valor está en el formulario:

AARRGGBB

Los bytes que se encuentran en el orden:

AA BB GG RR

Se trata de la `Bgra8888` ordenación en lugar de `Rgba8888` ordenación. El `Brga8888` formato es el valor predeterminado para la plataforma Windows Universal, que es la razón por la degradados en la última fila de esa captura de pantalla son los mismos que la primera fila. Pero las dos filas intermedias son incorrectas porque el código que crea los mapas de bits supone un `Rgba8888` ordenación.

Si desea utilizar el mismo código para tener acceso a los bits de píxeles en cada plataforma, puede crear explícitamente un `SKBitmap` mediante el `Rgba8888` o `Bgra8888` formato. Si desea convertir `SKColor` valores en píxeles del mapa de bits, utilice `Bgra8888`.

## <a name="random-access-of-pixels"></a>Acceso aleatorio de píxeles

El `FillBitmapBytePtr` y `FillBitmapUintPtr` métodos en el **degradado del mapa de bits** página beneficiado `for` bucles diseñados para satisfacer el mapa de bits de forma secuencial, de la fila superior a la fila inferior y, en cada fila de izquierda a derecha. El píxel se puede establecer con la misma instrucción que incrementa el puntero.

A veces es necesario tener acceso a los píxeles aleatoriamente en lugar de secuencialmente. Si usas el `GetPixels` enfoque, tendrá que calcular los punteros basados en la fila y columna. Esto se muestra en el **arco iris seno** página, que crea un mapa de bits que se muestra un arco iris en forma de un ciclo de una curva de seno.

Los colores del arco iris son más fáciles de crear mediante el modelo de color HSL (matiz, saturación, luminosidad). El `SKColor.FromHsl` método crea un `SKColor` valor con los valores de matiz comprendidos entre 0 y 360 (por ejemplo, ángulos de un círculo, pero va de rojo, pasando por verde y azul y de vuelta a rojo) y los valores de saturación y luminosidad comprendida entre 0 y 100. Para los colores de un arco iris, la saturación debe establecerse en un máximo de 100 y la luminosidad a un punto medio de 50.

**Seno de arco iris** crea esta imagen recorriendo las filas del mapa de bits, y, a continuación, recorrer los valores de matiz 360. Desde cada valor de matiz, calcula una columna de mapa de bits que también se basa en un valor de seno:

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

Tenga en cuenta que el constructor crea el mapa de bits en función de la `SKColorType.Bgra8888` formato:

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Esto permite al programa usar la conversión de `SKColor` valores en `uint` píxeles sin preocupaciones. Aunque éste no reproduce un rol en este programa determinado, siempre que se utiliza el `SKColor` conversión establecer píxeles, que también se debe especificar `SKAlphaType.Unpremul` porque `SKColor` no Premultiplicar sus componentes de color por el valor alfa.

El constructor, a continuación, usa el `GetPixels` método para obtener un puntero al primer píxel del mapa de bits:

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Para las filas y columnas concretas, se debe agregar un valor de desplazamiento a `basePtr`. Este desplazamiento es la fila multiplicado por el ancho del mapa de bits, además de la columna:

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

El `SKColor` valor se almacena en memoria mediante este puntero:

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

En el `PaintSurface` controlador de la `SKCanvasView`, el mapa de bits se expande para rellenar el área de presentación:

[![Seno de arco iris](pixel-bits-images/RainbowSine.png "arco iris seno")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>De un mapa de bits a otro

Muchas tareas de procesamiento de imágenes implican modificar píxeles a medida que se transfieren desde un mapa de bits a otro. Esta técnica se muestra en el **ajuste de Color** página. La página se cargará uno de los recursos de mapa de bits y, a continuación, le permite modificar la imagen con tres `Slider` vistas:

[![Ajuste de color](pixel-bits-images/ColorAdjustment.png "ajuste de Color")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Para cada color de píxel, la primera `Slider` agrega un valor de 0 a 360 para el matiz, pero, a continuación, usa el operador módulo para mantener el resultado entre 0 y 360, eficazmente desplazar los colores a lo largo del espectro (como se muestra en la captura de pantalla UWP). El segundo `Slider` le permite seleccionar un factor multiplicativo comprendida entre 0,5 y 2 para aplicar a la saturación y el tercero `Slider` hace lo mismo para la luminosidad, como se muestra en la captura de pantalla de Android.

El programa mantiene dos mapas de bits, el mapa de bits de origen original denominado `srcBitmap` y el mapa de bits de destino ajustada denominado `dstBitmap`. Cada vez que un `Slider` se mueve, el sistema calcula todos los píxeles nuevo en `dstBitmap`. Por supuesto, va a experimentar los usuarios moviendo el `Slider` vistas muy rápidamente, por lo que recomienda el mejor rendimiento que puede administrar. Esto implica la `GetPixels` método para los mapas de bits de origen y destino.

El **ajuste de Color** página no controla el formato de color de los mapas de bits de origen y destino. En su lugar, contiene la lógica ligeramente diferente para `SKColorType.Rgba8888` y `SKColorType.Bgra8888` formatos. El origen y destino pueden ser diferentes formatos, y el programa seguirá funcionando.

Este es el programa, excepto el fundamental `TransferPixels` método que transfiere los píxeles forman el origen al destino. El constructor establece `dstBitmap` igual a `srcBitmap`. El `PaintSurface` controlador muestra `dstBitmap`:

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

El `ValueChanged` controlador para el `Slider` vistas calcula los valores de ajuste y llamadas `TransferPixels`.

Toda la `TransferPixels` método está marcado como `unsafe`. Comience por obtener punteros de bytes a los bits de píxeles de ambos mapas de bits y, a continuación, recorre en bucle todas las filas y columnas. En el mapa de bits de origen, el método obtiene cuatro bytes para cada píxel. Podría tratarse de cualquiera el `Rgba8888` o `Bgra8888` orden. Comprobar el tipo de color permite un `SKColor` valor al crearse. Los componentes HSL, a continuación, se extraen, ajustar y usa para volver a crear la `SKColor` valor. Dependiendo de si el mapa de bits de destino es `Rgba8888` o `Bgra8888`, los bytes se almacenan en el destino bitmp:

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

Es probable que el rendimiento de este método se puede mejorar aún más mediante la creación de métodos independientes para las diversas combinaciones de tipos de color de los mapas de bits de origen y destino y evitar la comprobación del tipo para cada píxel. Otra opción consiste en tener varios `for` bucles para el `col` variable según el tipo de color.

## <a name="posterization"></a>Posterización

Otro trabajo común que implica el acceso a los bits de píxeles es _posterización_. Se reduce el número si colores codifican en píxeles del mapa de bits para que el resultado es similar a un póster de trazados a mano mediante una paleta de colores limitado.

El **Posterizar** página lleva a cabo este proceso en una de las imágenes de monkey:

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

El código en el constructor tiene acceso a cada píxel, realiza una operación AND bit a bit con el valor 0xE0E0E0FF y, a continuación, almacena el resultado en el mapa de bits. Los valores 0xE0E0E0FF mantiene 3 bits superiores de cada componente de color y los 5 bits inferiores se establece en 0. En lugar de 2<sup>24</sup> o 16 777 216 colores, el mapa de bits se reduce a 2<sup>9</sup> o 512 colores:

[![Posterización](pixel-bits-images/Posterize.png "posterización")](pixel-bits-images/posterización-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
