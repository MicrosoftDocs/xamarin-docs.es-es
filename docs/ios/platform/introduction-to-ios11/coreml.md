---
title: Introducción a CoreML en Xamarin. iOS
description: En este documento se describe CoreML, que habilita machine learning en iOS. En este documento se explica cómo empezar a usar CoreML y cómo usarlo con Vision Framework.
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 572ba31a1f19ab099765cc92bb1b389ba1115d1b
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84564698"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Introducción a CoreML en Xamarin. iOS

CoreML ofrece aprendizaje automático a iOS: las aplicaciones pueden aprovechar los modelos de aprendizaje automático entrenados para realizar todo tipo de tareas, desde solucionar problemas hasta el reconocimiento de imágenes.

Esta introducción abarca lo siguiente:

- [Introducción con CoreML](#coreml)
- [Uso de CoreML con Vision Framework](#coremlvision)

<a name="coreml"></a>

## <a name="getting-started-with-coreml"></a>Introducción con CoreML

En estos pasos se describe cómo agregar CoreML a un proyecto de iOS. Consulte el ejemplo del [precio del hábitat de Mars](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/) para ver un ejemplo práctico.

![Captura de pantalla del ejemplo de predicción de precio de hábitat](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1. agregar el modelo CoreML al proyecto

Agregue un modelo de CoreML (un archivo con la extensión **. mlmodel** ) al directorio **Resources** del proyecto. 

En las propiedades del archivo del modelo, su **acción de compilación** se establece en **CoreMLModel**. Esto significa que se compilará en un archivo **. mlmodelc** cuando se compile la aplicación.

### <a name="2-load-the-model"></a>2. cargar el modelo

Cargue el modelo mediante el `MLModel.Create` método estático:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. establecer los parámetros

Los parámetros del modelo se pasan y salen mediante una clase de contenedor que implementa `IMLFeatureProvider` .

Las clases de proveedor de características se comportan como un diccionario de cadenas y `MLFeatureValue` s, donde cada valor de característica puede ser una cadena o un número simple, una matriz o datos o un búfer de píxeles que contiene una imagen.

A continuación se muestra el código para un proveedor de características de un solo valor:

```csharp
public class MyInput : NSObject, IMLFeatureProvider
{
  public double MyParam { get; set; }
  public NSSet<NSString> FeatureNames => new NSSet<NSString>(new NSString("myParam"));
  public MLFeatureValue GetFeatureValue(string featureName)
  {
    if (featureName == "myParam")
      return MLFeatureValue.FromDouble(MyParam);
    return MLFeatureValue.FromDouble(0); // default value
  }
```

Mediante el uso de clases como esta, se pueden proporcionar parámetros de entrada de una manera entendida por CoreML. Los nombres de las características (como `myParam` en el ejemplo de código) deben coincidir con lo que espera el modelo.

### <a name="4-run-the-model"></a>4. ejecutar el modelo

El uso del modelo requiere que se cree una instancia del proveedor de características y que se establezcan los parámetros y, a continuación, que `GetPrediction` se llame al método:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. extraer los resultados

El resultado de la predicción `outFeatures` es también una instancia de `IMLFeatureProvider` ; se puede tener acceso a los valores de salida mediante `GetFeatureValue` con el nombre de cada parámetro de salida (como `theResult` ), como en este ejemplo:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision"></a>

## <a name="using-coreml-with-the-vision-framework"></a>Uso de CoreML con Vision Framework

CoreML también se puede usar junto con Vision Framework para realizar operaciones en una imagen, como el reconocimiento de formas, la identificación de objetos y otras tareas.

En los pasos siguientes se describe cómo se usan CoreML y Vision conjuntamente en el [ejemplo CoreMLVision](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision). El ejemplo combina el [reconocimiento de los rectángulos](~/ios/platform/introduction-to-ios11/vision.md#rectangles) de Vision Framework con el modelo _MNINSTClassifier_ CoreML para identificar un dígito manuscrito en una fotografía.

![Reconocimiento de imágenes del número 3](coreml-images/vision3.png) ![Reconocimiento de imagen del número 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. crear un modelo de Vision CoreML

El modelo CoreML _MNISTClassifier_ se carga y, a continuación, se ajusta en un `VNCoreMLModel` que hace que el modelo esté disponible para las tareas de visión. Este código también crea dos solicitudes de visión: primero para buscar rectángulos en una imagen y, a continuación, para procesar un rectángulo con el modelo CoreML:

```csharp
// Load the ML model
var bundle = NSBundle.MainBundle;
var assetPath = bundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
NSError mlErr, vnErr;
var mlModel = MLModel.Create(assetPath, out mlErr);
var model = VNCoreMLModel.FromMLModel(mlModel, out vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(model, HandleClassification);
```

La clase aún debe implementar los `HandleRectangles` métodos y `HandleClassification` para las solicitudes de visión, que se muestran en los pasos 3 y 4 siguientes.

### <a name="2-start-the-vision-processing"></a>2. iniciar el procesamiento de la visión

El código siguiente inicia el procesamiento de la solicitud. En el ejemplo **CoreMLVision** , este código se ejecuta después de que el usuario haya seleccionado una imagen:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Este controlador pasa `ciImage` a Vision Framework `VNDetectRectanglesRequest` que se creó en el paso 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. controlar los resultados del procesamiento de la visión

Una vez completada la detección de rectángulos, se ejecuta el `HandleRectangles` método, que recorta la imagen para extraer el primer rectángulo, convierte la imagen de rectángulo en una de gris y la pasa al modelo CoreML para la clasificación.

El `request` parámetro que se pasa a este método contiene los detalles de la solicitud de visión y, mediante el `GetResults<VNRectangleObservation>()` método, devuelve una lista de rectángulos encontrados en la imagen. El primer rectángulo `observations[0]` se extrae y se pasa al modelo CoreML:

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] {ClassificationRequest}, out NSError err);
  });
}
```

`ClassificationRequest`Se inicializó en el paso 1 para usar el `HandleClassification` método definido en el paso siguiente.

### <a name="4-handle-the-coreml"></a>4. controlar el CoreML

El `request` parámetro que se pasa a este método contiene los detalles de la solicitud CoreML y, mediante el `GetResults<VNClassificationObservation>()` método, devuelve una lista de los posibles resultados ordenados por confianza (la mayor confianza primero):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  // ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```

## <a name="samples"></a>Muestras

Hay tres ejemplos de CoreML para probar:

- El [ejemplo de predicción de precios de hábitat de Mars](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/) tiene entradas y salidas numéricas simples.

- El [ejemplo de vision & CoreML](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision) acepta un parámetro de imagen y usa el marco de trabajo para identificar las regiones cuadradas de la imagen, que se pasan a un modelo CoreML que reconoce los dígitos únicos.

- Por último, el [ejemplo de reconocimiento de imágenes CoreML](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition) usa CoreML para identificar las características de una foto. De forma predeterminada, usa el modelo **SqueezeNet** más pequeño (5 MB), pero se ha escrito para que pueda descargar e incorporar el modelo **VGG16** mayor (553MB). Para obtener más información, vea el [archivo Léame del ejemplo](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).

## <a name="related-links"></a>Vínculos relacionados

- [Machine Learning (Apple)](https://developer.apple.com/machine-learning/)
- [Ejemplo de CoreML (hábitat de Mars) (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/)
- [CoreML y Vision (reconocimiento de número) (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision)
- [Reconocimiento de imagen CoreML (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition)
- [CoreML con Azure Custom Vision (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlazuremodel)
- [Introducción a CoreML (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/703/)
