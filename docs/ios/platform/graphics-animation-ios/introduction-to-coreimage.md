---
title: Imagen básica en Xamarin. iOS
description: La imagen principal es un nuevo marco de trabajo incluido en iOS 5 para proporcionar funciones de procesamiento de imágenes y mejora de vídeo en directo. En este artículo se presentan estas características con los ejemplos de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 7cb94956266a3094cc62ccc66c3d44e97884f04d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435295"
---
# <a name="core-image-in-xamarinios"></a>Imagen básica en Xamarin. iOS

_La imagen principal es un nuevo marco de trabajo incluido en iOS 5 para proporcionar funciones de procesamiento de imágenes y mejora de vídeo en directo. En este artículo se presentan estas características con los ejemplos de Xamarin. iOS._

La imagen principal es un nuevo marco de trabajo incluido en iOS 5 que proporciona una serie de filtros y efectos integrados que se aplican a las imágenes y los vídeos, incluida la detección de caras.

Este documento contiene ejemplos sencillos de:

- Detección de caras.
- Aplicar filtros a una imagen
- Enumerar los filtros disponibles.

Estos ejemplos le ayudarán a empezar a incorporar características de imagen principales en sus aplicaciones de Xamarin. iOS.

## <a name="requirements"></a>Requisitos

Debe usar la versión más reciente de Xcode.

## <a name="face-detection"></a>Detección facial

La característica de detección de caras de imagen principal hace exactamente lo que dice: intenta identificar caras en una foto y devuelve las coordenadas de las caras que reconoce. Esta información se puede usar para contar el número de personas de una imagen, dibujar indicadores en la imagen (por ejemplo, para "etiquetar" personas en una fotografía) o cualquier otro elemento que pueda considerar.

Este código de CoreImage\SampleCode.cs muestra cómo crear y usar la detección de caras en una imagen incrustada:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

La matriz de características se rellenará con `CIFaceFeature` objetos (si se detectaron rostros). Hay un `CIFaceFeature` para cada una de las caras. `CIFaceFeature` tiene las siguientes propiedades:

- HasMouthPosition: indica si se ha detectado una boca para este problema.
- HasLeftEyePosition: indica si se ha detectado el ojo izquierdo para esta esfera.
- HasRightEyePosition: indica si se ha detectado el ojo correcto para esta esfera. 
- MouthPosition: las coordenadas de la boca de este tipo.
- LeftEyePosition: las coordenadas del ojo izquierdo para esta esfera.
- RightEyePosition: las coordenadas del ojo adecuado para esta esfera.

Las coordenadas de todas estas propiedades tienen su origen en la parte inferior izquierda, a diferencia de UIKit, que usa la parte superior izquierda como el origen. Al usar las coordenadas en, `CIFaceFeature` Asegúrese de ' voltearlas '. Esta vista de imagen personalizada muy básica de CoreImage\CoreImageViewController.cs muestra cómo dibujar los triángulos de ' indicador de caras ' en la imagen (tenga en cuenta el `FlipForBottomOrigin` método):

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

Después, en el archivo SampleCode.cs, la imagen y las características se asignan antes de que se vuelva a dibujar la imagen:

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

En la captura de pantalla se muestra el resultado del ejemplo: las ubicaciones de las características faciales detectadas se muestran en un UITextView y se dibujan en la imagen de origen con CoreGraphics.

Debido a la forma en que funciona el reconocimiento facial, detectará algunas cosas además de caras humanas (como estas monkeyss de juguete).

## <a name="filters"></a>Filtros

Hay más de 50 filtros integrados diferentes y el marco es extensible para que se puedan implementar nuevos filtros.

## <a name="using-filters"></a>Usar filtros

Aplicar un filtro a una imagen tiene cuatro pasos distintos: cargar la imagen, crear el filtro, aplicar el filtro y guardar (o mostrar) el resultado.

En primer lugar, cargue una imagen en un `CIImage` objeto.

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

En segundo lugar, cree la clase de filtro y establezca sus propiedades.

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

En tercer lugar, tenga acceso a la `OutputImage` propiedad y llame al `CreateCGImage` método para representar el resultado final.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Por último, asigne la imagen a una vista para ver el resultado. En una aplicación real, la imagen resultante podría guardarse en el sistema de archivos, el álbum de fotos, un tweet o un correo electrónico.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Estas capturas de pantallas muestran el resultado de los `CISepia` `CIHueAdjust` filtros y que se muestran en el CoreImage.zip código de ejemplo.

Consulte la [receta ajustar el contrato y el brillo de la imagen](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) para obtener un ejemplo del `CIColorControls` filtro.

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>Enumerar los filtros y sus propiedades

Este código de CoreImage\SampleCode.cs genera una lista completa de los filtros integrados y sus parámetros.

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

La [referencia de la clase CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) describe los filtros integrados 50 y sus propiedades. Con el código anterior, puede consultar las clases de filtro, incluidos los valores predeterminados de los parámetros y los valores máximos y mínimos permitidos (que se pueden usar para validar las entradas antes de aplicar un filtro).

La salida de las categorías de lista tiene el siguiente aspecto en el simulador: puede desplazarse por la lista para ver todos los filtros y sus parámetros.

 [![La salida de las categorías de lista tiene el siguiente aspecto en el simulador](introduction-to-coreimage-images/coreimage05.png)](introduction-to-coreimage-images/coreimage05.png#lightbox)

Cada filtro enumerado se ha expuesto como una clase en Xamarin. iOS, por lo que también puede explorar la API de Xamarin. iOS. CoreImage en el explorador de ensamblados o usar autocompletar en Visual Studio para Mac o Visual Studio. 

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo usar algunas de las nuevas características del marco de imagen de iOS 5 Core, como la detección de caras y la aplicación de filtros a una imagen. Hay docenas de distintos filtros de imagen disponibles en el marco de trabajo para su uso.

## <a name="related-links"></a>Vínculos relacionados

- [Imagen principal (ejemplo)](/samples/xamarin/ios-samples/coreimage)
- [Ajustar el contrato y el brillo de una receta de imagen](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [Uso de filtros de imagen principales](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Referencia de la clase CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)