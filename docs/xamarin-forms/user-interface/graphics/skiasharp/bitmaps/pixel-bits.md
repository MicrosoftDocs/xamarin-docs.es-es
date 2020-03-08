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
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918176"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>Acceso a los bits de píxeles del mapa de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como vio en el artículo [**Guardar mapas de bits de SkiaSharp en archivos**](saving.md), los mapas de bits se almacenan generalmente en archivos con formato comprimido, como JPEG o PNG. Al contrario, no se comprime un mapa de bits de SkiaSharp almacenada en memoria. Se almacena como una serie secuencial de píxeles. Este formato no comprimido facilita a la transferencia de los mapas de bits a una superficie de pantalla.

El bloque de memoria ocupado por un mapa de bits de SkiaSharp está organizado de manera muy sencilla: comienza con la primera fila de píxeles, de izquierda a derecha y, a continuación, continúa con la segunda fila. Para los mapas de bits de color completo, cada píxel consta de cuatro bytes, lo que significa que el espacio de memoria total requerido por el mapa de bits es cuatro veces el producto de su ancho y alto.

Este artículo describe cómo una aplicación puede obtener acceso a los píxeles, ya sea directamente mediante el acceso a bloque de memoria de píxel del mapa de bits, directa o indirectamente. En algunos casos, es posible que un programa desea analizar los píxeles de una imagen y generar un histograma de algún tipo. Más comúnmente, las aplicaciones pueden crear imágenes únicas mediante la creación de forma algorítmica los píxeles que componen el mapa de bits:

![Ejemplos de bits de píxeles](pixel-bits-images/PixelBitsSample.png "Ejemplo de bits de píxeles")

## <a name="the-techniques"></a>Las técnicas

SkiaSharp proporciona varias técnicas para tener acceso a los bits de píxeles de un mapa de bits. Cuál elegir suele ser un equilibrio entre la codificación comodidad (que está relacionado con mantenimiento y la facilidad de depuración) y el rendimiento. En la mayoría de los casos, usará uno de los siguientes métodos y propiedades de `SKBitmap` para tener acceso a los píxeles del mapa de bits:

- Los métodos `GetPixel` y `SetPixel` permiten obtener o establecer el color de un solo píxel.
- La propiedad `Pixels` obtiene una matriz de colores de píxeles para todo el mapa de bits o establece la matriz de colores.
- `GetPixels` devuelve la dirección de la memoria de píxeles utilizada por el mapa de bits.
- `SetPixels` reemplaza la dirección de la memoria de píxeles utilizada por el mapa de bits.

Puede pensar en las primeras dos técnicas como "alto nivel" y los dos segundos como "bajo nivel." Hay algunos otros métodos y propiedades que puede usar, pero estos son los más valiosos.

Para que pueda ver las diferencias de rendimiento entre estas técnicas, la aplicación [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contiene una página denominada **mapa de bits de degradado** que crea un mapa de bits con píxeles que combinan sombras de color rojo y azul para crear un degradado. El programa crea ocho copias diferentes del mapa de bits, todos utilizan diferentes técnicas para establecer los píxeles del mapa de bits. Cada uno de estos mapas de bits de ocho se crea en un método independiente que también establece una breve descripción de la técnica y calcula el tiempo necesario para establecer todos los píxeles. Cada método recorre en bucle la lógica de la configuración de píxel 100 veces para obtener una estimación más precisa del rendimiento.

### <a name="the-setpixel-method"></a>El método SetPixel

Si solo necesita establecer u obtener varios píxeles individuales, los métodos [`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) y [`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) son ideales. Para cada uno de estos dos métodos, especifique la columna de tipo entero y la fila. Independientemente del formato de píxel, estos dos métodos permiten obtener o establecer el píxel como un valor `SKColor`:

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

El argumento `col` debe oscilar entre 0 y uno menos que la propiedad `Width` del mapa de bits y `row` oscila entre 0 y uno menos que la propiedad `Height`.

Este es el método en el **mapa de bits de degradado** que establece el contenido de un mapa de bits mediante el método `SetPixel`. El mapa de bits es 256 por 256 píxeles y los bucles `for` están codificados de forma rígida con el intervalo de valores:

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

El método de `SetPixel` se denomina 65.536 veces y, con independencia de la eficacia que pueda ser este método, por lo general no es una buena idea hacer esa cantidad de llamadas API si está disponible una alternativa. Afortunadamente, hay varias alternativas.

### <a name="the-pixels-property"></a>La propiedad de píxeles

`SKBitmap` define una propiedad [`Pixels`](xref:SkiaSharp.SKBitmap.Pixels) que devuelve una matriz de valores `SKColor` para el mapa de bits completo. También puede usar `Pixels` para establecer una matriz de valores de color para el mapa de bits:

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

Los píxeles se organizan en la matriz, empezando con la primera fila, de izquierda a derecha, a continuación, la segunda fila y así sucesivamente. El número total de colores de la matriz es igual al producto del alto y ancho del mapa de bits.

Aunque esta propiedad parece ser eficaz, tenga en cuenta que los píxeles se copian del mapa de bits en la matriz y, de nuevo, de la matriz al mapa de bits, y los píxeles se convierten de y a `SKColor` valores.

Este es el método de la clase `GradientBitmapPage` que establece el mapa de bits mediante la propiedad `Pixels`. El método asigna una matriz `SKColor` del tamaño requerido, pero podría haber usado la propiedad `Pixels` para crear esa matriz:

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

Observe que el índice de la matriz de `pixels` debe calcularse a partir de las variables `row` y `col`. La fila se multiplica por el número de píxeles en cada fila (256 en este caso) y, a continuación, se agrega la columna.

`SKBitmap` también define una propiedad `Bytes` similar, que devuelve una matriz de bytes para el mapa de bits completo, pero es más complicado para los mapas de bits de color completo.

### <a name="the-getpixels-pointer"></a>El puntero GetPixels

Potencialmente, la técnica más eficaz para tener acceso a los píxeles del mapa de bits es [`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels), no confundirse con el método `GetPixel` o la propiedad `Pixels`. Observará inmediatamente una diferencia con `GetPixels` en que devuelve algo que no es muy común en C# la programación:

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

El tipo de [`IntPtr`](xref:System.IntPtr) de .net representa un puntero. Se denomina `IntPtr` porque es la longitud de un entero en el procesador nativo del equipo en el que se ejecuta el programa, generalmente de 32 bits o 64 bits de longitud. La `IntPtr` que `GetPixels` devuelve es la dirección del bloque de memoria real que el objeto de mapa de bits utiliza para almacenar sus píxeles.

Puede convertir el `IntPtr` en un C# tipo de puntero mediante el método [`ToPointer`](xref:System.IntPtr.ToPointer) . La sintaxis de C# puntero es igual que C y C++:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

La variable `ptr` es del tipo _puntero de bytes_. Esta `ptr` variable permite tener acceso a los bytes de memoria individuales que se usan para almacenar los píxeles del mapa de bits. Use código similar al siguiente para leer un byte de esta memoria o escribir un byte en la memoria:

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

En este contexto, el asterisco es el C# _operador de direccionamiento indirecto_ y se usa para hacer referencia al contenido de la memoria a la que apunta `ptr`. Inicialmente, `ptr` apunta al primer byte del primer píxel de la primera fila del mapa de bits, pero puede realizar operaciones aritméticas en la variable `ptr` para moverla a otras ubicaciones del mapa de bits.

Una desventaja es que solo se puede usar esta variable `ptr` en un bloque de código marcado con la palabra clave `unsafe`. Además, el ensamblado debe estar marcado como Permitir bloques inseguros. Esto se realiza en las propiedades del proyecto.

Uso de punteros en C# es muy eficaz, pero también muy peligroso. Debe tener cuidado de que no tiene acceso a memoria más allá de lo que el puntero se supone que hacen referencia. Por eso uso del puntero está asociado con la palabra "unsafe".

Este es el método de la clase `GradientBitmapPage` que usa el método `GetPixels`. Observe el bloque `unsafe` que abarca todo el código mediante el puntero de bytes:

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

Cuando la variable de `ptr` se obtiene por primera vez desde el método `ToPointer`, apunta al primer byte del píxel situado más a la izquierda de la primera fila del mapa de bits. Los bucles de `for` para `row` y `col` se configuran de forma que `ptr` se pueda incrementar con el operador `++` después de establecer cada byte de cada píxel. En el caso de los demás bucles 99 a través de los píxeles, el `ptr` se debe volver a establecer al principio del mapa de bits.

Cada píxel es de cuatro bytes de memoria, por lo que cada byte debe establecerse por separado. En el código siguiente se supone que los bytes están en el orden rojo, verde, azul y alfa, que es coherente con el tipo de color `SKColorType.Rgba8888`. Puede que recuerde que esto es el tipo de color predeterminado para iOS y Android, pero no para la plataforma Universal de Windows. De forma predeterminada, UWP crea mapas de bits con el tipo de color `SKColorType.Bgra8888`. Por este motivo, se esperaba ver algunos resultados diferentes en esa plataforma.

Es posible convertir el valor devuelto de `ToPointer` en un puntero de `uint` en lugar de un puntero de `byte`. Esto permite que un píxel completo tener acceso en una sola instrucción. Al aplicar el operador `++` a ese puntero, se incrementa en cuatro bytes para que apunte al siguiente píxel:

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

El píxel se establece mediante el método `MakePixel`, que construye un píxel entero a partir de los componentes rojo, verde, azul y alfa. Tenga en cuenta que el formato de `SKColorType.Rgba8888` tiene un orden de bytes de píxel similar al siguiente:

RR GG BB AA

Pero es el entero correspondiente a esos bytes:

AABBGGRR

Se almacena el primero el byte menos significativo del entero de acuerdo con la arquitectura de little-endian. Este método `MakePixel` no funcionará correctamente para los mapas de bits con el tipo de color `Bgra8888`.

El método `MakePixel` se marca con la opción [`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions) para animar al compilador a evitar que se convierta en un método independiente, pero en su lugar para compilar el código donde se llama al método. Esto debe mejorar el rendimiento.

Curiosamente, la estructura de `SKColor` define una conversión explícita de `SKColor` a un entero sin signo, lo que significa que se puede crear un valor de `SKColor` y se puede usar una conversión a `uint` en lugar de `MakePixel`:

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

La única pregunta es esto: es el formato de entero del valor `SKColor` en el orden del tipo de color `SKColorType.Rgba8888`, o el tipo de color `SKColorType.Bgra8888`, o ¿es algo totalmente diferente? La respuesta a esa pregunta deberá mostrarse en breve.

### <a name="the-setpixels-method"></a>El método SetPixels

`SKBitmap` también define un método denominado [`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)), que se llama de la siguiente manera:

```csharp
bitmap.SetPixels(intPtr);
```

Recuerde que `GetPixels` obtiene un `IntPtr` que hace referencia al bloque de memoria utilizado por el mapa de bits para almacenar sus píxeles. La llamada a `SetPixels` _reemplaza_ ese bloque de memoria por el bloque de memoria al que hace referencia el `IntPtr` especificado como el argumento `SetPixels`. El mapa de bits, a continuación, libera el bloque de memoria que se estaba usando anteriormente. La próxima vez que se llame a `GetPixels`, obtiene el conjunto de bloques de memoria con `SetPixels`.

En primer lugar, parece como si `SetPixels` no ofrece más potencia y rendimiento que `GetPixels` y, al mismo tiempo, es menos práctico. Con `GetPixels` obtiene el bloque de memoria de mapa de bits y accede a él. Con `SetPixels` asigna y obtiene acceso a una memoria y, a continuación, la establece como el bloque de memoria de mapa de bits.

Pero el uso de `SetPixels` ofrece una ventaja sintáctica distinta: permite tener acceso a los bits de píxel de mapa de bits mediante una matriz. Este es el método de `GradientBitmapPage` que muestra esta técnica. En primer lugar, el método define una matriz de bytes multidimensional correspondientes a los bytes de los píxeles del mapa de bits. La primera dimensión es la fila, la segunda dimensión es la columna y la tercera dimensión corresponde a los cuatro componentes de cada píxel:

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

Después, una vez que la matriz se ha rellenado con píxeles, se usa un bloque `unsafe` y una instrucción `fixed` para obtener un puntero de byte que apunte a esta matriz. A continuación, ese puntero de bytes se puede convertir en una `IntPtr` para pasar a `SetPixels`.

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

El método `MakePixel` se usa de nuevo para combinar los componentes de color en un píxel de 32 bits.

Solo por integridad, este es el mismo código, pero con un valor `SKColor` convertido en un entero sin signo:

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

El constructor finaliza creando una `SKCanvasView` para mostrar los mapas de bits resultantes. El controlador de `PaintSurface` divide su superficie en ocho rectángulos y llama a `Display` para mostrar cada una de ellas:

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

Para permitir que el compilador optimice el código, esta página se ejecutó en modo de **versión** . Aquí es esa página se ejecuta en un simulador de iPhone 8 en un MacBook Pro, un teléfono Android de Nexus 5 y Surface Pro 3 que ejecutan Windows 10. Debido a las diferencias de hardware, evitar comparar los tiempos de rendimiento entre los dispositivos, pero en su lugar, examinar las horas relativas en cada dispositivo:

[![Mapa de bits de degradado](pixel-bits-images/GradientBitmap.png "Mapa de bits de degradado")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Esta es una tabla que consolida los tiempos de ejecución en milisegundos:

| API       | Tipo de datos | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3,17 |   10.77 | 3.49 |
| píxeles    |           | 0,32 |    1.23 | 0,07 |
| GetPixels | byte      | 0,09 |    0,24 | 0,10 |
|           | uint      | 0.06 |    0.26 | 0,05 |
|           | SKColor   | 0,29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6.78 | 0,11 |
|           | uint      | 0.14 |    0.69 | 0.06 |
|           | SKColor   | 0.35 |    1,93 | 0,10 |

Como cabría esperar, llamar a `SetPixel` 65.536 veces es la forma menos effeicient de establecer los píxeles de un mapa de bits. Rellenar una matriz de `SKColor` y establecer la propiedad `Pixels` es mucho mejor e incluso se compara de forma favorable con algunas de las técnicas `GetPixels` y `SetPixels`. Trabajar con valores de `uint` píxeles suele ser más rápido que establecer componentes de `byte` independientes y convertir el valor de `SKColor` en un entero sin signo agrega cierta sobrecarga al proceso.

También es interesante comparar los degradados distintos: las primeras filas de cada plataforma son los mismos y mostrar el degradado y como estaba previsto. Esto significa que el método `SetPixel` y la propiedad `Pixels` crean correctamente píxeles a partir de los colores, independientemente del formato de píxel subyacente.

Las dos filas siguientes de las capturas de pantallas de iOS y Android también son las mismas, lo que confirma que el poco `MakePixel` método está definido correctamente para el formato de `Rgba8888` píxeles predeterminado para estas plataformas.

La fila inferior de las capturas de pantallas de iOS y Android es hacia atrás, lo que indica que el entero sin signo obtenido al convertir un valor de `SKColor` tiene el formato:

AARRGGBB

Los bytes que se encuentran en el orden:

AA BB GG RR

Se trata de la ordenación de `Bgra8888` en lugar de la ordenación de `Rgba8888`. El formato de `Brga8888` es el valor predeterminado para la plataforma universal de Windows, que es el motivo por el que los degradados de la última fila de esa captura de pantalla son los mismos que en la primera fila. Pero las dos filas centrales son incorrectas porque el código que crea esos mapas de bits asumió una ordenación `Rgba8888`.

Si desea utilizar el mismo código para tener acceso a los bits de píxeles en cada plataforma, puede crear explícitamente un `SKBitmap` mediante el formato de `Rgba8888` o `Bgra8888`. Si desea convertir valores `SKColor` en píxeles de mapa de bits, utilice `Bgra8888`.

## <a name="random-access-of-pixels"></a>Acceso aleatorio de píxeles

Los métodos `FillBitmapBytePtr` y `FillBitmapUintPtr` de la página de **mapa de bits de degradado** se benefician de los bucles `for` diseñados para rellenar el mapa de bits secuencialmente, de la fila superior a la inferior y en cada fila de izquierda a derecha. El píxel se puede establecer con la misma instrucción que incrementa el puntero.

A veces es necesario tener acceso a los píxeles aleatoriamente en lugar de secuencialmente. Si utiliza el enfoque `GetPixels`, deberá calcular los punteros basados en la fila y la columna. Esto se muestra en la página **sinusoidal de arco iris** , que crea un mapa de bits que muestra un arco iris en forma de un ciclo de una curva de seno.

Los colores del arco iris son más fáciles de crear mediante el modelo de color HSL (matiz, saturación, luminosidad). El método `SKColor.FromHsl` crea un valor de `SKColor` usando valores de matiz que van de 0 a 360 (como los ángulos de un círculo pero van de rojo, a verde y azul, y de nuevo a rojo), y los valores de saturación y luminosidad que van de 0 a 100. Para los colores de un arco iris, la saturación debe establecerse en un máximo de 100 y la luminosidad a un punto medio de 50.

**Arcoseno de arco iris** crea esta imagen mediante un bucle a través de las filas del mapa de bits y, a continuación, se recorren los valores de matiz 360. Desde cada valor de matiz, calcula una columna de mapa de bits que también se basa en un valor de seno:

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

Observe que el constructor crea el mapa de bits basado en el formato de `SKColorType.Bgra8888`:

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Esto permite que el programa utilice la conversión de valores `SKColor` en `uint` píxeles sin preocuparse. Aunque no desempeña un rol en este programa en particular, siempre que use la conversión `SKColor` para establecer píxeles, también debe especificar `SKAlphaType.Unpremul` porque `SKColor` no multiplica sus componentes de color por el valor alfa.

A continuación, el constructor usa el método `GetPixels` para obtener un puntero al primer píxel del mapa de bits:

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Para cualquier fila y columna en particular, se debe agregar un valor de desplazamiento a `basePtr`. Este desplazamiento es la fila multiplicado por el ancho del mapa de bits, además de la columna:

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

El valor `SKColor` se almacena en memoria mediante este puntero:

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

En el controlador de `PaintSurface` del `SKCanvasView`, el mapa de bits se ajusta para rellenar el área de presentación:

[![Arcoseno de arco iris](pixel-bits-images/RainbowSine.png "Arcoseno de arco iris")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>De un mapa de bits a otro

Muchas tareas de procesamiento de imágenes implican modificar píxeles a medida que se transfieren desde un mapa de bits a otro. Esta técnica se muestra en la página **ajuste de color** . La página carga uno de los recursos de mapa de bits y, a continuación, le permite modificar la imagen con tres `Slider` vistas:

[![Ajuste de color](pixel-bits-images/ColorAdjustment.png "Ajuste de color")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Para cada color de píxel, el primer `Slider` agrega un valor de 0 a 360 al matiz, pero, a continuación, usa el operador de módulo para mantener el resultado entre 0 y 360, desplazando de forma eficaz los colores a lo largo del espectro (como se muestra en la captura de pantalla de UWP). La segunda `Slider` le permite seleccionar un factor de multiplicación entre 0,5 y 2 para aplicarlo a la saturación, y el tercer `Slider` hace lo mismo para la luminosidad, tal y como se muestra en la captura de pantalla de Android.

El programa mantiene dos mapas de bits, el mapa de bits de origen original denominado `srcBitmap` y el mapa de bits de destino ajustado denominado `dstBitmap`. Cada vez que se mueve un `Slider`, el programa calcula todos los píxeles nuevos en `dstBitmap`. Por supuesto, los usuarios experimentarán el movimiento de las vistas de `Slider` muy rápidamente, por lo que querrá el mejor rendimiento que pueda administrar. Esto implica el método de `GetPixels` para los mapas de bits de origen y de destino.

La página **ajuste de color** no controla el formato de color de los mapas de bits de origen y de destino. En su lugar, contiene una lógica ligeramente diferente para los formatos `SKColorType.Rgba8888` y `SKColorType.Bgra8888`. El origen y destino pueden ser diferentes formatos, y el programa seguirá funcionando.

Este es el programa, excepto el método fundamental `TransferPixels` que transfiere los píxeles del origen al destino. El constructor establece `dstBitmap` igual a `srcBitmap`. El controlador de `PaintSurface` muestra `dstBitmap`:

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

El controlador de `ValueChanged` para las vistas de `Slider` calcula los valores de ajuste y llama a `TransferPixels`.

Todo el método de `TransferPixels` se marca como `unsafe`. Comience por obtener punteros de bytes a los bits de píxeles de ambos mapas de bits y, a continuación, recorre en bucle todas las filas y columnas. En el mapa de bits de origen, el método obtiene cuatro bytes para cada píxel. Pueden estar en el orden `Rgba8888` o `Bgra8888`. La comprobación del tipo de color permite crear un valor de `SKColor`. Los componentes HSL se extraen, ajustan y se usan para volver a crear el valor de `SKColor`. Dependiendo de si el mapa de bits de destino es `Rgba8888` o `Bgra8888`, los bytes se almacenan en el bitmp de destino:

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

Es probable que el rendimiento de este método se puede mejorar aún más mediante la creación de métodos independientes para las diversas combinaciones de tipos de color de los mapas de bits de origen y destino y evitar la comprobación del tipo para cada píxel. Otra opción consiste en tener varios bucles `for` para la variable `col` basada en el tipo de color.

## <a name="posterization"></a>Posterización

Otro trabajo común que implica el acceso a bits de píxeles es la _posterización_. Se reduce el número si colores codifican en píxeles del mapa de bits para que el resultado es similar a un póster de trazados a mano mediante una paleta de colores limitado.

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

El código en el constructor tiene acceso a cada píxel, realiza una operación AND bit a bit con el valor 0xE0E0E0FF y, a continuación, almacena el resultado en el mapa de bits. Los valores 0xE0E0E0FF mantiene 3 bits superiores de cada componente de color y los 5 bits inferiores se establece en 0. En lugar de<sup>2 o</sup> 16.777.216 colores, el mapa de bits se reduce<sup></sup> a 2 o 512 colores:

[![Posterización](pixel-bits-images/Posterize.png "Posterizar")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
