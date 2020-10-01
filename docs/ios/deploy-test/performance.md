---
title: Rendimiento de Xamarin.iOS
description: Este documento describe las técnicas que pueden usarse para mejorar el rendimiento y el uso de memoria de las aplicaciones Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 02b1f628-52d9-49de-8479-f2696546ca3f
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/29/2016
ms.openlocfilehash: 06d413b2bf07df38f78e93af027de2c7dc0badcc
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436779"
---
# <a name="xamarinios-performance"></a>Rendimiento de Xamarin.iOS

El mal rendimiento de una aplicación se manifiesta de muchas formas. Puede hacer que parezca que una aplicación deja de responder, puede ocasionar un desplazamiento lento y puede reducir la duración de la batería. La optimización del rendimiento conlleva mucho más que la mera implementación de código eficaz. También debe tenerse en cuenta la experiencia de rendimiento de la aplicación del usuario. Por ejemplo, asegurarse de que las operaciones se ejecuten sin evitar que el usuario realice otras actividades puede ayudar a mejorar su experiencia.

Este documento describe las técnicas que pueden usarse para mejorar el rendimiento y el uso de memoria de las aplicaciones Xamarin.iOS.

> [!NOTE]
> Antes de leer este artículo, debería leer [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md), donde se explican técnicas no específicas de una plataforma para mejorar el uso de memoria y el rendimiento de las aplicaciones compiladas con la plataforma Xamarin.

## <a name="avoid-strong-circular-references"></a>Evitar referencias circulares seguras

En algunas situaciones es posible crear referencias circulares seguras que podrían impedir que el recolector de elementos no utilizados reclame memoria de los objetos. Por ejemplo, considere el caso en el que una subclase derivada de [`NSObject`](xref:Foundation.NSObject), como una clase que hereda de [`UIView`](xref:UIKit.UIView), se agrega a un contenedor derivado de `NSObject` y se hace referencia fuerte a la misma desde Objective-C, como se muestra en el ejemplo de código siguiente:

```csharp
class Container : UIView
{
    public void Poke ()
    {
    // Call this method to poke this object
    }
}

class MyView : UIView
{
    Container parent;
    public MyView (Container parent)
    {
        this.parent = parent;
    }

    void PokeParent ()
    {
        parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

Cuando este código crea la instancia `Container`, el objeto de C# tendrá una referencia fuerte a un objeto de Objective-C. De forma similar, la instancia `MyView` también tendrá una referencia fuerte a un objeto de Objective-C.

Además, la llamada a `container.AddSubview` aumentará el recuento de referencias en la instancia `MyView` no administrada. Cuando esto sucede, el tiempo de ejecución de Xamarin.iOS crea una instancia `GCHandle` para mantener activo el objeto `MyView` en código administrado, porque no hay ninguna garantía de que otros objetos administrados mantengan una referencia al mismo. Desde una perspectiva de código administrado, el objeto `MyView` se reclamaría tras la llamada a [`AddSubview`](xref:UIKit.UIView.AddSubview(UIKit.UIView)) si no fuera por el `GCHandle`.

El objeto `MyView` no administrado tendrá un `GCHandle` que apunta al objeto administrado, conocido como *vínculo fuerte*. El objeto administrado contendrá una referencia a la instancia `Container`. A su vez, la instancia `Container` tendrá una referencia administrada al objeto `MyView`.

En casos donde un objeto contenido mantiene un vínculo a su contenedor, hay varias opciones disponibles para solucionar la referencia circular:

- Interrumpir manualmente el ciclo estableciendo el vínculo al contenedor en `null`.
- Quitar manualmente el objeto contenido del contenedor.
- Llamar a `Dispose` en los objetos.
- Evitar la referencia circular manteniendo una referencia débil al contenedor. Para más información sobre las referencias débiles.

### <a name="using-weakreferences"></a>Empleo de referencias débiles

Una manera de evitar un ciclo es usar una referencia débil del elemento secundario al primario. Por ejemplo, el código anterior podría escribirse así:

```csharp
class Container : UIView
{
    public void Poke ()
    {
        // Call this method to poke this object
    }
}

class MyView : UIView
{
    WeakReference<Container> weakParent;
    public MyView (Container parent)
    {
        this.weakParent = new WeakReference<Container> (parent);
    }

    void PokeParent ()
    {
        if (weakParent.TryGetTarget (out var parent))
            parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

Aquí, el objeto contenido no mantiene activo el elemento primario. Pero el elemento primario mantiene activo el elemento secundario durante la llamada realizada a `container.AddSubView`.

Esto también ocurre en las API de iOS que usan el modelo de delegado o de origen de datos, donde una clase del mismo nivel contiene la implementación; por ejemplo, al establecer la propiedad [`Delegate`](xref:UIKit.UITableView.Delegate*)
o [`DataSource`](xref:UIKit.UITableView.DataSource*)
de la clase [`UITableView`](xref:UIKit.UITableView).

En el caso de las clases que se crean exclusivamente para la implementación de un protocolo, por ejemplo, [`IUITableViewDataSource`](xref:UIKit.IUITableViewDataSource), en lugar de crear una subclase, puede implementar la interfaz de la clase, invalidar el método y asignar la propiedad `DataSource` a `this`.

#### <a name="weak-attribute"></a>Atributo Weak

[Xamarin.iOS 11.10](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_11/xamarin.ios_11.10.md#WeakAttribute) ha presentado el atributo `[Weak]`. Al igual que `WeakReference <T>`, `[Weak]` puede usarse para interrumpir [referencias circulares seguras](#avoid-strong-circular-references), pero con aún menos código.

Considere el código siguiente, que usa `WeakReference <T>`:

```csharp
public class MyFooDelegate : FooDelegate {
    WeakReference<MyViewController> controller;
    public MyFooDelegate (MyViewController ctrl) => controller = new WeakReference<MyViewController> (ctrl);
    public void CallDoSomething ()
    {
        MyViewController ctrl;
        if (controller.TryGetTarget (out ctrl)) {
            ctrl.DoSomething ();
        }
    }
}
```

El código equivalente con `[Weak]` es mucho más conciso:

```csharp
public class MyFooDelegate : FooDelegate {
    [Weak] MyViewController controller;
    public MyFooDelegate (MyViewController ctrl) => controller = ctrl;
    public void CallDoSomething () => controller.DoSomething ();
}
```

A continuación hay otro ejemplo del uso de `[Weak]` en el contexto del modelo de [delegación](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html):

```csharp
public class MyViewController : UIViewController
{
    WKWebView webView;

    protected MyViewController (IntPtr handle) : base (handle) { }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        webView = new WKWebView (View.Bounds, new WKWebViewConfiguration ());
        webView.UIDelegate = new UIDelegate (this);
        View.AddSubview (webView);
    }
}

public class UIDelegate : WKUIDelegate
{
    [Weak] MyViewController controller;

    public UIDelegate (MyViewController ctrl) => controller = ctrl;

    public override void RunJavaScriptAlertPanel (WKWebView webView, string message, WKFrameInfo frame, Action completionHandler)
    {
        var msg = $"Hello from: {controller.Title}";
        var alertController = UIAlertController.Create (null, msg, UIAlertControllerStyle.Alert);
        alertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
        controller.PresentViewController (alertController, true, null);
        completionHandler ();
    }
}
```

### <a name="disposing-of-objects-with-strong-references"></a>Eliminación de objetos con referencias seguras

Si existe una referencia fuerte y la dependencia es difícil de quitar, haga que un método `Dispose` borre el puntero primario.

Para los contenedores, invalide el método `Dispose` para quitar los objetos contenidos, como se muestra en el ejemplo de código siguiente:

```csharp
class MyContainer : UIView
{
    public override void Dispose ()
    {
        // Brute force, remove everything
        foreach (var view in Subviews)
        {
              view.RemoveFromSuperview ();
        }
        base.Dispose ();
    }
}
```

Para un objeto secundario que mantiene una referencia fuerte a su elemento primario, borre la referencia al elemento primario en la implementación de `Dispose`:

```csharp
class MyChild : UIView
{
    MyContainer container;
    public MyChild (MyContainer container)
    {
        this.container = container;
    }
    public override void Dispose ()
    {
        container = null;
    }
}
```

Para más información sobre la liberación de las referencias fuertes, vea [Liberar los recursos de IDisposable](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable).
También hay una excelente explicación en esta entrada de blog: [Xamarin.iOS, the garbage collector and me](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me) (Xamarin.iOS, el recolector de elementos no utilizados y yo).

### <a name="more-information"></a>Más información

Para más información, vea [Rules to Avoid Retain Cycles](https://www.cocoawithlove.com/2009/07/rules-to-avoid-retain-cycles.html) (Reglas para evitar ciclos de retención) en Cocoa With Love, [Is this a bug in MonoTouch GC](https://stackoverflow.com/questions/13058521/is-this-a-bug-in-monotouch-gc) (¿Esto es un error en MonoTouch GC) en StackOverflow y [Why can't MonoTouch GC kill managed objects with refcount > 1?](https://stackoverflow.com/questions/13064669/why-cant-monotouch-gc-kill-managed-objects-with-refcount-1) (¿Por qué no puede MonoTouch GC eliminar objetos administrados con refcount > 1?) en StackOverflow.

## <a name="optimize-table-views"></a>Optimizar las vistas de tabla

Los usuarios esperan un desplazamiento sin problemas y unos tiempos de carga rápidos para las instancias de [`UITableView`](xref:UIKit.UITableView). Pero el rendimiento del desplazamiento puede verse afectado si las celdas contienen jerarquías de vista profundamente anidadas o si las celdas contienen diseños complejos. Pero hay técnicas que se pueden usar para evitar un mal rendimiento de `UITableView`:

- Reutilizar las celdas. Para más información, vea [Reutilizar celdas](#reuse-cells).
- Reducir el número de subvistas.
- Almacenar en caché el contenido de celda recuperado de un servicio web.
- Almacenar en caché el alto de todas las filas si no son idénticas.
- Hacer que la celda, y otras vistas, sean opacas.
- Evitar la escala de imágenes y los degradados.

Colectivamente, estas técnicas facilitan que las instancias de [`UITableView`](xref:UIKit.UITableView) se desplacen sin problemas.

### <a name="reuse-cells"></a>Reutilizar las celdas

Al mostrar cientos de filas en una [`UITableView`](xref:UIKit.UITableView), sería un desperdicio de memoria crear cientos de objetos [`UITableViewCell`](xref:UIKit.UITableViewCell) cuando solo un pequeño número de ellos se muestra en pantalla a la vez. En su lugar, se pueden cargar en la memoria solo las celdas visibles en la pantalla y cargar el **contenido** en estas celdas reutilizadas. Esto evita la creación de instancias de cientos de objetos adicionales, con lo que se ahorra tiempo y memoria.

Por tanto, cuando una celda desaparece de la pantalla, su vista puede colocarse en una cola para su reutilización, como se muestra en el ejemplo de código siguiente:

```csharp
class MyTableSource : UITableViewSource
{
    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        // iOS will create a cell automatically if one isn't available in the reuse pool
        var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

        // Perform required cell actions
        return cell;
    }
}
```

Cuando el usuario se desplaza, la [`UITableView`](xref:UIKit.UITableView) llama a la invalidación de `GetCell` para solicitar nuevas vistas para mostrar. Esta invalidación llama al método [`DequeueReusableCell`](xref:UIKit.UITableView.DequeueReusableCell(Foundation.NSString)) y, si hay una celda disponible para su reutilización, se devuelve.

Para más información, vea [Reutilizar celdas](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) en [Rellenar una tabla con datos](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

## <a name="use-opaque-views"></a>Usar vistas opacas

Asegúrese de que las vistas que no tienen transparencia definida tienen establecida su propiedad [`Opaque`](xref:UIKit.UIView.Opaque). Esto garantizará que el sistema de dibujo represente las vistas de forma óptima. Esto es especialmente importante cuando se incrusta una vista en una [`UIScrollView`](xref:UIKit.UIScrollView) o cuando forma parte de una animación compleja. De lo contrario, el sistema de dibujo compondrá las vistas con otro contenido, lo que puede afectar considerablemente al rendimiento.

## <a name="avoid-fat-xibs"></a>Evitar FAT XIB

Aunque en gran medida los XIB se han sustituido por guiones gráficos, hay algunas circunstancias donde todavía se pueden usar XIB. Cuando se carga un XIB en memoria, todo su contenido se carga en la memoria, incluidas todas las imágenes. Si el XIB contiene una vista que no se está usando inmediatamente, la memoria se desperdicia. Por tanto, cuando use XIB, asegúrese de que hay solo un XIB por cada controlador de vista y, si es posible, separe la jerarquía de vistas del controlador de vista en XIB independientes.

## <a name="optimize-image-resources"></a>Optimizar los recursos de imagen

Las imágenes son uno de los recursos con más consumo usados por las aplicaciones y se suelen capturar en altas resoluciones. Por tanto, al mostrar una imagen del paquete de la aplicación en una [`UIImageView`](xref:UIKit.UIImageView), asegúrese de que la imagen y `UIImageView` tienen el mismo tamaño. La escala de imágenes en tiempo de ejecución puede ser una operación costosa, especialmente si la `UIImageView` está incrustada en una [`UIScrollView`](xref:UIKit.UIScrollView).

Para más información, vea [Optimizar los recursos de imagen](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) en la guía [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="test-on-devices"></a>Realizar pruebas en dispositivos

Comience a implementar y probar una aplicación en un dispositivo físico tan pronto como sea posible. Los simuladores no igualan a la perfección los comportamientos y las limitaciones de los dispositivos, por lo que es importante realizar las pruebas en un escenario de dispositivos reales tan pronto como sea posible.

En concreto, el simulador no simula de ningún modo las restricciones de memoria o CPU de un dispositivo físico.

## <a name="synchronize-animations-with-the-display-refresh"></a>Sincronizar animaciones con la actualización de la pantalla

Los juegos tienden a tener bucles estrechos para ejecutar la lógica del juego y actualizar la pantalla. Las velocidades de fotogramas típicas oscilan entre 30 y 60 fotogramas por segundo. Algunos desarrolladores creen que deben actualizar la pantalla tantas veces como sea posible por segundo, combinando la simulación del juego con las actualizaciones de la pantalla, y es posible que se vean tentados a superar los 60 fotogramas por segundo.

Pero el servidor de pantalla realiza las actualizaciones de pantalla a un límite máximo de 60 veces por segundo. Por tanto, intentar actualizar la pantalla más rápidamente que este límite puede provocar que la pantalla se entrecorte y se produzcan micro-parpadeos. Es mejor estructurar el código para que las actualizaciones de pantalla se sincronicen con la actualización en pantalla. Esto puede lograrse mediante el uso de la clase [`CoreAnimation.CADisplayLink`](xref:CoreAnimation.CADisplayLink), que es un temporizador adecuado para la visualización y juegos que se ejecuta a 60 fotogramas por segundo.

## <a name="avoid-core-animation-transparency"></a>Evitar la transparencia de la animación principal

Evitar la transparencia de la animación de principal mejora el rendimiento de la composición de mapas de bits. En general, evite capas transparentes y bordes difuminados si es posible.

## <a name="avoid-code-generation"></a>Evitar la generación de código

La generación de código dinámicamente con `System.Reflection.Emit` o *Dynamic Language Runtime* debe evitarse porque el kernel de iOS impide la ejecución de código dinámico.

## <a name="summary"></a>Resumen

En este artículo se han descrito y explicado técnicas para aumentar el rendimiento de las aplicaciones compiladas con Xamarin.iOS. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que está realizando una CPU y la cantidad de memoria consumida por una aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)