---
Título: "la transformación de escala" Descripción: "el artículo de Thhis explora la transformación de escala SkiaSharp para escalar objetos a varios tamaños y demuestra esto con código de ejemplo".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B Author: davidbritch ms. Author: dabritch ms. Date: 03/23/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="the-scale-transform"></a>Transformación de escala

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Detección de la transformación de escala SkiaSharp para escalar objetos a distintos tamaños_

Como ha visto en [**el artículo traducir transformación**](translate.md) , la transformación traducir puede trasladar un objeto gráfico de una ubicación a otra. En cambio, la transformación de escala cambia el tamaño del objeto gráfico:

![](scale-images/scaleexample.png "A tall word scaled in size")

La transformación de escala también hace que las coordenadas de gráficos se muevan a medida que se hacen más grandes.

Antes vio dos fórmulas de transformación que describen los efectos de los factores de traducción de `dx` y `dy` :

x ' = x + DX

y ' = y + DY

Los factores de escala de `sx` y `sy` son multiplicativos en lugar de aditivos:

x ' = SX · x1

y ' = SY · sí

Los valores predeterminados de los factores de traducción son 0; los valores predeterminados de los factores de escala son 1.

La `SKCanvas` clase define cuatro `Scale` métodos. El primer [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) método es para los casos en los que desea el mismo factor de escala horizontal y vertical:

```csharp
public void Scale (Single s)
```

Esto se conoce como *isotropic* &mdash; escalado de escalado anisotrópico que es el mismo en ambas direcciones. El escalado anisotrópico conserva la relación de aspecto del objeto.

El segundo [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) método permite especificar valores diferentes para el escalado horizontal y vertical:

```csharp
public void Scale (Single sx, Single sy)
```

Esto da como resultado el escalado *anisotrópico* .
El tercer [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) método combina los dos factores de escala en un solo `SKPoint` valor:

```csharp
public void Scale (SKPoint size)
```

El cuarto `Scale` método se describe en breve.

En la página **escala básico** se muestra el `Scale` método. El archivo [**BasicScalePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) contiene dos `Slider` elementos que permiten seleccionar los factores de escala horizontal y vertical entre 0 y 10. El archivo de código subyacente [**BasicScalePage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) usa esos valores para llamar `Scale` antes de mostrar un rectángulo redondeado con una línea discontinua y ajustarlo a un texto en la esquina superior izquierda del lienzo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Puede que se pregunte: ¿Cómo afectan los factores de escala al valor devuelto desde el `MeasureText` método de `SKPaint` ? La respuesta es: no en absoluto. `Scale`es un método de `SKCanvas` . No afecta a nada que se haga con un `SKPaint` objeto hasta que se use ese objeto para representar algo en el lienzo.

Como puede ver, todo lo dibujado después de la `Scale` llamada aumenta proporcionalmente:

[![](scale-images/basicscale-small.png "Triple screenshot of the Basic Scale page")](scale-images/basicscale-large.png#lightbox "Triple screenshot of the Basic Scale page")

El texto, el ancho de la línea discontinua, la longitud de los guiones de esa línea, el redondeo de las esquinas y el margen de 10 píxeles entre los bordes izquierdo y superior del lienzo y el rectángulo redondeado están sujetos a los mismos factores de escala.

> [!IMPORTANT]
> El Plataforma universal de Windows no representa correctamente el texto con escalado anisotrópico.

El escalado anisotrópico hace que el ancho del trazo sea diferente para las líneas alineadas con los ejes horizontal y vertical. (Esto también es evidente en la primera imagen de esta página). Si no desea que el ancho del trazo se vea afectado por los factores de escala, establézcalo en 0 y siempre tendrá un ancho de píxel, independientemente de la `Scale` configuración.

El escalado es relativo a la esquina superior izquierda del lienzo. Esto puede ser exactamente lo que desea, pero puede que no sea así. Supongamos que desea colocar el texto y el rectángulo en otra parte del lienzo y desea escalarlo en relación con su centro. En ese caso, puede usar la cuarta versión del [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) método, que incluye dos parámetros adicionales para especificar el centro del escalado:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

Los `px` `py` parámetros y definen un punto que a veces se denomina *centro de escalado* , pero en la documentación de SkiaSharp se conoce como *punto de pivote*. Este es un punto relativo a la esquina superior izquierda del lienzo que no se ve afectado por el escalado. Todo el ajuste de escala se produce con respecto a ese centro.

La página [**escala centrada**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) muestra cómo funciona esto. El `PaintSurface` controlador es similar al programa de **escala básico** , salvo que el `margin` valor se calcula para centrar el texto horizontalmente, lo que implica que el programa funciona mejor en el modo vertical:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

La esquina superior izquierda del rectángulo redondeado está posicionada `margin` en píxeles desde la izquierda del lienzo y `margin` los píxeles desde la parte superior. Los dos últimos argumentos del `Scale` método se establecen en esos valores más el ancho y el alto del texto, que también es el ancho y el alto del rectángulo redondeado. Esto significa que todo el ajuste de escala es relativo al centro de ese rectángulo:

[![](scale-images/centeredscale-small.png "Triple screenshot of the Centered Scale page")](scale-images/centeredscale-large.png#lightbox "Triple screenshot of the Centered Scale page")

Los `Slider` elementos de este programa tienen un intervalo de &ndash; 10 a 10. Como puede ver, los valores negativos del escalado vertical (por ejemplo, en la pantalla de Android del centro) provocan que los objetos se volteen alrededor del eje horizontal que pasa a través del centro del escalado. Los valores negativos del escalado horizontal (por ejemplo, en la pantalla de UWP a la derecha) hacen que los objetos se volteen alrededor del eje vertical que pasa a través del centro del escalado.

La versión del [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) método con puntos dinámicos es un acceso directo para una serie de `Translate` tres `Scale` llamadas y. Es posible que desee ver cómo funciona reemplazando el `Scale` método en la página **escala centrada** con lo siguiente:

```csharp
canvas.Translate(-px, -py);
```

Estos son los negativos de las coordenadas del punto de pivote.

Ejecute el programa otra vez. Verá que el rectángulo y el texto se desplazan para que el centro se encuentre en la esquina superior izquierda del lienzo. Puede verlo apenas. Los controles deslizantes no funcionan por supuesto porque ahora el programa no se escala en absoluto.

Ahora agregue la `Scale` llamada básica (sin un centro de escalado) *antes* de que se `Translate` llame a:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Si está familiarizado con este ejercicio en otros sistemas de programación de gráficos, puede pensar que es incorrecto, pero no es así. Skia controla las llamadas de transformación sucesivas de un modo ligeramente diferente del que le puede resultar familiar.

Con las sucesivas `Scale` `Translate` llamadas y, el centro del rectángulo redondeado todavía está en la esquina superior izquierda, pero ahora se puede escalar con relación a la esquina superior izquierda del lienzo, que también es el centro del rectángulo redondeado.

Ahora, antes de `Scale` llamar a, agregue otra `Translate` llamada con los valores de centro:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

De esta forma, se devuelve el resultado de la escala a la posición original. Estas tres llamadas son equivalentes a:

```csharp
canvas.Scale(sx, sy, px, py);
```

Las transformaciones individuales están compuestas para que la fórmula de transformación total sea:

 x ' = SX · (x – PX) + PX

 y ' = SY · (y – py) + py

Tenga en cuenta que los valores predeterminados de `sx` y `sy` son 1. Es fácil convencerse de que estas fórmulas no transforman el punto de pivote (PX, py). Permanece en la misma ubicación en relación con el lienzo.

Cuando combina `Translate` y `Scale` llama a, el orden es importante. Si `Translate` se encuentra después de `Scale` , los factores de conversión se escalan eficazmente según los factores de escala. Si `Translate` viene antes de `Scale` , los factores de traslación no se escalan. Este proceso se vuelve ligeramente más claro (aunque más matemático) cuando se introduce el asunto de las matrices de transformación.

La `SKPath` clase define una propiedad de solo lectura [`Bounds`](xref:SkiaSharp.SKPath.Bounds) que devuelve un que `SKRect` define el alcance de las coordenadas de la ruta de acceso. Por ejemplo, cuando la `Bounds` propiedad se obtiene de la ruta de acceso hendecagram creada anteriormente, las `Left` `Top` propiedades y del rectángulo son aproximadamente – 100, `Right` las `Bottom` propiedades y son aproximadamente 100 y las `Width` `Height` propiedades y son aproximadamente 200. (La mayoría de los valores reales son un poco menos porque los puntos de las estrellas se definen mediante un círculo con un radio de 100, pero solo el punto superior es paralelo con los ejes horizontal o vertical).

La disponibilidad de esta información implica que debe ser posible derivar la escala y traducir los factores adecuados para escalar una ruta de acceso al tamaño del lienzo. La página de [**escalado de anisotrópico**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) muestra esto con una estrella de 11 puntas. Una escala *anisotrópico* significa que no es igual en las direcciones horizontal y vertical, lo que significa que la estrella no conservará su relación de aspecto original. Este es el código relevante en el `PaintSurface` controlador:

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

El `pathBounds` rectángulo se obtiene cerca de la parte superior de este código y, después, se usa más adelante con el ancho y el alto del lienzo en la `Scale` llamada. Esa llamada por sí misma escalará las coordenadas de la ruta de acceso cuando se representen mediante la `DrawPath` llamada, pero la estrella se centrará en la esquina superior derecha del lienzo. Debe desplazarse hacia abajo y hacia la izquierda. Este es el trabajo de la `Translate` llamada. Las dos propiedades de `pathBounds` son aproximadamente – 100, por lo que los factores de traducción son aproximadamente 100. Dado que la `Translate` llamada es después de la `Scale` llamada, esos valores se escalan eficazmente mediante los factores de escala, por lo que mueven el centro de la estrella al centro del lienzo:

[![](scale-images/anisotropicscaling-small.png "Triple screenshot of the Anisotropic Scaling page")](scale-images/anisotropicscaling-large.png#lightbox "Triple screenshot of the Anisotropic Scaling page")

Otra manera de pensar `Scale` en las llamadas a y `Translate` es determinar el efecto en la secuencia inversa: la `Translate` llamada desplaza la ruta de acceso para que se vuelva totalmente visible pero esté orientada en la esquina superior izquierda del lienzo. `Scale`Después, el método hace que esa estrella sea más grande en relación con la esquina superior izquierda.

En realidad, parece que la estrella es un poco más grande que el lienzo. El problema es el ancho del trazo. La `Bounds` propiedad de `SKPath` indica las dimensiones de las coordenadas codificadas en la ruta de acceso y eso es lo que el programa usa para escalarla. Cuando la ruta de acceso se representa con un ancho de trazo determinado, la ruta de acceso representada es mayor que el lienzo.

Para corregir este problema, debe compensarlo. Un enfoque sencillo en este programa es agregar la siguiente instrucción justo antes de la `Scale` llamada:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Esto aumenta el `pathBounds` rectángulo en 1,5 unidades en los cuatro lados. Esta es una solución razonable solo cuando se redondea la combinación de trazos. Una unión angular puede ser más larga y es difícil de calcular.

También puede usar una técnica similar con el texto, como se muestra en la página de **texto anisotrópico** . Esta es la parte relevante del `PaintSurface` controlador de la [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) clase:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

Es una lógica similar y el texto se expande hasta el tamaño de la página en función del rectángulo de límite de texto devuelto de `MeasureText` (que es un poco mayor que el texto real):

[![](scale-images/anisotropictext-small.png "Triple screenshot of the Anisotropic Test page")](scale-images/anisotropictext-large.png#lightbox "Triple screenshot of the Anisotropic Test page")

Si necesita conservar la relación de aspecto de los objetos gráficos, querrá usar el escalado anisotrópico. La página de **escalado de anisotrópico** muestra esto para la estrella de 11 puntas. Conceptualmente, los pasos para mostrar un objeto gráfico en el centro de la página con el escalado anisotrópico son:

- Traduzca el centro del objeto gráfico a la esquina superior izquierda.
- Escalar el objeto en función del mínimo de dimensiones de página horizontal y vertical dividida por las dimensiones de objeto gráfico.
- Traduce el centro del objeto escalado al centro de la página.

[`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs)Realiza estos pasos en orden inverso antes de mostrar la estrella:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

El código también muestra la estrella 10 veces más, cada vez que reduce el factor de escala en un 10% y cambia progresivamente el color de rojo a azul:

[![](scale-images/isotropicscaling-small.png "Triple screenshot of the Isotropic Scaling page")](scale-images/isotropicscaling-large.png#lightbox "Triple screenshot of the Isotropic Scaling page")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
