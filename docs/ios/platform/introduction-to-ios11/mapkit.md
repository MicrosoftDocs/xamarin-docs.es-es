---
title: Nuevas características de MapKit en iOS 11
description: 'En este documento se describen las nuevas características de MapKit de iOS 11: marcadores de agrupación, el botón de brújula, la vista de escala y el botón de seguimiento de usuario.'
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 02bd25c4b4e251536dfdabdef109eb659fe3be37
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032156"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Nuevas características de MapKit en iOS 11

iOS 11 agrega las siguientes características nuevas a MapKit:

- [Agrupación en clústeres de anotación](#clustering)
- [Botón brújula](#compass)
- [Vista de escala](#scale)
- [Botón seguimiento de usuario](#user-tracking)

![Mapa que muestra marcadores agrupados y el botón brújula](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>Agrupar marcadores automáticamente al hacer zoom

En el ejemplo de MapKit de ejemplo ["Tandm"](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) se muestra cómo implementar la nueva característica de agrupación en clústeres de anotaciones de iOS 11.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. crear una subclase `MKPointAnnotation`

La clase de anotación Point representa cada marcador en el mapa. Se pueden agregar individualmente mediante `MapView.AddAnnotation()` o desde una matriz mediante `MapView.AddAnnotations()`.

Las clases de anotación de puntos no tienen una representación visual, solo son necesarias para representar los datos asociados con el marcador (lo que es más importante, la propiedad `Coordinate`, que es su latitud y longitud en el mapa) y las propiedades personalizadas:

```csharp
public class Bike : MKPointAnnotation
{
  public BikeType Type { get; set; } = BikeType.Tricycle;
  public Bike(){}
  public Bike(NSNumber lat, NSNumber lgn, NSNumber type)
  {
    Coordinate = new CLLocationCoordinate2D(lat.NFloatValue, lgn.NFloatValue);
    switch(type.NUIntValue) {
      case 0:
        Type = BikeType.Unicycle;
        break;
      case 1:
        Type = BikeType.Tricycle;
        break;
    }
  }
}
```

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. crear una subclase `MKMarkerAnnotationView` para marcadores únicos

La vista de anotaciones de marcador es la representación visual de cada anotación y tiene un estilo mediante propiedades como:

- **MarkerTintColor** : el color del marcador.
- **GlyphText** : texto que se muestra en el marcador.
- **GlyphImage** : establece la imagen que se muestra en el marcador.
- **DisplayPriority** : determina el orden z (comportamiento de apilamiento) cuando el mapa está abarrotado con marcadores. Use una de `Required`, `DefaultHigh`o `DefaultLow`.

Para admitir la agrupación en clústeres automática, también debe establecer:

- **ClusteringIdentifier** : controla qué marcadores se agrupan en clústeres. Puede usar el mismo identificador para todos los marcadores o usar identificadores diferentes para controlar la manera en que se agrupan.

```csharp
[Register("BikeView")]
public class BikeView : MKMarkerAnnotationView
{
  public static UIColor UnicycleColor = UIColor.FromRGB(254, 122, 36);
  public static UIColor TricycleColor = UIColor.FromRGB(153, 180, 44);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;

      var bike = value as Bike;
      if (bike != null){
        ClusteringIdentifier = "bike";
        switch(bike.Type){
          case BikeType.Unicycle:
            MarkerTintColor = UnicycleColor;
            GlyphImage = UIImage.FromBundle("Unicycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultLow;
            break;
          case BikeType.Tricycle:
            MarkerTintColor = TricycleColor;
            GlyphImage = UIImage.FromBundle("Tricycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
            break;
        }
      }
    }
  }
```

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. crear un `MKAnnotationView` para representar clústeres de marcadores

Aunque la vista de anotación que representa un clúster de marcadores _podría_ ser una imagen simple, los usuarios esperan que la aplicación proporcione indicaciones visuales sobre cuántos marcadores se han agrupado.

En el [código de ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) se usa CoreGraphics para representar el número de marcadores del clúster, así como una representación de gráfico en círculos de la proporción de cada tipo de marcador.

También debe establecer:

- **DisplayPriority** : determina el orden z (comportamiento de apilamiento) cuando el mapa está abarrotado con marcadores. Use una de `Required`, `DefaultHigh`o `DefaultLow`.
- **CollisionMode** : `Circle` o `Rectangle`.

```csharp
[Register("ClusterView")]
public class ClusterView : MKAnnotationView
{
  public static UIColor ClusterColor = UIColor.FromRGB(202, 150, 38);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;
      var cluster = MKAnnotationWrapperExtensions.UnwrapClusterAnnotation(value);
      if (cluster != null)
      {
        var renderer = new UIGraphicsImageRenderer(new CGSize(40, 40));
        var count = cluster.MemberAnnotations.Length;
        var unicycleCount = CountBikeType(cluster.MemberAnnotations, BikeType.Unicycle);

        Image = renderer.CreateImage((context) => {
          // Fill full circle with tricycle color
          BikeView.TricycleColor.SetFill();
          UIBezierPath.FromOval(new CGRect(0, 0, 40, 40)).Fill();
          // Fill pie with unicycle color
          BikeView.UnicycleColor.SetFill();
          var piePath = new UIBezierPath();
          piePath.AddArc(new CGPoint(20,20), 20, 0, (nfloat)(Math.PI * 2.0 * unicycleCount / count), true);
          piePath.AddLineTo(new CGPoint(20, 20));
          piePath.ClosePath();
          piePath.Fill();
          // Fill inner circle with white color
          UIColor.White.SetFill();
          UIBezierPath.FromOval(new CGRect(8, 8, 24, 24)).Fill();
          // Finally draw count text vertically and horizontally centered
          var attributes = new UIStringAttributes() {
            ForegroundColor = UIColor.Black,
            Font = UIFont.BoldSystemFontOfSize(20)
          };
          var text = new NSString($"{count}");
          var size = text.GetSizeUsingAttributes(attributes);
          var rect = new CGRect(20 - size.Width / 2, 20 - size.Height / 2, size.Width, size.Height);
          text.DrawString(rect, attributes);
        });
      }
    }
  }
  public ClusterView(){}
  public ClusterView(MKAnnotation annotation, string reuseIdentifier) : base(annotation, reuseIdentifier)
  {
    DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
    CollisionMode = MKAnnotationViewCollisionMode.Circle;
    // Offset center point to animate better with marker annotations
    CenterOffset = new CoreGraphics.CGPoint(0, -10);
  }
  private nuint CountBikeType(IMKAnnotation[] members, BikeType type) {
    nuint count = 0;
    foreach(Bike member in members){
      if (member.Type == type) ++count;
    }
    return count;
  }
}
```

### <a name="4-register-the-view-classes"></a>4. registrar las clases de vista

Cuando el control de vista de mapa se crea y se agrega a una vista, registre los tipos de vista de anotación para habilitar el comportamiento de la agrupación en clústeres automática a medida que se acerca y se aleja el mapa:

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. representar el mapa.

Cuando se representa el mapa, los marcadores de anotación se agrupan o se representan en función del nivel de zoom. A medida que cambia el nivel de zoom, los marcadores se animan dentro y fuera de los clústeres.

![Simulador que muestra marcadores agrupados en la asignación](mapkit-images/cyclemap-sml.png)

Consulte la [sección mapas](~/ios/user-interface/controls/ios-maps/index.md) para obtener más información sobre cómo Mostrar datos con MapKit.

<a name="compass" />

## <a name="compass-button"></a>Botón brújula

iOS 11 agrega la capacidad de extraer la brújula del mapa y representarla en otro lugar de la vista. Consulte la [aplicación de ejemplo Tandm](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) para obtener un ejemplo.

Cree un botón que se parezca a una brújula (incluida la animación en directo cuando cambie la orientación del mapa) y lo represente en otro control.

![Botón brújula en la barra de navegación](mapkit-images/compass-sml.png)

El código siguiente crea un botón de brújula y lo representa en la barra de navegación:

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

La propiedad `ShowsCompass` se puede utilizar para controlar la visibilidad de la brújula predeterminada dentro de la vista del mapa.

<a name="scale" />

## <a name="scale-view"></a>Vista de escala

Agregue la escala en otra parte de la vista mediante el método `MKScaleView.FromMapView()` para obtener una instancia de la vista de escala que se va a agregar en otra parte de la jerarquía de vistas.

![Vista de escalado superpuesta en un mapa](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

La propiedad `ShowsScale` se puede utilizar para controlar la visibilidad de la brújula predeterminada dentro de la vista del mapa.

<a name="user-tracking" />

## <a name="user-tracking-button"></a>Botón seguimiento de usuario

El botón de seguimiento del usuario centra el mapa en la ubicación actual del usuario. Use el método `MKUserTrackingButton.FromMapView()` para obtener una instancia del botón, aplicar los cambios de formato y agregar en cualquier parte de la jerarquía de vistas.

![Botón de ubicación de usuario superpuesto en un mapa](mapkit-images/user-location-sml.png)

```csharp
var button = MKUserTrackingButton.FromMapView(MapView);
button.Layer.BackgroundColor = UIColor.FromRGBA(255,255,255,80).CGColor;
button.Layer.BorderColor = UIColor.White.CGColor;
button.Layer.BorderWidth = 1;
button.Layer.CornerRadius = 5;
button.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(button); // constraints omitted for simplicity
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de MapKit ' Tandm '](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [Novedades de MapKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/237/)
