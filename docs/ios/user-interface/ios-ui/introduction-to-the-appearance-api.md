---
title: API de apariencia en Xamarin. iOS
description: iOS le permite aplicar valores de propiedades visuales en un nivel de clase estático en lugar de en objetos individuales para que el cambio se aplique a todas las instancias de ese control en la aplicación.
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/15/2018
ms.openlocfilehash: 6366a51f18ae2af8a94cf60a82e31ff413180e2d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573955"
---
# <a name="appearance-api-in-xamarinios"></a>API de apariencia en Xamarin. iOS

_iOS le permite aplicar valores de propiedades visuales en un nivel de clase estático en lugar de en objetos individuales para que el cambio se aplique a todas las instancias de ese control en la aplicación._

Esta funcionalidad se expone en Xamarin. iOS a través de una `Appearance` propiedad estática en todos los controles UIKit que lo admiten. La apariencia visual (propiedades como el color de matiz y la imagen de fondo) se puede personalizar fácilmente para dar una apariencia coherente a la aplicación. La API de apariencia se presentó en iOS 5 y, aunque algunas partes de ella han quedado en desuso en iOS 9, sigue siendo una buena forma de lograr algunos estilos y aplicarles efectos en las aplicaciones de Xamarin. iOS.

## <a name="overview"></a>Información general

iOS permite personalizar la apariencia de muchos controles UIKit para que los controles estándar se ajusten a la personalización de marca que se desea aplicar a la aplicación.

Hay dos maneras diferentes de aplicar una apariencia personalizada:

- **Directamente en una instancia del control** : puede establecer el color del matiz, la imagen de fondo y la posición del título (así como otros atributos) en muchos controles, como barras de herramientas, barras de navegación, botones y controles deslizantes.

- **Establecer valores predeterminados en la propiedad estática de la apariencia** : los atributos personalizables de cada control se exponen a través de la `Appearance` propiedad estática. Cualquier personalización que se aplique a estas propiedades se usará como valor predeterminado para cualquier control de ese tipo que se cree una vez establecida la propiedad.

La aplicación de ejemplo de apariencia muestra los tres métodos, tal y como se muestra en estas capturas de pantallas:

[![](introduction-to-the-appearance-api-images/appearance01-sml.png "The Appearance sample application demonstrates all three methods")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

A partir de iOS 8, el proxy de apariencia se ha ampliado a TraitCollections.
 `AppearanceForTraitCollection`se puede usar para establecer la apariencia predeterminada en una colección de rasgos determinada. Puede obtener más información al respecto en la guía [Introducción a los guiones gráficos](~/ios/user-interface/storyboards/unified-storyboards.md) .

## <a name="setting-appearance-properties"></a>Establecer propiedades de apariencia

En la primera pantalla, se usa la clase de apariencia estática para aplicar estilo a los botones y a los elementos amarillo/naranja de la siguiente manera:

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

Los estilos de elementos verdes se establecen como este, en el `ViewDidLoad` método que reemplaza los valores predeterminados y la clase de *apariencia* estática:

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Usar UIAppearance en Xamarin. Forms

La API de apariencia puede ser útil al [aplicar estilos a la aplicación de iOS en las](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api) soluciones de Xamarin. Forms. Algunas líneas de la `AppDelegate` clase pueden ayudar a implementar una combinación de colores específica sin tener que crear un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

### <a name="custom-themes-and-uiappearance"></a>Temas personalizados y UIAppearance

iOS permite que muchos atributos visuales de los controles de interfaz de usuario se "deshagan" mediante las API de *UIAppearance* para forzar que todas las instancias de un control determinado tengan la misma apariencia. Esto se expone como una propiedad de apariencia en muchas clases de control de interfaz de usuario, no en instancias individuales del control. El establecimiento de una propiedad de visualización en la `Appearance` propiedad estática afecta a todos los controles de ese tipo en la aplicación.

Para comprender mejor el concepto, considere un ejemplo.

Para cambiar un específico `UISegmentedControl` para que tenga un matiz magenta, se haría referencia al control específico de la pantalla de la siguiente manera `ViewDidLoad` :

```csharp
sg1.TintColor = UIColor.Magenta;
```

Como alternativa, establezca el valor en el panel de propiedades del diseñador:

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "Properties Pad Tint")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

En la imagen siguiente se muestra que esto establece el matiz solo en el control denominado ' SG1 '.

[![](introduction-to-the-appearance-api-images/image53.png "Setting the individual control tint")](introduction-to-the-appearance-api-images/image53.png#lightbox)

Para establecer muchos controles de esta manera sería completamente ineficaz, por lo que podemos establecer la `Appearance` propiedad estática en la propia clase. Esto se muestra en el código siguiente:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

La imagen siguiente muestra ahora ambos controles segmentados con la apariencia establecida en magenta:

[![](introduction-to-the-appearance-api-images/image54.png "Setting the Appearance control tint")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance`las propiedades se deben establecer en una fase temprana del ciclo de vida de la aplicación, como en el evento de AppDelegate `FinishedLaunching` o en un ViewController antes de que se muestren los controles afectados.

Consulte la [Introducción a la API de apariencia](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) para obtener información más detallada.

## <a name="related-links"></a>Vínculos relacionados

- [Apariencia (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/appearance)
- [Referencia del protocolo UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Apariencia de Xamarin. Forms](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api)
