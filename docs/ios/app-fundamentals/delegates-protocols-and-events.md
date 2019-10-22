---
title: Eventos, protocolos y delegados en Xamarin. iOS
description: En este documento se describe cómo trabajar con eventos, protocolos y delegados en Xamarin. iOS. Estos conceptos fundamentales están omnipresentes en el desarrollo de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: d42263733c7fa793713738be4b389eaa4850f38b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "68649355"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Eventos, protocolos y delegados en Xamarin. iOS

Xamarin. iOS usa controles para exponer eventos para la mayoría de las interacciones del usuario.
Las aplicaciones de Xamarin. iOS consumen estos eventos de la misma manera que las aplicaciones .NET tradicionales. Por ejemplo, la clase botón de Xamarin. iOS tiene un evento denominado TouchUpInside y consume este evento como si esta clase y evento estuvieran en una aplicación .NET.

Además de este enfoque de .NET, Xamarin. iOS expone otro modelo que se puede usar para la interacción más compleja y el enlace de datos. Esta metodología utiliza lo que Apple llama a los delegados y protocolos. Los delegados son similares en concepto C#a los delegados en, pero en lugar de definir y llamar a un método único, un delegado en Objective-C es una clase completa que se ajusta a un protocolo. Un protocolo es similar a una interfaz en C#, excepto en que sus métodos pueden ser opcionales. Por ejemplo, para rellenar un UITableView con datos, debe crear una clase de delegado que implemente los métodos definidos en el protocolo UITableViewDataSource a los que UITableView llamaría para rellenarse.

En este artículo, obtendrá información sobre todos estos temas, lo que le proporciona una base sólida para controlar los escenarios de devolución de llamada en Xamarin. iOS, entre los que se incluyen:

- **Eventos** : usar eventos .net con controles UIKit.
- **Protocolos** : Obtenga información sobre qué son los protocolos y cómo se usan, y cómo crear un ejemplo que proporcione datos para una anotación de mapa.
- **Delegados** : Obtenga información sobre los delegados de Objective-C extendiendo el ejemplo de mapa para controlar la interacción del usuario que incluye una anotación y, a continuación, aprender la diferencia entre los delegados fuertes y débiles y Cuándo utilizar cada uno de ellos.

Para ilustrar los protocolos y los delegados, crearemos una aplicación de mapa simple que agrega una anotación a un mapa, como se muestra aquí:

[![](delegates-protocols-and-events-images/01-map-sml.png "An example of a simple map application that adds an annotation to a map")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "An example annotation added to a map")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Antes de abordar esta aplicación, vamos a empezar examinando los eventos de .NET en UIKit.

## <a name="net-events-with-uikit"></a>Eventos de .NET con UIKit

Xamarin. iOS expone eventos .NET en controles UIKit. Por ejemplo, botón tiene un evento TouchUpInside, que se controla como lo haría normalmente en .NET, tal y como se muestra en el código siguiente C# , que usa una expresión lambda:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

También se puede implementar con un C# método anónimo de estilo 2,0 como este:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

El código anterior se conecta en el método `ViewDidLoad` de UIViewController. La variable `aButton` hace referencia a un botón, que se puede Agregar en el diseñador de iOS o con código. En la ilustración siguiente se muestra un botón que se ha agregado en el diseñador de iOS:

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "A button added in iOS Designer")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin. iOS también admite el estilo de acción de destino de la conexión del código a una interacción que se produce con un control. Para crear una acción de destino para el botón **Hello** , haga doble clic en él en el diseñador de iOS. Se mostrará el archivo de código subyacente de UIViewController y se pedirá al desarrollador que seleccione una ubicación para insertar el método de conexión:

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "The UIViewControllers code-behind file")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Una vez seleccionada una ubicación, se crea un nuevo método y se conecta al control. En el ejemplo siguiente, se escribirá un mensaje en la consola cuando se haga clic en el botón:

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "A message will be written to the console when the button is clicked")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Para más información sobre el patrón de acción de destino de iOS, consulte la sección de acciones de destino de las [principales capacidades de aplicación para iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) en la biblioteca de desarrolladores de iOS de Apple.

Para obtener más información sobre cómo usar el diseñador de iOS con Xamarin. iOS, consulte la documentación de [información general del diseñador de iOS](~/ios/user-interface/designer/index.md) .

## <a name="events"></a>Events

Si desea interceptar eventos de UIControl, tiene una serie de opciones: desde el uso de las C# expresiones lambda y las funciones de delegado hasta el uso de las API de Objective-C de bajo nivel.

En la siguiente sección se muestra cómo se captura el evento TouchDown en un botón, en función del control que se necesite.

## <a name="c-style"></a>C#Aplicar

Usar la sintaxis de delegado:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

Si lo prefiere:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Si desea que varios botones usen el mismo controlador para compartir el mismo código:

```csharp
void handler (object sender, EventArgs args)
{
   if (sender == button1)
      Console.WriteLine ("button1");
   else
      Console.WriteLine ("some other button");
}

button1.TouchDown += handler;
button2.TouchDown += handler;
```

## <a name="monitoring-more-than-one-kind-of-event"></a>Supervisión de más de un tipo de evento

Los C# eventos de las marcas UIControlEvent tienen una asignación de uno a uno a las marcas individuales. Si desea que el mismo fragmento de código Controle dos o más eventos, use el método `UIControl.AddTarget`:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Usar la sintaxis lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Si necesita usar características de bajo nivel de Objective-C, como enlazar a una instancia de objeto determinada e invocar un selector determinado:

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Tenga en cuenta que si implementa el método de instancia en una clase base heredada, debe ser un método público.

## <a name="protocols"></a>Protocolos

Un protocolo es una característica de lenguaje Objective-C que proporciona una lista de declaraciones de método. Sirve para un propósito similar a una interfaz en C#, la principal diferencia es que un protocolo puede tener métodos opcionales. No se llama a los métodos opcionales si la clase que adopta un protocolo no los implementa. Además, una sola clase de Objective-C puede implementar varios protocolos, de la misma C# forma que una clase puede implementar varias interfaces.

Apple usa protocolos a lo largo de iOS para definir contratos para que las clases adopten, al mismo tiempo que abstraen la clase de implementación del llamador, lo que funciona como una C# interfaz. Los protocolos se usan en escenarios que no son de delegado (por ejemplo, con el `MKAnnotation` ejemplo que se muestra a continuación) y con delegados (como se muestra más adelante en este documento, en la sección delegados).

### <a name="protocols-with-xamarinios"></a>Protocolos con Xamarin. iOS

Echemos un vistazo a un ejemplo que usa un protocolo Objective-C de Xamarin. iOS. En este ejemplo, usaremos el protocolo de `MKAnnotation`, que forma parte del marco de `MapKit`. `MKAnnotation` es un protocolo que permite a cualquier objeto que lo adopte proporcionar información sobre una anotación que se puede Agregar a un mapa. Por ejemplo, un objeto que implementa `MKAnnotation` proporciona la ubicación de la anotación y el título asociado a ella.

De este modo, el protocolo de `MKAnnotation` se utiliza para proporcionar los datos pertinentes que acompañan a una anotación. La vista real de la propia anotación se crea a partir de los datos del objeto que adopta el protocolo `MKAnnotation`. Por ejemplo, el texto de la llamada que aparece cuando el usuario puntea en la anotación (como se muestra en la siguiente captura de pantalla) procede de la propiedad `Title` de la clase que implementa el protocolo:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Example text for the callout when the user taps on the annotation")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Como se describe en la sección siguiente, los [protocolos Profunden](#protocols-deep-dive), Xamarin. iOS enlaza los protocolos a las clases abstractas. En el caso del Protocolo de `MKAnnotation` C# , la clase enlazada se denomina `MKAnnotation` para imitar el nombre del protocolo y es una subclase de `NSObject`, la clase base raíz para CocoaTouch. El protocolo requiere que se implemente un captador y un establecedor para la coordenada; sin embargo, un título y un subtítulo son opcionales. Por lo tanto, en la clase `MKAnnotation`, la propiedad `Coordinate` es *abstracta*, lo que requiere que se implemente y que las propiedades `Title` y `Subtitle` estén marcadas como *virtuales*, convirtiéndolos en opcionales, como se muestra a continuación:

```csharp
[Register ("MKAnnotation"), Model ]
public abstract class MKAnnotation : NSObject
{
    public abstract CLLocationCoordinate2D Coordinate
    {
        [Export ("coordinate")]
        get;
        [Export ("setCoordinate:")]
        set;
    }

    public virtual string Title
    {
        [Export ("title")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }

    public virtual string Subtitle
    {
        [Export ("subtitle")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }
...
}
```

Cualquier clase puede proporcionar datos de anotación simplemente derivando de `MKAnnotation`, siempre que se implemente al menos la propiedad `Coordinate`. Por ejemplo, a continuación se muestra una clase de ejemplo que toma la coordenada en el constructor y devuelve una cadena para el título:

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return title;
        }
    }
}
```

A través del Protocolo al que está enlazado, cualquier clase que cree subclases `MKAnnotation` puede proporcionar los datos pertinentes que usará la asignación al crear la vista de la anotación. Para agregar una anotación a un mapa, basta con llamar al método `AddAnnotation` de una instancia de `MKMapView`, como se muestra en el código siguiente:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

Aquí, la variable de mapa es una instancia de un `MKMapView`, que es la clase que representa la propia asignación. El `MKMapView` usará los datos de `Coordinate` derivados de la instancia de `SampleMapAnnotation` para colocar la vista de anotación en el mapa.

El protocolo `MKAnnotation` proporciona un conjunto conocido de funcionalidades a través de cualquier objeto que lo implemente, sin el consumidor (en este caso, la asignación) que necesita conocer los detalles de la implementación. Esto simplifica la adición de una serie de anotaciones posibles a un mapa.

### <a name="protocols-deep-dive"></a>Profundización en los protocolos

Puesto C# que las interfaces no admiten métodos opcionales, Xamarin. iOS asigna protocolos a clases abstractas. Por lo tanto, la adopción de un protocolo en Objective-C se logra en Xamarin. iOS derivando de la clase abstracta que está enlazada al Protocolo e implementando los métodos necesarios. Estos métodos se expondrán como métodos abstractos en la clase. Los métodos opcionales del Protocolo se enlazarán a los métodos virtuales de C# la clase.

Por ejemplo, a continuación se muestra una parte del Protocolo de `UITableViewDataSource` como enlazada en Xamarin. iOS:

```csharp
public abstract class UITableViewDataSource : NSObject
{
    [Export ("tableView:cellForRowAtIndexPath:")]
    public abstract UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath);
    [Export ("numberOfSectionsInTableView:")]
    public virtual int NumberOfSections (UITableView tableView){...}
...
}
```

Tenga en cuenta que la clase es abstracta. Xamarin. iOS hace que la clase sea abstracta para admitir métodos opcionales o necesarios en los protocolos.
Sin embargo, a diferencia de los protocolos Objective-C C# (o las C# interfaces), las clases no admiten la herencia múltiple. Esto afecta al diseño del C# código que usa los protocolos y, normalmente, conduce a clases anidadas. Más adelante en este documento encontrará más información sobre este problema, en la sección delegados.

 `GetCell(…)` es un método abstracto, enlazado al *selector*Objective-C, `tableView:cellForRowAtIndexPath:`, que es un método necesario del protocolo `UITableViewDataSource`. Selector es el término Objective-C para el nombre del método. Para aplicar el método según sea necesario, Xamarin. iOS lo declara como abstracto. El otro método, `NumberOfSections(…)`, se enlaza a `numberOfSectionsInTableview:`. Este método es opcional en el protocolo, por lo que Xamarin. iOS lo declara como virtual, por lo que es opcional C#reemplazar en.

Xamarin. iOS se encarga de todo el enlace de iOS. Sin embargo, si alguna vez necesita enlazar un protocolo desde Objective-C manualmente, puede hacerlo decorando una clase con el `ExportAttribute`. Este es el mismo método que usa Xamarin. iOS.

Para obtener más información sobre cómo enlazar tipos de Objective-C en Xamarin. iOS, consulte el artículo [enlace de tipos de Objective-c](~/ios/platform/binding-objective-c/index.md).

Sin embargo, aún no hemos terminado con los protocolos. También se usan en iOS como base para los delegados de Objective-C, que es el tema de la sección siguiente.

## <a name="delegates"></a>Delegados

iOS utiliza delegados de Objective-C para implementar el patrón de delegación, en el que un objeto pasa el trabajo a otro. El objeto que realiza el trabajo es el delegado del primer objeto. Un objeto indica a su delegado que realice el trabajo mediante el envío de mensajes después de que ocurran ciertas cosas. El envío de un mensaje como este en Objective-C es funcionalmente equivalente a llamar a C#un método en. Un delegado implementa métodos en respuesta a estas llamadas y, por tanto, proporciona funcionalidad a la aplicación.

Los delegados permiten ampliar el comportamiento de las clases sin necesidad de crear subclases. Las aplicaciones de iOS suelen usar delegados cuando una clase vuelve a llamar a otra después de que se produzca una acción importante. Por ejemplo, la clase `MKMapView` vuelve a llamar a su delegado cuando el usuario puntea una anotación en un mapa, lo que permite al autor de la clase delegar la oportunidad de responder dentro de la aplicación. Puede trabajar en un ejemplo de este tipo de uso de delegado más adelante en este artículo, en el ejemplo de uso de un delegado con Xamarin. iOS.

Llegados a este punto, es posible que se pregunte cómo una clase determina qué métodos llamar en su delegado. Se trata de otro lugar en el que se usan los protocolos. Normalmente, los métodos disponibles para un delegado proceden de los protocolos que adopten.

### <a name="how-protocols-are-used-with-delegates"></a>Cómo se usan los protocolos con delegados

Vimos anteriormente cómo se usan los protocolos para admitir la adición de anotaciones a un mapa.
Los protocolos también se usan para proporcionar un conjunto conocido de métodos para que las clases llamen después de que se produzcan ciertos eventos, por ejemplo, después de que el usuario pulse una anotación en un mapa o seleccione una celda en una tabla. Las clases que implementan estos métodos se conocen como los delegados de las clases que las llaman.

Las clases que admiten la delegación exponen una propiedad delegada a la que se asigna una clase que implementa el delegado. Los métodos que implemente para el delegado dependerán del protocolo que adopte el delegado determinado. Para el método `UITableView`, implemente el protocolo `UITableViewDelegate`, para el método `UIAccelerometer`, implementará `UIAccelerometerDelegate`, y así sucesivamente para cualquier otra clase a lo largo de iOS para la que desee exponer un delegado.

La clase `MKMapView` que vimos en el ejemplo anterior también tiene una propiedad denominada delegado, a la que llamará después de que se produzcan varios eventos. El delegado para `MKMapView` es de tipo `MKMapViewDelegate`.
Lo usará en breve en un ejemplo para responder a la anotación una vez seleccionada, pero primero vamos a analizar la diferencia entre los delegados fuertes y débiles.

### <a name="strong-delegates-vs-weak-delegates"></a>Delegados seguros frente a delegados débiles

Los delegados que hemos examinado hasta ahora son delegados fuertes, lo que significa que están fuertemente tipados. Los enlaces de Xamarin. iOS se incluyen con una clase fuertemente tipada para cada protocolo de delegado en iOS. Sin embargo, iOS también tiene el concepto de un delegado débil. En lugar de subclaser una clase enlazada al protocolo Objective-C para un determinado delegado, iOS también le permite enlazar los métodos de protocolo por su cuenta en cualquier clase que desee que derive de NSObject, decorando los métodos con ExportAttribute y, a continuación, proporcionar los selectores adecuados.
Al adoptar este enfoque, se asigna una instancia de la clase a la propiedad WeakDelegate en lugar de a la propiedad Delegate. Un delegado débil le ofrece la flexibilidad de tomar la clase delegada en una jerarquía de herencia diferente. Echemos un vistazo a un ejemplo de Xamarin. iOS que utiliza delegados fuertes y débiles.

### <a name="example-using-a-delegate-with-xamarinios"></a>Ejemplo de uso de un delegado con Xamarin. iOS

Para ejecutar código en respuesta al usuario que pulsa la anotación en nuestro ejemplo, podemos subclase `MKMapViewDelegate` y asignar una instancia a la propiedad `Delegate` del `MKMapView`. El protocolo `MKMapViewDelegate` solo contiene métodos opcionales.
Por lo tanto, todos los métodos son virtuales que se enlazan a este protocolo en la clase de `MKMapViewDelegate` de Xamarin. iOS. Cuando el usuario selecciona una anotación, la instancia de `MKMapView` enviará el mensaje de `mapView:didSelectAnnotationView:` a su delegado. Para controlar esto en Xamarin. iOS, es necesario invalidar el método `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` en la subclase MKMapViewDelegate de la siguiente manera:

```csharp
public class SampleMapDelegate : MKMapViewDelegate
{
    public override void DidSelectAnnotationView (
        MKMapView mapView, MKAnnotationView annotationView)
    {
        var sampleAnnotation =
            annotationView.Annotation as SampleMapAnnotation;

        if (sampleAnnotation != null) {

            //demo accessing the coordinate of the selected annotation to
            //zoom in on it
            mapView.Region = MKCoordinateRegion.FromDistance(
                sampleAnnotation.Coordinate, 500, 500);

            //demo accessing the title of the selected annotation
            Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
        }
    }
}
```

La clase SampleMapDelegate mostrada anteriormente se implementa como una clase anidada en el controlador que contiene la instancia de `MKMapView`. En Objective-C, a menudo verá que el controlador adopta varios protocolos directamente dentro de la clase. Sin embargo, como los protocolos se enlazan a las clases de Xamarin. iOS, las clases que implementan delegados fuertemente tipados suelen incluirse como clases anidadas.

Con la implementación de la clase delegada, solo tiene que crear una instancia del delegado en el controlador y asignarlo a la propiedad `Delegate` del `MKMapView`, como se muestra aquí:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    SampleMapDelegate _mapDelegate;
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //set the map's delegate
        _mapDelegate = new SampleMapDelegate ();
        map.Delegate = _mapDelegate;
        ...
    }
    class SampleMapDelegate : MKMapViewDelegate
    {
        ...
    }
}
```

Para usar un delegado débil para lograr lo mismo, debe enlazar el método personalmente en cualquier clase que derive de `NSObject` y asignarlo a la propiedad `WeakDelegate` de la `MKMapView`. Dado que la clase `UIViewController` deriva en última instancia de `NSObject` (como cada clase de Objective-C en CocoaTouch), podemos simplemente implementar un método enlazado a `mapView:didSelectAnnotationView:` directamente en el controlador y asignar el controlador al `WeakDelegate` de `MKMapView`, evitando la necesidad de la clase anidada. En el código siguiente se muestra este enfoque:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        //assign the controller directly to the weak delegate
        map.WeakDelegate = this;
    }
    //bind to the Objective-C selector mapView:didSelectAnnotationView:
    [Export("mapView:didSelectAnnotationView:")]
    public void DidSelectAnnotationView (MKMapView mapView,
        MKAnnotationView annotationView)
    {
        ...
    }
}
```

Al ejecutar este código, la aplicación se comporta exactamente igual que cuando se ejecutaba la versión del delegado fuertemente tipado. La ventaja de este código es que el delegado débil no requiere la creación de la clase adicional que se creó cuando se utiliza el delegado fuertemente tipado. Sin embargo, esto se produce a costa de la seguridad de tipos. Si quisiera cometer un error en el selector que se pasó al `ExportAttribute`, no se encontraría hasta el tiempo de ejecución.

### <a name="events-and-delegates"></a>Eventos y delegados

Los delegados se usan para las devoluciones de llamada en iOS de manera similar a la forma en que .NET utiliza los eventos. Para que las API de iOS y la manera en que usan los delegados de Objective-C parezcan más que .NET, Xamarin. iOS expone eventos .NET en muchos lugares en los que se usan delegados en iOS.

Por ejemplo, la implementación anterior en la que `MKMapViewDelegate` respondió a una anotación seleccionada también podría implementarse en Xamarin. iOS mediante un evento de .NET. En ese caso, el evento se definiría en `MKMapView` y se llamara `DidSelectAnnotationView`. Tendría una subclase `EventArgs` de tipo `MKMapViewAnnotationEventsArgs`. La propiedad `View` de `MKMapViewAnnotationEventsArgs` le proporcionaría una referencia a la vista de anotación, desde la que podría continuar con la misma implementación que tenía anteriormente, como se muestra aquí:

```csharp
map.DidSelectAnnotationView += (s,e) => {
    var sampleAnnotation = e.View.Annotation as SampleMapAnnotation;
    if (sampleAnnotation != null) {
        //demo accessing the coordinate of the selected annotation to
        //zoom in on it
        mapView.Region = MKCoordinateRegion.FromDistance (
            sampleAnnotation.Coordinate, 500, 500);

        //demo accessing the title of the selected annotation
        Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
    }
};
```

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo usar eventos, protocolos y delegados en Xamarin. iOS. Vimos cómo Xamarin. iOS expone eventos de estilo .NET normales para los controles.
A continuación, hemos aprendido los protocolos de Objective-C, incluido cómo son C# diferentes de las interfaces y cómo Xamarin. iOS los usa. Por último, hemos examinado los delegados de Objective-C desde una perspectiva de Xamarin. iOS. Vimos cómo Xamarin. iOS admite delegados fuertemente tipados y fuertemente tipados y cómo enlazar eventos .NET a métodos delegados.

## <a name="related-links"></a>Vínculos relacionados

- [Protocolos, delegados y eventos (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/protocols-delegates-events)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Enlazar tipos de Objective-C](~/ios/platform/binding-objective-c/index.md)
- [El lenguaje de programación Objective-C](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Diseño de interfaces de usuario en Xcode 4](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Principales capacidades de aplicación para iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
