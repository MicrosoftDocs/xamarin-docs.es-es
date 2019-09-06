---
title: Gráficos principales en Xamarin. iOS
description: En este artículo se describen los marcos de trabajo de iOS de gráficos principales. Muestra cómo usar gráficos principales para dibujar geometrías, imágenes y archivos PDF.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 72c233dc492fe1c93546121bc9103e666c2127ac
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286264"
---
# <a name="core-graphics-in-xamarinios"></a>Gráficos principales en Xamarin. iOS

_En este artículo se describen los marcos de trabajo de iOS de gráficos principales. Muestra cómo usar gráficos principales para dibujar geometrías, imágenes y archivos PDF._

iOS incluye el marco de [*gráficos básico*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) para proporcionar soporte de dibujo de bajo nivel. Estos marcos de trabajo son lo que habilita las funcionalidades gráficas enriquecidas dentro de UIKit.

Core Graphics es un marco de gráficos 2D de bajo nivel que permite dibujar gráficos independientes del dispositivo. Todos los dibujos 2D de UIKit usan internamente los gráficos principales.

Los gráficos principales admiten el dibujo en varios escenarios, entre los que se incluyen:

- [Dibujar en la pantalla mediante un `UIView` ](#Drawing_in_a_UIView_Subclass) .
- [Dibujar imágenes en memoria o en pantalla](#Drawing_Images_and_Text).
- Crear y dibujar en un PDF.
- Leer y dibujar un PDF existente.


## <a name="geometric-space"></a>Espacio geométrico

Independientemente del escenario, todo el dibujo realizado con gráficos principales se realiza en el espacio geométrico, lo que significa que funciona en puntos abstractos en lugar de píxeles. Se describe lo que se desea dibujar en términos de geometría y estado de dibujo, como colores, estilos de línea, etc. y controladores de gráficos principales que traducen todo en píxeles. Este estado se agrega a un contexto de gráficos, que puede considerar como el lienzo de una pintor.

Este enfoque tiene algunas ventajas:

- El código de dibujo se convierte en dinámico y, posteriormente, puede modificar los gráficos en tiempo de ejecución.
- Reducir la necesidad de imágenes estáticas en el lote de aplicaciones puede reducir el tamaño de la aplicación.
- Los gráficos son más resistentes a los cambios de resolución en todos los dispositivos.

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>Dibujar en una subclase UIView

Cada `UIView` tiene un `Draw` método llamado por el sistema cuando sea necesario dibujar. Para agregar código de dibujo a una vista, subclase `UIView` e invalidación: `Draw`

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Nunca se debe llamar directamente a Draw. Es llamado por el sistema durante el procesamiento de bucle de ejecución. La primera vez a través del bucle de ejecución después de agrega una vista a la jerarquía de vistas, su `Draw` se llama al método. Las llamadas subsiguientes a `Draw` se producen cuando la vista está marcada como que necesitan dibujar llamando `SetNeedsDisplay` o `SetNeedsDisplayInRect` en la vista.

### <a name="pattern-for-graphics-code"></a>Patrón para el código gráfico

El código de la `Draw` implementación debe describir lo que desea dibujar. El código de dibujo sigue un patrón en el que establece un estado de dibujo y llama a un método para solicitar que se dibuje. Este patrón se puede generalizar de la siguiente manera:

1. Obtiene un contexto de gráficos.

2. Configure los atributos de dibujo.

3. Cree alguna geometría a partir de los primitivos de dibujo.

4. Llame a un método Draw o Stroke.

### <a name="basic-drawing-example"></a>Ejemplo de dibujo básico

Por ejemplo, considere el siguiente fragmento de código:

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {

    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100),
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

Vamos a interrumpir este código:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```

Con esta línea, primero obtiene el contexto de gráficos actual que se va a usar para dibujar. Puede pensar en un contexto de gráficos como el lienzo en el que se produce el dibujo, que contiene todo el estado del dibujo, como los colores del trazo y del relleno, así como la geometría que se va a dibujar.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
```

Después de obtener un contexto de gráficos, el código configura algunos atributos que se van a usar al dibujar, mostrados anteriormente. En este caso, se establece el ancho de línea, el trazo y los colores de relleno. Cualquier dibujo posterior usará estos atributos porque se mantienen en el estado del contexto de gráficos.

Para crear una geometría `CGPath`, el código utiliza, lo que permite describir una ruta de acceso de gráficos a partir de líneas y curvas. En este caso, la ruta de acceso agrega líneas que conectan una matriz de puntos para componer un triángulo. Tal y como se muestra a continuación, los gráficos principales usan un sistema de coordenadas para el dibujo de la vista, donde el origen está en la parte superior izquierda, con x-Direct positivo a la derecha y la dirección positivo y hacia abajo:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100),
new CGPoint (220, 200)});

path.CloseSubpath ();
```

Una vez creada la ruta de acceso, se agrega al contexto de gráficos para que `AddPath` llamar `DrawPath` a y, respectivamente, puede dibujarla.

La vista resultante se muestra a continuación:

 ![](core-graphics-images/00-bluetriangle.png "Triángulo de salida de ejemplo")

## <a name="creating-gradient-fills"></a>Crear relleno de degradado

También están disponibles formas más enriquecidas de dibujo. Por ejemplo, los gráficos principales permiten crear relleno de degradado y aplicar trazados de recorte. Para dibujar un relleno de degradado dentro de la ruta de acceso del ejemplo anterior, primero se debe establecer la ruta de acceso como el trazado de recorte:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

Al establecer la ruta de acceso actual como el trazado de recorte, se restringe todo el dibujo subsiguiente dentro de la geometría del trazado, como el código siguiente, que dibuja un degradado lineal:

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient,
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top),
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom),
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

Estos cambios producen un relleno de degradado como se muestra a continuación:

 ![](core-graphics-images/01-gradient-fill.png "El ejemplo con un relleno de degradado")

## <a name="modifying-line-patterns"></a>Modificar patrones de línea

Los atributos de dibujo de las líneas también se pueden modificar con gráficos principales. Esto incluye cambiar el ancho de línea y el color del trazo, así como el propio patrón de línea, tal como se muestra en el código siguiente:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

Al agregar este código antes de cualquier operación de dibujo, se produce una longitud de trazos de guiones de 10 unidades, con 4 unidades de espaciado entre guiones, como se muestra a continuación:

 ![](core-graphics-images/02-dashed-stroke.png "Agregar este código antes de que las operaciones de dibujo resulten en trazos discontinuos")

Tenga en cuenta que al usar el Unified API en Xamarin. iOS, el tipo de matriz debe `nfloat`ser y también debe convertirse explícitamente a Math. PI.

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>Dibujar imágenes y texto

Además de dibujar trazados en el contexto de gráficos de una vista, los gráficos principales también admiten el dibujo de imágenes y texto. Para dibujar una imagen, basta con crear `CGImage` un y pasarlo a `DrawImage` una llamada:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

Sin embargo, esto produce una imagen dibujada al revés, como se muestra a continuación:

 ![](core-graphics-images/03-upside-down-monkey.png "Una imagen dibujada al revés")

El motivo es que el origen de los gráficos principales del dibujo de la imagen está en la parte inferior izquierda, mientras que la vista tiene su origen en la parte superior izquierda. Por lo tanto, para mostrar la imagen correctamente, es necesario modificar el origen, lo que se puede lograr modificando la *matriz de transformación actual* *(CTM)* . CTM define dónde se encuentran los puntos en vivo, también conocido como *espacio de usuario*. Si se invierte el valor de CTM en la dirección y y se desplaza por el alto de los límites en la dirección y negativa, puede voltear la imagen.

El contexto de gráficos tiene métodos auxiliares para transformar el CTM. En este caso, `ScaleCTM` "voltea" el dibujo y `TranslateCTM` lo desplaza a la parte superior izquierda, como se muestra a continuación:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}
```

La imagen resultante se muestra a continuación verticalmente:

 ![](core-graphics-images/04-upright-monkey.png "La imagen de ejemplo mostrada verticalmente")

> [!IMPORTANT]
> Los cambios en el contexto de gráficos se aplican a todas las operaciones de dibujo posteriores. Por lo tanto, cuando se transforma el CTM, afectará a cualquier dibujo adicional. Por ejemplo, si dibujó el triángulo después de la transformación CTM, aparecería al revés.

### <a name="adding-text-to-the-image"></a>Agregar texto a la imagen

Al igual que con las rutas de acceso e imágenes, el dibujo de texto con gráficos principales implica el mismo patrón básico de configuración de algunos Estados gráficos y la llamada a un método para dibujar. En el caso de texto, el método para mostrar el texto `ShowText`es. Cuando se agrega al ejemplo de dibujo de imagen, el código siguiente dibuja texto mediante gráficos principales:

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);

    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

Como puede ver, establecer el estado de los gráficos para el dibujo de texto es similar al dibujo de geometría. En el caso del dibujo de texto, el modo de dibujo de texto y la fuente también se aplican. En este caso, también se aplica una sombra, aunque la aplicación de sombras funciona igual para dibujar trazados.

El texto resultante se muestra con la imagen, como se muestra a continuación:

 ![](core-graphics-images/05-text-on-image.png "El texto resultante se muestra con la imagen.")

## <a name="memory-backed-images"></a>Imágenes con copia de seguridad de memoria

Además de dibujar en el contexto de los gráficos de una vista, los gráficos principales admiten el dibujo de imágenes respaldadas por memoria, también conocida como dibujo fuera de la pantalla. Esto requiere:

- Crear un contexto de gráficos respaldado por un mapa de bits en memoria
- Establecer el estado del dibujo y emitir comandos de dibujo
- Obtención de la imagen desde el contexto
- Quitar el contexto


A diferencia del `Draw` método, donde la vista proporciona el contexto, en este caso se crea el contexto de una de estas dos maneras:

1. Llamando a `UIGraphics.BeginImageContext` (o `BeginImageContextWithOptions`)

2. Mediante la creación de un nuevo`CGBitmapContextInstance`

 `CGBitmapContextInstance`resulta útil cuando se trabaja directamente con los bits de imagen, como en los casos en los que se usa un algoritmo de manipulación de imágenes personalizado. En todos los demás casos, debe usar `BeginImageContext` o `BeginImageContextWithOptions`.

Una vez que tiene un contexto de imagen, agregar código de dibujo es igual que en `UIView` una subclase. Por ejemplo, el ejemplo de código usado anteriormente para dibujar un triángulo se puede usar para dibujar en una imagen en la memoria en lugar de `UIView`en una, como se muestra a continuación:

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100),
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }

    return triangleImage;
}
```

Un uso común del dibujo en un mapa de bits respaldado por la memoria es capturar una imagen `UIView`de cualquier. Por ejemplo, el código siguiente representa la capa de una vista en un contexto de mapa de bits `UIImage` y crea un a partir de ella:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Dibujar pdf

Además de las imágenes, los gráficos principales admiten dibujos en PDF. Al igual que las imágenes, puede representar un PDF en memoria, así como leer un PDF para representarlo en un `UIView`.

### <a name="pdf-in-a-uiview"></a>PDF en un UIView

Los gráficos principales también admiten la lectura de un PDF desde un archivo y su representación en una `CGPDFDocument` vista mediante la clase. La `CGPDFDocument` clase representa un PDF en el código y se puede usar para leer y dibujar páginas.

Por ejemplo, el siguiente código en una `UIView` subclase Lee un archivo PDF de un archivo en `CGPDFDocument`un:

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }

     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

Después `Draw` , el método puede `CGPDFDocument` usar `CGPDFPage` para leer una página y representarla `DrawPDFPage`llamando a, como se muestra a continuación:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);

        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {

        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);

        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);

        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>PDF con copia de seguridad en memoria

Para un PDF en memoria, debe crear un contexto `BeginPDFContext`de PDF llamando a. Dibujar en PDF es granular en páginas. Cada página se inicia `BeginPDFPage` llamando a y se completa llamando a `EndPDFContent`, con el código gráfico entre. Además, al igual que con el dibujo de imagen, el dibujo de PDF respaldado por memoria utiliza un origen en la parte inferior izquierda, que se puede tener en cuenta modificando el CTM de la misma manera que con las imágenes.

En el código siguiente se muestra cómo dibujar texto en un archivo PDF:

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();

using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }

//complete a PDF page
UIGraphics.EndPDFContent ();
```

El texto resultante se dibuja en el PDF, que después se incluye en un `NSData` que se puede guardar, cargar, enviar por correo electrónico, etc.


## <a name="summary"></a>Resumen

En este artículo, analizamos las capacidades de gráficos proporcionadas a través del marco de trabajo de *gráficos básico* . Vimos cómo usar gráficos principales para dibujar geometrías, imágenes y documentos PDF en el contexto de un `UIView,` y de contextos de gráficos con copia de seguridad en memoria.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de gráficos principales](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [Tutorial de animación y gráficos](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animación básica](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Recetas básicas de animación](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
